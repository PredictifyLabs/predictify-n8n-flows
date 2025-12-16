# 🚀 PredictifyLabs Twitter Automation Workflow

[![n8n](https://img.shields.io/badge/n8n-workflow-orange)](https://n8n.io)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-success.svg)]()

An automated n8n workflow that generates and publishes optimized tweets for events using AI (Google Gemini 2.5 Pro).

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Documentation](#api-documentation)
- [Workflow Details](#workflow-details)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [License](#license)

## 🎯 Overview

This workflow automates the entire process of creating engaging Twitter/X posts for events. It receives event data via webhook, processes it through Google Gemini AI to generate an optimized tweet following a specific template, validates the content, and automatically publishes it to Twitter/X.

**Perfect for:**
- Event organizers who need consistent social media presence
- Marketing teams managing multiple events
- Community managers automating repetitive tasks
- Tech conferences and meetups

## ✨ Features

- 🤖 **AI-Powered Content Generation** - Uses Google Gemini 2.5 Pro for intelligent tweet creation
- 📏 **Automatic Character Limit Enforcement** - Ensures tweets never exceed 280 characters
- 🎨 **Consistent Visual Format** - Maintains brand consistency with emoji templates
- 🧠 **Context Memory** - Learns from previous tweets for better consistency
- 🛡️ **Safety & Validation** - Multiple layers of content validation and cleanup
- 📅 **Smart Date Formatting** - Automatically formats dates for readability
- 🔗 **Webhook Integration** - Easy integration with external systems
- 📊 **Error Handling** - Intelligent truncation if AI exceeds limits

## 🏗️ Architecture

```
┌─────────────┐
│   Webhook   │ (Receives Event Data)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Edit Fields │ (Extracts & Maps Data)
└──────┬──────┘
       │
       ▼
┌─────────────┐       ┌──────────────┐
│  AI Agent   │◄──────┤ Gemini 2.5   │
│             │       │  Pro Model   │
└──────┬──────┘       └──────────────┘
       │              ┌──────────────┐
       │         ◄────┤    Memory    │
       │              │    Buffer    │
       ▼              └──────────────┘
┌─────────────┐
│   Format &  │ (Validation & Cleanup)
│   Safety    │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│Create Tweet │ (Publish to X/Twitter)
└─────────────┘
```

## 📦 Prerequisites

Before you begin, ensure you have:

- **n8n instance** (self-hosted or cloud) - [Installation Guide](https://docs.n8n.io/hosting/)
- **Google Gemini API Key** - [Get API Key](https://ai.google.dev/)
- **Twitter/X Developer Account** with OAuth2 credentials - [Developer Portal](https://developer.twitter.com/)
- Node.js 18+ (if self-hosting)

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/predictifylabs-twitter-automation.git
cd predictifylabs-twitter-automation
```

### 2. Import into n8n

**Option A: Via UI**
1. Open your n8n instance
2. Click on **Menu (☰)** → **Import from File**
3. Select `PredictifyLabs_Twitter_Automation.json`
4. Click **Import**

**Option B: Via CLI** (if using n8n CLI)
```bash
n8n import:workflow --input=PredictifyLabs_Twitter_Automation.json
```

### 3. Install Required Nodes

The workflow uses the following n8n packages (install if not available):
```bash
npm install @n8n/n8n-nodes-langchain
npm install n8n-nodes-base
```

## ⚙️ Configuration

### 1. Configure Google Gemini API

1. Navigate to the **Google Gemini Chat Model** node
2. Click on **Credentials** → **Create New**
3. Enter your API key
4. Save the credential

### 2. Configure Twitter/X OAuth2

1. Go to [Twitter Developer Portal](https://developer.twitter.com/en/portal/dashboard)
2. Create a new app or use existing one
3. Generate OAuth2 credentials
4. In n8n, navigate to **Create Tweet** node
5. Click **Credentials** → **Create New**
6. Enter your OAuth2 credentials:
   - Client ID
   - Client Secret
   - Redirect URL (use your n8n OAuth callback URL)
7. Authorize the app

### 3. Activate the Workflow

1. Toggle the workflow to **Active** in the top-right corner
2. Note the webhook URL (will be displayed in the Webhook node)

### 4. Test the Webhook

The webhook URL format:
```
https://your-n8n-instance.com/webhook/create-event
```

## 💻 Usage

### Making a Request

Send a POST request to your webhook URL with the following payload:

```bash
curl -X POST https://your-n8n-instance.com/webhook/create-event \
  -H "Content-Type: application/json" \
  -d '{
    "body": {
      "title": "AI Workshop 2025",
      "description": "Learn to build AI applications with hands-on exercises and real-world case studies. Perfect for developers wanting to integrate AI.",
      "location": "San Francisco",
      "venueDetails": {
        "address": "Tech Hub, 123 Innovation St"
      },
      "startAt": "2025-01-20T14:00:00Z",
      "coverImageUrl": "https://example.com/image.jpg",
      "tags": ["AI", "Tech", "Workshop", "Development"]
    }
  }'
```

### Expected Output

The workflow will generate and publish a tweet like:

```
🚀 AI Workshop 2025 | San Francisco
Build AI apps with hands-on exercises. Perfect for developers.
📅 20 Jan • 2:00 PM
📍 Tech Hub, 123 Innovation St
Info aquí 👇
[Link]
#Development #Tech
```

## 📖 API Documentation

### Webhook Endpoint

**Endpoint:** `POST /webhook/create-event`

**Headers:**
```json
{
  "Content-Type": "application/json"
}
```

**Request Body:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `body.title` | string | Yes | Event title |
| `body.description` | string | Yes | Full event description |
| `body.location` | string | Yes | City or location name |
| `body.venueDetails.address` | string | Yes | Complete venue address |
| `body.startAt` | datetime | Yes | Event start date/time (ISO 8601) |
| `body.coverImageUrl` | string | No | Event cover image URL |
| `body.tags` | array | Yes | Array of tags (min 4 elements) |

**Response:**

Success (200):
```json
{
  "success": true,
  "tweet_id": "1234567890",
  "text": "Generated tweet text...",
  "final_length": 267
}
```

Error (4xx/5xx):
```json
{
  "error": "Error message",
  "details": "Detailed error information"
}
```

## 🔧 Workflow Details

### Node Breakdown

#### 1. Webhook (Trigger)
- **Type:** HTTP Webhook
- **Method:** POST
- **Path:** `/create-event`
- **Function:** Receives event data from external systems

#### 2. Edit Fields
- **Type:** Set Node
- **Function:** Extracts and structures required fields
- **Mappings:**
  - Extracts title, description, location
  - Retrieves venue address
  - Selects primary hashtag (tags[3])

#### 3. AI Agent
- **Type:** LangChain AI Agent
- **Model:** Google Gemini 2.5 Pro
- **Function:** Generates optimized tweet content
- **Constraints:**
  - Maximum 270 characters (leaving room for link)
  - Short description: max 100 characters
  - Concise date format

#### 4. Google Gemini Chat Model
- **Type:** Language Model Connection
- **Model:** `models/gemini-2.5-pro`
- **Function:** Provides AI capabilities to the agent

#### 5. Simple Memory
- **Type:** Buffer Window Memory
- **Session:** `test_session`
- **Function:** Maintains context between executions

#### 6. Format & Safety Logic
- **Type:** Code (JavaScript)
- **Function:** 
  - Cleans Markdown formatting
  - Removes extra quotes
  - Validates 280 character limit
  - Intelligent truncation if needed

#### 7. Create Tweet
- **Type:** Twitter Node
- **Function:** Publishes the final tweet to X/Twitter

### Tweet Template

The AI follows this strict template:

```
🚀 [Event Title] | [Location]
[Compelling one-liner - max 100 chars]
📅 [DD Mon] • [HH:MM AM/PM]
📍 [Venue Address]
Info aquí 👇
[Link]
#[PrimaryTag] #Tech
```

## 🐛 Troubleshooting

### Common Issues

**Issue: Webhook not responding**
- Verify workflow is **Active**
- Check webhook URL is correct
- Ensure n8n instance is accessible

**Issue: Tweet exceeds 280 characters**
- Format & Safety Logic should handle this automatically
- Check if the description is extremely long
- Verify date formatting is working correctly

**Issue: AI Agent timeout**
- Check Google Gemini API quota
- Verify API key is valid
- Increase timeout in node settings

**Issue: Tweet not publishing**
- Verify Twitter OAuth2 credentials
- Check API rate limits
- Ensure account has posting permissions

### Debug Mode

Enable debug mode in n8n:
1. Settings → Workflow Settings
2. Enable "Save Execution Progress"
3. Check execution logs for detailed information

## 🔐 Security Best Practices

- Store API keys in n8n credentials (encrypted)
- Use environment variables for sensitive data
- Implement rate limiting on webhook
- Regularly rotate API keys
- Monitor execution logs for suspicious activity

## 📊 Monitoring & Analytics

### Recommended Metrics to Track

- Number of successful tweets published
- Average character count
- AI generation time
- Webhook response time
- Error rate

### Integration with Analytics Tools

Consider adding nodes for:
- Google Sheets logging
- Slack notifications
- Email alerts on errors

## 🚧 Roadmap

- [ ] Image attachment support
- [ ] Multi-language tweet generation
- [ ] Scheduled tweet posting
- [ ] A/B testing capabilities
- [ ] Analytics dashboard integration
- [ ] Thread support for longer content

## 🤝 Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Development Setup

```bash
# Clone your fork
git clone https://github.com/yourusername/predictifylabs-twitter-automation.git

# Create a branch
git checkout -b feature/your-feature

# Make changes and test in n8n

# Commit and push
git add .
git commit -m "Description of changes"
git push origin feature/your-feature
```

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👥 Authors

- **PredictifyLabs Team** - *Initial work*

## 🙏 Acknowledgments

- [n8n](https://n8n.io) - Workflow automation platform
- [Google Gemini](https://ai.google.dev/) - AI language model
- [Twitter/X API](https://developer.twitter.com/) - Social media integration

## 📧 Support

For support, email support@predictifylabs.com or open an issue in the GitHub repository.

## 🔗 Links

- [n8n Documentation](https://docs.n8n.io)
- [Google Gemini API Docs](https://ai.google.dev/docs)
- [Twitter API Documentation](https://developer.twitter.com/en/docs)

---

Made with ❤️ by PredictifyLabs
