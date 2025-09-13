# A simple authentication API using Node.js, Express, and MySQL.
Supports Signup, Login, Logout (token revocation), and Profile (protected route).

# Features
-User signup (register with email + password)
-User login (returns JWT token)
-Profile route (protected with token)
-Logout (token revocation)
-Passwords hashed with bcrypt
-Tokens signed with JWT

# Tech Stack
-Node.js + Express
-MySQL2
-bcryptjs
-jsonwebtoken
-dotenv
-cors

# Setup Instructions
# 1. Database Setup
Open XAMPP → start Apache and MySQL.
Go to phpMyAdmin → create a new database:
lab_auth

# Create required tables:
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  full_name VARCHAR(255),
  role VARCHAR(50) DEFAULT 'student',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE revoked_tokens (
  id INT AUTO_INCREMENT PRIMARY KEY,
  jti VARCHAR(64) UNIQUE NOT NULL,
  expires_at DATETIME
);

# 2. Project Setup
# Clone or create the folder
mkdir lab-auth-api
cd lab-auth-api

# Install dependencies
npm init -y
npm install express mysql2 dotenv cors bcryptjs jsonwebtoken uuid
npm install -D nodemon

# 3. Project Structure
lab-auth-api/
├─ config/
│  └─ db.js
├─ controllers/
│  └─ authController.js
├─ middleware/
│  └─ auth.js
├─ routes/
│  └─ authRoutes.js
├─ .env
├─ .gitignore
└─ server.js

# 4. .env File
Create a file called .env in the root:
# Note: Make sure to change the credentials of your DB_USER, DB_PASS, and DB_NAME (change JWT_SECRET and  JWT_EXPIRES if you like to change it) to the one you've assigned your system.
DB_HOST=localhost
DB_USER=root
DB_PASS=
DB_NAME=lab_auth
DB_PORT=3306
SERVER_PORT=3000

JWT_SECRET=your_long_random_secret_here
JWT_EXPIRES=1h

# 5. Run the Server
npm run dev
Visit:
http://localhost:3000/api/health

# You should see:
{ "status": "ok", "db": "connected", "time": "..." }

# API Endpoints
Signup
POST {{baseUrl}}/auth/signup
{
  "email": "user1@example.com",
  "password": "Pass@1234",
  "full_name": "User One",
  "role": "student"
}

Login
POST {{baseUrl}}/auth/login

{
  "email": "user1@example.com",
  "password": "Pass@1234"
}

Returns a token.
Save it in Postman → Tests tab:
if (pm.response.code === 200) {
  const data = pm.response.json();
  pm.environment.set("token", data.token);
}

Profile (Protected)
GET {{baseUrl}}/profile
Headers → Authorization: Bearer {{token}}

Logout
POST {{baseUrl}}/auth/logout
Headers → Authorization: Bearer {{token}}

After logout, try /profile again → should return 401 token revoked.
# Negative Tests
Duplicate signup → 409
Wrong password → 401
No token on /profile → 401
Expired/Tampered token → 401

# Pre-Flight Checklist
XAMPP Apache + MySQL running
Database lab_auth created
.env file set correctly
npm run dev works
Visit /api/health → shows connected
