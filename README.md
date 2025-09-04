# N8N Internship Project

This repository contains workflows for **[n8n](https://n8n.io/)**, a workflow automation tool that allows you to connect and automate different apps and services without heavy coding. This project showcases various automation scenarios and best practices for workflow development using Docker deployment.

---

## Project Structure

```
n8n-internship-project/
├── workflows/
│   ├── autoReplyAndSaveAttachmentsEmail.json          # Automates email replies and attachment saving
│   ├── chatbotWorkshop.json                           # WhatsApp chatbot for workshop registrations
│   ├── embeddingFile.json                             # Embeds FAQ documents into vector stores
│   ├── kirimLoAOtomatis.json                          # Generates and sends PDF letters of acceptance
│   ├── pengirimanSertifikatOtomatis.json              # Sends participation certificates via email
│   ├── shareInfoSelesaiUpdate.json                    # Sends WhatsApp notifications for system updates
│   └── testingChatbot.json                            # Tests chatbot responses using vector stores
└── README.md                                          # Main project documentation (overview, installation, usage)
```

---

## Getting Started

### Prerequisites
- Docker & Docker Compose installed
- Basic understanding of APIs and webhooks
- Text editor or IDE for workflow development

### 1. Setup with Docker

This project uses Docker for consistent deployment and easy setup across different environments.

#### Quick Start with Docker Compose
```bash
# Clone the repository
git clone <your-repo-url>
cd n8n-internship-project

# Copy environment template
cp docker/.env.example docker/.env

# Edit the .env file with your configurations
nano docker/.env

# Start n8n with Docker Compose
cd docker
docker-compose up -d

# Access n8n interface at http://localhost:5678
```

#### Docker Compose Configuration
```yaml
version: '3.8'

services:
  n8n:
    image: n8nio/n8n
    restart: always
    ports:
      - "5678:5678"
    volumes:
      - n8n_data:/home/node/.n8n
    environment:
      - N8N_COMMUNITY_PACKAGES_ALLOW_TOOL_USAGE=true
      - N8N_EDITOR_BASE_URL={your_url}
      - N8N_WEBHOOK_URL={your_url}
      - N8N_DEFAULT_BINARY_DATA_MODE=filesystem
      - N8N_HOST={your_host}
      - N8N_PORT=5678
      - N8N_PROTOCOL=https

volumes:
  n8n_data:
```

#### Single Docker Command (Development)
```bash
# Run n8n with persistent data storage
docker run -it --rm \
  --name n8n \
  -p 5678:5678 \
  -e N8N_BASIC_AUTH_ACTIVE=true \
  -e N8N_BASIC_AUTH_USER=admin \
  -e N8N_BASIC_AUTH_PASSWORD=yourpassword \
  -e GENERIC_TIMEZONE=Asia/Jakarta \
  -v n8n_data:/home/node/.n8n \
  n8nio/n8n
```

### 2. Import Workflows

1. Open n8n in your browser (http://localhost:5678)
2. Login with your configured credentials
3. Click **"Import from File"** or use the **"+"** button
4. Select the workflow JSON files from the `workflows/` folder
5. Configure the required credentials and settings for each workflow

---

## Workflow Descriptions

### Workflow 1: Auto Reply and Save Attachments Email
**File:** `workflows/autoReplyAndSaveAttachmentsEmail.json`
- **Purpose:** This workflow automates handling incoming emails related to internship/PKL registrations and web service requests, including data storage and automatic replies.
- **Trigger:** The workflow is triggered by new email arrivals in Gmail every minute or new row additions in Google Sheets.
- **Nodes Used:**
  -  **Gmail Trigger**: Monitors incoming emails.
  -  **Set Content**: Sets data variables.
  -  **Customer Support (OpenAI)**: Categorizes email content.
  -  **Jenis Konten (Switch)**: Routes based on category.
  -  **Edit Fields (Set)**: Processes attachment data.
  -  **Merge**: Combines data streams.
  -  **Upload file (Google Drive)**: Saves attachments.
  -  **Code**: Executes custom JavaScript logic.
  -  **Append or update row in sheet (Google Sheets)**: Stores data in sheet.
  -  **If**: Handles conditional branching.
  -  **Google Sheets Trigger**: Monitors sheet changes.
  -  **Send a message (Gmail)**: Sends notification emails.
  -  **OpenAI Chat Model**: Provides AI responses.
  -  **Pinecone Vector Store**: Retrieves vector context.
  -  **Embeddings OpenAI**: Generates text embeddings.
  -  **Reply to a message (Gmail)**: Replies to emails.
  -  **AI Agent**: Generates AI-assisted replies.
  -  **Get a message (Gmail)**: Fetches email details.
  -  **Split Out**: Separates array data.
  -  **Sticky Note**: Adds documentation notes.
- **Use Case:** This workflow automates the handling of incoming emails for internship/PKL applications and web service client requests.
- **Features:**
  - **Auto-reply**: Generates automatic responses.
  - **Content categorization**: Classifies emails using AI.
  - **Attachment handling**: Downloads and stores files.
  - **Data storage**: Saves to Google Sheets and Drive.
  - **Notification system**: Sends daily summary emails.
  - **Vector search**: Uses Pinecone for relevant context.
  - **Conditional logic**: Branches based on conditions.
  - **AI integration**: Provides intelligent responses via OpenAI.

### Workflow 2: Chatbot for Workshop via WhatsApp
**File:** `workflows/chatbotWorkshop.json`
- **Purpose:** This workflow powers a WhatsApp chatbot for workshop registrations, using AI to handle inquiries and store data in Google Sheets.
- **Trigger:** The workflow is triggered by incoming WhatsApp messages via webhook.
- **Nodes Used:**
  - **Webhook**: Receives WhatsApp messages.
  - **Pinecone Vector Store**: Queries workshop data.
  - **Embeddings OpenAI**: Generates embeddings.
  - **AI Agent**: Handles user interactions.
  - **OpenAI Chat Model**: Provides AI responses.
  - **Append row in sheet (Google Sheets)**: Saves registration data.
  - **HTTP Request**: Sends WhatsApp replies.
  - **Simple Memory**: Maintains conversation context.
  - **Code**: Processes data logic.
  - **Google Sheets Trigger**: Monitors sheet changes.
  - **Create New Workshop Sheet**: Generates new sheets.
  - **Add Headers to New Sheet**: Sets up sheet structure.
  - **Get Master Workshops**: Retrieves workshop info.
  - **Check Workshop Exists**: Validates workshop data.
  - **Append to Workshop Sheet**: Updates participant lists.
  - **Update Workshop Count**: Tracks registrations.
  - **Workshop Not Found Error**: Handles errors.
- **Use Case:** This workflow is used to manage workshop registrations and provide information via WhatsApp chatbot.
- **Features:**
  - **WhatsApp integration**: Receives and sends messages.
  - **AI conversation**: Provides intelligent responses.
  - **Data storage**: Saves to Google Sheets.
  - **Workshop management**: Handles registrations and updates.
  - **Vector search**: Retrieves relevant workshop info.
  - **Memory persistence**: Maintains chat context.
  - **Error handling**: Manages invalid inputs.

### Workflow 3: Embedding File
**File:** `workflows/embeddingFile.json`
- **Purpose:** This workflow automates the embedding of FAQ documents from Google Docs into Pinecone vector stores for AI-powered retrieval.
- **Trigger:** The workflow is triggered by changes in specific Google Drive files every minute.
- **Nodes Used:**
  - **Google Drive Trigger**: Monitors file changes.
  - **Get a document (Google Docs)**: Retrieves document content.
  - **Split Out**: Separates content into chunks.
  - **Pinecone Vector Store**: Stores vectors in Pinecone.
  - **Embeddings OpenAI**: Generates text embeddings.
  - **Sticky Note**: Adds documentation notes.
- **Use Case:** This workflow is used to update vector databases with FAQ content for internship, web services, and workshop information.
- **Features:**
  - **Document monitoring**: Tracks Google Docs changes.
  - **Content splitting**: Breaks documents into manageable parts.
  - **Vector embedding**: Converts text to vectors.
  - **Multi-index support**: Handles multiple Pinecone indexes.
  - **Automated updates**: Refreshes vectors on file changes.

### Workflow 4: Kirim LoA Otomatis via Email
**File:** `workflows/kirimLoAOtomatis.json`
- **Purpose:** This workflow automates generating and sending PDF letters of acceptance for internship applicants via email.
- **Trigger:** The workflow is triggered manually to process pending applications.
- **Nodes Used:**
  - **Get rows - Belum Follow Up (Google Sheets)**: Retrieves pending applications.
  - **Process Data (Code)**: Formats data for letters.
  - **Loop Over Items**: Processes each application.
  - **HTML**: Generates letter templates.
  - **Convert to PDF**: Creates PDF files.
  - **Download PDF**: Retrieves generated PDFs.
  - **Reply to a message (Gmail)**: Sends emails with attachments.
  - **Update row in sheet (Google Sheets)**: Marks as processed.
- **Use Case:** This workflow is used to send official acceptance letters for internship placements.
- **Features:**
  - **Data retrieval**: Fetches pending applications.
  - **PDF generation**: Creates official letters.
  - **Email automation**: Sends with attachments.
  - **Status updates**: Tracks processing.
  - **Batch processing**: Handles multiple items.

### Workflow 5: Pengiriman Sertifikat Otomatis Partisipasi Workshop
**File:** `workflows/pengirimanSertifikatOtomatis.json`
- **Purpose:** This workflow automates sending participation certificates to workshop attendees based on attendance data in Google Sheets.
- **Trigger:** The workflow is triggered by changes in a specific Google Sheets document every minute.
- **Nodes Used:**
  - **Google Sheets Trigger**: Monitors sheet changes.
  - **Code**: Filters and deduplicates attendees.
  - **Search files and folders (Google Drive)**: Finds certificate files.
  - **Download file (Google Drive)**: Retrieves certificate attachments.
  - **Send a message (Gmail)**: Emails certificates to attendees.
- **Use Case:** This workflow is used to distribute certificates to participants who attended the "Bikin Aplikasi Tanpa Ngoding" workshop.
- **Features:**
  - Attendance filtering: Processes only present participants.
  - Deduplication: Removes duplicate names.
  - File search: Locates certificates in Drive.
  - Automated emailing: Sends personalized emails with attachments.
  - Real-time monitoring: Triggers on sheet updates.

### Workflow 6: Kirim Progress untuk Setiap Update (Commit)
**File:** `workflows/shareInfoSelesaiUpdate.json`
- **Purpose:** This workflow generates and sends WhatsApp notifications about system updates based on GitHub commits.
- **Trigger:** The workflow is triggered by incoming POST requests from GitHub webhooks.
- **Nodes Used:**
  - **Webhook**: Receives GitHub payloads.
  - **AI Agent**: Generates update descriptions.
  - **OpenAI Chat Model**: Provides AI language model.
  - **HTTP Request**: Sends WhatsApp messages.
- **Use Case:** This workflow is used to notify team members about code changes and system updates via WhatsApp.
- **Features:**
  - Webhook integration: Captures GitHub events.
  - AI summarization: Creates professional update reports.
  - WhatsApp messaging: Sends notifications.
  - Commit analysis: Processes file changes.

### Workflow 7: Testing Chatbot
**File:** `workflows/testingChatbot.json`
- **Purpose:** This workflow tests AI chatbot responses by generating answers from Pinecone vector stores and updating Google Sheets with results.
- **Trigger:** The workflow is triggered manually to execute the test process.
- **Nodes Used:**
  - **Get row(s) in sheet (Google Sheets)**: Retrieves test questions.
  - **Pinecone Vector Store**: Queries vector database.
  - **Embeddings OpenAI**: Generates embeddings.
  - **AI Agent**: Generates responses.
  - **OpenAI Chat Model**: Provides AI language model.
  - **Update row in sheet (Google Sheets)**: Saves generated answers.
- **Use Case:** This workflow is used to evaluate chatbot performance on internship, web services, and workshop FAQs.
- **Features:**
  - **Question retrieval**: Fetches test data from sheets.
  - **Vector search**: Queries relevant context.
  - **AI response generation**: Creates answers using agents.
  - **Result storage**: Updates sheets with outputs.
  - **Multi-category testing**: Covers multiple FAQ types.
    
### Required Credentials
Before running workflows, set up these credentials in n8n:

1. **Gmail OAuth2** - for sending and receiving emails, including replies and notifications.
2. **Google Sheets OAuth2 API** - for reading, updating, and appending data in Google Sheets.
3. **Google Drive OAuth2 API** - for uploading files, downloading attachments, and accessing Drive folders.
4. **Google Docs OAuth2 API** - for retrieving content from Google Docs documents.
5. **Pinecone API** - for vector database operations, including storing and querying embeddings.
6. **OpenAI API** - for AI language models, chat completions, and text embeddings.
7. **PDF.co API** - for converting HTML content to PDF files.
8. **Google Sheets Trigger OAuth2 API** - for triggering workflows on Google Sheets changes.
9. **Fonnte API** - for sending WhatsApp messages (used in chatbot and update notification workflows).

---

## Learning Resources

### Documentation
- [n8n Documentation](https://docs.n8n.io/)
- [Docker Documentation](https://docs.docker.com/)
- [n8n Community Forum](https://community.n8n.io/)

### Tutorials
- [n8n YouTube Channel](https://www.youtube.com/c/n8nio)
- [Docker Compose Guide](https://docs.docker.com/compose/)
- [Workflow Templates](https://n8n.io/workflows/)

---
