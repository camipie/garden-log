# Garden Log

A cozy pixel-art habit tracker for the small routines that keep a balcony garden and a craft table going.

<!-- **[Live demo](https://your-app.netlify.app)** · **[API](https://your-api.onrender.com/habits)**

![Garden Log screenshot](docs/screenshot.png)-->

<!-- Replace the screenshot with a 15-second GIF of adding a habit and checking it in. -->

## Features

- Add habits with a name, a pixel icon, and a short note (for example, "needs 6 hours of sun")
- Check in once a day and keep a streak going
- Edit and delete habits
- Data is stored on a real server, so it follows you from phone to laptop
- Responsive layout, keyboard friendly, and reduced-motion aware

## Tech stack

| Layer | Tools |
| --- | --- |
| Frontend | React, Vite, plain CSS (custom pixel-art UI kit) |
| Backend | Node.js, Express |
| Database | PostgreSQL (hosted on [Neon](https://neon.tech)) |
| Hosting | Frontend on [Netlify](https://www.netlify.com), API on [Render](https://render.com) |

## How it works

```
Browser (React)  ──fetch──>  Express API  ──SQL──>  PostgreSQL
```

The React app never talks to the database directly. It calls the REST API, and the API reads and writes the data.

### API

| Method | Route | What it does |
| --- | --- | --- |
| GET | `/habits` | List all habits with their current streak |
| POST | `/habits` | Create a habit |
| PUT | `/habits/:id` | Update a habit |
| DELETE | `/habits/:id` | Delete a habit |
| POST | `/habits/:id/checkins` | Check in for today |
| DELETE | `/habits/:id/checkins/today` | Undo today's check-in |

Errors return JSON like `{ "error": "Habit not found" }` with a proper status code (400, 404, 500).

### Database

```sql
CREATE TABLE habits (
  id         SERIAL PRIMARY KEY,
  name       TEXT NOT NULL,
  icon       TEXT NOT NULL DEFAULT 'plant',
  note       TEXT,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE TABLE checkins (
  id       SERIAL PRIMARY KEY,
  habit_id INTEGER NOT NULL REFERENCES habits(id) ON DELETE CASCADE,
  day      DATE NOT NULL,
  UNIQUE (habit_id, day)
);
```

The `UNIQUE (habit_id, day)` rule means a habit can only be checked in once per day.

## Run it locally

You need [Node.js](https://nodejs.org) (LTS) and a PostgreSQL database. A free Neon database works well.

```bash
git clone https://github.com/your-username/garden-log.git
cd garden-log
```

**1. Start the API**

```bash
cd server
npm install
cp .env.example .env     # then fill in the values below
npm run dev
```

`server/.env`:

```
DATABASE_URL=postgres://user:password@host/dbname
PORT=3001
CLIENT_URL=http://localhost:5173
```

**2. Start the frontend** (in a second terminal)

```bash
cd client
npm install
cp .env.example .env
npm run dev
```

`client/.env`:

```
VITE_API_URL=http://localhost:3001
```

Open http://localhost:5173.

## Project structure

```
garden-log/
├── client/                 React app (Vite)
│   └── src/
│       ├── components/     HabitCard, HabitForm, HabitList, ...
│       ├── assets/         sprites and fonts
│       └── styles/         design tokens and pixel UI kit
└── server/                 Express API
    ├── routes/             habit and check-in routes
    ├── db.js               database connection
    └── index.js            app entry point
```

## Design

The look is inspired by cozy farming games: wooden frames, parchment panels, and a twelve-color palette drawn from a garden and a craft table. Sprites are drawn on a 32×32 grid and scaled by whole numbers with `image-rendering: pixelated` so edges stay sharp.

- **Type:** Pixelify Sans for titles and buttons, Nunito for body text
- **UI rules:** one main action per screen, tap targets of at least 44px, and state is never shown by color alone

## What I learned

<!-- TBD. Good prompts:
- What was the hardest bug, and how did you find it?
- What surprised you about deploying?
- What would you change if you started again?
-->

## Roadmap

- [ ] User accounts and login
- [ ] Reminders
- [ ] Weekly stats and charts
- [ ] Photo journal for plant growth

## Credits
