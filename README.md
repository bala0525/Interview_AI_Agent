# Interview AI Agent

An AI-powered interview copilot designed to help candidates during live interviews. It captures interview questions, understands the context, and generates clear, relevant answers using large language models.

The goal is simple: help candidates prepare better and get useful answer suggestions while an interview is happening.

## Overview

Interview AI Agent combines a React-based frontend with a FastAPI backend. Candidates can create interview sessions, provide company and job-description details, manage their resumes and supporting documents, and choose an AI model for generating answers.

During a live session, the application uses browser speech recognition to convert spoken questions into text. The text is sent to the backend through a WebSocket connection, where the selected AI provider generates a response and sends it back to the frontend in real time. Candidates can also enter questions manually.

## Key Features

### 1. Live Interview Copilot
- Captures spoken questions using browser speech recognition.
- Converts recognized speech into text.
- Sends questions to the backend through WebSockets.
- Generates AI-powered answers in real time.
- Supports manual question input.
- Displays the generated answer in the interview workspace.
- Includes a floating Picture-in-Picture window to keep the copilot visible while using another window.

### 2. Multiple AI Model Providers

The backend includes integrations for three AI providers:

| Provider | Configured Models |
|---|---|
| Google Gemini | Gemini 3.1 Flash Lite, Gemini 3.5 Flash |
| OpenAI | GPT-4.1, GPT-4.1 Mini, GPT-5.5, GPT-5.5 Mini |
| Anthropic | Claude 4.5 Haiku |

The selected model is used to generate answers based on the interview question and the available session context. The model-to-provider mapping is maintained in the backend.

### 3. Interview Session Management

Candidates can create and manage interview sessions by providing:
- Company name
- Job description
- Interview session type
- Preferred AI model
- Response language
- Resume selection
- Supporting document selection

Sessions are stored in the database and can be retrieved later through the session management APIs.

The current backend also checks for an active subscription. Users without an active subscription are limited to four interview sessions.

### 4. Resume Management

The resume workspace supports:
- Uploading PDF resumes
- Creating resume profiles manually
- Editing resume details
- Managing multiple resumes
- Renaming and deleting resumes
- Viewing and downloading uploaded files

The backend uses `pypdf` to extract text from uploaded PDF resumes and attempts to identify email addresses and phone numbers.

### 5. Supporting Document Management

Candidates can add supporting information in three ways:
- Upload a document
- Extract webpage content from a URL
- Create a document manually

Website content is extracted using Requests and BeautifulSoup. Scraped and manually entered text can be stored and retrieved as supporting information.

### 6. User Account Management

The application includes basic user registration and login functionality. User records and interview-related information are stored in the database.

### 7. Interview Workspace

The interview workspace brings together:
- Live question transcription
- AI-generated answers
- Manual question entry
- Interview session information
- Browser/tab capture controls
- Picture-in-Picture support

The Picture-in-Picture feature uses the browser's Document Picture-in-Picture API. Browser support is required for this functionality.

## How It Works

The application follows this workflow:

1. **Create an account:** The candidate registers or logs in.
2. **Prepare the session:** The candidate enters the company name and job description.
3. **Add context:** The candidate selects a resume and supporting documents.
4. **Choose an AI model:** The candidate selects an available model and response language.
5. **Start the interview:** The frontend creates an interview session through the FastAPI REST API.
6. **Capture the question:** Browser speech recognition converts spoken questions into text. Manual questions can also be entered.
7. **Send the question:** The frontend sends the question through the session's WebSocket connection.
8. **Generate an answer:** The backend prepares the available company, job-description, language, resume, and document context and sends the prompt to the selected AI provider.
9. **Display the response:** The generated answer is returned to the frontend and displayed in the interview workspace.
10. **End the session:** The session status is updated to `ended`.

## System Architecture

```mermaid
flowchart TD
    A[Candidate] --> B[React Frontend]
    B --> C[Interview Session Setup]
    C --> D[FastAPI REST API]
    D --> E[(Database)]
    B <--> F[WebSocket Connection]
    F --> G[FastAPI Interview WebSocket]
    G --> H[LLM Service]
    H --> I[OpenAI]
    H --> J[Google Gemini]
    H --> K[Anthropic Claude]
    I --> L[Generated Answer]
    J --> L
    K --> L
    L --> G
    G --> F
    F --> B
```

### Backend Request Flow

The backend uses a separate LLM service to select the correct provider based on the model chosen by the candidate.

The WebSocket handler receives the question, loads the available session context, calls the LLM service, and returns the generated answer to the frontend.

This keeps the frontend responsible for the interview experience while the backend handles session data, prompt construction, provider selection, and AI responses.

## Technology Stack

| Layer | Technologies |
|---|---|
| Frontend | React, JavaScript, CSS |
| UI Libraries | React Icons, Bootstrap, Framer Motion |
| Backend | Python, FastAPI |
| API Communication | REST APIs, WebSockets |
| Database | SQLAlchemy, PostgreSQL-compatible database |
| AI Integration | OpenAI, Google Gemini, Anthropic Claude |
| Resume Parsing | pypdf |
| Web Scraping | Requests, BeautifulSoup |
| Server | Uvicorn |
| Browser Features | Web Speech Recognition, Document Picture-in-Picture, Screen Capture API |

## Project Structure

