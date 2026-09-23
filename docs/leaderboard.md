# Leaderboard setup (Supabase)

The client is already written. It stays completely hidden until two constants are filled
in, so nothing is broken while this is unconfigured.

## 1. Create the project

New project at [supabase.com](https://supabase.com) — free tier is far beyond what a beta
needs. Pick any name and region.

## 2. Create the table

**SQL Editor → New query**, paste this, run it:

```sql
create table public.scores (
  id         bigint generated always as identity primary key,
  day        int  not null,
  name       text not null check (char_length(name) between 1 and 16),
  points     int  not null check (points  between 0 and 2000),
  letters    int  not null check (letters between 0 and 8),
  seconds    int  not null check (seconds between 0 and 86400),
  created_at timestamptz not null default now()
);

alter table public.scores enable row level security;

-- anyone may add a score and read the board; nobody may edit or delete one
create policy "insert scores" on public.scores for insert to anon with check (true);
create policy "read scores"   on public.scores for select to anon using (true);

create index scores_day_points on public.scores (day, points desc, seconds asc);
```

The `check` constraints are not anti-cheat, they just stop absurd values landing in the
table. See the note at the bottom.

## 3. Paste the credentials

**Project Settings → API** gives you a *Project URL* and an *anon public* key. Put them at
the top of the `<script>` block in `index.html`:

```js
var LB_URL="https://xxxxxxxxxxxx.supabase.co";
var LB_KEY="eyJhbGciOi...";
```

The anon key is designed to be public — it is in every Supabase web app's source. The RLS
policies above are what actually limit it.

Commit, push, and the board appears at the bottom of the result card.

## What the player sees

A name field and a **Post** button on the result card, then the day's top 20 ordered by
points, ties broken by time, with their own row highlighted. One post per browser per day;
the field disappears once posted. A new board every day, because rows are keyed on the
puzzle number.

If the network fails or the credentials are wrong, it says *board unavailable* and the rest
of the result card is unaffected.

## Known limits

- **Scores are forgeable.** They are posted by the browser, so anyone with devtools can
  send any number. This was a deliberate choice for a friends-scale beta. The upgrade, if
  the board starts mattering, is to post the five words played and have the server recompute
  the score from the day's seed — then cheating requires finding genuinely better words.
- **One post per day per browser**, enforced in `localStorage`, so clearing site data allows
  another. Same trade.
- **The board only works on GitHub Pages, not the Artifact version.** Artifacts run under a
  content policy that blocks requests to outside hosts, so there it will show *board
  unavailable*. The Pages link is the one to give testers.
