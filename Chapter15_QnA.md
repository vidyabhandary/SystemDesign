# Chapter 15 - Design Airbnb - (Q&A)

## Question 1

What are the three main characteristics of Airbnb as described?
#airbnb #systemdesign #businessmodel

**Answer:**
Airbnb has three main characteristics:

1. It's a reservation app, where users (guests) make reservations on finite items (rooms/properties) that are listed by hosts.
2. It's a marketplace app that matches people who sell products and services (hosts) to people who buy them (guests).
3. It handles payments and collects commissions, which means it also requires internal users who handle customer support and operations to mediate disputes and monitor for fraud.

## Question 2

Why might caching might not be suitable for Airbnb's search results?
#caching #systemdesign #searchoptimization

**Answer:**
Caching might not be suitable for Airbnb's search results because:

- In search results, only available rooms are displayed
- If a room is highly desirable, it will quickly be reserved and no longer be available for display in searches
- If a room keeps being displayed in searches, it's likely to be undesirable
- Cache freshness is difficult to maintain, and the cached data quickly becomes stale
- The costs and additional complexity of providing a cache may not be justified by the benefits

## Question 3

What approach is recommended for handling replication of room data across data centers?
#replication #datadistribution #systemarchitecture

**Answer:**
For replication of room data across data centers:

- Taking advantage of the localized nature of the products (searches are done on one city at a time)
- Allocating a data center host to a city with many listings or to multiple cities with fewer listings
- Using single-leader replication since write performance is not critical
- Placing secondary leader and followers geographically spread out across data centers to minimize read latency
- Using a metadata service to contain mapping of city to leader and follower host IP addresses
- Replicating the mapping (which is tiny in size) on all data centers with manual updates for consistency

## Question 4

Describe the two data models proposed for representing room availability and their tradeoffs.
#datamodeling #databasedesign #availability

**Answer:**
Two data models for representing room availability:

1. (room_id, date, guest_id) table:

   - Conceptually simple
   - Contains multiple rows that differ only by date
   - Example: If room 1 is booked by guest 1 for all of January, there will be 31 rows

2. (room_id, guest_id, check_in, check_out) table:
   - More compact representation
   - Requires algorithm to determine overlapping dates
   - Decision point: whether to implement this overlap-checking algorithm in the database query or in the backend
   - Database implementation would be harder to maintain and test
   - Backend implementation would incur I/O costs from fetching availability data

The tradeoff is between simplicity and storage efficiency versus computational complexity.

## Question 5

What is the main difference between Airbnb and hotels in terms of handling overbooking, and how does this affect the system design?
#overbooking #businessconstraints #systemdesign

**Answer:**
The main difference is that hotels can allow overbooking of cheaper rooms because they can upgrade guests to more expensive rooms if needed (as they control multiple room types in the same location). Airbnb hosts cannot do this because each listing is typically independent.

This constraint affects the system design in several ways:

- The Airbnb system must prioritize preventing double bookings over maximizing bookings
- It must implement strong consistency for bookings and listing availability
- It may need to implement a room locking mechanism during the booking flow
- The system accepts that some potential bookings will be lost to prevent double bookings
- There's a need for careful transaction management when checking availability and making bookings

## Question 6

Explain the concept of functional partitioning as applied to Airbnb's system design.
#functionalpartitioning #systemarchitecture #scalability

**Answer:**
Functional partitioning in Airbnb's system design refers to dividing the system by geographical region, similar to the approach used by Craigslist. The document describes this implementation as:

- Placing listings in data centers based on their geographical location
- Deploying the application into multiple data centers
- Routing each user to the data center that serves their city
- Taking advantage of the fact that Airbnb's products are localized (searches are typically done for one city at a time)

This approach allows for better scalability and performance by keeping data close to where it's most frequently accessed. It also helps manage the system's complexity by creating natural boundaries for data and services.

## Question 7

What are the major services in the high-level architecture of Airbnb?
#architecture #microservices #systemdesign

**Answer:**
The major services in Airbnb's high-level architecture include:

