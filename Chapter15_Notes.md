# Chapter 14 - Design Airbnb (Summary)

[Mindmap for designing Airbnb](https://raw.githubusercontent.com/vidyabhandary/SystemDesign/refs/heads/main/imgs/DesignAirbnb.svg)

## Introduction to Reservation Systems like Airbnb

Reservation systems form the backbone of many modern applications across various domains:

- Accommodation rentals (like Airbnb)
- Movie ticket booking platforms
- Airline reservation systems
- Parking lot management

#reservation-systems #accommodation #booking-platforms

## System Classification

Airbnb-like systems operate as:

1. **Reservation application**: Facilitates bookings of finite resources between two user types

   - **Guests**: Users who make reservations
   - **Hosts**: Users who create listings of available items

2. **Marketplace platform**: Connects sellers (hosts) with buyers (guests) in a two-sided marketplace

3. **Payment processing system**: Handles transactions and commission collection
   - Requires support staff for dispute resolution and fraud detection
   - Majority of employees typically work in customer support and operations

#marketplace #two-sided-platform #reservation-app

## Key Users and Stakeholders

Understanding the various users is critical for system design:

- **Hosts**: Create and manage property listings
- **Guests**: Search, book, and pay for accommodations
- **Operations staff**: Handle disputes, review listings, detect fraud
- **Analytics team**: Study platform metrics, user behavior, and business performance

#stakeholders #user-types #platform-roles

## Host Use Cases

Hosts interact with the system through various workflows:

### Listing Management

- Creating new property listings
- Updating existing listings (photos, descriptions, pricing)
- Setting booking parameters (minimum/maximum duration)
- Managing availability calendars
- Handling local regulations compliance
- Submitting listings for approval

### Booking Operations

- Reviewing and responding to booking requests
- Setting automated acceptance criteria
- Modifying or canceling existing bookings
- Setting dynamic pricing based on demand

### Communication and Reviews

- Messaging with potential and current guests
- Rating and reviewing guests after stays
- Viewing guest profiles and past reviews

### Financial Management

- Processing payments from guests
- Receiving earnings (minus platform commission)
- Accessing tax documentation

### Analytics and Support

- Tracking earnings and performance metrics
- Communicating with operations staff for assistance
- Requesting mediation for damages or disputes

#host-functions #listing-management #host-operations

## Guest Use Cases

Guest interactions center around finding and booking accommodations:

### Discovery

- Searching for available properties with filters
- Viewing detailed listing information
- Reading reviews of properties

### Booking

- Submitting booking requests with dates
- Processing payments for reservations
- Checking booking request status
- Cancelling bookings when needed

### Communication and Reviews

- Messaging with hosts before/during stays
- Rating and reviewing properties after stays
- Viewing host profiles and response rates

### Support

- Requesting assistance from operations staff
- Reporting issues with listings or hosts
- Seeking refunds or dispute resolution

#guest-functions #booking-process #traveler-experience

## Operations Staff Use Cases

Operations team members perform critical behind-the-scenes functions:

- Reviewing and approving new listings
- Removing inappropriate content
- Mediating disputes between hosts and guests
- Processing refund requests
- Providing alternative options for displaced guests
- Monitoring for fraudulent activity
- Enforcing platform policies

#operations #content-moderation #dispute-resolution

## Core Functional Requirements

When designing the reservation system, focus on these primary functions:

1. Hosts can list rooms with basic properties:

   - Location (city)
   - Price
   - Media (up to 10 photos and a 25MB video)

2. Guests can search with filters:

   - City
   - Check-in date
   - Check-out date

3. Booking functionality:

   - Guests can book rooms with check-in/out dates
   - Bookings are confirmed immediately (no host approval required)
   - Users can cancel bookings before check-in
   - Users can view their booking history

4. Availability rules:
   - Guests can only book one room per date
   - Rooms cannot be double-booked
   - System must prevent overlapping reservations

#functional-requirements #core-features #booking-rules

## Simplified Scope

For manageability, certain features are deliberately excluded:

- Manual booking approval by hosts
- Mid-stay cancellations
- Detailed notification systems
- In-app messaging between parties
- Address specifics beyond city
- Multi-guest bookings
- Room type categorization (whole place/private room)
- Amenity details
- Advanced analytics
- Dynamic pricing recommendations
- Additional fees (cleaning, service)
- Payment processing details
- Insurance coverage
- Authentication/authorization (assumed as given)

#scope-limitations #feature-boundaries

## API Endpoint Structure

Core API endpoints would include:

```
findRooms(cityId, checkInDate, checkOutDate)
bookRoom(userId, roomId, checkInDate, checkOutDate)
cancelBooking(bookingId)
viewBookings(hostId)
viewBookings(guestId)
```

#api-design #endpoints #interface-definition

## Non-Functional Requirements

The system must satisfy these operational characteristics:

1. **Scalability**: Handle up to 1 billion rooms or 100 million daily bookings

2. **Consistency**: Strong consistency for booking operations to prevent double-bookings; eventual consistency acceptable for non-critical listing data

3. **Availability**: High availability required due to financial implications, with awareness that preventing lost bookings completely is impossible while also preventing double bookings

4. **Performance**: P99 response time of a few seconds is acceptable; not speed-critical

5. **Security**: Standard authentication requirements; user data privacy protection

#non-functional-requirements #scalability #availability #consistency

## Key Design Decisions

### Replication Strategy

- Use single-leader replication for data consistency
- Secondary leaders and followers geographically distributed to minimize read latency
- Metadata service maps cities to appropriate data center hosts
- CDN stores static content (photos, videos, JavaScript, CSS)
- Consider avoiding caching highly desirable listings since they quickly become booked

#replication #data-consistency #geographical-distribution

### Room Availability Data Models

Two potential approaches:

1. **Date-based model**: `(room_id, date, guest_id)` table

   - Simple conceptually
   - Multiple rows per booking (one per date)
   - Easier to query for availability

2. **Range-based model**: `(room_id, guest_id, check_in, check_out)` table
   - More compact storage
   - Requires overlap detection algorithm
   - More complex queries

The choice affects database queries and application logic complexity.

#data-models #availability-tracking #database-schema

### Handling Concurrent Bookings

To prevent double-bookings while maximizing conversions:

1. **Search result randomization**: Display listings in variable order to reduce contention

2. **Temporary locking**: When a user views detailed listing information, temporarily lock the dates for a few minutes, making them unavailable to other searchers
   - Accept some booking losses to prevent double bookings
   - Different from hotel systems that can handle overbookings through upgrades

#concurrency #booking-conflicts #reservation-locking

## High-Level Architecture

The system is decomposed into specialized services:

1. **Booking Service**: Handles guest booking operations

   - Revenue-critical component
   - High availability requirements
   - May sacrifice consistency for speed

2. **Listing Service**: Manages host listing operations

   - Less performance-critical than booking
   - Handles media uploads and listing information

3. **Availability Service**: Tracks listing availability

   - Used by both booking and listing services
   - Requires high availability and read scalability
   - Strong consistency requirements

4. **Approval Service**: Manages operations review process

   - Internal-facing service
   - Handles queue of listings requiring review

5. **Recommender Service**: Provides personalized listings

   - Functions like an internal ads system
   - Tailors search results to guest preferences

6. **Regulations Service**: Handles local legal requirements

   - Provides regulatory information by location
   - Ensures listings comply with local laws

7. **Support Services**: Analytics, monitoring, etc.

#architecture #service-decomposition #microservices

## Functional Partitioning

Geographic partitioning provides scalability:

- Listings grouped by city/region
- Each data center serves specific geographic areas
- Users routed to appropriate data center based on search location
- Reduces query scope and improves performance

#functional-partitioning #geographic-sharding #data-locality

## Listing Creation Process

The listing creation flow involves multiple services:

1. **Regulation Check**:

   - Host initiates listing creation
   - Listing service requests applicable regulations from regulation service
   - UI adapts based on local requirements

2. **Listing Submission**:

   - Host submits listing information
   - Listing service validates request data
   - Data written to SQL database with "unapproved" status
   - Approval service notified of pending review

3. **Distributed Transaction**:

   - Change Data Capture (CDC) ensures consistency
   - SQL writes and approval notifications processed reliably
   - All steps designed to be idempotent

4. **Multi-step Process**:
   - Hosts may complete listing details in phases
   - Each submission updates the same listing record
   - System handles partial or incomplete listings

#listing-creation #approval-flow #regulatory-compliance

## Approval Service Architecture

Operations staff use a specialized system:

1. **Core Components**:

   - Web interface for operations staff
   - Backend service for business logic
   - Integration with listing service
   - SQL database for tracking review status

2. **Database Structure**:

   - `listing_request` table with columns:
     - Request ID
     - Listing ID
     - Creation timestamp
     - Listing data hash (for conflict detection)
     - Status (none/assigned/reviewed)
     - Last accessed timestamp
     - Review code (approval/rejection reason)
     - Reviewer ID
     - Review submission timestamp
     - Review notes

3. **Data Volume**:
   - Assuming 10,000 operations staff
   - Each reviewing 5,000 listings weekly
   - 50 million rows written weekly
   - ~1.5TB monthly storage growth
   - Periodic archiving required

#approval-service #content-moderation #operations-staff

## Approval Workflow

The review process follows these steps:

1. **Assignment**:

   - Operations staff requests work
   - System assigns oldest unreviewed listing
   - Status updated to "assigned" with reviewer ID

2. **Review Process**:

   - Staff reviews listing content
   - Approves or rejects with reason code
   - Submits decision with optional notes

3. **Synchronous Implementation**:

   - Update approval table
   - Update listing service
   - Notify booking service
   - Send notification to host
   - Potential race conditions managed with hash verification

4. **Asynchronous Alternative**:
   - Approval service publishes to Kafka queue
   - Consumer processes updates to other services
   - More resilient to service failures
   - Audit jobs ensure consistency across services

#review-workflow #moderation-process #approval-flow

## Booking Service Architecture

The booking flow handles guest interactions:

1. **Core Components**:

   - Backend service
   - Elasticsearch for search
   - SQL database for bookings
   - Integration with availability service
   - CDN for media content

2. **Booking Process**:

   - Guest searches for available listings
   - Filters applied to results
   - Detailed listing information displayed
   - Booking request submitted
   - Availability confirmed
   - Payment processed
   - Booking confirmed or rejected

3. **Database Structure**:

   - `Booking` table with:
     - Booking ID
     - Listing ID
     - Guest ID
     - Check-in date
     - Check-out date
     - Timestamp

4. **Distributed Transactions**:
   - Booking requires updates to multiple services
   - Consistency maintained through transaction coordination
   - Elasticsearch updated when availability changes

#booking-service #search-functionality #reservation-process

## Availability Service

This critical service prevents double-bookings:

1. **Core Functions**:

   - Track listing availability by date
   - Lock listings during booking process
   - Process booking requests
   - Update availability when bookings change

2. **Database Structure**:

   - `availability` table with:
     - Listing ID
     - Date
     - Booking ID (if booked)
     - Availability status (available/locked/booked)
     - Update timestamp

3. **Concurrency Control**:

   - SQL transactions with row locking
   - Two-step process:
     1. SELECT to check availability
     2. INSERT/UPDATE to mark status
   - Leader-follower architecture may produce stale search results

4. **Storage Requirements**:
   - Approximately 40 bytes per row
   - 1 million listings × 180 days ≈ 7.2GB
   - Easily fits on single host

#availability-service #double-booking-prevention #concurrency-control

## Monitoring and Alerting

The system requires comprehensive monitoring:

1. **Standard Metrics**:

   - CPU, memory, disk usage
   - Network traffic and latency
   - Database performance
   - Cache hit rates

2. **Business Metrics**:

   - Booking completion rates
   - Listing creation funnel
   - Cancellation rates
   - Flag/rejection rates

3. **Anomaly Detection**:

   - Unusual booking patterns
   - Spikes in listing creation/deletion
   - Elevated cancellation rates
   - Low conversion rates in user flows

4. **End-to-End Monitoring**:
   - Track complete user journeys
   - Identify abandonment points
   - Alert on funnel conversion drops

#monitoring #alerting #anomaly-detection #business-metrics

## Additional Design Considerations

Many enhancements could extend the basic system:

1. **Search Improvements**:

   - Fuzzy date matching
   - Geographic proximity searches
   - Relevant recommendations

2. **Advanced Features**:

   - Inappropriate content reporting
   - Behavior monitoring systems
   - Account restriction mechanisms

3. **Extended Functionality**:

   - Full-text search capabilities
   - Business traveler accommodations
   - Room statistics displays

4. **Personalization**:

   - Recommendation algorithms
   - New listing promotion
   - User preference matching

5. **Fraud Protection**:
   - Suspicious activity detection
   - Identity verification
   - Payment validation

#enhancements #feature-extensions #future-development

## Handling Regulations

A dedicated regulatory system provides flexibility:

1. **Centralized Approach**:

   - Dedicated regulation service
   - Standard API for regulatory information
   - All services designed to respect regulations

2. **Types of Regulations**:

   - Data privacy laws (GDPR, CCPA, COPPA)
   - Local rental restrictions
   - Property age limitations
   - Minimum/maximum stay durations
   - Safety equipment requirements
   - Regional booking prohibitions

3. **Implementation Strategy**:
   - Design for regulatory change
   - Avoid hardcoding rules
   - Plan for easy updates
   - Consider geographical variations

#regulations #compliance #legal-requirements #regulatory-design

## Summary of Key Points

1. Airbnb-like systems combine reservation, marketplace, and operational functions serving hosts, guests, and internal staff.

2. Localizing products by geography enables efficient data center organization.

3. The system architecture involves numerous specialized services handling different aspects of the platform.

4. Listing creation involves regulatory checks, submission, and approval processes.

5. Preventing double bookings while maintaining availability requires careful concurrency control.

6. Distributed transactions ensure consistency across services.

7. Geographic partitioning improves performance and scalability.

8. Monitoring must track both technical metrics and business outcomes.

9. Regulatory compliance requires flexible design to accommodate changing requirements.

10. Both synchronous and asynchronous approaches have appropriate applications depending on performance needs.

#key-takeaways #system-design #reservation-platform
