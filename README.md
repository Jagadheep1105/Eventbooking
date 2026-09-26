# SD-08 — Event Ticketing System With Concurrent Seat Booking

> **Pulse Pass** — A production-grade, dark-cinematic event ticketing platform built with React, TypeScript, Node.js, Express, PostgreSQL, and Socket.IO. Features atomic transaction seat locking, SVG venue maps, real-time availability sync, and dual-browser race condition protection.

---

## 🌟 Quick Overview

In event ticketing, **concurrency protection is paramount**. When thousands of users attempt to purchase high-demand seats at the exact same millisecond, the database must strictly enforce that **no two users ever acquire the same seat**.

Pulse Pass demonstrates how to solve this critical engineering problem using **PostgreSQL row-level locking (`SELECT ... FOR UPDATE`)**, atomic transaction boundaries, real-time WebSocket state broadcasting, and a dark-cinematic user interface.

---

## 🚀 Key Features

* **Event Discovery**: Search and filter live music festivals, conferences, and sports events by category.
* **Interactive SVG Seat Map**: Centerpiece venue visualization with stage arc, seat hover micro-animations, price tooltips, and seat state legend.
* **Temporary Seat Locks**: 10-minute authoritative backend reservation locks with live countdown timer and automatic background release.
* **Real-time Synchronization**: Socket.IO broadcasts `seat_locked`, `seat_released`, `seat_sold`, and `booking_cancelled` events across all connected clients immediately.
* **Race Condition Prevention**: Enforced at the PostgreSQL database transaction layer. Dual attempts on the same seat yield atomic success for User A and immediate conflict rejection (`HTTP 409`) for User B.
* **Digital Boarding Pass & QR Code**: Stylized ticket vouchers with QR code representation, booking reference, and print capability.
* **Booking History & Cancellation**: View upcoming/completed orders and cancel bookings to release seats back to the venue map.

---

## 🛠️ Technology Stack

| Component | Technology | Purpose |
|---|---|---|
| **Frontend** | React 18 + TypeScript + Vite | Component structure and speed |
| **Styling** | Tailwind CSS + Motion | Dark cinematic visual system & animations |
| **Seat Map** | SVG (Scalable Vector Graphics) | Dynamic vector venue grid and seat states |
| **Backend** | Node.js + Express.js + TS | RESTful API and service layer |
| **Database** | PostgreSQL (WASM PGlite / `pg` Pool) | Relational persistence & `FOR UPDATE` transaction locks |
| **Real-time** | Socket.IO | WebSockets for instant seat updates |
| **Auth & Validation** | JWT + bcryptjs + Zod | Secure token authentication & request validation |

---

## 💻 Quick Start Guide

### 1. Prerequisites
- Node.js `v18+` or `v24+`
- `npm`

### 2. Run Backend
```bash
cd backend
npm install
npm run dev
```
*The backend automatically runs schema migrations, seeds 3 realistic events with 200+ total seats, starts the background expiration worker, and listens on `http://localhost:5000`.*

### 3. Run Frontend
```bash
cd frontend
npm install
npm run dev
```
*Open `http://localhost:3000` in your web browser.*

---

## ⚡ 5-Minute Technical Demo Sequence

1. Open `http://localhost:3000` in **Browser Window A**.
2. Click **Explore Events** and open *CyberPulse Music Festival 2026*.
3. Click **Choose Your Seats** to enter the interactive SVG seat map.
4. Select Seat **`A1`** (VIP Stage Row).
5. Click **Reserve & Lock Seats**. Notice the 10-minute timer start.
6. Open `http://localhost:3000` in **Browser Window B** (or Incognito window).
7. Notice Seat **`A1`** in Window B instantly changed to **TEMPORARILY LOCKED** (amber) in real-time via Socket.IO without refreshing!
8. Attempt to click and submit Seat **`A1`** in Window B.
9. Observe immediate rejection: `"Seat A1 is no longer available. It was reserved or sold to another user."`
10. Complete checkout in Window A to generate your digital boarding pass with QR code.
