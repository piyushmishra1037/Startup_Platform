# Startup Benefits Platform

A premium benefits platform for startups to access exclusive deals on SaaS tools. This project is a full-stack application built with **Next.js (App Router)** and **Node.js/Express**, designed with a focus on high-quality UI/UX and solid architectural patterns.

## 🚀 Features

- **Interactive 3D UI**: Custom-built tilt cards and 3D hero elements using React Three Fiber and Framer Motion.
- **Deal Management**: Browse, search, and filter exclusive perks.
- **Verification System**: "Locked" deals are only accessible to verified users.
- **Claim System**: Securely claim deals with backend validation preventing duplicates and unauthorized access.
- **User Dashboard**: Track claimed perks and their status.
- **Authentication**: JWT-based auth with secure password hashing.

## 🛠 Tech Stack

### Frontend
- **Framework**: Next.js 15+ (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **Animation**: Framer Motion (Page transitions, micro-interactions, 3D tilt effects)
- **3D**: React Three Fiber (@react-three/drei)
- **State**: React Context API (Auth)

### Backend
- **Runtime**: Node.js
- **Framework**: Express.js
- **Database**: MongoDB (Mongoose ODM)
- **Auth**: JWT (JSON Web Tokens) & bcryptjs

## 🏗 Architecture & Flow

### End-to-End Flow
1. **Landing**: Users land on a high-impact animated page explaining the value prop.
2. **Auth**: Users register/login. Tokens are stored in `localStorage` and managed via `AuthContext`.
3. **Discovery**: Users browse the `/deals` page.
   - **Public Deals**: Visible to everyone.
   - **Locked Deals**: Visible but blurred/disabled for unverified users.
4. **Claiming**:
   - Verified users click "Claim" on a deal.
   - Frontend sends a POST request to `/api/claims`.
   - Backend verifies eligibility (User Verified? Deal Locked? Already Claimed?).
   - If successful, the claim is recorded.
5. **Dashboard**: User sees their claimed deals and status in `/dashboard`.

### Authentication Strategy
- **JWT**: Stateless authentication. The server issues a signed token upon login.
- **Protection**: Middleware (`protect`) intercepts requests to private routes (`/claims`, `/auth/me`), verifying the token signature.
- **Context**: The frontend `AuthContext` hydrates the user state on app load by validating the stored token against `/auth/me`.

### Claiming Logic (Internal Flow)
1. **Request**: `POST /claims` with `{ dealId }`.
2. **Validation**:
   - Is the user logged in? (Middleware)
   - Does the deal exist?
   - **Security Check**: If `deal.isLocked`, is `user.isVerified` true?
   - **Uniqueness**: Has this user already claimed this deal? (Compound Index `user + deal` ensures DB integrity).
3. **Result**: 
   - Success: Returns claim object.
   - Error: Returns 403 (Forbidden) or 400 (Duplicate).

## 📂 Project Structure

```
├── client/                 # Next.js Frontend
│   ├── src/
│   │   ├── app/           # App Router Pages (dashboard, deals, login, etc.)
│   │   ├── components/    # Reusable UI (DealCard, Navbar, ThreeHero)
│   │   ├── context/       # Global State (AuthContext)
│   │   └── lib/           # API utilities (Axios instance)
│
├── server/                 # Node.js Backend
│   ├── src/
│   │   ├── config/        # DB Connection
│   │   ├── controllers/   # Business Logic
│   │   ├── middleware/    # Auth Protection
│   │   ├── models/        # Mongoose Schemas (User, Deal, Claim)
│   │   └── routes/        # API Endpoints
```

## ⚠️ Known Limitations & Future Improvements

- **Verification**: Currently, `isVerified` is a manual boolean flag in the database. In a real-world scenario, this would integrate with an ID verification provider (e.g., Stripe Identity) or business email validation.
- **Email Notifications**: No emails are sent upon claiming. Adding a queue (BullMQ + Redis) for async email delivery would be the next step.
- **Admin Panel**: There is no UI for admins to add deals. Currently, deals are seeded via `seeder.ts`.
- **SSR/SEO**: While Next.js is used, the deals are fetched client-side for simplicity in this demo. Moving this to Server Components would improve SEO and initial load performance.

## 🎨 UI & Performance Considerations

- **Animations**: Used `framer-motion` for layout transitions (shared layout animations) to ensure smooth filtering without jank.
- **3D Elements**: The 3D hero is optimized to not block the main thread, but it's kept simple to avoid heavy GPU usage on mobile.
- **Optimistic UI**: The interface provides immediate feedback (loading states, hover effects) to make the app feel faster than it is.

## 🚀 Setup

1. **Server**:
   ```bash
   cd server
   npm install
   # Create .env with MONGO_URI and JWT_SECRET
   npm run seed # (Optional: populates DB with dummy deals)
   npm run dev
   ```

2. **Client**:
   ```bash
   cd client
   npm install
   npm run dev
   ```

---
*Built for the Startup Benefits Platform Assignment.*
