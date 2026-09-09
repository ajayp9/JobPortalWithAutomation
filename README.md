# 🚀 NicheNest — Job Portal with Automation

NicheNest is a full-stack **Job Portal with Automation** designed to connect job seekers and employers on a single platform.

The platform allows job seekers to discover, search, filter, and apply for jobs, while employers can post jobs and manage the applications they receive.

The project also includes:
- Automated job-matching email notifications based on job niches and user preferences
- JWT-based authentication and role-based authorization
- Resume upload and storage using Cloudinary
- An AI-powered job chatbot
- A dedicated registration-email service
- A Node.js API Gateway between the frontend and backend services

---

## 📌 Problem Statement

Traditional job portals require job seekers to repeatedly search for suitable jobs. Employers also need a convenient way to publish jobs and manage candidate applications.

This project provides a centralized platform where:

- **Job seekers** can find and apply for suitable jobs.
- **Employers** can publish jobs and manage applications.
- The system can automatically identify users whose selected niches match newly posted jobs.
- Matching job seekers receive an automated email notification.
- An AI chatbot helps users with job, resume, interview, career, and placement-related questions.

---

# ✨ Key Features

## 👨‍💼 Job Seeker Features

- User registration and login
- JWT-based authentication
- Job seeker role-based access
- Select job niches/preferences during registration
- Resume upload
- Resume storage through Cloudinary
- Search jobs
- Filter jobs by:
  - City/location
  - Job niche
  - Search keyword
- View individual job details
- Apply for a job
- Upload a resume while applying
- Use an existing resume when applying
- Prevent duplicate applications
- View submitted applications
- Delete own applications
- Update profile
- Change password
- Receive automated matching-job email notifications
- Use the AI job chatbot

---

## 🏢 Employer Features

- Employer registration and login
- JWT-based authentication
- Employer role-based access
- Post jobs
- View own posted jobs
- Delete own posted jobs
- View applications received for their jobs
- Manage/hide received applications
- Manage profile information

---

## 🤖 AI Chatbot

The project contains a separate Python FastAPI chatbot service.

The chatbot:

- Provides job-related assistance
- Fetches current jobs from the Spring Boot backend
- Uses Groq's LLM API
- Uses the `llama-3.3-70b-versatile` model in the current implementation
- Can answer questions about:
  - Jobs
  - Resume
  - Interviews
  - Career guidance
  - Placement preparation

For job-related questions, the chatbot is instructed to use the available job data.

---

## 📧 Automated Job Notification

One of the main automation features is automatic job notification.

### Flow

```text
Employer posts a job
        ↓
Job is saved in MySQL
        ↓
Scheduled automation checks new jobs
        ↓
Job niche is identified
        ↓
Users with matching niches are found
        ↓
Email notification is sent
        ↓
Job is marked as newsletter sent
```

The Spring Boot application contains a `NewsletterScheduler` that runs on a scheduled cron and processes jobs whose newsletter has not yet been sent.

Users are matched using their selected niches.

---

# 🏗️ System Architecture

```text
                         ┌─────────────────────┐
                         │   React Frontend    │
                         │   Vite + Redux      │
                         └──────────┬──────────┘
                                    │
                                    │ HTTP / HTTPS
                                    ▼
                         ┌─────────────────────┐
                         │   Node.js Gateway   │
                         │      Express        │
                         └───────┬───────┬─────┘
                                 │       │
                  JSON / Upload  │       │ Chat
                                 │       │
                                 ▼       ▼
                    ┌────────────────┐  ┌────────────────┐
                    │ Spring Boot    │  │ FastAPI        │
                    │ Main Backend   │  │ AI Chatbot     │
                    └───────┬────────┘  └───────┬────────┘
                            │                   │
                 ┌──────────┼──────────┐        │
                 │          │          │        │
                 ▼          ▼          ▼        │
              MySQL    Cloudinary    Email      │
                 │          │          │        │
                 │          │          ▼        │
                 │          │    .NET Email     │
                 │          │     Service        │
                 │          │                   │
                 └──────────┴───────────────────┘
```

---

# 🧩 Architecture Components

## 1. React Frontend

The frontend provides the user interface for both job seekers and employers.

Technology:

- React.js
- Vite
- Redux Toolkit
- React Router
- Axios
- React Icons
- React Toastify
- React Markdown
- Remark GFM

Main frontend pages/components include:

