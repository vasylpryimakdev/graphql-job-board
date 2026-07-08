# GraphQL Job Board

A full-stack job board application built with GraphQL, React, and Node.js. Post job listings, browse available positions by company, and manage your job postings with user authentication.

## Tech Stack

### Frontend

- **React 19** - UI framework
- **Vite** - Build tool and dev server
- **Apollo Client** - GraphQL client
- **React Router 7** - Client-side routing
- **Bulma** - CSS framework
- **JWT** - Authentication token handling

### Backend

- **Apollo Server 4** - GraphQL server
- **Express.js** - Web server framework
- **SQLite** - Database (better-sqlite3)
- **Knex** - SQL query builder
- **DataLoader** - Batch loading and caching
- **JWT** - Authentication (jsonwebtoken)
- **Nodemon** - Development auto-reload

## Project Structure

```
graphql-job-board-project/
├── client/                    # React frontend
│   ├── src/
│   │   ├── App.jsx           # Main app component with routing
│   │   ├── components/       # Reusable UI components
│   │   │   ├── JobList.jsx
│   │   │   ├── NavBar.jsx
│   │   │   └── PaginationBar.jsx
│   │   ├── pages/            # Page components
│   │   │   ├── HomePage.jsx
│   │   │   ├── JobPage.jsx
│   │   │   ├── CompanyPage.jsx
│   │   │   ├── CreateJobPage.jsx
│   │   │   └── LoginPage.jsx
│   │   └── lib/
│   │       ├── auth.js       # Auth utilities
│   │       ├── formatters.js # Date/data formatters
│   │       └── graphql/      # GraphQL setup
│   │           ├── hooks.js
│   │           └── queries.js
│   ├── package.json
│   ├── vite.config.js
│   └── index.html
│
└── server/                    # Node.js backend
    ├── server.js             # Express + Apollo setup
    ├── schema.graphql        # GraphQL schema
    ├── resolvers.js          # GraphQL resolvers
    ├── auth.js               # Authentication middleware
    ├── package.json
    ├── db/
    │   ├── connection.js     # SQLite connection
    │   ├── jobs.js           # Job queries
    │   ├── companies.js      # Company queries & DataLoader
    │   ├── users.js          # User queries
    │   └── ids.js            # ID generation
    ├── scripts/
    │   ├── create-db.js      # Database initialization
    │   └── insert-50-jobs.js # Sample data
    └── data/
        └── db.sqlite3        # SQLite database file
```

## Getting Started

### Prerequisites

- Node.js 18+
- npm or yarn

### Installation

1. **Clone and install dependencies**

```bash
# Install server dependencies
cd server
npm install

# Install client dependencies
cd ../client
npm install
cd ..
```

2. **Initialize the database** (if needed)

```bash
cd server
node scripts/create-db.js          # Create database schema
node scripts/insert-50-jobs.js     # Populate with sample data
cd ..
```

### Running the Application

**Terminal 1 - Start the GraphQL server:**

```bash
cd server
npm start
# Server runs on http://localhost:9000
# GraphQL endpoint: http://localhost:9000/graphql
```

**Terminal 2 - Start the React development server:**

```bash
cd client
npm start
# Open http://localhost:5173 (or the URL shown in terminal)
```

## Features

### Job Browsing

- **Browse All Jobs** - View paginated list of all available job postings
- **Job Details** - Click on a job to see full description and company information
- **Company Pages** - View all jobs posted by a specific company

### User Authentication

- **Login** - Authenticate with JWT-based login
- **Protected Routes** - Job creation and management requires authentication
- **Session Persistence** - Authentication tokens stored securely

### Job Management (Authenticated Users)

- **Create Jobs** - Post new job listings for your company
- **Edit Jobs** - Update job title and description
- **Delete Jobs** - Remove job postings
- **Company Association** - Jobs are automatically linked to your company

## API Documentation

### GraphQL Endpoints

**Queries:**

```graphql
# Get a specific company
query GetCompany($id: ID!) {
  company(id: $id) {
    id
    name
    description
    jobs {
      id
      title
      date
    }
  }
}

# Get a specific job
query GetJob($id: ID!) {
  job(id: $id) {
    id
    title
    description
    date
    company {
      id
      name
    }
  }
}

# Get paginated jobs list
query GetJobs($limit: Int, $offset: Int) {
  jobs(limit: $limit, offset: $offset) {
    items {
      id
      title
      date
      company {
        name
      }
    }
    totalCount
  }
}
```

**Mutations:**

```graphql
# Create a new job (requires authentication)
mutation CreateJob($input: CreateJobInput!) {
  createJob(input: $input) {
    id
    title
    description
    date
  }
}

# Update a job (requires authentication)
mutation UpdateJob($input: UpdateJobInput!) {
  updateJob(input: $input) {
    id
    title
    description
  }
}

# Delete a job (requires authentication)
mutation DeleteJob($id: ID!) {
  deleteJob(id: $id) {
    id
  }
}
```

### REST Endpoint

**Login:**

```bash
POST /login
Content-Type: application/json

# Response includes JWT token
```

## Development

### Build for Production

**Client:**

```bash
cd client
npm run build
# Output in client/dist/
```

**Server:** The server runs as-is in production (no build step needed)

### Linting

```bash
cd client
npm run lint
```

## Database Schema

The application uses SQLite with the following main tables:

- **users** - User accounts linked to companies
- **companies** - Companies posting jobs
- **jobs** - Job listings

Use `sqlite3 server/data/db.sqlite3` to inspect the database directly.

## Authentication

- JWT tokens are issued on login
- Tokens must be included in the `Authorization: Bearer <token>` header for authenticated mutations
- Tokens are decoded client-side to determine user identity and permissions

## Performance Optimizations

- **DataLoader** - Batch loading of company data to prevent N+1 queries
- **Pagination** - Jobs are paginated to handle large result sets
- **Apollo Client Caching** - Automatic query result caching
- **Vite** - Fast development server with hot module reloading

## Troubleshooting

**"Cannot connect to server"**

- Ensure the server is running on port 9000
- Check that `npm start` in the `server` directory executed successfully

**"Database file not found"**

- Run `node scripts/create-db.js` in the server directory
- Ensure the `server/data/` directory exists

**"Authentication errors"**

- Clear browser local storage and retry login
- Ensure the login credentials match a user in the database
