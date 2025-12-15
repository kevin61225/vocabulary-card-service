# Vocabulary Card Management Specification

## Feature Overview

This specification defines the functionality for managing existing vocabulary cards. Users can view, update, delete, search, and organize their vocabulary cards.

## User Stories

### US-003: View Vocabulary Card Details
**As a** language learner  
**I want to** view the details of a specific vocabulary card  
**So that** I can review my learning material

**Acceptance Criteria:**
- User can retrieve a card by its ID
- System returns all card information including metadata
- System returns 404 if card doesn't exist or belongs to another user

### US-004: List My Vocabulary Cards
**As a** language learner  
**I want to** see a list of all my vocabulary cards  
**So that** I can browse my collection

**Acceptance Criteria:**
- User can retrieve a paginated list of their cards
- User can specify page size and page number
- Results include total count and pagination metadata
- Cards are sorted by creation date (newest first) by default

### US-005: Update Vocabulary Card
**As a** language learner  
**I want to** edit my vocabulary card  
**So that** I can correct mistakes or add more information

**Acceptance Criteria:**
- User can update any field of their vocabulary card
- System validates updated data
- System updates the `updatedAt` timestamp
- User cannot update cards belonging to other users

### US-006: Delete Vocabulary Card
**As a** language learner  
**I want to** delete a vocabulary card  
**So that** I can remove cards I no longer need

**Acceptance Criteria:**
- User can delete their own vocabulary card
- System performs soft delete (marks as deleted but keeps data)
- User cannot delete cards belonging to other users
- Deleted cards don't appear in list/search results

### US-007: Search Vocabulary Cards
**As a** language learner  
**I want to** search my vocabulary cards  
**So that** I can quickly find specific words

**Acceptance Criteria:**
- User can search by source word or translation
- Search is case-insensitive and supports partial matching
- User can filter by language pair (source + target)
- User can filter by difficulty level
- User can filter by tags

### US-008: Filter and Sort Cards
**As a** language learner  
**I want to** filter and sort my vocabulary cards  
**So that** I can organize my learning

**Acceptance Criteria:**
- User can sort by creation date, alphabetically, or difficulty
- User can filter by date range
- Multiple filters can be combined

## API Endpoints

### GET /api/v1/vocabulary-cards/{id}

Get a specific vocabulary card by ID.

**Path Parameters:**
- `id` (UUID, required): The vocabulary card ID

**Response 200 (OK):**
```json
{
  "id": "uuid",
  "sourceWord": "string",
  "translation": "string",
  "sourceLanguage": "string",
  "targetLanguage": "string",
  "pronunciation": "string",
  "exampleSentence": "string",
  "notes": "string",
  "difficultyLevel": "string",
  "tags": ["string"],
  "userId": "uuid",
  "createdAt": "datetime",
  "updatedAt": "datetime"
}
```

**Response 404 (Not Found):**
```json
{
  "error": "NotFound",
  "message": "Vocabulary card not found"
}
```

### GET /api/v1/vocabulary-cards

List vocabulary cards with pagination and filtering.

**Query Parameters:**
- `page` (integer, optional, default: 1): Page number
- `pageSize` (integer, optional, default: 20, max: 100): Items per page
- `search` (string, optional): Search term for source word or translation
- `sourceLanguage` (string, optional): Filter by source language code
- `targetLanguage` (string, optional): Filter by target language code
- `difficultyLevel` (string, optional): Filter by difficulty level
- `tags` (string, optional): Comma-separated list of tags
- `sortBy` (string, optional, default: createdAt): Sort field (createdAt, sourceWord, difficultyLevel)
- `sortOrder` (string, optional, default: desc): Sort order (asc, desc)
- `createdAfter` (datetime, optional): Filter cards created after this date
- `createdBefore` (datetime, optional): Filter cards created before this date

**Response 200 (OK):**
```json
{
  "items": [
    {
      "id": "uuid",
      "sourceWord": "string",
      "translation": "string",
      "sourceLanguage": "string",
      "targetLanguage": "string",
      "pronunciation": "string",
      "exampleSentence": "string",
      "notes": "string",
      "difficultyLevel": "string",
      "tags": ["string"],
      "userId": "uuid",
      "createdAt": "datetime",
      "updatedAt": "datetime"
    }
  ],
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 100,
    "totalPages": 5,
    "hasNext": true,
    "hasPrevious": false
  }
}
```

### PUT /api/v1/vocabulary-cards/{id}

Update an existing vocabulary card.

**Path Parameters:**
- `id` (UUID, required): The vocabulary card ID

**Request Body:**
```json
{
  "sourceWord": "string (optional)",
  "translation": "string (optional)",
  "sourceLanguage": "string (optional)",
  "targetLanguage": "string (optional)",
  "pronunciation": "string (optional)",
  "exampleSentence": "string (optional)",
  "notes": "string (optional)",
  "difficultyLevel": "string (optional)",
  "tags": ["string (optional)"]
}
```

