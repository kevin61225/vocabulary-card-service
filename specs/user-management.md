# User Management Specification

## Feature Overview

This specification defines the functionality for user registration, authentication, and profile management in the Vocabulary Card Service.

## User Stories

### US-009: User Registration
**As a** new visitor  
**I want to** register for an account  
**So that** I can start using the vocabulary card service

**Acceptance Criteria:**
- User can register with email and password
- System validates email format and password strength
- System creates a unique user ID
- System sends a verification email
- User cannot register with an already registered email

### US-010: User Login
**As a** registered user  
**I want to** log in to my account  
**So that** I can access my vocabulary cards

**Acceptance Criteria:**
- User can log in with email and password
- System verifies credentials
- System returns a JWT token upon successful login
- System returns an error for invalid credentials
- System supports token refresh

### US-011: View User Profile
**As a** logged-in user  
**I want to** view my profile information  
**So that** I can see my account details

**Acceptance Criteria:**
- User can retrieve their profile information
- Profile includes email, name, preferred languages, and account metadata
- Sensitive information (password) is not exposed

### US-012: Update User Profile
**As a** logged-in user  
**I want to** update my profile information  
**So that** I can keep my account details current

**Acceptance Criteria:**
- User can update their name
- User can update their preferred learning languages
- User can update their native language
- User cannot change their email directly (requires verification)
- System validates all updates

### US-013: Change Password
**As a** logged-in user  
**I want to** change my password  
**So that** I can keep my account secure

**Acceptance Criteria:**
- User must provide current password
- User must provide new password twice for confirmation
- New password must meet security requirements
- System invalidates old sessions after password change

### US-014: Password Reset
**As a** user who forgot their password  
**I want to** reset my password  
**So that** I can regain access to my account

**Acceptance Criteria:**
- User can request password reset by email
- System sends a reset token to user's email
- Reset token expires after 1 hour
- User can set new password using valid token

## API Endpoints

### POST /api/v1/auth/register

Register a new user account.

**Request Body:**
```json
{
  "email": "string (required, valid email)",
  "password": "string (required, min 8 chars)",
  "confirmPassword": "string (required, must match password)",
  "firstName": "string (optional, max 50 chars)",
  "lastName": "string (optional, max 50 chars)",
  "nativeLanguage": "string (optional, ISO 639-1 code)",
  "learningLanguages": ["string (optional, array of ISO 639-1 codes)"]
}
```

**Response 201 (Created):**
```json
{
  "id": "uuid",
  "email": "string",
  "firstName": "string",
  "lastName": "string",
  "nativeLanguage": "string",
  "learningLanguages": ["string"],
  "createdAt": "datetime",
  "emailVerified": false
}
```

**Response 400 (Bad Request):**
```json
{
  "error": "ValidationError",
  "message": "Invalid input data",
  "validationErrors": [
    {
      "field": "email",
      "message": "Email is already registered"
    }
  ]
}
```

### POST /api/v1/auth/login

Authenticate and receive JWT token.

**Request Body:**
```json
{
  "email": "string (required)",
  "password": "string (required)"
}
```

**Response 200 (OK):**
```json
{
  "accessToken": "string (JWT token)",
  "refreshToken": "string",
  "tokenType": "Bearer",
  "expiresIn": 3600,
  "user": {
    "id": "uuid",
    "email": "string",
    "firstName": "string",
    "lastName": "string"
  }
}
```

**Response 401 (Unauthorized):**
```json
{
  "error": "Unauthorized",
  "message": "Invalid email or password"
}
```

### POST /api/v1/auth/refresh

Refresh an expired access token.

**Request Body:**
```json
{
  "refreshToken": "string (required)"
}
```

**Response 200 (OK):**
```json
{
  "accessToken": "string (JWT token)",
  "refreshToken": "string",
  "tokenType": "Bearer",
  "expiresIn": 3600
}
```

**Response 401 (Unauthorized):**
```json
{
  "error": "Unauthorized",
  "message": "Invalid or expired refresh token"
}
```

### POST /api/v1/auth/logout

Logout and invalidate tokens.

**Request Headers:**
- `Authorization: Bearer {token}`

**Response 204 (No Content)**

### GET /api/v1/users/me

Get current user's profile.

**Request Headers:**
- `Authorization: Bearer {token}`

