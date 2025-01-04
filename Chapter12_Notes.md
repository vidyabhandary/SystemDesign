# Chapter 12 - Design Flickr (Summary)

[Mindmap for designing Flickr](https://raw.githubusercontent.com/vidyabhandary/SystemDesign/refs/heads/main/imgs/DesignFlickr.svg)

#### **Key Topics Covered**

- Designing scalable and cost-effective systems for image processing and sharing.
- Asynchronous operations using saga patterns.

### 1. **User Stories and Functional Requirements**

**Core Features:**

1. 1. **Photo Viewing:** Users ("Viewers") can browse and view photos shared by others.
   - #ViewPhotos #UserRoles
2. **Thumbnails:** The system generates 50 px wide thumbnails for grid views, with full-resolution accessible upon selection.
   - #Thumbnails #FullResolution #GridView
3. **Photo Uploading:** Users ("Sharers") can upload photos.
   - #PhotoUpload #UserRoles
4. **Access Control:** Global access permissions allow sharers to manage who can view their photos.
   - #AccessControl #GlobalPermissions
5. **Metadata Management:** Handles predefined fields like tags and dynamic fields such as viewer access.
   - #Metadata #DynamicFields
6. **Commenting and Notifications:** Supports comments and alerts for new interactions.
   - #Commenting #Notifications
7. **Photo Favoriting:** Users can favorite specific photos for quick access.
   - #PhotoFavorites
8. **Search Functionality:** Enables search by photo titles and descriptions.
   - #SearchFunctionality
9. **Programmatic Downloads:** Viewing photos and programmatic downloads are synonymous.
   - #APIDownloads

**Out of Scope:**

- Video uploads and metadata like camera details.
- Advanced filtering by metadata.
  - #ScopeClarification

### 2. **Non-Functional Requirements**

**Scalability:**

- Serve 1 billion users worldwide with traffic spikes up to 10 GB/sec. Daily uploads estimated at 1 PB.
  - #Scalability #GlobalUsers

**Consistency and Latency:**

- Eventual consistency is acceptable for photos and comments.
- Privacy settings should take effect within minutes.
  - #ConsistencyTradeoffs

**Performance:**

- Thumbnails: High performance, P99 latency under 1 second.
- High-resolution uploads/downloads: Moderate performance.
  - #PerformanceMetrics

**Cost Control:**

- Limit simultaneous downloads of high-resolution photos; batch upload processes.
  - #CostOptimization

**Availability:**

- Aim for 99.999% availability (five 9s).
  - #HighAvailability

### 3. **High-Level Architecture**

**Components:**

1. **Users:** Sharers and viewers interact with the backend for uploads and downloads.
   - #Users #BackendIntegration
2. **Backend Service:** Handles requests and interfaces with SQL and storage layers.
   - #BackendService
3. **SQL Service:** Stores metadata like user info, image access permissions.
   - #SQLDatabase #MetadataStorage
4. **CDN:** Provides distributed and fast access to images.
   - #CDN #DistributedAccess
5. **File Storage Service:** Buffers uploads, retains backups, and interacts with the CDN.
   - #FileStorage #BackupSystem

### 4. **SQL Schema**

| **Table**    | **Columns**                                                        | **Details**                                                          |
| ------------ | ------------------------------------------------------------------ | -------------------------------------------------------------------- |
| **Image**    | cdn_path, cdn_photo_key, file_key, resolution, owner_id, is_public | Tracks metadata for images. Includes indexes for efficient querying. |
| **ImageDir** | cdn_dir, user_id                                                   | Maps sharers to CDN directories.                                     |
| **Share**    | id, cdn_photo_key, user_id                                         | Grants view permissions.                                             |
| **Favorite** | id, cdn_photo_key, user_id                                         | Tracks user favorites.                                               |

- Use composite indexes (e.g., resolution + user_id) for faster lookups.
  - #SQLSchema #MetadataIndexing

### 5. **Organizing Directories and Files on the CDN**

**Structure:**

- User Directory → Album → Resolution → File.
- Default album for ungrouped photos.
- Example path: `nature/thumbnail/swans_thumbnail.png`.
  - #CDNHierarchy #FileOrganization

### 6. **Uploading Photos**

**Thumbnail Generation:**

1. **Client-side:**
   - Lightweight but error-prone due to device variations, failures, and security risks.
   - #ClientSideProcessing #DeviceLimitations
2. **Server-side:**
   - Centralized, scalable, and reliable; uses streaming jobs for thumbnail creation.
   - #ServerSideProcessing #BackendReliability

**Asynchronous Uploads:**

- Use saga patterns (choreography/orchestration) to manage upload steps asynchronously.
  - #SagaPattern #AsynchronousProcessing

**Steps for Saga (Choreography Example):**

1. Check if file exists (hash-based).
2. Upload to file storage service and trigger Kafka events.
3. Process thumbnails asynchronously.
   - #ChoreographySaga #FileUploadFlow

### 7. **Downloading Images and Data**

**Thumbnails:**

- Query SQL for accessible images.
- Return CDN paths with OAuth2 tokens for secure download.
  - #ThumbnailDownload #OAuthTokens

**Pagination Techniques:**

1. "Previous-first/last" for consistent navigation.
   - #Pagination

### 8. **Monitoring and Alerting**

- Monitor file uploads/downloads, SQL queries, and cache performance.
  - #Monitoring #Alerting

### 9. **Additional Features**

**Premium Features:**

- Paid features like larger upload limits, selling photos, and analytics dashboards.
  - #PremiumFeatures #Monetization

**Data Protection:**

- Strategies for backup, security, and cost control (e.g., tiered storage for older files).
  - #DataProtection #CostManagement

**Other Possible Enhancements:**

1. Implement watermarking for copyright protection.
2. Batch pipelines for analytics on usage pattern
3. Extend support to video or audio streaming.

   - #Enhancements #FutureFeatures

### 10. **Summary**

- Focus on scalability, high availability, and cost optimization.
- Balance between client-side and server-side processing.
- Use asynchronous techniques like sagas for efficient processing.
- Ensure robust monitoring and user-centric premium features.
  - #SystemDesignPrinciples
