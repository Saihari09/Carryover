# Carryover

A daily five-word chain game.

You're given a word. Three of its letters are **marked** — your next word must contain all
three, in any position. Do that four times. The catch: a letter only scores the **first**
time the chain ever sees it, and each word can bring in at most two new ones. So a perfect
run finds **8 letters**.

**Play it:** https://saihari09.github.io/Carryover/

## Scoring

| | |
|---|---|
| Two new letters | **30** |
| One new letter | **10** |
| Each new letter's Scrabble value | **+1 to +10** (Z and Q pay) |
| Word played within 15s | **+10** — countdown reads green |
| Word played within 40s | **+5** — countdown reads amber |
| After that | **0** — countdown reads black |

A live countdown sits beside the row you're typing into — the bonus on top, seconds left
below — with a bar draining underneath the row in the same colour. It runs green, then
amber, then black as each band expires, and the last five seconds of a band pulse. Each
played word keeps the points it earned in that same column.

The clock restarts on every word, so one long think won't sink a run.

Tap **♪** in the header to mute sound and vibration.

At the end you get points, letters and time as three headline numbers, a per-word breakdown,
and a spoiler-free grid. **Share result** opens the phone's native share sheet where one
exists (and attaches the game's link); elsewhere it falls back to copying to the clipboard.

A move only needs *one* letter the previous word lacks to be legal — but scoring counts
letters new to the **whole chain**. So a legal move can score zero. Seeing that coming is
the game.

## Bonus round

When the chain ends you get **30 seconds** to spend the letters you found: tap them (or type)
to build any word, submit as many as you like, best one counts. **7 points a letter**, plus
**20** for using every letter in the pool.

A bonus word can never repeat a letter, because the pool holds each found letter exactly
once — so the dictionary only carries the 29,506 words of 3–8 letters with no repeat, which
is all the pool can ever spell. Simulation over 120 playthroughs found at least one possible
word every single time (median 115 available), so the round is never a dead end.

## Screen sizes

Laid out to fit entirely above the keyboard with no scrolling, verified from 320×568
(original iPhone SE) up. Tiles and keys scale with viewport height; below 740px tall the
tiles stop being square so the board loses height without losing width.

## Running it locally

No build step and no dependencies — it's two static files.

```bash
python3 -m http.server 8791
```

Then open http://localhost:8791.

## Deploying to GitHub Pages

In **Settings → Pages** set *Source* to "Deploy from a branch", branch `main`, folder
`/ (root)`. The site goes live at https://saihari09.github.io/Carryover/ within a minute
or two of the first push.

## What's in here

| File | |
|---|---|
| `index.html` | the whole game — markup, styles and logic, no dependencies |
| `words.js` | 14,855 accepted five-letter words; 2,315 of them marked "everyday" and used for seeds and the "N fit" counter |
| `bonus.js` | 29,506 words of 3–8 letters with no repeated letter, for the bonus round |

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
