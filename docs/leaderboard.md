# Leaderboard options

GitHub Pages serves static files. There is no server and no database, so a board that
every player sees needs something else. Four routes, cheapest first.

## 1. Supabase free tier — recommended

A hosted Postgres with a REST API. The "anon" key is designed to sit in public client
code; Row Level Security limits what it can do.

- One table: `scores(day int, name text, points int, letters int, seconds int, created_at)`.
- Policy: **insert only, no update, no delete**, and select limited to the current day.
- The game POSTs a result at the end of a run and GETs the top 20 for today.

Free tier is far beyond what a beta needs. You create the project; the URL and anon key
go straight into the page.

## 2. Cloudflare Workers + KV

A tiny worker with a KV namespace, on Cloudflare's free tier. More code than Supabase but
no database to model and no vendor row-level-security to reason about. Good if you would
rather own the endpoint.

## 3. Move hosting to Netlify or Vercel

Both give serverless functions next to the static site, so the game and the endpoint share
an origin and there is no CORS to configure. Costs you the GitHub Pages URL.

## 4. No backend: share text into a group chat

What most daily word games actually rely on. The result already copies as a spoiler-free
grid with points, letters and time. For a friends-sized beta this may be enough, and it is
already built.

---

## The thing to decide first: can scores be trusted?

Any score a browser submits can be forged — open devtools, POST whatever number you like.
This is not a Supabase problem, it is true of every client-submitted board.

Options, in order of effort:

- **Accept it.** For a friends-scale beta, fine. Add a name field and move on.
- **Submit the played words, not the score.** The server recomputes the score from the
  words using the same rules and the day's seed. Forging then requires finding genuinely
  better words, which is just playing well. This is the right answer if the board matters,
  and it means the scoring rules have to live server-side too.
- **Accounts and sessions.** Real anti-cheat. Out of proportion for this.

My suggestion: start with option 1 plus "accept it", with a name field and a daily reset.
If the board gets gamed, move to recomputing from the words.
