# Lastmile Work Board

A shared task board for two people. Open the page, tick a box, and the other person sees it.

**Live page:** https://pegbitstudio.github.io/lastmile-board/

This repo holds one file, `index.html`. The tasks live in Supabase.

---

## Warning: this page is public

Anyone who has the link can read it and tick things. Do not put anything private in it —
no payout details, no keys, no personal information.

---

## Setup, once

### 1. Make a Supabase project

Go to supabase.com, create a project, and wait for it to finish.

### 2. Make the table

Open the SQL editor in Supabase and run this:

```sql
create table board (
  id text primary key,
  state jsonb not null,
  updated_at timestamptz not null default now()
);

alter table board enable row level security;

-- anyone with the page can read and write the one board row
create policy "read board"   on board for select using (true);
create policy "insert board" on board for insert with check (id = 'main');
create policy "update board" on board for update using (id = 'main') with check (id = 'main');
```

### 3. Put your two values in the page

In `index.html`, near the top of the script, replace these two lines:

```js
const SUPABASE_URL = "PASTE_YOUR_SUPABASE_URL_HERE";
const SUPABASE_KEY = "PASTE_YOUR_SUPABASE_ANON_KEY_HERE";
```

Both are in Supabase under **Project Settings → API**. Use the **anon public** key, never the
service role key.

The anon key is meant to be public, so it is safe in this file. The service role key is not.

### 4. Turn on GitHub Pages

Repo **Settings → Pages → Source: deploy from branch → main → / (root)**.

The page goes live in about a minute.

---

## How it works

- The board renders from a task list saved in Supabase.
- Clicking a chip saves the whole list back, a couple of seconds after you stop clicking.
- The page checks for the other person's changes every 5 seconds.
- If Supabase cannot be reached, it keeps working and saves in your own browser, and it says so
  in a red box at the top.

Last write wins. With two people that is fine. If you are both editing the same task at the same
second, one of you will not see their change stick — click it again.

---

## Using it

- **Left chip** — status: To do, Doing, Blocked, Done
- **Name chip** — who is doing it. Click to pass it to the other person
- **Star** — pin it to Today at the top
- **Done tab** — everything finished. Click a chip there to bring a task back

The full plan and the technical spec live in the main project repo, which is private.
