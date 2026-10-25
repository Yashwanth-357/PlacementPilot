# PlacementPilot

**Turn your resume into a role-specific skills assessment, learning roadmap, and interview practice plan.**

[![License](https://img.shields.io/badge/license-TBD-lightgrey.svg)](#license)
[![Build](https://img.shields.io/badge/build-not_configured-lightgrey.svg)](#contributing)
[![Version](https://img.shields.io/badge/version-TBD-blue.svg)](#)

## Overview

PlacementPilot is a web application that helps job seekers understand how their resume aligns with a target career role. Users can paste resume text or upload a PDF or DOCX file, choose a role, and receive an AI-generated summary of relevant skills, skill gaps, a step-by-step learning roadmap, and tailored interview questions.

The application provides account sign-up and sign-in, a dashboard for running analyses, and a history page for revisiting successful evaluations. It uses a Flask server-rendered interface, SQLAlchemy for persistence, and Google's Gemini API for resume analysis. The application is intended as a career-planning aid; generated guidance should be reviewed and validated by the user.

## Key Features

- **Role-specific resume analysis:** Compare resume content with a target career role.
- **Resume input options:** Paste text or upload a PDF or DOCX document.
- **Skills and gap identification:** Review detected relevant skills and areas to develop.
- **Actionable learning roadmap:** Get a phased plan focused on identified gaps.
- **Interview preparation:** Generate role- and background-specific interview questions.
- **Accounts and saved history:** Sign up, sign in, and revisit successful analysis results.
- **SQLite by default:** Start locally without provisioning a separate database; configure another SQLAlchemy-supported database with `DATABASE_URL` if needed.

## Tech Stack

| Area | Technology |
| --- | --- |
| Web framework and UI | Python, Flask, Jinja templates, HTML, CSS |
| Persistence | SQLAlchemy ORM; SQLite by default; PyMySQL included for MySQL |
| AI analysis | Google Gemini 2.5 Flash Generative Language API |
| Document parsing | PyPDF2, python-docx |
| Configuration | python-dotenv |

## Architecture and Project Structure

```text
PlacementPilot/
├── ai.py                  # Gemini API integration and analysis prompt
├── app.py                 # Flask application, routes, and request handling
├── db.py                  # Database URL configuration and SQLAlchemy setup
├── models.py              # User and report database models
├── requirements.txt       # Python dependencies
├── .env.example           # Example local environment configuration
└── templates/
    ├── base.html          # Shared layout and navigation
    ├── dashboard.html     # Resume input and analysis results
    ├── history.html       # Saved report history
    ├── login.html         # Sign-in form
    ├── signup.html        # Account creation form
    └── style.css          # Application styles
```

At startup, `app.py` creates the database tables from the SQLAlchemy models. The dashboard extracts text from an uploaded document when provided, sends the resume text and target role to the Gemini integration, then saves successful analysis results for the signed-in user.

## Getting Started

### Prerequisites

- Python 3.10 or newer
- A Google Gemini API key for resume analysis
- Git

SQLite is used automatically when `DATABASE_URL` is not set. No separate database server is needed for local development.

### 1. Clone the repository

```bash
git clone https://github.com/Yashwanth-357/PlacementPilot.git
cd PlacementPilot
```

### 2. Create and activate a virtual environment

```bash
python -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell, activate it with:

```powershell
.\.venv\Scripts\Activate.ps1
```

### 3. Install dependencies

```bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

### 4. Configure environment variables

Copy the example file and add your Gemini API key:

```bash
cp .env.example .env
```

On Windows PowerShell:

```powershell
Copy-Item .env.example .env
```

Edit `.env` and set `GEMINI_API_KEY`. You can leave `DATABASE_URL` unset to use the local SQLite database.

### 5. Run the application

```bash
python app.py
```

Open [http://127.0.0.1:5000](http://127.0.0.1:5000) in your browser. The application creates its database tables on startup. The default SQLite database file is `placement_pilot.db` in the project directory.

> **Development note:** The current entry point starts Flask with debug mode enabled, and the session secret is set directly in `app.py`. Do not expose this development server to the public internet. Before deployment, move the secret to secure environment-based configuration and use a production WSGI server with debug mode disabled.

## Environment Variables

| Variable | Required | Description |
| --- | --- | --- |
| `GEMINI_API_KEY` | Yes, for AI analysis | API key used to call the Gemini 2.5 Flash generation endpoint. |
| `DATABASE_URL` | No | SQLAlchemy database connection URL. If omitted, the app uses `sqlite:///./placement_pilot.db`. For MySQL, install/configure a compatible URL such as `mysql+pymysql://<user>:<password>@<host>:<port>/<database>`. |
| `OPENAI_API_KEY` | No | Present in the example configuration, but the current analysis implementation calls the Gemini endpoint; this variable alone does not configure OpenAI analysis. |

Keep `.env` private and do not commit API keys or database credentials.

## Web Routes

PlacementPilot currently serves HTML pages and form submissions; it does not expose a JSON REST API or Socket.IO events.

| Method | Route | Description |
| --- | --- | --- |
| `GET` | `/` | Redirects to the dashboard for a signed-in user, otherwise to sign-in. |
| `GET`, `POST` | `/signup` | Displays the account creation form and processes sign-up submissions. |
| `GET`, `POST` | `/login` | Displays the sign-in form and processes login submissions. |
| `GET`, `POST` | `/dashboard` | Shows the resume analyzer; accepts resume text or a PDF/DOCX upload and a target role. |
| `GET` | `/history` | Displays saved analysis reports for the signed-in user. |
| `GET` | `/logout` | Clears the current session and redirects to sign-in. |

## Contributing

Contributions are welcome. To propose a change:

1. Fork the repository and create a focused feature branch.
2. Make the change and verify it locally.
3. Open a pull request describing the change and any setup or testing steps.

There is no automated test command currently defined in the repository. Include relevant test coverage when adding or changing behavior.

## License

No license file is currently included. The license badge above is a placeholder; add a `LICENSE` file and update this section before redistributing the project.
