# Task-Mangement-App

# Project Setup and Run Instructions

This README provides step-by-step instructions to set up and run the Directus backend server and the Svelte frontend for the project.

## Prerequisites

Ensure you have the following installed on your machine:

- Node.js (v14.x or higher)
- npm (v6.x or higher) or yarn
- MySQL (v8.x or higher)

## Setup for Backend (Directus)

### Step 1: Clone the Repository

```bash
git clone https://github.com/Abhishek111883/Task-Mangement-App.git
cd <this-repo-directory>/backend
```

### Step 2: Install Dependencies

```bash
npm install
```

### Step 3: Configure Environment Variables
Alter a .env file in the backend directory with the following content:

```plaintext
DB_CLIENT=mysql
DB_HOST=localhost
DB_PORT=3306
DB_USER=root
DB_PASSWORD=<your-password>
DB_DATABASE=directus
PORT=8055
SECRET=<your-secret-key>
```

### Step 4: Initialize the Database
Ensure your MySQL server is running, then initialize the Directus database:

```bash
npx directus bootstrap
```

### Step 5: Start the Directus Server

```bash
npx directus start
```

The Directus server should now be running on http://localhost:8055.

### Setup for Frontend (Svelte)

### Step 1: Navigate to the Frontend Directory

``` bash
cd ../frontend
```

### Step 2: Install Dependencies
Install the necessary dependencies for the frontend:

``` bash
npm install
```

### Step 3: Run the Svelte Development Server
Start the Svelte development server:

```bash
npm run dev
```