**Response 200 (OK):**
```json
{
  "id": "uuid",
  "sourceWord": "string",
  "translation": "string",
  "sourceLanguage": "string",
  "targetLanguage": "string",
  "pronunciation": "string",
  "exampleSentence": "string",
  "notes": "string",
  "difficultyLevel": "string",
  "tags": ["string"],
  "userId": "uuid",
  "createdAt": "datetime",
  "updatedAt": "datetime"
}
```

**Response 404 (Not Found):**
```json
{
  "error": "NotFound",
  "message": "Vocabulary card not found"
}
```

### DELETE /api/v1/vocabulary-cards/{id}

Delete a vocabulary card (soft delete).

**Path Parameters:**
- `id` (UUID, required): The vocabulary card ID

**Response 204 (No Content)**

**Response 404 (Not Found):**
```json
{
  "error": "NotFound",
  "message": "Vocabulary card not found"
}
```

### GET /api/v1/vocabulary-cards/statistics

Get statistics about user's vocabulary cards.

**Response 200 (OK):**
```json
{
  "totalCards": 150,
  "cardsByLanguagePair": [
    {
      "sourceLanguage": "en",
      "targetLanguage": "zh",
      "count": 80
    }
  ],
  "cardsByDifficulty": {
    "beginner": 50,
    "intermediate": 70,
    "advanced": 30
  },
  "recentlyAdded": 10,
  "lastCreatedAt": "datetime"
}
```

## Business Rules

### BR-007: Authorization
- Users can only view, update, or delete their own vocabulary cards
- System must verify card ownership before any operation

### BR-008: Soft Delete
- Deleted cards are marked with `IsDeleted = true` and `DeletedAt` timestamp
- Soft-deleted cards are excluded from all queries
- Data is retained for potential recovery

### BR-009: Pagination Limits
- Default page size: 20 items
- Maximum page size: 100 items
- Page numbers start from 1

### BR-010: Search Behavior
- Search is case-insensitive
- Search supports partial matching (contains)
- Search applies to both source word and translation fields

### BR-011: Update Validation
- At least one field must be provided for update
- Same validation rules apply as creation
- `createdAt` and `userId` cannot be modified

### BR-012: Concurrent Updates
- Use optimistic locking to handle concurrent updates
- Include version field or use `updatedAt` for conflict detection

## Database Schema Updates

### Table: VocabularyCards (Additional Columns)

| Column | Type | Constraints |
|--------|------|-------------|
| IsDeleted | BIT | DEFAULT 0 |
| DeletedAt | DATETIME | NULL |

### Indexes

```sql
-- For user queries
CREATE INDEX IX_VocabularyCards_UserId_IsDeleted ON VocabularyCards(UserId, IsDeleted);

-- For search functionality
CREATE INDEX IX_VocabularyCards_SourceWord ON VocabularyCards(SourceWord);
CREATE INDEX IX_VocabularyCards_Translation ON VocabularyCards(Translation);

-- For filtering
CREATE INDEX IX_VocabularyCards_Languages ON VocabularyCards(SourceLanguage, TargetLanguage);
CREATE INDEX IX_VocabularyCards_CreatedAt ON VocabularyCards(CreatedAt DESC);

-- For tags
CREATE INDEX IX_VocabularyCardTags_Tag ON VocabularyCardTags(Tag);
```

## Security Considerations

- All endpoints require JWT authentication
- User ID from JWT token is used to filter results
- No user can access another user's cards
- Input sanitization for search queries to prevent injection
- Rate limiting: 
  - List/Search: max 100 requests per minute per user
  - Get: max 500 requests per minute per user
  - Update: max 50 requests per minute per user
  - Delete: max 20 requests per minute per user

## Testing Scenarios

### Positive Test Cases
1. Get a card that exists and belongs to user
2. List cards with default pagination
3. List cards with custom page size
4. Search cards by source word
5. Search cards by translation
6. Filter by language pair
7. Filter by difficulty level
8. Filter by tags
9. Sort by different fields
10. Update card with valid data
11. Delete card
12. Get statistics

### Negative Test Cases
1. Get a card that doesn't exist
2. Get a card belonging to another user
3. List with invalid page number (< 1)
4. List with page size exceeding limit
5. Update a non-existent card
6. Update a card belonging to another user
7. Update with invalid data
8. Delete a non-existent card
9. Delete a card belonging to another user
10. Search without authentication

## Performance Requirements

- Card retrieval by ID: < 50ms (95th percentile)
- List operation: < 200ms (95th percentile)
- Search operation: < 300ms (95th percentile)
- Update operation: < 150ms (95th percentile)
- Delete operation: < 100ms (95th percentile)
- Support 100 concurrent read operations
- Support 50 concurrent write operations

## Caching Strategy

- Cache individual cards for 5 minutes
- Invalidate cache on update/delete
- Don't cache list/search results (too dynamic)
- Cache statistics for 1 hour per user
