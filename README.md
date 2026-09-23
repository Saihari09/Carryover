# Carryover

A daily five-word chain game.

You're given a word. Three of its letters are **marked** — your next word must contain all
three, in any position. Do that four times. The catch: a letter only scores the **first**
time the chain ever sees it, and each word can bring in at most two new ones. So a perfect
run finds **8 letters**.

**Play it:** https://YOUR-USERNAME.github.io/carryover/

## Scoring

| | |
|---|---|
| Two new letters | **30** |
| One new letter | **10** |
| Each new letter's Scrabble value | **+1 to +10** (Z and Q pay) |
| Word played within 15s / 40s | **+10 / +5** |

The speed clock restarts on every word, so one long think won't sink a run. The live bonus
is shown beside the row you're typing into, ticking +10 → +5 → —, and each played word keeps
its score in the same column.

Tap **♪** in the header to mute sound and vibration.

A move only needs *one* letter the previous word lacks to be legal — but scoring counts
letters new to the **whole chain**. So a legal move can score zero. Seeing that coming is
the game.

## Running it locally

No build step and no dependencies — it's two static files.

```bash
python3 -m http.server 8791
```

Then open http://localhost:8791.

## Deploying to GitHub Pages

Push this directory to a repo, then in **Settings → Pages** set *Source* to
"Deploy from a branch", branch `main`, folder `/ (root)`. The site appears at
`https://<username>.github.io/<repo>/` within a minute or so.

## What's in here

| File | |
|---|---|
| `index.html` | the whole game — markup, styles and logic, no dependencies |
| `words.js` | 14,855 accepted five-letter words; 2,315 of them marked "everyday" and used for seeds and the "N fit" counter |

The puzzle is seeded from the date, so everyone gets the same chain on the same day.
Progress is kept in `localStorage`, per browser.

## Notes for beta testers

Things worth telling me about:

- **Words it rejected that it shouldn't have** (or accepted that it shouldn't have) — the
  word list is the part most likely to annoy people.
- **Whether the marked-letters rule was clear** from the instructions alone, without anyone
  explaining it.
- **Whether the speed bonus felt motivating or stressful.** It's the part I'm least sure about.
- Anything that looked broken on your phone.
- **Whether the sound and vibration helped or annoyed.** Vibration only works on Android —
  iOS Safari has no Vibration API — so iPhone testers get sound and animation only.

Known and deliberate: a sharp player reaches 8/8 on most puzzles, so the letter count is a
completion badge and the points are the real competition.