```text
Home
Login
Register
Jobs
Dashboard
PostApplication
JobPost
MyJobs
MyApplications
MyProfile
UpdateProfile
UpdatePassword
Applications
ChatBot
```

---

## 2. Node.js API Gateway

The Node.js gateway acts as an intermediary between the React frontend and backend services.

Technology:

- Node.js
- Express.js
- Axios
- Multer
- Form-Data
- CORS
- Morgan
- dotenv

### Gateway responsibilities

- Receive requests from React
- Forward JSON requests to Spring Boot
- Forward multipart/file-upload requests to Spring Boot
- Forward chatbot requests to FastAPI
- Forward authorization headers
- Handle backend responses/errors
- Provide a single backend entry point for the frontend

### Request routing

```text
React
  │
  ▼
Node Gateway
  │
  ├── /api/* ───────────────► Spring Boot
  │
  └── /chat ────────────────► FastAPI
```

---

# ☕ 3. Spring Boot Backend

The Spring Boot application is the main backend service.

Technology:

- Java 21
- Spring Boot 3.5.x
- Spring Web
- Spring Data JPA
- Hibernate
- Spring Security
- JWT
- Bean Validation
- Spring Actuator
- Spring Mail
- Quartz Scheduler
- Cloudinary
- OpenCSV
- Swagger / OpenAPI
- MySQL

The backend follows a layered architecture:

```text
Controller
     ↓
Service Interface
     ↓
Service Implementation
     ↓
Repository
     ↓
MySQL
```

---

# 🔐 Authentication & Authorization

The application uses:

- Spring Security
- JWT
- BCrypt password encoding
- Role-based authorization

Supported roles:

```text
JOB_SEEKER
EMPLOYER
```

### Authentication flow

```text
User Login
    ↓
AuthController
    ↓
AuthenticationManager
    ↓
AuthenticationProvider
    ↓
UserDetailsService
    ↓
Password Verification
    ↓
JWT Generated
    ↓
JWT returned to Frontend
```

For protected requests:

```text
Frontend
   ↓
Authorization: Bearer <JWT>
   ↓
Node Gateway
   ↓
Spring Boot
   ↓
JwtAuthenticationFilter
   ↓
JWT Validation
   ↓
Spring Security
   ↓
Controller
```

Role-based authorization is implemented using Spring Security, including `@PreAuthorize`.

Example:

```java
@PreAuthorize("hasRole('JOB_SEEKER')")
```

and:

```java
@PreAuthorize("hasRole('EMPLOYER')")
```

---

# 👤 User Management

The user module handles:

- Registration
- Login
- Profile
- Profile update
- Password change
- Logout
- User lookup
- User roles
- User niches
- User resumes

### Registration logic

For a `JOB_SEEKER`:

```text
Registration
    ↓
Validate user details
    ↓
Validate 3 niches
    ↓
Validate resume
    ↓
Encode password using BCrypt
    ↓
Upload resume to Cloudinary
    ↓
Save user
    ↓
Save user niches
    ↓
Call Registration Email Service
    ↓
Registration completed
```

For an `EMPLOYER`:

- Resume is not required.
- Job niches are not required.

---

# 💼 Job Management

The Job Module allows employers to create and manage jobs.

### Employer can:

- Post a job
- View their own jobs
- Delete their own jobs

### Public job functionality:

- Get all jobs
- Get job by ID
- Search jobs
- Filter by location
- Filter by niche
- Search by keyword

### Job search

The backend supports multiple filters:

```text
City
Niche
Search Keyword
```

Search can match fields such as:

```text
Job Title
Company Name
Introduction
Job Type
```

---

# 📝 Application Management

The Application Module manages the complete job application lifecycle.

### Application APIs

| Method | Endpoint | Description |
|---|---|---|
| POST | `/api/applications/post/{jobId}` | Apply for a job |
| GET | `/api/applications/jobseeker/getall` | Get job seeker's applications |
| GET | `/api/applications/employer/getall` | Get employer's received applications |
| DELETE | `/api/applications/delete/{applicationId}` | Delete/hide an application |

---

## Apply-for-Job Flow

```text
Job Seeker
    ↓
Click Apply
    ↓
React Application Form
    ↓
Node.js Gateway
    ↓
ApplicationController
    ↓
ApplicationService
    ↓
ApplicationServiceImpl
    ↓
Validate User
    ↓
Check Job
    ↓
Check Duplicate Application
    ↓
Handle Resume
    ↓
Create Application
    ↓
ApplicationRepository
    ↓
MySQL
```

