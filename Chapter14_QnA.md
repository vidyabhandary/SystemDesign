# Chapter 14 - Design a Text Messaging App - (Q&A)

# Fundamental Requirements Questions

1. **What are the key functional requirements for the text messaging app design mentioned in the document?** #messaging #requirements #functional-specs

   **Answer:** The key functional requirements include:

   - Support for chatrooms containing between 2 to 1,000 users
   - Messages limited to 1,000 UTF-8 characters (up to 4KB in size)
   - Delivery confirmation and read receipt capabilities
   - Message logging with users able to view/search up to 10MB of past messages
   - Private message bodies with end-to-end encryption
   - No requirement to handle user onboarding, multiple chatrooms for the same group, template messages, online status indicators, or media messages (only text)

2. **What are the non-functional requirements specified for the text messaging app design?** #scalability #performance #availability #security

   **Answer:** The non-functional requirements include:

   - Scalability: Support for 100K simultaneous users, with each user sending a 4KB message every minute (400MB/min write rate)
   - High availability: Four nines (99.99%) availability
   - High performance: 10-second P99 message delivery time
   - Security and privacy: User authentication required and private messages
   - Consistency: Strict ordering of messages is not necessary

# Architecture Design Questions

1. **Describe the high-level architecture of the proposed text messaging system with its main components.** #system-architecture #service-design #component-breakdown

   **Answer:** The high-level architecture consists of several key services:

   - Sender Service: Receives messages from senders and immediately delivers them to recipients while recording messages in the Message Service
   - Message Service: Handles requests for sent messages, received messages, and unreceived messages
   - Connection Service: Manages users' active and blocked connections, stores connection metadata (names, avatars, public keys)
   - API Gateway: Entry point for user requests to the various services

   The Sender Service is further divided into the New Message Service and the Message-Sending Service with a Kafka topic between them. The Message-Sending Service includes components like the Message Generator, Message Consumer Service, Host Assigner Service, and interfaces with Redis for checkpointing.

2. **How does the system handle message delivery to multiple devices belonging to the same user?** #multi-device #synchronization #message-delivery

   **Answer:** The system handles multi-device delivery through several mechanisms:

   - When a message is sent to a user, it's delivered to all their active devices via the sender service and the WebSocket connections of the receivers
   - The Message Service logs all messages, allowing devices to retrieve missed messages
   - When a user logs into a new device, it can download past messages from the Message Service
   - For key management, when a user's device generates a new public-private key pair, the Connection Service notifies all the user's connections with the new public key
   - The design suggests a grace period when changing keys (e.g., seven days) during which both old and new keys are valid
   - If a message cannot be delivered to all devices immediately, it's stored in the Message Service with a retention period of a few weeks for later retrieval

3. **Explain the role of Kafka in the text messaging system architecture and why it's used.** #kafka #message-broker #scalability #fault-tolerance

   **Answer:** Kafka plays several critical roles in the text messaging system:

   1. Acts as a buffer between the New Message Service and the Message-Sending Service, allowing the system to handle unpredictable traffic surges without outages
   2. Enables asynchronous processing of messages that might contain up to 5,000 recipients
   3. Provides persistent storage for messages before they're delivered, improving fault tolerance
   4. Allows the Message-Sending Service to consume messages only when it has sufficient resources to process them
   5. Enables separate scaling of message producers and message consumers
   6. Provides partitioning capabilities that can be used with consistent hashing to ensure messages to a particular receiver are consumed in order
   7. Buffers writes to Redis for higher fault-tolerance, especially during traffic spikes

   Overall, Kafka enhances the system's scalability, fault tolerance, and ability to handle traffic surges, though it introduces additional latency.

# Connection Management Questions

1. **How does the Connection Service handle sender blocking, and what are the challenges associated with it?** #blocking #user-connections #privacy

   **Answer:** The Connection Service handles sender blocking at multiple layers:

   - On the sender's device to prevent message sending attempts
   - On the server to block messages that bypass client-side blocking
   - On the recipient's device to block any messages that get through server-side blocking

   Challenges include:

   1. Implementing blocking at every layer to prevent circumvention
   2. Storing blocked recipient connections on devices to reduce server traffic
   3. Enabling immediate blocking/unblocking even when offline (using a dead letter queue)
   4. Dealing with potential inconsistencies when blocking/unblocking requests are made from multiple devices
   5. Handling security concerns where senders might attempt to hack their app to remove blocking data
   6. Managing the general consistency problem present in apps with offline write operations

   Solutions discussed include asking users to confirm the final state of each device or placing limits on write operations to prevent inconsistency, both of which present UX tradeoffs.

2. **What consistency challenges exist in the design when implementing blocking and unblocking features across multiple devices?** #consistency #multi-device #offline-functionality

   **Answer:** The design faces several consistency challenges when implementing blocking/unblocking:

   1. When a user makes blocking/unblocking requests from multiple devices, it can be unclear which request should take precedence
   2. Device clocks cannot be perfectly synchronized, so timestamps cannot reliably determine request order
   3. Even limiting connections to one device at a time doesn't solve the problem, as requests might be queued offline
   4. The general consistency problem exists whenever an app offers offline functionality with write operations

   The design proposes two potential solutions:

   - Having the app ask the user to confirm the final state when conflicts are detected
   - Placing limits on write operations, such as not allowing a device to unblock until all other devices have synchronized

   Both solutions involve tradeoffs between usability and consistency. This is an inherent challenge where offline functionality is required for better user experience.

