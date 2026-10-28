# HotelPro

> A production-minded MERN hotel booking and management platform.

HotelPro lets guests discover rooms, check date-based availability, reserve stays, view booking history, and cancel bookings. Administrators can manage rooms, review all bookings, and view registered users through a protected dashboard.

![HotelPro](https://img.shields.io/badge/HotelPro-hotel%20booking-c9a227?style=for-the-badge)
![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react&logoColor=white)
![Express](https://img.shields.io/badge/Express-4-000000?style=flat-square&logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose-47A248?style=flat-square&logo=mongodb&logoColor=white)
![Deployment](https://img.shields.io/badge/Deploy-Vercel%20%2B%20Render-000000?style=flat-square)

## Features

### Guest experience

- Responsive luxury-hotel landing page
- Room highlights with images, amenities, descriptions, and nightly rates
- Date-aware room availability
- JWT-based signup and login
- Protected guest dashboard
- Instant booking confirmation without payment collection
- Booking history with room, dates, amount, and status
- Booking cancellation
- Automatic handling of expired sessions

### Administrator experience

- Protected admin dashboard
- Booking overview and status tracking
- Room inventory overview
- Add rooms with photos and amenities
- Delete rooms
- Registered user listing
- Dashboard summary statistics

### Security and reliability

- Password hashing with `bcryptjs`
- JWT authentication with explicit algorithm verification
- Role-based authorization middleware
- Helmet security headers
- Express rate limiting
- Restricted CORS
- Request body size limits
- Production database startup validation
- MongoDB transaction support for booking creation
- Full card and CVV data are not collected or stored
- Environment secrets excluded from Git

## Architecture

```text
React client
    |
    | Axios / Vite proxy in development
    v
Express API
    |
    +-- Routes
    |     +-- User routes
    |     +-- Room routes
    |     +-- Booking routes
    |
    +-- Authentication middleware
    |
    +-- Controllers
    |     +-- User controller
    |     +-- Room controller
    |     +-- Booking controller
    |
    +-- Mongoose models
    |
    v
MongoDB
```

The backend follows an MVC-style structure:

- **Models** define MongoDB documents and relationships.
- **Controllers** contain request and business logic.
- **Routes** map HTTP endpoints to controllers.
- **Middleware** handles authentication and authorization.
- **Utilities** contain shared date and async helpers.
- **React pages/components** provide the view layer.

## Project structure

```text
HotelPro/
├── client/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── App.jsx
│   │   ├── AuthContext.jsx
│   │   ├── api.js
│   │   └── index.css
│   ├── .env.example
│   ├── package.json
│   ├── vercel.json
│   └── vite.config.js
├── server/
│   ├── controllers/
│   ├── middleware/
│   ├── models/
│   ├── routes/
│   ├── seed/
│   ├── utils/
│   ├── .env.example
│   ├── index.js
│   └── package.json
├── render.yaml
├── .gitignore
└── README.md
```

## Technology stack

### Frontend

- React 18
- Vite
- React Router
- Axios
- Bootstrap
- React Bootstrap

### Backend

- Node.js
- Express
- Mongoose
- MongoDB
- JSON Web Tokens
- bcryptjs
- Helmet
- express-rate-limit
- dotenv

### Hosting

- Frontend: Vercel
- Backend: Render
- Database: MongoDB Atlas

## Local development

### Prerequisites

- Node.js 18 or newer
- npm
- MongoDB running locally, or a MongoDB Atlas connection string

### 1. Clone the repository

```bash
git clone https://github.com/Pritam-017/HotelPro.git
cd HotelPro
```

### 2. Install frontend dependencies

```bash
cd client
npm install
```

### 3. Install backend dependencies

```bash
cd ../server
npm install
```

### 4. Configure the backend

Create `server/.env` from `server/.env.example`:

```env
PORT=8001
MONGO_URI=mongodb://127.0.0.1:27017/hotel
NODE_ENV=development
CLIENT_ORIGIN=http://localhost:5173
ALLOW_IN_MEMORY_DB=true
SEED_DEMO_DATA=true
JWT_SECRET=replace-this-with-a-long-development-secret
```

The in-memory database fallback is intended only for local development. It must remain disabled in production.

### 5. Configure the frontend

Create `client/.env` from `client/.env.example`:

```env
VITE_API_BASE_URL=http://localhost:8001
```

### 6. Start the backend

```bash
cd server
npm run dev
```

The API runs at:

```text
http://localhost:8001
```

### 7. Start the frontend

Open another terminal:

```bash
cd client
npm run dev
```

The frontend runs at:

```text
http://localhost:5173
```

## Demo accounts

Demo accounts are created only when `SEED_DEMO_DATA=true`.

| Role | Email | Password |
|---|---|---|
| Admin | `admin@hotelpro.com` | `Admin@123` |
| Guest | `guest@hotelpro.com` | `Guest@123` |

Change or remove seeded credentials before using a production database.

## API reference

### Authentication and users

| Method | Endpoint | Access |
|---|---|---|
| `POST` | `/user/SignUp` | Public |
| `POST` | `/user/Login` | Public |
| `GET` | `/user/getallusers` | Admin |

### Rooms

| Method | Endpoint | Access |
|---|---|---|
| `GET` | `/rooms/getallrooms` | Public |
| `GET` | `/rooms/getroom/:id` | Public |
| `POST` | `/rooms/addroom` | Admin |
| `PUT` | `/rooms/updateroom/:id` | Admin |
| `DELETE` | `/rooms/deleteroom/:id` | Admin |

Room availability can be filtered with:

```text
GET /rooms/getallrooms?fromdate=2030-01-10&todate=2030-01-12
```

### Bookings

| Method | Endpoint | Access |
|---|---|---|
| `POST` | `/bookings/bookroom` | Authenticated user |
| `POST` | `/bookings/cancelbooking` | Booking owner or admin |
| `GET` | `/bookings/getuserbookings` | Authenticated user |
| `GET` | `/bookings/getallbookings` | Admin |

Booking creation requires only:

```json
{
  "roomid": "room-object-id",
  "fromdate": "2030-01-10",
  "todate": "2030-01-12"
}
```

No payment information is collected.

## Production deployment

### MongoDB Atlas

Create a MongoDB Atlas cluster and obtain a connection string similar to:

```text
mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/hotelpro
```

MongoDB Atlas is recommended because booking creation uses transactions.

### Render backend

The repository includes [render.yaml](./render.yaml).

Render settings:

```text
Root directory: server
Build command: npm ci
Start command: npm start
Health check path: /
```

Set these Render environment variables:

```env
NODE_ENV=production
MONGO_URI=mongodb+srv://USERNAME:PASSWORD@cluster.mongodb.net/hotelpro
JWT_SECRET=generate-a-long-random-secret
CLIENT_ORIGIN=https://your-frontend.vercel.app
ALLOW_IN_MEMORY_DB=false
SEED_DEMO_DATA=false
```

Do not use the local MongoDB URL on Render.

### Vercel frontend

Deploy the `client` directory as a Vite project.

```text
Root directory: client
Build command: npm run build
Output directory: dist
```

Set this Vercel environment variable:

```env
VITE_API_BASE_URL=https://your-render-service.onrender.com
```

The included [client/vercel.json](./client/vercel.json) enables SPA fallback routing for React Router.

After Vercel gives you the final frontend URL, update Render:

```env
CLIENT_ORIGIN=https://your-frontend.vercel.app
```

## Production checklist

- [ ] MongoDB Atlas is configured.
- [ ] Render uses `NODE_ENV=production`.
- [ ] `ALLOW_IN_MEMORY_DB=false`.
- [ ] `SEED_DEMO_DATA=false`.
- [ ] `JWT_SECRET` is a strong random secret.
- [ ] `CLIENT_ORIGIN` matches the deployed Vercel URL exactly.
- [ ] `VITE_API_BASE_URL` points to the Render API URL.
- [ ] Local `.env` files are not committed.
- [ ] MongoDB Atlas network access allows the Render service.
- [ ] The backend health endpoint returns HTTP 200.
- [ ] Login, room browsing, booking, cancellation, and admin access are tested after deployment.

## Validation

Build the frontend:

```bash
cd client
npm run build
```

Check backend syntax:

```bash
cd server
node --check index.js
node --check controllers/userController.js
node --check controllers/roomController.js
node --check controllers/bookingController.js
```

Check the backend health endpoint:

```text
GET /
```

Expected response:

```json
{
  "status": "ok",
  "service": "HotelPro API"
}
```

## License

This project is maintained for educational and portfolio use.