**Response 200 (OK):**
```json
{
  "id": "uuid",
  "email": "string",
  "firstName": "string",
  "lastName": "string",
  "nativeLanguage": "string",
  "learningLanguages": ["string"],
  "emailVerified": true,
  "createdAt": "datetime",
  "updatedAt": "datetime"
}
```

### PUT /api/v1/users/me

Update current user's profile.

**Request Headers:**
- `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "firstName": "string (optional, max 50 chars)",
  "lastName": "string (optional, max 50 chars)",
  "nativeLanguage": "string (optional, ISO 639-1 code)",
  "learningLanguages": ["string (optional, array of ISO 639-1 codes)"]
}
```

**Response 200 (OK):**
```json
{
  "id": "uuid",
  "email": "string",
  "firstName": "string",
  "lastName": "string",
  "nativeLanguage": "string",
  "learningLanguages": ["string"],
  "emailVerified": true,
  "createdAt": "datetime",
  "updatedAt": "datetime"
}
```

### POST /api/v1/users/me/change-password

Change user's password.

**Request Headers:**
- `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "currentPassword": "string (required)",
  "newPassword": "string (required, min 8 chars)",
  "confirmNewPassword": "string (required, must match newPassword)"
}
```

**Response 204 (No Content)**

**Response 400 (Bad Request):**
```json
{
  "error": "ValidationError",
  "message": "Current password is incorrect"
}
```

### POST /api/v1/auth/forgot-password

Request password reset email.

**Request Body:**
```json
{
  "email": "string (required)"
}
```

**Response 200 (OK):**
```json
{
  "message": "If the email exists, a password reset link has been sent"
}
```

### POST /api/v1/auth/reset-password

Reset password using token from email.

**Request Body:**
```json
{
  "token": "string (required)",
  "newPassword": "string (required, min 8 chars)",
  "confirmNewPassword": "string (required, must match newPassword)"
}
```

**Response 200 (OK):**
```json
{
  "message": "Password has been reset successfully"
}
```

**Response 400 (Bad Request):**
```json
{
  "error": "ValidationError",
  "message": "Invalid or expired reset token"
}
```

### POST /api/v1/auth/verify-email

Verify user's email address.

**Request Body:**
```json
{
  "token": "string (required)"
}
```

**Response 200 (OK):**
```json
{
  "message": "Email verified successfully"
}
```

### DELETE /api/v1/users/me

Delete user account (soft delete).

**Request Headers:**
- `Authorization: Bearer {token}`

**Request Body:**
```json
{
  "password": "string (required)",
  "confirmation": "DELETE"
}
```

**Response 204 (No Content)**

## Business Rules

### BR-013: Email Uniqueness
- Email addresses must be unique across the system
- Email validation is case-insensitive

