# Leaderboard (Supabase)

**Live.** Project `yxpzaxztozmnvvnyddhw`, wired into `index.html`. What follows is the
setup that was run, for reference or for rebuilding it elsewhere.

## The table

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

## The credentials

**Project Settings → API** gives a *Project URL* and an *anon public* key. They sit at the
top of the `<script>` block in `index.html`:

```js
var LB_URL="https://xxxxxxxxxxxx.supabase.co";
var LB_KEY="eyJhbGciOi...";
```

The anon key is designed to be public — it is in every Supabase web app's source, and this
one was checked before committing: its JWT payload carries `"role":"anon"`, not
`service_role`. The RLS policies above are what actually limit it.

Verified against the live project: insert works, read works, **update and delete are both
refused** (the row survives the attempt unchanged), and the check constraints reject absurd
values. Note that PostgREST answers a blocked UPDATE or DELETE with `204`, the same status
as a successful one — the only way to tell is to read the row back afterwards.

## What the player sees

After the result card lands, a prompt invites them onto the board, showing the score and
streak they are about to post. **Join the board** posts it; **Not now** is remembered and
never asks again, leaving a small *join the board* link on the board instead. Once a name
is known the game posts automatically each day without asking, and offers *change name*.

The board shows the day's top 20 ordered by points, ties broken by time, with each player's
**streak** and letters, and their own row highlighted.

### Streak without a schema change

The board fetches the last 30 days in one request and works each player's streak out from
which days their name appears on — consecutive days counting back from today. No extra
column, no write path to keep in sync. A name appearing twice on one day (someone cleared
site data and posted again) collapses to their best score, so a name is never listed twice.

Because a streak is keyed on the *name*, two players choosing the same name share a streak.
Fine at friends scale; it is the same trade as the forgeable scores.

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