### Duplicate application prevention

Before saving an application, the backend checks:

```text
User ID + Job ID
```

If the same user has already applied for the same job:

```text
"You have already applied for this job"
```

is returned and a duplicate application is not created.

---

# 📄 Resume Management

Resume files are handled using Cloudinary.

### Resume upload flow

```text
React
   ↓
MultipartFile
   ↓
Node Gateway
   ↓
Spring Boot
   ↓
Cloudinary
   ↓
Secure URL + Public ID
   ↓
MySQL stores resume reference
```

### Resume validation

The backend validates:

- Resume must not be empty
- Maximum size: **5 MB**
- Allowed extensions:
  - PDF
  - DOC
  - DOCX

Cloudinary stores the actual file, while the application stores the file reference such as:

```text
resumeUrl
publicId
```

---

# 🗃️ Database

The application uses **MySQL** with Spring Data JPA/Hibernate.

Important entities include:

```text
User
Job
Application
UserResume
UserNiche
Niche
JobPersonalWebsite
ApplicationJobSeekerInfo
ApplicationJobSeekerResume
ApplicationEmployerInfo
ApplicationJobInfo
ApplicationDeletedBy
```

### Simplified relationship

```text
User
 ├── UserNiche
 ├── UserResume
 ├── Job
 └── Application

Job
 ├── postedBy → User
 ├── JobPersonalWebsite
 └── Application

Application
 ├── JobSeekerInfo
 ├── JobSeekerResume
 ├── EmployerInfo
 ├── JobInfo
 └── DeletedBy

Niche
 └── UserNiche
```

---

# 🧑‍💻 Application Deletion / Visibility

Application deletion is role-aware.

### Job seeker

If the logged-in job seeker owns the application:

```text
Application is deleted
```

### Employer

If the employer removes an application from their view:

```text
deletedBy.employer = true
```

This allows the application to remain available in the system while being hidden from the employer.

Authorization checks are performed before deletion so users cannot modify applications that do not belong to them.

---

# 📬 Email Services

The project uses two email-related mechanisms.

## Spring Boot Email

Spring Boot uses `JavaMailSender` for automated job notification emails.

The newsletter scheduler sends matching job notifications to users.

## Registration Email Microservice

A separate .NET service handles registration confirmation emails.

Technology:

- ASP.NET Core
- .NET 10
- MailKit
- SMTP

Endpoint:

```text
POST /api/registration-email/send
```

### Registration email flow

```text
React
   ↓
Spring Boot Registration
   ↓
User saved successfully
   ↓
RegistrationEmailClient
   ↓
.NET Email Service
   ↓
MailKit
   ↓
SMTP
   ↓
User's Email
```

---

# 🤖 Chatbot Architecture

The chatbot is implemented as a separate FastAPI service.

```text
React Chatbot
      ↓
Node Gateway
      ↓
FastAPI
      ↓
JobService
      ↓
Spring Boot / Jobs API
      ↓
Job Data
      ↓
Groq LLM
      ↓
Chat Response
      ↓
React
```

### Chatbot components

```text
chatbot/
├── app.py
├── requirements.txt
├── models/
│   └── chat_model.py
├── routes/
│   └── chat.py
└── services/
    ├── ai_service.py
    ├── job_service.py
    └── response_service.py
```

---

# ⏰ Automation

The Spring Boot project uses scheduled processing.

Current scheduler:

```java
@Scheduled(cron = "0 */1 * * * *")
```

The scheduler:

1. Finds jobs where newsletter has not been sent.
2. Gets users whose niches match the job niche.
3. Sends an email to each matching user.
4. Marks the job as processed.

Simplified logic:

```text
New Job
   ↓
newslettersSent = false
   ↓
Scheduler
   ↓
Find matching users
   ↓
Send email
   ↓
newslettersSent = true
```

---

# 📡 Main API Endpoints

## Authentication

```text
POST /api/auth/register
POST /api/auth/register-json
POST /api/auth/login
```

## Users

```text
GET  /api/users/profile
PUT  /api/users/profile
PUT  /api/users/change-password
POST /api/users/logout
GET  /api/users
GET  /api/users/{id}
```

## Jobs