3. **How does the design handle public key management for end-to-end encryption?** #e2e-encryption #key-management #security

   **Answer:** The design handles public key management through several mechanisms:

   1. When a device installs or reinstalls the app, it generates a public-private key pair
   2. The public key is stored in the Connection Service
   3. The Connection Service immediately updates all the user's connections with the new public key via WebSocket connections
   4. To handle key updates smoothly, the design suggests a grace period (e.g., seven days) during which both old and new keys are valid
   5. If a sender encrypts a message with an outdated key, the recipient can request a resend with the new key included
   6. Messages include a cryptographic hash (e.g., SHA-2) to verify successful decryption
   7. The system accommodates up to 5,000 key update notifications (1,000 connections with 5 devices each)

   This approach balances security with the practical challenges of key distribution in a multi-device environment.

# Message Processing Questions

1. **Describe the message flow from when a user composes a message until it's delivered to a recipient.** #message-flow #system-process #end-to-end

   **Answer:** The complete message flow includes:

   1. User composes a message on their device, including sender ID, recipient IDs, and body text
   2. Client encrypts the message body with each recipient's public key
   3. Client sends the encrypted message to the Sender Service via the API Gateway
   4. New Message Service validates the request format and produces it to the Kafka topic
   5. Message Generator consumes from Kafka and generates a separate message for each recipient
   6. Messages are produced to a Recipient Kafka Topic with checkpoints in Redis
   7. Message Consumer Service checks if the sender should be blocked
   8. The service queries the Host Assigner Service to find which host serves each recipient
   9. The appropriate host delivers the message to the recipient client via WebSocket
   10. In parallel, the message is logged to the Message Service
   11. The recipient client verifies the message isn't a duplicate and displays it
   12. When the user reads the message, a read receipt is sent back to the sender

   If the recipient is offline, the message is stored in the Message Service for later retrieval.

# Scalability and Fault Tolerance Questions

1. **How does the system handle traffic spikes and unpredictable message volumes?** #traffic-management #scalability #performance

   **Answer:** The system handles traffic spikes and unpredictable volumes through:

   1. Using Kafka as a buffer between message reception and processing
      - The New Message Service accepts messages and produces them to Kafka
      - The Message-Sending Service consumes messages only when ready to process them
   2. Asynchronous processing of messages, particularly for messages with many recipients
   3. Separating text messaging from media file transfers, allowing text to continue with low latency during spikes
   4. Using a host cluster with dynamic scaling capabilities
   5. Implementing monitoring for utilization across services to make scaling decisions
   6. Rate limiting to prevent abuse and abnormal message volumes
   7. The ability to store messages in the Message Service if immediate delivery isn't possible

   Using queues introduces latency, particularly during traffic spikes, which presents a tradeoff between real-time messaging requirements and cost-efficient scaling.

# Privacy and Security Questions

1. **How does the design implement end-to-end encryption, and what are the benefits mentioned?** #encryption #privacy #security

   **Answer:** The design implements end-to-end encryption through the following process:

   1. Receivers generate public-private key pairs when setting up the app
   2. The Connection Service stores and distributes users' public keys to their connections
   3. Senders encrypt messages with each recipient's public key before transmission
   4. Messages remain encrypted during transit through the system
   5. Recipients decrypt messages using their private keys stored only on their devices

   Benefits mentioned include:

   - Messages being automatically encrypted both in transit and at rest
   - The system itself cannot access message contents, enhancing privacy
   - Even if the system is breached, attackers cannot read message contents without private keys
   - Limited system access to message metadata reduces privacy risks
   - Messages can be deleted after a retention period, limiting the impact of potential future security breaches
   - Including message hashes allows verification of successful decryption

2. **What privacy considerations are included in the logging and monitoring approach?** #privacy #logging #monitoring

   **Answer:** The design includes several privacy considerations for logging and monitoring:

   1. Never logging the contents of messages, including all fields (sender, receiver, body, delivery confirmation, and read receipt)
   2. Logging requests between services without message content
   3. Logging message sent events with limited details to preserve privacy
   4. Logging whether messages are sent within a data center or between data centers
   5. Logging error events like failures in sending messages or delivery confirmations
   6. Monitoring for fraud and anomalous situations (like high message rates) without examining message content
   7. Using rate limiters to prevent abuse while preserving privacy

   The approach balances the need for operational visibility and security monitoring with user privacy, ensuring that sensitive content remains private even in logging and monitoring systems.

# Additional Feature Questions

1. **How does the system approach search functionality for users' messages?** #search #user-experience #data-access

   **Answer:** The system takes a simple yet practical approach to search functionality:

   1. Each user can only search their own messages
   2. The design suggests implementing a direct text search on the client device rather than building a reverse index
   3. This avoids the costs of designing, implementing, and maintaining a reverse index
   4. The average client's message storage would likely be less than 1GB (excluding media files)
   5. Messages can be loaded into memory on the device and searched directly
   6. For media files, the system would allow searching file names but not file contents
   7. This approach balances functionality with implementation complexity

   This client-side approach avoids additional server infrastructure while providing adequate search functionality for the typical messaging use case.

2. **What approach is suggested for handling group communications and chatrooms?** #group-chat #chatrooms #messaging-features

   **Answer:** For group communications and chatrooms, the document outlines:

   1. Limiting chatroom sizes to between 2 and 1,000 users
   2. Using a similar message flow for group messages, but with the sender encrypting the message with each recipient's public key
   3. For message delivery to chatrooms, the Message-Sending Service:
      - Looks up which host serves the chatroom
      - Sends the message to that host, which delivers to all connected members
   4. Blocking mechanisms that prevent users from being in the same chatroom as blocked users
   5. Handling ordering challenges when multiple participants send messages nearly simultaneously
   6. Acknowledging that message ordering might differ between participants, especially with offline members
