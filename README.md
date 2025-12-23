# Lena AI Documentation

Welcome to the Lena AI documentation. This repository contains comprehensive guides for integrating and working with the Lena AI platform.

## 📚 Documentation Overview

This documentation is organized into the following guides:

### 1. [Chat IFrame Integration Guide](./Chat%20IFrame%20integration.md)

Learn how to embed the Lena Chat widget into your website using a standard HTML `<iframe>`. This guide covers:

- **Basic Integration**: Quick start guide for embedding the chat widget
- **Client Configuration**: How to use your `client_id` to control account and branding
- **URL Parameters**: Supported parameters including `client`, `campaign`, `source`, and `force_user_info_request`
- **Layout Examples**: Code samples for different widget positioning (centered, floating, etc.)
- **Theme & Branding**: How theme customization works (managed on the Lena side)
- **Permissions**: Required browser permissions for full functionality
- **Best Practices**: Checklist and tips for production deployment

**Use this guide when:**
- You want to add the Lena Chat widget to your website
- You need to customize the chat appearance or behavior
- You're setting up a new client integration

---

### 2. [Messages API Documentation](./MESSAGES_API_DOCUMENTATION.md)

Complete API reference for programmatically accessing conversation data and managing sessions. This guide covers:

- **Authentication**: Login, token refresh, and logout endpoints
- **Conversation Management**: Retrieve all conversations/leads and individual conversation details
- **Code Examples**: Working examples in Python and JavaScript/Node.js
- **Error Handling**: Common error codes and retry strategies
- **Security Best Practices**: Token management, HTTPS requirements, and security guidelines

**Use this guide when:**
- You need to integrate Lena Chat data into your own systems
- You want to build custom dashboards or analytics
- You're developing backend integrations or automation tools

---

## 🚀 Quick Start

### For Website Integration

1. Read the [Chat IFrame Integration Guide](./Chat%20IFrame%20integration.md)
2. Get your `client_id` from the Lena team
3. Add the iframe code to your website
4. Test and customize as needed

### For API Integration

1. Read the [Messages API Documentation](./MESSAGES_API_DOCUMENTATION.md)
2. Obtain your API credentials
3. Authenticate using the login endpoint
4. Start retrieving conversation data

---

## 🔗 Related Resources

- **Chat URL**: `https://chat.lenaai.net`
- **API Base URL**: `https://api.lenaai.net`

---

## 📝 Document Structure

```
Docs/
├── README.md                           # This file - overview and navigation
├── Chat IFrame integration.md          # Website integration guide
└── MESSAGES_API_DOCUMENTATION.md       # API reference documentation
```

---

## 💡 Common Use Cases

### Embedding Chat on Your Website
→ See [Chat IFrame Integration Guide](./Chat%20IFrame%20integration.md)

### Building a Custom Dashboard
→ See [Messages API Documentation](./MESSAGES_API_DOCUMENTATION.md) - Get All Conversations endpoint

### Tracking Campaign Performance
→ See [Chat IFrame Integration Guide](./Chat%20IFrame%20integration.md) - URL Parameters section  
→ See [Messages API Documentation](./MESSAGES_API_DOCUMENTATION.md) - Filtering by campaign_ids

### Analyzing Conversation Data
→ See [Messages API Documentation](./MESSAGES_API_DOCUMENTATION.md) - Get Conversation Details endpoint

---

## 🆘 Support

For questions, support, or to request changes:

- **WhatsApp**: [wa.me/01016080323](https://wa.me/201016080323)
- **Email**: abozaid@lenaai.net

---

## 📄 Document Versions

- **Chat IFrame Integration Guide**: Current version
- **Messages API Documentation**: Version 1.0.0 (Last Updated: 2025-12-22)

---

**Last Updated**: 2025-12-22