```text
POST   /api/jobs/post
GET    /api/jobs/getall
GET    /api/jobs/get/{id}
GET    /api/jobs/getmyjobs
DELETE /api/jobs/delete/{id}
```

## Applications

```text
POST   /api/applications/post/{jobId}
GET    /api/applications/jobseeker/getall
GET    /api/applications/employer/getall
DELETE /api/applications/delete/{applicationId}
```

## Chatbot

```text
POST /chat
```

## Registration Email Service

```text
POST /api/registration-email/send
```

---

# 📁 Repository Structure

The project is organized into separate services.

```text
NicheNest/
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── store/
│   │   │   └── slices/
│   │   ├── App.jsx
│   │   └── main.jsx
│   ├── package.json
│   └── vite.config.js
│
├── gateway/
│   ├── config/
│   ├── middleware/
│   ├── routes/
│   │   ├── chatbot.js
│   │   ├── springJson.js
│   │   └── springUpload.js
│   ├── utils/
│   ├── server.js
│   └── package.json
│
├── spring_boot_backend_template/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/backend/
│   │   │   │   ├── automation/
│   │   │   │   ├── config/
│   │   │   │   ├── controller/
│   │   │   │   ├── dto/
│   │   │   │   ├── entities/
│   │   │   │   ├── exception/
│   │   │   │   ├── repository/
│   │   │   │   ├── security/
│   │   │   │   └── service/
│   │   │   └── resources/
│   │   └── test/
│   └── pom.xml
│
├── chatbot/
│   ├── models/
│   ├── routes/
│   ├── services/
│   ├── app.py
│   └── requirements.txt
│
└── RegistrationEmailService/
    ├── Controllers/
    ├── DTOs/
    ├── Services/
    ├── Program.cs
    ├── appsettings.json
    └── RegistrationEmailService.csproj
```

---

# 🛠️ Technology Stack

## Frontend

- React.js 18
- Vite
- Redux Toolkit
- React Router
- Axios
- React Icons
- React Toastify
- React Markdown

## Backend

- Java 21
- Spring Boot
- Spring MVC
- Spring Data JPA
- Hibernate
- Spring Security
- JWT
- Bean Validation
- Spring Actuator
- Quartz Scheduler
- Spring Mail
- Swagger/OpenAPI

## Gateway

- Node.js
- Express.js
- Axios
- Multer
- Form-Data
- CORS
- Morgan
- dotenv

## Chatbot

- Python
- FastAPI
- Groq
- Llama 3.3 70B Versatile
- Requests
- Pydantic
- python-dotenv

## Email Microservice

- ASP.NET Core
- .NET 10
- MailKit
- SMTP

## Database & Storage

- MySQL
- Cloudinary

## Development Tools

- Git
- GitHub
- Maven
- npm
- Postman
- VS Code / Eclipse / IntelliJ
- Visual Studio

---

# 🚀 Local Setup

## Prerequisites

Install:

```text
Java 21
Node.js
npm
Python 3.x
MySQL
.NET 10 SDK
Git
```

---

## 1. Clone the repositories

Clone the frontend, gateway, Spring Boot backend, chatbot, and email service repositories.

---

## 2. Configure MySQL

Create the required database.

Then configure Spring Boot with your local database:

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/your_database
spring.datasource.username=your_username
spring.datasource.password=your_password
```

---

## 3. Configure Spring Boot

Add your required environment/configuration values for:

```text
Database
JWT
Cloudinary
SMTP
Registration Email Service
```

---

## 4. Start Spring Boot

From the backend:

```bash
./mvnw spring-boot:run
```

Windows:

```bash
mvnw.cmd spring-boot:run
```

---

## 5. Start Node Gateway

```bash
cd gateway
npm install
npm start
```

---

## 6. Start FastAPI Chatbot

```bash
cd chatbot
pip install -r requirements.txt
uvicorn app:app --reload
```

---

## 7. Start Frontend

```bash
cd frontend
npm install
npm run dev
```

Vite normally starts the frontend at:

```text
http://localhost:5173
```

---

## 8. Start Registration Email Service

From the .NET project:

```bash
dotnet restore
dotnet run
```

Configure SMTP settings before testing registration emails.

---

# 🔧 Environment Variables

Never commit real credentials to GitHub.

Use environment variables for:

```text
MYSQLHOST
MYSQLPORT
MYSQLDATABASE
MYSQLUSER
MYSQLPASSWORD

JWT_SECRET

