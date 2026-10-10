# foodbridge_bwt
FoodBridge: a real-time MERN marketplace that connects donors with surplus food to NGOs. Atomic reservations prevent double booking, and an expiry guard keeps expired food from being claimed.
# 🌉 FoodBridge

**Food should find people, not landfills.**

A real-time marketplace that connects surplus food from donors to NGOs, built on the MERN stack for a hackathon.

🔗 **Live demo:** https://rule-replicator.lovable.app
> Use the **Demo Donor** and **Demo NGO** buttons on the sign-in page. No sign-up needed.

---

## The Problem

Edible food is thrown away by restaurants, hostels, canteens and households while nearby NGOs struggle to source meals. The food isn't the problem. Coordination is. Surplus food has a short shelf life, pickup windows are narrow, and donors and receivers have no shared, trustworthy place to find each other in time.

## The Solution

FoodBridge gives donors and NGOs one live listing board:

- **Donors** post surplus food with quantity, pickup location, pickup time and expiry time.
- **NGOs** discover listings, search and filter them, reserve what they can collect, and mark it collected after pickup.
- Every donation moves through a clear lifecycle: **Available → Reserved → Collected**.

## ✨ Features

| Feature | What it does |
|---|---|
| Donor posting | Post food with type, quantity, location, pickup and expiry times in under a minute |
| Live listings | Shared board with status labels |
| Search and filters | Free-text search (food, location, donor) plus status and food-type filters |
| **Atomic reservation** | Only one NGO can claim a donation, even if several click at once |
| **Expiry guard** | Expired food can't be claimed, whatever the client sends |
| Status lifecycle | Available → Reserved → Collected, persisted in MongoDB |
| Auth and roles | JWT + bcrypt with separate Donor and NGO permissions |
| Impact counters | Dashboard tiles for available, reserved, collected and total food |
| One-click demo | Demo Donor / Demo NGO sign-in buttons |

## 🛠 Tech Stack

- **Frontend:** React + Vite
- **Backend:** Node.js, Express.js
- **Database:** MongoDB + Mongoose
- **Auth and security:** JWT, bcrypt, role-based access control

## 🏗 Architecture

Three-tier MERN architecture:

    React (Vite)  →  Express REST API  →  MongoDB (Mongoose)

Business rules such as expiry and ownership live on the **server**, so even a modified client can't reserve expired or already-claimed food.

## ⚙️ How Atomic Reservation Works

Reservation is a single conditional `findOneAndUpdate`:

1. Match the donation where `_id = donationId` **AND** `status = "Available"` **AND** `expiryTime > now`.
2. In the same operation, set `status = "Reserved"` and `reservedBy = ngoId`.
3. If nothing matches, it was already claimed or has expired, so the API returns an error.

The check and the update happen as one indivisible operation, so there is no race window.

## 🔌 API Overview

| Method | Route | Access | Purpose |
|---|---|---|---|
| POST | `/api/auth/register` | Public | Create an account with a role |
| POST | `/api/auth/login` | Public | Sign in and receive a token |
| GET | `/api/donations` | Signed in | List donations (search, status, food-type filters) |
| POST | `/api/donations` | Donor | Create a donation |
| PUT | `/api/donations/:id/reserve` | NGO | Atomically reserve a donation |
| PUT | `/api/donations/:id/collect` | NGO | Mark a reserved donation as collected |

## 🔐 Security

- Passwords stored only as **bcrypt** hashes
- **JWT**-based authentication on protected routes
- **Role checks** (Donor vs NGO) enforced server-side
- Input validation, with errors that never expose stack traces

## 🚀 Running Locally

```bash
# 1. Clone the repo
git clone <your-repo-url>
cd foodbridge

# 2. Install dependencies
cd server && npm install
cd ../client && npm install

# 3. Add environment variables in /server/.env
MONGO_URI=<your MongoDB connection string>
JWT_SECRET=<your token secret>

# 4. Start the API and the client
cd server && npm start
cd client && npm run dev
```

## 🧪 Testing

Functional tests cover registration and login, form validation, search and filters, role restrictions, persistence after refresh, and the two safety-critical cases:

- Two NGOs reserving the same donation at once → exactly one succeeds
- Reserving expired food → rejected

## 🗺 Roadmap

- **Connect:** map view and distance sorting, SMS/WhatsApp alerts
- **Build trust:** NGO verification, meals-saved dashboard, ratings and handoff history
- **Scale:** cloud deployment, caching, rate limiting, pagination

## ⚠️ Limitations

This is a hackathon prototype. NGOs aren't formally verified beyond sign-in, there are no notifications or map view yet, and it hasn't been load-tested for production.

## 📚 Reference

UNEP, [Food Waste Index Report 2024](https://www.unep.org/resources/publication/food-waste-index-report-2024): about 19% of food available to consumers was wasted in 2022.

## 👤 Author

Built by **B.Praveen**