1. Booking Service - For guests to make bookings; the direct revenue source with stringent requirements for availability and latency
2. Listing Service - For hosts to create and manage listings
3. Availability Service - Keeps track of listing availability; used by both booking and listing services
4. Approval Service - For operations staff to approve listing requests
5. Recommender Service - Provides personalized listing recommendations to guests
6. Regulations Service - Provides API for local regulations to the listing service
7. Payment Service - Handles payment processing
8. Metadata Service - Contains mapping of cities to data centers
9. Other services for internal use, such as analytics

These services are designed to be developed and scaled separately, as they serve different functional requirements.

## Question 8

How is the approval process for new listings handled?
#approvalprocess #workflowdesign #operations

**Answer:**
It is suggested to handle the approval process for new listings through a dedicated Approval Service with the following components:

1. A client web application for operations staff to review listings
2. A backend service that interfaces with the Listings Service and a shared SQL database
3. A listing_request SQL table to track requests requiring review with columns for:
   - IDs, timestamps, listing details, status tracking
   - Review status, reviewer information, and review notes
4. A workflow where:
   - Ops staff request work and are assigned listings to review
   - SQL transactions prevent multiple staff from reviewing the same listing
   - The approval or rejection is recorded and communicated to other services
   - The host is notified of the decision

There are both synchronous and asynchronous approaches to the approval process, with the asynchronous approach using Kafka for communication between services.

## Question 9

What strategies are proposed for handling overlapping bookings?
#concurrency #bookingmanagement #userexperience

**Answer:**
Several strategies are proposed for handling overlapping bookings:

1. First-come, first-served approach: The first user's booking is granted, and other users are informed that the room is no longer available.

2. Room locking during the booking flow: When a user views a room's details, the system locks the room for a few minutes. During this time, the room won't appear in search results for other users with overlapping dates.

3. SQL row locking: Using database-level locking to prevent multiple simultaneous bookings for the same dates.

4. Transaction-based approach: Using an SQL transaction to first check availability and then immediately update the listing if it's available.

These strategies will result in some lost booking opportunities, but considers this an acceptable tradeoff to prevent double bookings.

## Question 10

Describe the high-level architecture of the Availability Service and its purpose in the Airbnb system.
#availabilityservice #architecture #consistency

**Answer:**
The Availability Service in the Airbnb system has the following high-level architecture:

- A backend service that processes client requests
- A shared SQL service with a leader-follower architecture
- An availability table that tracks the availability status of listings by date

Its purpose is to:

1. Prevent double bookings
2. Ensure host-marked unavailable dates can't be booked
3. Provide visibility of bookings to both hosts and guests
4. Reduce customer support issues related to availability conflicts

The service provides endpoints to:

- Get available listings for a specific location and date range
- Lock a listing for a short period during the booking flow
- Create, read, update, and delete reservations

This service needs strong consistency to prevent booking conflicts, which is achieved through SQL transactions and row locking, even though this may occasionally result in some lost booking opportunities.

## Question 11

What are the key aspects of the Regulations Service and why is it important?
#regulations #compliance #servicedesign

**Answer:**
The key aspects of the Regulations Service include:

1. Providing a standard API for communicating regulations that other services can interact with
2. Allowing the system to be flexible to changing regulations
3. Handling various types of regulations such as:
   - Maximum number of days a listing can be hosted per year
   - Property construction year restrictions
   - Booking date restrictions (e.g., holidays)
   - Minimum/maximum booking duration requirements
   - Geographic restrictions on listings
   - Safety equipment requirements
   - Livability standards

This service is important because:

- Regulations vary by country, state, city, and even specific addresses
- They can directly affect the core business operations
- Designing for regulatory flexibility is often a blind spot in many companies
- Considerable resources are spent on re-architecture, implementation, and migration each time regulations change
- Having a dedicated service allows other components to adapt more easily to regulatory changes

## Question 12

How is the booking process from search to confirmation handled?
#bookingflow #userexperience #systeminteraction

**Answer:**
The following steps are outlined for handling the booking process:

1. Search phase:

   - Guest submits a search query with city, check-in date, and check-out date
   - System processes the search via Elasticsearch
   - Results show available listings with thumbnails and brief information

2. Exploration phase:

   - Guest may filter results by price and other criteria
   - Guest clicks on listings to view more details, including high-resolution photos and videos
   - System may optionally "lock" the listing for a short period while the guest views details

3. Booking phase:

   - Guest submits a booking request
   - System checks availability through the Availability Service
   - If available, the system confirms the booking and updates availability
   - If unavailable, the system returns an error

