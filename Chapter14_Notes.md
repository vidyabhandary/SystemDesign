# Chapter 14 - Design a Text Messaging App (Summary)

[Mindmap for designing a Text Messaging App](https://raw.githubusercontent.com/vidyabhandary/SystemDesign/refs/heads/main/imgs/DesignATextMessagingApp.svg)

## 1. Introduction to Text Messaging App Design

A text messaging application needs to support 100K users sending messages to each other within seconds. The system must handle unpredictable traffic surges and ensure exactly-once delivery - messages should neither be lost nor sent more than once.

This represents a critical real-time communication system where reliability and performance are essential considerations. #messagingapp #systemdesign #realtime

## 2. Functional Requirements Analysis

### 2.1 Core Requirements

- **Message Delivery Timeframe**: Can be real-time or eventually consistent
- **Chatroom Capacity**: Between 2 to 1,000 users per chatroom
- **Message Size**: 1,000 UTF-8 characters maximum (up to 4 KB per message)
- **Platform-specific Notifications**: Handled by respective platform libraries
- **Delivery Confirmation**: Must support both delivery confirmation and read receipts
- **Message History**: Users can view and search up to 10 MB of past messages
- **Privacy**: Message content must be private with end-to-end encryption
- **Message Format**: Text-only for this design (no media, voice, photos, videos)

### 2.2 Excluded Requirements

- User onboarding processes
- Multiple chatrooms for the same group
- Template messages (considered client-side feature)
- Online status indicators

#functionalrequirements #chatroom #messagingcapacity

## 3. Non-Functional Requirements

### 3.1 Scalability

- 100K simultaneous users
- 4 KB message per user per minute = 400 MB/minute write rate
- Each user can have up to 1,000 connections
- Messages can be sent to up to 1,000 recipients
- Each recipient may have up to 5 devices

### 3.2 Other Quality Attributes

- **Availability**: Four nines (99.99%) uptime
- **Performance**: 10-second P99 message delivery time
- **Security**: User authentication required and message privacy
- **Consistency**: Strict ordering not necessary; simultaneous messages can appear in different orders

#nonfunctionalrequirements #scalability #availability #security

## 4. Comparing with Notification Systems

### 4.1 Differences from Notification/Alerting Service

| Messaging App                                            | Notification/Alerting Service                             |
| -------------------------------------------------------- | --------------------------------------------------------- |
| Equal priority messages with 10-second P99 delivery time | Different priority levels for events                      |
| Single channel within one service                        | Multiple channels (email, SMS, calls, push notifications) |
| Manual triggers only                                     | Manual, programmatic, or periodic triggers                |
| No message templates                                     | Users can create notification templates                   |
| End-to-end encryption limits deduplication               | No end-to-end encryption hence it enables abstractions    |
| Old message retrieval needed                             | Most notifications sent once only                         |
| Built-in delivery confirmation and read receipts         | Limited delivery/read confirmations on many channels      |

#notificationsystems #servicedifferences #comparativeanalysis

## 5. High-Level Architecture Design

### 5.1 Message Flow Process

1. User selects recipient from contacts list
2. User composes message on mobile/desktop/browser app
3. App encrypts message with recipient's public key
4. App sends request to messaging service
5. Service delivers message to recipient
6. Recipient sends delivery confirmation and read receipt

### 5.2 Core Services

- **Sender Service**: Receives messages from senders and delivers to recipients while recording in Message Service
- **Message Service**: Allows senders and recipients to retrieve sent/received messages including undelivered ones
- **Connection Service**: Manages active/blocked connections, contact lists, and connection metadata

### 5.3 Architecture Components

- API Gateway: Entry point for user requests
- Sender Service: Checks recipient blocks, delivers messages
- Message Service: Records message history
- Connection Service: Manages user connections and public keys

#architecturedesign #services #messageflow #apigateway

## 6. Connection Service Design

### 6.1 Blocking Implementation

Blocking should be implemented at every layer:

- Sender's app: Prevents sending to blocked recipients
- Messaging service: Secondary layer to block messages
- Recipient's app: Final layer to filter unwanted messages

### 6.2 Public Key Management

- Device generates public-private key pair on first start
- Public key stored in Connection Service
- Service updates user's connections via WebSocket
- Changes propagate to up to 5,000 devices (1,000 connections × 5 devices)
- Grace period for key changes (e.g., 7 days) where both keys are valid

### 6.3 Connection Storage

- Connections stored on user devices as primary source
- Connection Service as backup and for cross-device synchronization
- Implemented as stateless backend with shared SQL service

### 6.4 Consistency Challenges

When providing offline functionality:

- Multiple devices might send conflicting requests
- Timestamps unreliable for ordering (clock synchronization issues)
- Solutions include:
  - User confirmation of final state when conflicts detected
  - Limiting write operations to prevent inconsistency

#connectionservice #blocking #publickey #gracefuldegrade #consistency

## 7. Sender Service Design

### 7.1 Architecture Components

- **New Message Service**: Validates requests, produces to Kafka topic
- **Message-Sending Service**: Processes and delivers messages to recipients
- **Kafka Topic**: Buffers messages for processing

### 7.2 Message Flow

1. **Client-side**:

   - User composes message with sender ID, recipient IDs, body
   - Client encrypts body and sends to Sender Service

2. **New Message Service**:

   - Validates request format
   - Produces message to Kafka topic
   - Returns 200 success to sender
   - Returns 400 for invalid requests

3. **Message-Sending Service**:

   - Message Generator consumes from Kafka topic
   - Generates separate message for each recipient
   - Produces message to recipient Kafka topic
   - Maintains checkpoints in Redis for failure recovery

4. **Message Consumer Service**:

   - Checks if sender should be blocked
   - Determines appropriate host for recipient
   - Logs message to Message Service
   - Sends message to recipient client

5. **Receipt Processing**:

   - Receiver ensures message isn't duplicate
   - Display and notification triggered
   - Read receipts sent back to sender

### 7.3 Message Ordering

- Use consistent hashing to ensure messages to particular receiver go to same Kafka partition
- In-memory database (Redis) can store receiver-to-partition mapping
- Client can verify correct message ordering

#senderservice #messageflow #kafka #checkpointing #messagingarchitecture

## 8. Message Service Design

### 8.1 Purpose and Functionality

- Acts as a log of all messages
- Allows retrieval of past messages for:
  - New device logins
  - App storage clearance
  - Previously undeliverable messages

### 8.2 End-to-End Encryption Approach

1. Receiver generates public-private key pair
2. Sender encrypts message with receiver's public key
3. Receiver decrypts message with their private key

### 8.3 Storage Considerations

- Retention period of a few weeks before deletion
- Limited retention improves privacy and security
- Reduces potential data exposure from security breaches

### 8.4 Multi-Device Support Options

- Retain undelivered messages in service
- Limit login to one phone at a time with desktop apps connecting through phone
- Allow cloud backup/restore for device changes

### 8.5 Technology Selection

- High write traffic, low read traffic pattern
- Cassandra ideal for this workload
- Implemented as stateless backend with shared Cassandra service

#messageservice #encryption #cassandra #dataretention #multidevicessupport

## 9. Message-Sending Service Detailed Design

### 9.1 Connection Architecture

- Devices cannot act as servers due to:
  - Security concerns
  - Network traffic costs
  - Power consumption issues
- WebSocket connections maintained by service hosts
- Cluster of hosts to serve concurrent users
- ZooKeeper for distributed coordination and host assignment

### 9.2 Components

- **Messaging Cluster**: Hosts maintaining WebSocket connections with devices
- **Host Assigner Service**: Maps device IDs to hosts using ZooKeeper
- **Connection Service**: Previously described
- **Message Service**: Logs all messages

## 10. Message Search Functionality

- Each user searches only their own messages
- Direct text search without building reverse indexes
- Typical message volume (< 1GB) easily loaded into memory
- Media file names searchable but not content

#searchfunctionality #memorysearch #filenamessearch

## 11. Summary of Key Design Points

- Text messaging system design focuses on routing numerous messages between many clients
- Similar to notification services but with specific requirements
- Message queues provide scalability and cost-efficiency at expense of latency
- Lower latency requires fewer users per host (higher cost)
- Both approaches must handle host failures gracefully
- Message retrieval for unavailable devices is essential
- Comprehensive logging between services enhances system observability
- Usage metrics help optimize cluster sizing and detect fraud

#keytakeaways #designtradeoffs #systemoptimization #messagingsystems #scalabilitytradeoffs

---

References:

1. [How Computers Synchronize Their Clocks - NTP and PTP Explained](https://www.youtube.com/watch?v=WX5E8x3pYqg)

2. [NTP - The Obscure System That Syncs All The World’s Clocks](https://www.youtube.com/watch?v=CwZW0CO7F-g)

3. [State design pattern](https://www.youtube.com/watch?v=VNZMpEPCMGI)

4. [Restrict App installations per account](https://stackoverflow.com/questions/15064563/limit-restrict-app-installations-per-account-detemined-by-unique-devices)