```text
Interview_AI_Agent/
│
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── config.py
│   │   ├── database.py
│   │   │
│   │   ├── models/
│   │   │   ├── user.py
│   │   │   ├── interview.py
│   │   │   ├── resume.py
│   │   │   ├── document.py
│   │   │   └── subscription.py
│   │   │
│   │   ├── routes/
│   │   │   ├── users.py
│   │   │   ├── interviews.py
│   │   │   ├── copilot.py
│   │   │   ├── resumes.py
│   │   │   ├── documents.py
│   │   │   └── billing.py
│   │   │
│   │   ├── schemas/
│   │   ├── services/
│   │   │   ├── llm_service.py
│   │   │   ├── openai_service.py
│   │   │   ├── gemini_service.py
│   │   │   ├── claude_service.py
│   │   │   ├── pdf_service.py
│   │   │   └── scraper_service.py
│   │   │
│   │   └── websocket/
│   │       └── interview_socket.py
│   │
│   └── requirements.txt
│
└── frontend/
    ├── public/
    ├── src/
    │   ├── components/
    │   │   └── InterviewDashboard/
    │   ├── pages/
    │   │   └── InterviewDashboard.js
    │   ├── services/
    │   │   └── interviewApi.js
    │   ├── App.js
    │   └── index.js
    │
    └── package.json
```

## Getting Started

### Prerequisites

Make sure the following are installed:

- Python 3.10 or later
- Node.js and npm
- PostgreSQL
- API key for the AI provider you plan to use

### 1. Clone the Repository

```bash
git clone https://github.com/bala0525/Interview_AI_Agent.git
cd Interview_AI_Agent
```

### 2. Configure the Backend

Navigate to the backend directory:

```bash
cd backend
```

Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

For Windows:

```powershell
.venv\Scripts\activate
```

Install the dependencies:

```bash
pip install -r requirements.txt
```

Create a `.env` file inside the `backend` directory:

```env
DATABASE_URL=postgresql+psycopg2://username:password@localhost:5432/interview_agent

OPENAI_API_KEY=your_openai_api_key
GEMINI_API_KEY=your_gemini_api_key
ANTHROPIC_API_KEY=your_anthropic_api_key

CORS_ORIGINS=http://localhost:3000
```

Replace the database credentials and API keys with your own values. The database must exist before starting the backend. Only the API key for the provider you intend to use is needed.

Start the backend:

```bash
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000
```

Backend API:

`http://localhost:8000`

Interactive API documentation:

`http://localhost:8000/docs`

Health check:

`http://localhost:8000/health`

The application creates its SQLAlchemy tables when the backend starts.

### 3. Configure the Frontend

Open a separate terminal and navigate to the frontend directory:

```bash
cd frontend
```

Install the frontend dependencies:

```bash
npm install
```

Start the React application:

```bash
npm start
```

Frontend:

`http://localhost:3000`

The current frontend API and WebSocket connections are configured for a backend running locally on port `8000`.

## Important API Endpoints

### User APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/users/register` | Register a user |
| POST | `/api/users/login` | Log in a user |

### Interview APIs

| Method | Endpoint | Purpose |
|---|---|---|
| POST | `/api/interviews/` | Create an interview session |
| GET | `/api/interviews/user/{user_id}` | Get a user's sessions |
| GET | `/api/interviews/{session_id}` | Get session details |
| PUT | `/api/interviews/{session_id}` | Update a session |
| DELETE | `/api/interviews/{session_id}` | Delete a session |
| WS | `/ws/interview/{session_id}` | Connect to the live interview copilot |

### Resume APIs

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/resumes/user/{user_id}` | Get user resumes |
| POST | `/api/resumes/upload` | Upload a resume |
| POST | `/api/resumes/` | Create a resume |
| PUT | `/api/resumes/{resume_id}` | Update a resume |
| DELETE | `/api/resumes/{resume_id}` | Delete a resume |

### Document APIs

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/documents/user/{user_id}` | Get user documents |
| POST | `/api/documents/upload` | Upload a document |
| POST | `/api/documents/scrape` | Extract webpage content |
| POST | `/api/documents/manual` | Create a manual document |
| PUT | `/api/documents/{doc_id}` | Rename a document |
| DELETE | `/api/documents/{doc_id}` | Delete a document |

## Real-Time WebSocket Communication

The interview copilot uses a WebSocket connection for live question-and-answer communication.

Connection:

```text
ws://localhost:8000/ws/interview/{session_id}
```

The frontend sends different message types depending on the source of the question.

**Spoken question:**

```json
{
  "type": "transcript",
  "text": "Can you explain your experience with Python?"
}
```

**Manual question:**

```json
{
  "type": "manual_question",
  "question": "What is the difference between a list and a tuple?"
}
```

**End interview:**

```json
{
  "type": "end"
}
```

The backend responds with events such as:

- `connected` – confirms the WebSocket session.
- `ai_answer` – contains the generated answer and selected model.
- `session_ended` – confirms that the interview session has ended.
- `error` – reports an error.

## Current Implementation Notes

A few areas should be considered before using this as a production application:

- The frontend currently uses local development URLs for REST and WebSocket connections. Deployment requires configurable API URLs and secure WebSockets (`wss://`) where HTTPS is used.
- The current login implementation compares stored passwords directly. Password hashing, proper authentication tokens, and authorization checks should be implemented before production use.
- The frontend sends selected resume and document IDs during session creation, but those fields are not currently declared in the `InterviewSessionCreate` Pydantic schema. The backend schema and request handling need to be aligned for reliable context loading.
- The browser speech-recognition language is currently configured as `en-US`, even though the session interface offers other language choices.
- Transcript-related settings exist in the session model, but the live WebSocket handler does not currently persist the transcript.
- Uploaded document files are stored, but their text is not extracted into the document content field by the current upload flow. Scraped and manually entered document content is stored as text.

## Project Goal

Interview AI Agent brings interview preparation, resume context, AI model integration, and real-time answer generation into one workspace.

It is built around a straightforward idea: make useful AI assistance available during an interview without making the candidate switch between multiple tools.

---

**Repository:** https://github.com/bala0525/Interview_AI_Agent
