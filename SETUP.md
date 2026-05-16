# Block Drop — Setup Guide

## Files in this folder
```
blockdrop-pwa/
  index.html      ← the game
  manifest.json   ← PWA config
  sw.js           ← service worker (offline support)
  icon-192.png    ← app icon
  icon-512.png    ← app icon (large)
  SETUP.md        ← this file
```

---

## Step 1 — Create a Supabase project (free)

1. Go to https://supabase.com and sign up (free)
2. Click **New Project**, give it a name like `blockdrop`
3. Wait ~2 minutes for it to provision
4. Go to **Settings → API**
5. Copy:
   - **Project URL** (looks like `https://abcdefgh.supabase.co`)
   - **anon / public** key (long string starting with `eyJ...`)

---

## Step 2 — Create the leaderboard table

1. In Supabase, go to **SQL Editor**
2. Paste and run this:

```sql
create table leaderboard (
  id           bigint generated always as identity primary key,
  username     text not null unique,
  score        bigint not null default 0,
  title        text,
  achievements int not null default 0,
  updated_at   timestamptz not null default now()
);

-- Allow anyone to read scores
create policy "Public read" on leaderboard
  for select using (true);

-- Allow anyone to insert/update their own row by username
create policy "Public upsert" on leaderboard
  for insert with check (true);

create policy "Public update" on leaderboard
  for update using (true);

-- Enable Row Level Security
alter table leaderboard enable row level security;
```

---

## Step 3 — Add your keys to index.html

Open `index.html` and find these two lines near the top:

```js
const SUPABASE_URL = 'YOUR_SUPABASE_URL';
const SUPABASE_KEY = 'YOUR_SUPABASE_ANON_KEY';
```

Replace with your actual values:

```js
const SUPABASE_URL = 'https://abcdefgh.supabase.co';
const SUPABASE_KEY = 'eyJhbGciOiJIUzI1NiIs...';
```

---

## Step 4 — Publish to GitHub Pages

### First time setup:
1. Go to https://github.com and sign in
2. Click **New repository**
3. Name it `blockdrop` (or anything you like)
4. Set it to **Public**
5. Click **Create repository**

### Upload your files:
1. Click **uploading an existing file** on the repo page
2. Drag ALL files from this folder into the upload area:
   - `index.html`
   - `manifest.json`
   - `sw.js`
   - `icon-192.png`
   - `icon-512.png`
3. Click **Commit changes**

### Enable GitHub Pages:
1. Go to repo **Settings → Pages**
2. Under **Source**, select **Deploy from a branch**
3. Branch: `main`, folder: `/ (root)`
4. Click **Save**
5. Wait ~60 seconds, then your game is live at:
   `https://YOUR-GITHUB-USERNAME.github.io/blockdrop`

---

## Step 5 — Install on iPhone

1. Open your GitHub Pages URL in **Safari** on iPhone
2. Tap the **Share** button (box with arrow)
3. Tap **Add to Home Screen**
4. Tap **Add**

Done! It appears on your home screen like a real app, fullscreen, no browser chrome.

---

## Sharing with friends

Just send them the URL:
`https://YOUR-GITHUB-USERNAME.github.io/blockdrop`

They can play in browser OR install it the same way.
Scores submit to the shared leaderboard automatically.

---

## Updating the game later

1. Edit `index.html` locally
2. Go to your GitHub repo
3. Click `index.html` → pencil icon → paste new content → **Commit**
4. GitHub Pages updates in ~30 seconds

Or use the GitHub Desktop app for easier drag-and-drop updates.