### BR-014: Password Requirements
- Minimum 8 characters
- Must contain at least one uppercase letter
- Must contain at least one lowercase letter
- Must contain at least one digit
- Must contain at least one special character (!@#$%^&*()_+-=[]{}|;:,.<>?)

### BR-015: JWT Token
- Access tokens expire after 1 hour
- Refresh tokens expire after 30 days
- Tokens contain user ID and email claims
- Tokens are signed with HS256 algorithm

### BR-016: Email Verification
- Users receive verification email upon registration
- Verification tokens expire after 24 hours
- Users can request resend of verification email
- Unverified users can still log in but have limited features

### BR-017: Account Deletion
- Soft delete: user account is marked as deleted but data is retained
- User must provide password for deletion
- All user's vocabulary cards are also soft deleted
- Deleted accounts cannot log in

### BR-018: Rate Limiting
- Login attempts: max 5 failed attempts per 15 minutes per email
- Account locked for 15 minutes after 5 failed attempts
- Password reset: max 3 requests per hour per email
- Registration: max 5 registrations per hour per IP

## Database Schema

### Table: Users

| Column | Type | Constraints |
|--------|------|-------------|
| Id | UUID | Primary Key |
| Email | NVARCHAR(255) | NOT NULL, UNIQUE |
| PasswordHash | NVARCHAR(255) | NOT NULL |
| FirstName | NVARCHAR(50) | NULL |
| LastName | NVARCHAR(50) | NULL |
| NativeLanguage | VARCHAR(2) | NULL |
| EmailVerified | BIT | DEFAULT 0 |
| EmailVerificationToken | VARCHAR(255) | NULL |
| EmailVerificationTokenExpiry | DATETIME | NULL |
| PasswordResetToken | VARCHAR(255) | NULL |
| PasswordResetTokenExpiry | DATETIME | NULL |
| IsDeleted | BIT | DEFAULT 0 |
| DeletedAt | DATETIME | NULL |
| CreatedAt | DATETIME | NOT NULL |
| UpdatedAt | DATETIME | NOT NULL |

### Table: UserLearningLanguages

| Column | Type | Constraints |
|--------|------|-------------|
| Id | UUID | Primary Key |
| UserId | UUID | Foreign Key, NOT NULL |
| LanguageCode | VARCHAR(2) | NOT NULL |

### Table: RefreshTokens

| Column | Type | Constraints |
|--------|------|-------------|
| Id | UUID | Primary Key |
| UserId | UUID | Foreign Key, NOT NULL |
| Token | VARCHAR(500) | NOT NULL, UNIQUE |
| ExpiresAt | DATETIME | NOT NULL |
| CreatedAt | DATETIME | NOT NULL |
| RevokedAt | DATETIME | NULL |
| IsRevoked | BIT | DEFAULT 0 |

### Table: LoginAttempts

| Column | Type | Constraints |
|--------|------|-------------|
| Id | UUID | Primary Key |
| Email | NVARCHAR(255) | NOT NULL |
| IpAddress | VARCHAR(45) | NOT NULL |
| Success | BIT | NOT NULL |
| AttemptedAt | DATETIME | NOT NULL |

### Indexes

```sql
CREATE UNIQUE INDEX IX_Users_Email ON Users(Email);
CREATE INDEX IX_Users_EmailVerificationToken ON Users(EmailVerificationToken);
CREATE INDEX IX_Users_PasswordResetToken ON Users(PasswordResetToken);
CREATE INDEX IX_RefreshTokens_Token ON RefreshTokens(Token);
CREATE INDEX IX_RefreshTokens_UserId ON RefreshTokens(UserId);
CREATE INDEX IX_LoginAttempts_Email_AttemptedAt ON LoginAttempts(Email, AttemptedAt);
```

## Security Considerations

### Password Storage
- Passwords are hashed using bcrypt with salt rounds = 12
- Never store passwords in plain text
- Never return password hashes in API responses

### JWT Security
- Tokens are signed with a secure secret key (256-bit minimum)
- Secret key is stored in environment variables
- Tokens include expiry claims
- Validate token signature and expiry on every request

### Account Security
- Implement account lockout after failed login attempts
- Send email notifications for security events:
  - Successful login from new device/location
  - Password change
  - Email change
  - Account deletion

### Data Protection
- Use HTTPS for all API communication
- Implement CORS with specific allowed origins
- Sanitize all user inputs
- Use parameterized queries to prevent SQL injection

## Testing Scenarios

### Positive Test Cases
1. Register new user with valid data
2. Login with correct credentials
3. Refresh access token
4. Get user profile
5. Update user profile
6. Change password
7. Request password reset
8. Reset password with valid token
9. Verify email with valid token
10. Logout user

### Negative Test Cases
1. Register with existing email
2. Register with invalid email format
3. Register with weak password
4. Login with wrong password
5. Login with non-existent email
6. Use expired JWT token
7. Use invalid refresh token
8. Change password with wrong current password
9. Reset password with expired token
10. Exceed login rate limit
11. Request password reset too frequently
12. Verify email with invalid token

## Performance Requirements

- Registration: < 500ms (95th percentile)
- Login: < 300ms (95th percentile)
- Token refresh: < 100ms (95th percentile)
- Get profile: < 50ms (95th percentile)
- Update profile: < 200ms (95th percentile)
- Support 1000 concurrent authenticated users

## Email Templates

### Welcome Email
- Subject: "Welcome to Vocabulary Card Service"
- Includes email verification link
- Link format: `{frontendUrl}/verify-email?token={token}`

### Password Reset Email
- Subject: "Password Reset Request"
- Includes password reset link
- Link format: `{frontendUrl}/reset-password?token={token}`
- Token expires in 1 hour

### Password Changed Notification
- Subject: "Your Password Has Been Changed"
- Notifies user of password change
- Includes timestamp and IP address

## Future Enhancements

- OAuth2 authentication (Google, Facebook, GitHub)
- Two-factor authentication (2FA)
- Email change with verification
- Session management (view and revoke active sessions)
- Login history
- Account recovery questions
