# 🤖 Jarvis — Personal AI Assistant

![n8n](https://img.shields.io/badge/Built%20with-n8n-EF6C00?style=for-the-badge&logo=n8n&logoColor=white)
![GPT-4o](https://img.shields.io/badge/Powered%20by-Groq-412991?style=for-the-badge&logo=openai&logoColor=white)
![Telegram](https://img.shields.io/badge/Interface-Telegram-26A5E4?style=for-the-badge&logo=telegram&logoColor=white)
![Google](https://img.shields.io/badge/Integrations-Google%20APIs-4285F4?style=for-the-badge&logo=google&logoColor=white)

> An autonomous, multi-agent AI assistant that manages your calendar, emails, and contacts — all through a single Telegram conversation.

---

## 🎯 What It Does

Send Jarvis a message like:

- _"Schedule a meeting with Rahul tomorrow at 3pm"_
- _"Reply to John's email professionally"_
- _"What's on my calendar this week?"_
- _"Add Priya's email to my contacts"_

Jarvis understands the request, routes it to the right agent, executes the task, and confirms — all autonomously.

---

## 🏗️ System Architecture

```
User (Telegram Text or Voice)
            │
            ▼
┌─────────────────────────┐
│   Ultimate Assistant    │  ← Orchestrator (GPT-4o + Memory)
│   Routes & Coordinates  │
└────────────┬────────────┘
             │
    ┌────────┼────────────┐
    ▼        ▼            ▼
┌───────┐ ┌───────┐ ┌──────────┐
│ 📅    │ │ 📧    │ │ 👤       │
│ Cal   │ │ Email │ │ Contact  │
│ Agent │ │ Agent │ │ Agent    │
└───────┘ └───────┘ └──────────┘
    │          │           │
Google     Gmail      Google
Calendar   OAuth2     Sheets
```

Each sub-agent is a **standalone n8n workflow** with its own tools and system prompt — triggered by the orchestrator via internal workflow calls.

---

## ⚙️ Tech Stack

| Layer | Technology |
|---|---|
| Workflow Automation | n8n (self-hosted) |
| AI Model | Groq |
| Voice Transcription | OpenAI Whisper |
| User Interface | Telegram Bot |
| Email | Gmail API (OAuth2) |
| Calendar | Google Calendar API (OAuth2) |
| Contacts | Google Sheets API (OAuth2) |
| Web Search | Tavily API |

---

## 🤖 Agent Breakdown

### 1. Ultimate Assistant (Orchestrator)
- Entry point for all user requests via Telegram
- Accepts **text and voice messages** (voice is auto-transcribed)
- Maintains **conversation memory** across messages
- Routes tasks to the correct sub-agent
- Handles general queries and web search (Tavily) directly

### 2. Calendar Agent
- Creates, updates, and deletes Google Calendar events
- **Conflict detection** — checks availability before scheduling
- Supports attendees and event invites
- Defaults to 1-hour duration if not specified
- Suggests alternative times when conflicts exist

### 3. Email Agent
- Sends, drafts, and replies to Gmail messages
- **Priority classification** — Critical / High / Normal / Low
- Auto-labels and organises emails
- Extracts tasks and meeting details from email content
- Always drafts first, sends only after confirmation

### 4. Contact Agent
- Looks up contacts by name → returns email address
- Stores new contacts (name + email) to Google Sheets
- Feeds contact data to Calendar and Email agents for automation

---

## 🔄 Example Workflows

**Scheduling from an email:**
1. User: _"Check my emails from Rahul and schedule whatever he requested"_
2. Email Agent reads emails → extracts meeting request
3. Calendar Agent checks availability → creates event
4. Jarvis confirms with event details

**Voice command:**
1. User sends a voice note in Telegram
2. Whisper transcribes audio to text
3. Request is processed like a normal message

---

![Jarvis Demo](demo.gif)

---

## 📁 Repository Structure

```
jarvis-n8n-workflows/
├── Jarvis_Ultimate_Assistant.json        # Main orchestrator
├── Jarvis_Calendar_Agent.json            # Google Calendar agent
├── Jarvis_Email_Agent.json               # Gmail agent
├── Jarvis_Contact_Agent_GoogleSheets.json # Contacts agent
└── README.md
```

---



## 🚀 Setup & Installation

### Prerequisites
- n8n instance (self-hosted or cloud)
- OpenAI API key (GPT-4o + Whisper)
- Telegram Bot token
- Google OAuth2 credentials (Calendar, Gmail, Sheets)
- Tavily API key

### Steps

**1. Import workflows**
In your n8n instance: Settings → Import Workflow → import each JSON file

**2. Re-link credentials**
Each workflow needs credentials connected to your own accounts:
- OpenAI → Calendar Agent, Email Agent, Contact Agent
- Google Calendar OAuth2 → Calendar Agent
- Gmail OAuth2 → Email Agent
- Google Sheets OAuth2 → Contact Agent
- Telegram → Ultimate Assistant

**3. Configure Contact Agent**
Open `Jarvis_Contact_Agent_GoogleSheets.json` and set your Google Sheet ID in the **Get Contact** and **Add Contact** nodes. Your sheet should have columns: `name` | `email`

**4. Activate in order**
Activate sub-agents first, then activate the Ultimate Assistant last.

**5. Start chatting**
Send any message to your Telegram bot and Jarvis will respond.

---

## 📌 Notes

- Timezone is set to `Asia/Kolkata` — update in each agent's system prompt if needed
- All agents confirm every action with a professional timestamp signature
- The Email Agent always drafts before sending — it will never send without confirmation

---

## 👨‍💻 Built By

**Tejas MN** — Automation & AI Systems  
Building intelligent workflow systems with n8n, LLMs, and Google APIs.

---

> ⭐ If you found this useful, feel free to star the repo!