CLOUDINARY_CLOUD_NAME
CLOUDINARY_API_KEY
CLOUDINARY_API_SECRET

MAIL_USERNAME
MAIL_PASSWORD

GROQ_API_KEY

SPRING_BOOT_URL
FASTAPI_URL
DOTNET_URL
FRONTEND_URL

REGISTRATION_EMAIL_SERVICE_URL
```

Create an `.env.example` file containing variable names only.

Example:

```env
GROQ_API_KEY=

SPRING_BOOT_URL=
FASTAPI_URL=
DOTNET_URL=
FRONTEND_URL=

MYSQLHOST=
MYSQLPORT=
MYSQLDATABASE=
MYSQLUSER=
MYSQLPASSWORD=

JWT_SECRET=

CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=

MAIL_USERNAME=
MAIL_PASSWORD=

REGISTRATION_EMAIL_SERVICE_URL=
```

---

# 🌐 Deployment Architecture

A possible production deployment is:

```text
React
  ↓
Vercel

Node Gateway
  ↓
Railway / Render

Spring Boot
  ↓
Railway / Render

MySQL
  ↓
Railway / Managed MySQL

FastAPI Chatbot
  ↓
Render / Railway

.NET Email Service
  ↓
.NET-compatible cloud hosting

Cloudinary
  ↓
Cloud file storage
```

For production, replace all `localhost` URLs with environment-based service URLs.

---

# 🧪 Testing

Recommended testing flow:

### Authentication

```text
Register
   ↓
Registration email
   ↓
Login
   ↓
JWT received
```

### Job flow

```text
Employer Login
   ↓
Post Job
   ↓
Job visible in Job Seeker job list
```

### Application flow

```text
Job Seeker Login
   ↓
Search Job
   ↓
Open Job
   ↓
Apply
   ↓
Resume Upload
   ↓
Application saved
```

### Duplicate application

```text
Apply once
   ↓
Success

Apply same job again
   ↓
Duplicate validation
   ↓
Error message
```

### Automation

```text
Create job
   ↓
Matching niche
   ↓
Scheduler
   ↓
Email notification
```

### Chatbot

```text
Open chatbot
   ↓
Ask job-related question
   ↓
Gateway
   ↓
FastAPI
   ↓
Job data + LLM
   ↓
Response
```

---

# 🔒 Security Considerations

The application uses:

- JWT authentication
- BCrypt password hashing
- Role-based authorization
- Spring Security
- Ownership checks before sensitive operations
- Resume file type validation
- Resume size validation
- Environment variables for secrets

### Important

Do not commit:

```text
.env
Passwords
API keys
JWT secrets
SMTP passwords
Cloudinary secrets
```

If a real credential has already been pushed to GitHub, revoke/rotate it before making the repository public.

---

# 📚 What This Project Demonstrates

This project demonstrates practical experience with:

- Full-stack application development
- React.js
- Redux Toolkit
- REST API development
- Spring Boot
- Spring Data JPA
- Hibernate
- MySQL
- Spring Security
- JWT authentication
- Role-based authorization
- Node.js API Gateway
- Microservice-style architecture
- Python FastAPI
- LLM integration
- Groq API
- Cloudinary
- SMTP email
- .NET microservice
- Scheduled automation
- File upload handling
- Multipart requests
- JPQL queries
- DTO-based API design
- Exception handling
- Git/GitHub collaboration

---

# 👥 Team Contributions

The project was developed as a team project with different modules divided among team members.

Typical major modules include:

```text
Authentication / User Module
        ↓
Job Module
        ↓
Application Module
        ↓
Automation / Notification
        ↓
Chatbot
        ↓
Frontend Integration
        ↓
API Gateway
```

Individual responsibilities should be described according to the module actually implemented by each team member.



# ⭐ Future Improvements

Possible future enhancements:

- Advanced recommendation engine
- Elasticsearch-based job search
- Real-time chat between employer and candidate
- Application status tracking
- Interview scheduling
- Push notifications
- Admin dashboard
- Docker-based deployment
- CI/CD pipeline
- Centralized logging
- Redis caching
- Rate limiting
- Automated tests
- Production monitoring

---

# 📄 License

This project is developed for educational, portfolio, and placement purposes.

---

# 👤 Author

**Ajay Pal**

B.Tech — Computer Science & Engineering

**Project:** NicheNest — Job Portal with Automation
