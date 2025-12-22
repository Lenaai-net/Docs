# Messages API Documentation

This document provides comprehensive documentation for third-party clients to integrate with the Messages API. The API allows you to authenticate, manage sessions, and retrieve conversation data.

## Table of Contents

1. [Overview](#overview)
2. [Authentication](#authentication)
3. [Endpoints](#endpoints)
4. [Code Examples](#code-examples)
5. [Error Handling](#error-handling)
6. [Security Best Practices](#security-best-practices)

---

## Overview

### Base URL

```
https://api.lenaai.net
```

**Note:** Replace with your actual API base URL as provided by your administrator.

### Authentication

All API endpoints (except login) require authentication using a Bearer token in the Authorization header:

```
Authorization: Bearer <access_token>
```

### Response Format

All API responses follow the `StandardResponse` format:

```json
{
  "status": true,           // boolean: true for success, false for error
  "code": 200,             // integer: HTTP status code
  "message": "Optional success message",
  "data": {},              // object: response data (varies by endpoint)
  "error_message": null    // string: error message (only present on error)
}
```

**Success Response Example:**
```json
{
  "status": true,
  "code": 200,
  "message": "Operation completed successfully",
  "data": { /* endpoint-specific data */ }
}
```

**Error Response Example:**
```json
{
  "status": false,
  "code": 401,
  "error_message": "Invalid authentication credentials"
}
```

---

## Authentication

### 1. Login

Authenticate with your credentials to receive access and refresh tokens.

**Endpoint:** `POST /client/login`

**Content-Type:** `application/x-www-form-urlencoded`

**Request Parameters:**
- `username` (string, required): Your email address
- `password` (string, required): Your password

**Response:**
- `status`: boolean
- `code`: HTTP status code (200 on success)
- `message`: Success message
- `data`: Object containing:
  - `access_token` (string): JWT token valid for 15 minutes
  - `refresh_token` (string): JWT token valid for 15 days
  - Other client information (email, client_id, etc.)

**Token Expiration:**
- Access Token: 15 minutes
- Refresh Token: 15 days

**Example Request (cURL):**
```bash
curl -X POST 'https://api.lenaai.net/client/login' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'username=your-email@example.com&password=your-password'
```

**Example Response:**
```json
{
  "status": true,
  "code": 200,
  "message": "Client authenticated successfully",
  "data": {
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "client_id": "demo",
    "email": "demo@lenaai.net",
    "client_name": "Demo Client"
  }
}
```

---

### 2. Refresh Token

Refresh your access token when it expires using your refresh token.

**Endpoint:** `POST /client/refresh-token`

**Request Parameters:**
- `refresh_token` (string, required): Your refresh token (can be sent as form data or query parameter)

**Response:**
- `access_token` (string): New access token valid for 15 minutes

**Example Request (cURL):**
```bash
curl -X POST 'https://api.lenaai.net/client/refresh-token' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'refresh_token=your-refresh-token-here'
```

**Example Response:**
```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

**Note:** If the refresh token is invalid or expired, you will need to log in again.

---

### 3. Logout

Logout and invalidate your session.

**Endpoint:** `POST /client/logout`

**Authentication:** Required (Bearer token)

**Response:**
- `status`: boolean
- `code`: HTTP status code (200 on success)
- `message`: "Logged out successfully"

**Example Request (cURL):**
```bash
curl -X POST 'https://api.lenaai.net/client/logout' \
  -H 'Authorization: Bearer your-access-token-here'
```

**Example Response:**
```json
{
  "status": true,
  "code": 200,
  "message": "Logged out successfully"
}
```

---

## Endpoints

### 1. Get All Conversations/Leads

Retrieve a paginated list of all conversations (leads) for your client account.

**Endpoint:** `GET /messages/all/{client_id}`

**Authentication:** Required (Bearer token)

**Path Parameters:**
- `client_id` (string, required): Your client ID (automatically validated against your authenticated account)

**Query Parameters:**
- `limit` (integer, optional): Number of users per page
  - Default: `50`
  - Minimum: `1`
  - Maximum: `100`
- `cursor` (string, optional): Pagination cursor (user_id to start after)
- `direction` (string, optional): Pagination direction
  - Default: `"forward"`
  - Allowed values: `"forward"`, `"backward"`
- `start_date` (string, optional): Filter results from this date (ISO format, e.g., `2025-01-01T00:00:00Z`)
- `end_date` (string, optional): Filter results until this date (ISO format, e.g., `2025-12-31T23:59:59Z`)
- `action` (string, optional): Filter results by an action (e.g., `"Qualified lead"`, `"Missing requirement"`)
- `campaign_ids` (string, optional): Filter results by campaign IDs (comma-separated, e.g., `"demo,campaign1,campaign2"`)
- `unread_messages` (integer, optional): Filter results by unread messages count

**Response Structure:**
```json
{
  "status": true,
  "code": 200,
  "message": "Records retrieved successfully",
  "data": {
    "users": [
      {
        "user_id": "string",
        "phone_number": "string",
        "name": "string",
        "client_id": "string",
        "unread_messages_count": 0,
        "messages_count": 0,
        "campaign_ids": ["string"],
        "last_action": "string",
        "requirement_name": "string",
        "score": 0.0,
        "updated_at": "2025-12-22T14:57:25.716309Z"
      }
    ],
    "count": 0,
    "pagination": {
      "next_cursor": "string",
      "prev_cursor": "string",
      "has_more_next": true,
      "has_more_prev": false
    }
  }
}
```

**Example Request (cURL):**
```bash
curl -X GET 'https://api.lenaai.net/messages/all/demo?limit=50&direction=forward' \
  -H 'Authorization: Bearer your-access-token-here'
```

**Example Response:**
```json
{
  "status": true,
  "code": 200,
  "message": "Records retrieved successfully",
  "data": {
    "users": [
      {
        "unread_messages_count": 1,
        "phone_number": "",
        "messages_count": 1,
        "campaign_ids": [],
        "last_action": "Missing requirement",
        "client_id": "demo",
        "score": 25.5,
        "updated_at": "2025-12-22T14:57:25.716309Z",
        "user_id": "5bf252cd-cf66-4eae-a4c0-0ba6050b8175",
        "requirement_name": "Not defined",
        "name": ""
      },
      {
        "phone_number": "+491745684700",
        "unread_messages_count": 0,
        "messages_count": 7,
        "campaign_ids": [
          "demo"
        ],
        "requirement_name": "apartment",
        "client_id": "demo",
        "score": 25.5,
        "updated_at": "2025-12-22T14:55:16.091405Z",
        "user_id": "2f672cbe-7a7d-4825-8c7d-0926fc46ae06",
        "last_action": "Qualified lead",
        "name": "Abozaid Ibrahim"
      }
    ],
    "count": 2,
    "pagination": {
      "next_cursor": "2f672cbe-7a7d-4825-8c7d-0926fc46ae06",
      "prev_cursor": null,
      "has_more_next": true,
      "has_more_prev": false
    }
  },
  "error_message": null
}
```

**Security Note:** You can only access data for your own client_id. The API automatically validates that the `client_id` in the path matches your authenticated account.

---

### 2. Get Single Conversation Details

Retrieve the full conversation history for a specific user, including all messages and property details.

**Endpoint:** `GET /messages/conversation/{userId}`

**Authentication:** Required (Bearer token)

**Path Parameters:**
- `userId` (string, required): The user ID (phone number or UUID)

**Query Parameters:**
- `limit` (integer, optional): Number of messages to fetch
  - Default: `50`
  - Minimum: `1`
  - Maximum: `500`
- `offset` (integer, optional): Number of messages to skip (pagination offset)
  - Default: `0`
  - Minimum: `0`

**Response Structure:**
```json
{
  "status": true,
  "code": 200,
  "message": "Operation completed successfully",
  "data": {
    "user_id": "string",
    "phone_number": "string",
    "name": "string",
    "client_id": "string",
    "unread_messages_count": 0,
    "total_messages": 0,
    "campaign_ids": ["string"],
    "source": "string",
    "chunks_ref": ["string"],
    "last_chunk_id": 0,
    "messages": [
      {
        "user_message": "string",
        "bot_response": "string",
        "timestamp": "2025-12-22T14:55:01.142072Z",
        "platform": "string",
        "campaign_id": "string",
        "source": "string",
        "project_data": {},
        "project_phases": [],
        "properties": {},
        "crm_link": "string"
      }
    ]
  }
}
```

**Example Request (cURL):**
```bash
curl -X GET 'https://api.lenaai.net/messages/conversation/5bf252cd-cf66-4eae-a4c0-0ba6050b8175?limit=50&offset=0' \
  -H 'Authorization: Bearer your-access-token-here'
```

**Example Response:**
```json
{
  "status": true,
  "code": 200,
  "message": "Operation completed successfully",
  "data": {
    "phone_number": "+491745684700",
    "unread_messages_count": 3,
    "chunks_ref": [
      "5bf252cd-cf66-4eae-a4c0-0ba6050b8175_0"
    ],
    "source": "api",
    "campaign_ids": [
      "demo"
    ],
    "user_id": "5bf252cd-cf66-4eae-a4c0-0ba6050b8175",
    "client_id": "demo",
    "total_messages": 3,
    "last_chunk_id": 0,
    "name": "Abozaid Ibrahim",
    "messages": [
      {
        "campaign_id": "demo",
        "project_phases": [],
        "source": "website",
        "project_data": {},
        "bot_response": "في الساحل الشمالي، هناك 57 مشروع متاح حاليًا...",
        "user_message": "ايه المشاريع المتاحه حاليا ف الساحل الشمالى...",
        "properties": "",
        "timestamp": "2025-12-22T14:55:01.142072Z",
        "platform": "website",
        "crm_link": ""
      },
      {
        "campaign_id": "demo",
        "project_phases": [],
        "source": "website",
        "project_data": {},
        "bot_response": "وجدت لك خيارين قريبين من طلبك...",
        "user_message": "ابعتلى  كدا افضل تتل اخيارات",
        "properties": {
          "0": {
            "deliveryStatus": "ready to move",
            "owner_name": null,
            "compound": "madinaty",
            "images": [
              {
                "fileId": "demo_60ce0fde",
                "url": "https://api.lenaai.net/images/demo_60ce0fde"
              }
            ],
            "view": "park",
            "project_ar": "مدينتي",
            "city": "cairo",
            "clientId": "demo",
            "phase": "b12",
            "unit_id": "de3cfc4d-329d-4c48-a216-358c7b63f380",
            "downPayment": 2000000,
            "dataSource": "website",
            "floor": 1,
            "landArea": 78,
            "project_data": {
              "google_map_link": "https://maps.app.goo.gl/iAhwCkhBSt9cAsTT9",
              "description": "يقع المشروع شرق القاهرة...",
              "video_url": "https://youtu.be/q3RKDjCZKMo?feature=shared",
              "developer_id": "745ba40d-8ff8-4ec2-b64b-aafccd396838",
              "id": "09e7ace1-1b65-4dc6-a827-bf6a25b2232b",
              "developer_name": "TMG",
              "gated": true,
              "images": [
                {
                  "fileId": "demo_a414a582",
                  "url": "https://api.lenaai.net/images/demo_a414a582"
                }
              ],
              "client_id": "public",
              "updated_at": "2025-09-15T11:27:23.563504Z",
              "city": "cairo",
              "units_count": 47,
              "area": 100,
              "district": "first settlement",
              "name": "Madinaty",
              "phases": []
            },
            "buildingType": "apartment",
            "bathroomCount": 1,
            "unitTitle": "two bed room appartment",
            "project": "madinaty",
            "description": "property titled 'two bed room appartment'...",
            "clientName": "demo props",
            "deliveryDate": 1709424000,
            "id": "08499d8c-4cf4-4a5e-8bc3-43420b21cba1",
            "purpose": "sell",
            "garageArea": 0,
            "code": "",
            "model": "",
            "gardenSize": 0,
            "totalPrice": 4000000,
            "updatedAt": "2025-07-16 06:29:37.907538+00:00",
            "district": "first settlement",
            "isGated": false,
            "unitId": "de3cfc4d-329d-4c48-a216-358c7b63f380",
            "roomsCount": 2,
            "country": "egypt",
            "furnishing": "furnished",
            "developer": "tmg holding",
            "finishing": "fully finished",
            "developer_ar": "مجموعة طلعت مصطفى",
            "owner_mobile": null
          }
        },
        "timestamp": "2025-12-22T14:55:51.722082Z",
        "platform": "website",
        "crm_link": ""
      }
    ]
  },
  "error_message": null
}
```

**Security Note:** You can only access conversations for users belonging to your client account. The API automatically validates this based on your authenticated token.

---

## Code Examples

### Python Example

```python
import requests
from typing import Optional, Dict, Any

class MessagesAPIClient:
    def __init__(self, base_url: str):
        self.base_url = base_url
        self.access_token: Optional[str] = None
        self.refresh_token: Optional[str] = None
    
    def login(self, email: str, password: str) -> Dict[str, Any]:
        """Login and store tokens"""
        url = f"{self.base_url}/client/login"
        data = {
            "username": email,
            "password": password
        }
        response = requests.post(url, data=data)
        response.raise_for_status()
        result = response.json()
        
        if result.get("status"):
            self.access_token = result["data"]["access_token"]
            self.refresh_token = result["data"]["refresh_token"]
        
        return result
    
    def refresh_access_token(self) -> str:
        """Refresh access token using refresh token"""
        url = f"{self.base_url}/client/refresh-token"
        data = {"refresh_token": self.refresh_token}
        response = requests.post(url, data=data)
        response.raise_for_status()
        result = response.json()
        self.access_token = result["access_token"]
        return self.access_token
    
    def _get_headers(self) -> Dict[str, str]:
        """Get authorization headers"""
        if not self.access_token:
            raise ValueError("Not authenticated. Call login() first.")
        return {"Authorization": f"Bearer {self.access_token}"}
    
    def get_all_conversations(
        self,
        client_id: str,
        limit: int = 50,
        cursor: Optional[str] = None,
        direction: str = "forward",
        start_date: Optional[str] = None,
        end_date: Optional[str] = None,
        action: Optional[str] = None,
        campaign_ids: Optional[str] = None,
        unread_messages: Optional[int] = None
    ) -> Dict[str, Any]:
        """Get all conversations/leads"""
        url = f"{self.base_url}/messages/all/{client_id}"
        params = {
            "limit": limit,
            "direction": direction
        }
        if cursor:
            params["cursor"] = cursor
        if start_date:
            params["start_date"] = start_date
        if end_date:
            params["end_date"] = end_date
        if action:
            params["action"] = action
        if campaign_ids:
            params["campaign_ids"] = campaign_ids
        if unread_messages is not None:
            params["unread_messages"] = unread_messages
        
        response = requests.get(url, headers=self._get_headers(), params=params)
        response.raise_for_status()
        return response.json()
    
    def get_conversation(
        self,
        user_id: str,
        limit: int = 50,
        offset: int = 0
    ) -> Dict[str, Any]:
        """Get single conversation details"""
        url = f"{self.base_url}/messages/conversation/{user_id}"
        params = {
            "limit": limit,
            "offset": offset
        }
        response = requests.get(url, headers=self._get_headers(), params=params)
        response.raise_for_status()
        return response.json()
    
    def logout(self) -> Dict[str, Any]:
        """Logout"""
        url = f"{self.base_url}/client/logout"
        response = requests.post(url, headers=self._get_headers())
        response.raise_for_status()
        self.access_token = None
        self.refresh_token = None
        return response.json()


# Usage example
if __name__ == "__main__":
    client = MessagesAPIClient("https://api.lenaai.net")
    
    # Login
    login_result = client.login("your-email@example.com", "your-password")
    print("Login successful:", login_result["status"])
    
    # Get all conversations
    conversations = client.get_all_conversations("demo", limit=50)
    print(f"Found {conversations['data']['count']} conversations")
    
    # Get specific conversation
    if conversations["data"]["users"]:
        user_id = conversations["data"]["users"][0]["user_id"]
        conversation = client.get_conversation(user_id)
        print(f"Conversation has {conversation['data']['total_messages']} messages")
    
    # Logout
    client.logout()
    print("Logged out")
```

### JavaScript/Node.js Example

```javascript
const axios = require('axios');

class MessagesAPIClient {
    constructor(baseUrl) {
        this.baseUrl = baseUrl;
        this.accessToken = null;
        this.refreshToken = null;
    }

    async login(email, password) {
        const url = `${this.baseUrl}/client/login`;
        const data = new URLSearchParams({
            username: email,
            password: password
        });
        
        const response = await axios.post(url, data, {
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
        });
        
        if (response.data.status) {
            this.accessToken = response.data.data.access_token;
            this.refreshToken = response.data.data.refresh_token;
        }
        
        return response.data;
    }

    async refreshAccessToken() {
        const url = `${this.baseUrl}/client/refresh-token`;
        const data = new URLSearchParams({
            refresh_token: this.refreshToken
        });
        
        const response = await axios.post(url, data, {
            headers: { 'Content-Type': 'application/x-www-form-urlencoded' }
        });
        
        this.accessToken = response.data.access_token;
        return this.accessToken;
    }

    _getHeaders() {
        if (!this.accessToken) {
            throw new Error('Not authenticated. Call login() first.');
        }
        return {
            'Authorization': `Bearer ${this.accessToken}`
        };
    }

    async getAllConversations(clientId, options = {}) {
        const {
            limit = 50,
            cursor = null,
            direction = 'forward',
            startDate = null,
            endDate = null,
            action = null,
            campaignIds = null,
            unreadMessages = null
        } = options;

        const url = `${this.baseUrl}/messages/all/${clientId}`;
        const params = { limit, direction };
        
        if (cursor) params.cursor = cursor;
        if (startDate) params.start_date = startDate;
        if (endDate) params.end_date = endDate;
        if (action) params.action = action;
        if (campaignIds) params.campaign_ids = campaignIds;
        if (unreadMessages !== null) params.unread_messages = unreadMessages;

        const response = await axios.get(url, {
            headers: this._getHeaders(),
            params
        });
        
        return response.data;
    }

    async getConversation(userId, limit = 50, offset = 0) {
        const url = `${this.baseUrl}/messages/conversation/${userId}`;
        const params = { limit, offset };
        
        const response = await axios.get(url, {
            headers: this._getHeaders(),
            params
        });
        
        return response.data;
    }

    async logout() {
        const url = `${this.baseUrl}/client/logout`;
        const response = await axios.post(url, {
            headers: this._getHeaders()
        });
        
        this.accessToken = null;
        this.refreshToken = null;
        return response.data;
    }
}

// Usage example
(async () => {
    const client = new MessagesAPIClient('https://api.lenaai.net');
    
    try {
        // Login
        const loginResult = await client.login('your-email@example.com', 'your-password');
        console.log('Login successful:', loginResult.status);
        
        // Get all conversations
        const conversations = await client.getAllConversations('demo', { limit: 50 });
        console.log(`Found ${conversations.data.count} conversations`);
        
        // Get specific conversation
        if (conversations.data.users.length > 0) {
            const userId = conversations.data.users[0].user_id;
            const conversation = await client.getConversation(userId);
            console.log(`Conversation has ${conversation.data.total_messages} messages`);
        }
        
        // Logout
        await client.logout();
        console.log('Logged out');
    } catch (error) {
        console.error('Error:', error.response?.data || error.message);
    }
})();
```

---

## Error Handling

### Common Error Codes

| Status Code | Description | Solution |
|------------|-------------|----------|
| 401 | Unauthorized - Invalid or expired token | Refresh your access token or login again |
| 403 | Forbidden - Access denied | Verify your credentials and permissions |
| 404 | Not Found - Resource not found | Check the endpoint URL and parameters |
| 400 | Bad Request - Invalid request parameters | Verify request parameters match the API specification |
| 500 | Internal Server Error | Contact support if the issue persists |

### Error Response Format

```json
{
  "status": false,
  "code": 401,
  "error_message": "Token has expired"
}
```

### Token Expiration Handling

Access tokens expire after 15 minutes. Implement automatic token refresh:

```python
def make_authenticated_request(self, method: str, url: str, **kwargs):
    """Make request with automatic token refresh on 401"""
    try:
        response = requests.request(method, url, headers=self._get_headers(), **kwargs)
        if response.status_code == 401:
            # Token expired, refresh it
            self.refresh_access_token()
            # Retry the request
            response = requests.request(method, url, headers=self._get_headers(), **kwargs)
        return response
    except Exception as e:
        raise
```

### Retry Strategy

For production applications, implement exponential backoff for retries:

```python
import time
from typing import Callable

def retry_with_backoff(func: Callable, max_retries: int = 3):
    """Retry function with exponential backoff"""
    for attempt in range(max_retries):
        try:
            return func()
        except requests.exceptions.RequestException as e:
            if attempt == max_retries - 1:
                raise
            wait_time = 2 ** attempt  # Exponential backoff
            time.sleep(wait_time)
```

---

## Security Best Practices

### 1. Token Storage

**DO:**
- Store tokens securely (encrypted at rest)
- Use secure storage mechanisms (keychain, secure vault, environment variables)
- Implement token rotation
- Clear tokens on logout

**DON'T:**
- Store tokens in client-side code (JavaScript, mobile apps without encryption)
- Commit tokens to version control
- Log tokens in application logs
- Share tokens between different applications

### 2. Token Refresh Strategy

- Refresh access tokens before they expire (recommended: refresh at 14 minutes)
- Implement automatic token refresh on 401 errors
- Store refresh tokens securely (they last 15 days)
- Handle refresh token expiration gracefully (prompt user to login again)

### 3. HTTPS Requirement

**Always use HTTPS** for all API requests. Never send tokens over unencrypted connections.

### 4. Rate Limiting

The API implements rate limiting to prevent abuse. If you encounter rate limit errors:

- Implement exponential backoff in your retry logic
- Cache responses when appropriate
- Batch requests when possible
- Contact support if you need higher rate limits

### 5. Authorization Header

Always include the Bearer token in the Authorization header:

```
Authorization: Bearer <access_token>
```

**Never** send tokens in URL parameters or request bodies (except for login/refresh endpoints).

### 6. Input Validation

- Validate all input parameters before sending requests
- Sanitize user-provided data
- Respect parameter constraints (min/max values, allowed patterns)

### 7. Error Handling

- Never expose sensitive information in error messages to end users
- Log errors securely (without tokens)
- Implement proper error handling and user feedback

### 8. Security Headers

When making requests, ensure:
- Use HTTPS only
- Validate SSL certificates
- Don't disable certificate validation in production

---

## Support

For API support, questions, or to report issues, please contact your API administrator or support team.

---

**Document Version:** 1.0  
**Last Updated:** 2025-12-22

