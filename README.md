# Video Downloader API

A powerful and secure API service built with NestJS for downloading videos from various social media platforms including YouTube, Facebook, Instagram, TikTok, and Twitter.

## Features

- 🔐 Secure API Key Authentication
- 📊 Rate Limiting
- 🌐 IP Whitelisting
- ⏱️ Download Duration Limits
- 📝 Download History Tracking
- 🔄 Multiple Platform Support
- 📈 Usage Analytics
- 🚀 Swagger API Documentation

## Supported Platforms

- YouTube
- Facebook
- Instagram
- TikTok
- Twitter

## Prerequisites

- Node.js (v16 or higher)
- PostgreSQL
- pnpm
- yt-dlp (for video downloading)

## Project Setup

1. Clone the repository:

```bash
git clone <your-repo-url>
cd <project-directory>
```

2. Install dependencies:

```bash
pnpm install
```

3. Configure environment variables:
   - Copy `.env.example` to `.env`
   - Update the following variables:

```env
DATABASE_URL="postgresql://postgres:root@localhost:5432/downloader?schema=public"
ALLOWED_ORIGINS="your-frontend-domain.com,another-domain.com"
NODE_ENV="development"
```

4. Set up the database:

```bash
# Generate Prisma client
pnpm prisma generate

# Run database migrations
pnpm prisma migrate dev
```

## Running the Application

```bash
# Development mode
pnpm run start:dev

# Production mode
pnpm run build
pnpm run start:prod
```

The API will be available at `http://localhost:3001` by default.

## API Documentation

When running in development mode, Swagger documentation is available at:

```
http://localhost:3001/api
```

## Authentication

The API uses API key authentication. You can provide the API key in one of two ways:

1. Via the `X-API-Key` header:

```
X-API-Key: your-api-key
```

2. Via Bearer token in the Authorization header:

```
Authorization: Bearer your-api-key
```

## API Routes Documentation

### User Management (Admin Only)

All user management routes require admin privileges.

#### Create User

```http
POST /users
Content-Type: application/json

{
  "email": "user@example.com",
  "name": "John Doe",
  "isAdmin": false
}
```

#### List Users

```http
GET /users?includeApiKeys=true
```

#### Get User Details

```http
GET /users/:id
```

#### Update User

```http
PUT /users/:id
Content-Type: application/json

{
  "name": "Updated Name",
  "isBlocked": false,
  "isAdmin": false
}
```

#### Delete User

```http
DELETE /users/:id
```

### API Key Management

#### Admin Routes

##### List All API Keys

```http
GET /api-keys/all
```

##### Create New API Key

```http
POST /api-keys
Content-Type: application/json

{
  "userId": "user_id",
  "name": "API Key Name",
  "expiresAt": "2024-12-31T23:59:59Z",
  "ipWhitelist": ["192.168.1.1"],
  "rateLimit": 100,
  "maxDuration": 1800
}
```

##### Block API Key

```http
PUT /api-keys/:id/block
Content-Type: application/json

{
  "reason": "Violation of terms"
}
```

##### Unblock API Key

```http
PUT /api-keys/:id/unblock
```

##### Revoke API Key (Admin)

```http
DELETE /api-keys/:id
```

#### User Routes

##### List My API Keys

```http
GET /api-keys/my-keys
```

##### Update My API Key

```http
PUT /api-keys/my-key/:id
Content-Type: application/json

{
  "name": "Updated Name",
  "ipWhitelist": ["192.168.1.1"],
  "maxDuration": 1800
}
```

##### Revoke My API Key

```http
DELETE /api-keys/my-key/:id
```

##### Validate API Key

```http
GET /api-keys/validate
X-API-Key: your-api-key
```

## API Key Format

API keys follow the format: `dk_<32_bytes_hex>`
Example: `dk_1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef`

## System Maintenance

The system includes automatic maintenance features:

### Cleanup Service

- Runs every hour
- Removes downloaded files older than 12 hours
- Updates download status to EXPIRED in the database
- Maintains disk space and system performance

## API Key Properties

Each API key includes the following configurable properties:

- `name`: Descriptive name for the key
- `expiresAt`: Optional expiration date
- `ipWhitelist`: List of allowed IP addresses
- `rateLimit`: Requests per minute (default: 100)
- `maxDuration`: Maximum media duration in seconds (default: 1800)
- `isBlocked`: Whether the key is blocked
- `blockReason`: Reason for blocking (if blocked)
- `lastUsedAt`: Timestamp of last usage

## User Properties

User accounts have the following properties:

- `email`: Unique email address
- `name`: Optional display name
- `isAdmin`: Administrative privileges
- `isBlocked`: Account status
- `createdAt`: Account creation date
- `updatedAt`: Last update timestamp