4. Payment phase:

   - Guest is directed to make payment
   - If payment is successful, booking is confirmed
   - If payment fails, booking is canceled and availability is restored

5. Notification phase:
   - System notifies both guest and host about the booking status

This process should be handled as a distributed transaction to maintain consistency across services, particularly between the Booking Service and Availability Service.

## Question 13

Explain the two approaches to the approval process (synchronous and asynchronous).
#approvalprocess #distributedtransactions #systemdesign

**Answer:**
Two approaches to the approval process:

**Synchronous Approach:**

1. An Ops staff member approves or rejects a listing through a POST request to the Approval Service
2. The Approval Service updates the listing_request table
3. The Approval Service then makes a PUT request to the Listing Service to update the listing status
4. Next, it sends a POST request to the Booking Service to make the listing available
5. It also requests the Notification Service to notify the host
6. Finally, it sends a 200 response to the client
7. All steps must be idempotent to handle failures

**Asynchronous Approach (using Change Data Capture):**

1. The Approval Service receives an approval request
2. It produces a message to a Kafka queue and immediately returns a 200 response
3. A consumer reads from the Kafka queue and makes requests to the other services
4. The Notification Service consumes from two Kafka topics (one for each service)
5. The Notification Service waits for events from both services before sending notifications
6. A batch ETL job can audit the services for consistency

CDC is preferred over saga patterns for this process because none of the services are expected to reject the request, so there's no need for compensating transactions.

## Question 14

What are the main functional requirements for the Airbnb system?
#functionalrequirements #productdesign #specifications

**Answer:**
The main functional requirements for the Airbnb system are:

1. A host may list a room (properties include city and price, with up to 10 photos and a 25MB video)
2. A guest may filter rooms by city, check-in, and check-out date
3. A guest may book a room with a check-in and check-out date (host approval not required)
4. A host or guest may cancel a booking anytime before it begins
5. A host or guest may view their list of bookings
6. A guest can have only one room booked for any particular date
7. Rooms cannot be double-booked

The API endpoints to support these requirements:

- findRooms(cityId, checkInDate, checkOutDate)
- bookRoom(userId, roomId, checkInDate, checkOutDate)
- cancelBooking(bookingId)
- viewBookings(hostId)
- viewBookings(guestId)

Features explicitly excluded from scope include manual booking approval, cancellation after booking begins, notifications, and messaging between users.

## Question 15

What are the non-functional requirements for the Airbnb system?
#nonfunctionalrequirements #systemperformance #reliability

**Answer:**
The non-functional requirements for the Airbnb system include:

1. Scalability: The system should be scalable to 1 billion rooms or 100 million daily bookings. The document notes that past booking data can be deleted, and there's no programmatically generated user data.

2. Consistency: Strong consistency is required for bookings and listing availability to prevent double bookings. However, eventual consistency is acceptable for other listing information like descriptions or photos.

3. Availability: High availability is needed because there are monetary consequences for lost bookings. However, the document acknowledges that some bookings may be lost to prevent double bookings.

4. Performance: High performance is not a strict requirement. A P99 latency of a few seconds is considered acceptable.

5. Security and Privacy: The system requires typical security measures including authentication. User data should be kept private, though detailed authorization requirements are not specified.

## Question 16

How is monitoring and alerting for the Airbnb system handled?
#monitoring #alerting #systemoperations

**Answer:**
For monitoring and alerting in the Airbnb system:

1. Standard monitoring of system resources:

   - CPU and memory usage
   - Disk usage of Redis and Elasticsearch
   - General system health metrics

2. Business-specific monitoring:
   - Anomaly detection for unusual rates of bookings, listings, or cancellations
   - Alerts for high rates of listings being flagged for irregularities
   - Monitoring of end-to-end user stories/flows (e.g., listing creation, booking process)
   - Tracking the rate of completed vs. non-completed user flows
   - Monitoring "funnel conversion rates" and creating alerts for unusually low rates
   - Tracking rates of undesirable user stories (e.g., bookings canceled after guest-host communication)

The importance of monitoring is not just technical metrics but also business outcomes and user experience. This comprehensive approach allows for both proactive identification of technical issues and insight into how the system is performing from a business perspective.
