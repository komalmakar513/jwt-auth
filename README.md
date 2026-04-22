# JWT Authentication Server

A complete JWT-based authentication system built with Node.js, Express, MongoDB, and Docker.

## Features

- ✅ User Registration
- ✅ User Login
- ✅ JWT Token Generation
- ✅ Protected Routes Middleware
- ✅ Password Hashing with bcryptjs
- ✅ MongoDB Integration
- ✅ Docker & Docker Compose Support
- ✅ Environment Variables Configuration

## Project Structure

```
jwt-auth/
├── backend/
│   ├── models/
│   │   └── User.js           # MongoDB User model
│   ├── middleware/
│   │   └── auth.js           # JWT authentication middleware
│   ├── routes/
│   │   └── authRoutes.js     # Auth endpoints (register, login, me)
│   ├── .env                  # Environment variables
│   ├── .env.example          # Example environment variables
│   ├── package.json
│   └── server.js             # Main server file
├── Dockerfile                # Docker configuration
├── docker-compose.yml        # Docker Compose for MongoDB + Backend
├── .gitignore
└── README.md
```

## Installation

### Prerequisites
- Node.js (v18 or higher)
- MongoDB (or use Docker)
- npm or yarn

### Local Setup (Without Docker)

1. **Clone the repository**
   ```bash
   git clone https://github.com/komalmakar513/jwt-auth.git
   cd jwt-auth
   ```

2. **Install dependencies**
   ```bash
   cd backend
   npm install
   ```

3. **Set up environment variables**
   ```bash
   cp .env.example .env
   # Edit .env with your MongoDB URI and JWT Secret
   ```

4. **Install MongoDB** (if not already installed)
   - Download from [MongoDB](https://www.mongodb.com/try/download/community)
   - Or use MongoDB Atlas (cloud)

5. **Start the server**
   ```bash
   npm start
   # or for development with auto-reload
   npm run dev
   ```

Server will run on `http://localhost:5000`

### Using Docker Compose

1. **Clone the repository**
   ```bash
   git clone https://github.com/komalmakar513/jwt-auth.git
   cd jwt-auth
   ```

2. **Build and run containers**
   ```bash
   docker-compose up --build
   ```

This will start:
- MongoDB on port 27017
- Backend server on port 5000

3. **Stop containers**
   ```bash
   docker-compose down
   ```

## API Endpoints

### 1. Register User
```http
POST /api/auth/register
Content-Type: application/json

{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "password123"
}
```

**Response (201 Created):**
```json
{
  "msg": "User registered successfully",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "username": "john_doe",
    "email": "john@example.com"
  }
}
```

### 2. Login User
```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response (200 OK):**
```json
{
  "msg": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "user": {
    "id": "507f1f77bcf86cd799439011",
    "username": "john_doe",
    "email": "john@example.com"
  }
}
```

### 3. Get Current User (Protected Route)
```http
GET /api/auth/me
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

**Response (200 OK):**
```json
{
  "_id": "507f1f77bcf86cd799439011",
  "username": "john_doe",
  "email": "john@example.com",
  "createdAt": "2024-04-22T10:30:00.000Z",
  "updatedAt": "2024-04-22T10:30:00.000Z"
}
```

### 4. Protected Route Example
```http
GET /api/protected
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

## Using the Middleware

To protect any route, import and use the auth middleware:

```javascript
const auth = require("./middleware/auth");

app.get("/api/protected", auth, (req, res) => {
  res.json({ msg: "Protected route", user: req.user });
});
```

The `req.user` object contains:
```javascript
{
  id: "507f1f77bcf86cd799439011",
  username: "john_doe",
  email: "john@example.com"
}
```

## Environment Variables

Create a `.env` file in the `backend` folder:

```env
PORT=5000
MONGO_URI=mongodb://admin:password123@localhost:27017/jwt-auth?authSource=admin
JWT_SECRET=your-super-secret-jwt-key-change-in-production
NODE_ENV=development
```

For production, change:
- `JWT_SECRET` to a strong, random secret
- `MONGO_URI` to your production MongoDB URL
- `NODE_ENV` to `production`

## GitHub Deployment

The project is already pushed to GitHub. To update:

```bash
git add .
git commit -m "Your commit message"
git push origin main
```

## Docker Commands

### Build Docker image
```bash
docker build -t jwt-auth-backend .
```

### Run with Docker Compose
```bash
docker-compose up --build      # Build and start
docker-compose up -d            # Start in background
docker-compose logs -f backend  # View logs
docker-compose down             # Stop all containers
```

### Docker Environment Variables
Update `docker-compose.yml` to change environment variables for Docker:

```yaml
environment:
  PORT: 5000
  MONGO_URI: mongodb://admin:password123@mongodb:27017/jwt-auth?authSource=admin
  JWT_SECRET: your-super-secret-jwt-key
  NODE_ENV: production
```

## Security Notes

⚠️ **Important for Production:**

1. Change `JWT_SECRET` to a strong, random string
2. Use strong MongoDB password
3. Enable HTTPS/SSL
4. Use helmet middleware for security headers
5. Add rate limiting
6. Use environment-specific configurations
7. Never commit `.env` file to Git

Example with helmet:
```bash
npm install helmet
```

```javascript
const helmet = require("helmet");
app.use(helmet());
```

## Testing with Postman/Curl

### Register
```bash
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"username":"test","email":"test@test.com","password":"password123"}'
```

### Login
```bash
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@test.com","password":"password123"}'
```

### Protected Route
```bash
curl -X GET http://localhost:5000/api/auth/me \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

## Troubleshooting

### MongoDB Connection Error
- Make sure MongoDB is running
- Check `MONGO_URI` in `.env`
- For Docker: ensure MongoDB container is running

### Token Expiration
- Tokens expire after 7 days
- User needs to login again to get a new token

### Port Already in Use
```bash
# Kill process on port 5000 (Linux/Mac)
lsof -ti:5000 | xargs kill -9

# Kill process on port 5000 (Windows)
netstat -ano | findstr :5000
taskkill /PID <PID> /F
```

## License

ISC

## Author

Komal Makar
