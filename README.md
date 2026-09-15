# The Briefing — your hidden love-notes app

A little app that looks like a local news reader. Enter your **secret time** on the
keypad and it reveals the day's love note with music; any other time just filters
the news. The messages are **encrypted**, so no one can read them but you.

The secret page also has a **shared little note** you can both write in, and shows
**how many people have opened it** (counted once per person, not per visit).

---

## The files

| File | What it is | Goes online? |
|------|------------|--------------|
| `index.html` | The actual website | **Yes** — this is what you host |
| `functions/api/news.js` | News fetcher for **Cloudflare Pages** | Yes (recommended host) |
| `functions/api/seen.js` | "Seen by" counter (Cloudflare + KV) | Yes (recommended host) |
| `functions/api/note.js` | Shared note store (Cloudflare + KV) | Yes (recommended host) |
| `netlify/functions/news.js` + `netlify.toml` | News fetcher for **Netlify** | Yes (if you use Netlify) |
| `api/news.js` | News fetcher for **Vercel** | Yes (if you use Vercel) |
| `encrypt.html` | Tool to read / edit / encrypt your notes | Optional (safe to host) |
| `reasons.enc.json` | Your messages, **encrypted** | Only if you use the GitHub method |
| `reasons.json` | Your messages, **readable** (master copy) | **No — keep private** |

> You don't need all three news files — keep the one for your host. They sit
> harmlessly if unused, and the app finds whichever one is live automatically.

---

## How to READ what's already in there

Two ways — you can always see your notes because you have the time:

1. **The easy way:** open `reasons.json`. That's the plain, readable list of every
   message, in order. Keep it privately on your phone or computer.
2. **From the encrypted blob:** open `encrypt.html`, paste your encrypted blob into
   the top box, type your secret time, and hit **Unlock to edit**. All your messages
   appear in the text box.

---

## How to ADD or CHANGE messages

You never edit the encrypted file directly. You edit the readable list and
re-encrypt. It takes about a minute:

1. Open `encrypt.html`.
2. **Load your current notes:** paste the encrypted blob in step 1 + your secret
   time → **Unlock to edit**. (Skip this if you're starting fresh.)
3. Add or change lines in the messages box (one message per line, in day order).
4. Hit **Encrypt**.
5. Put the new blob back, either:
   - paste the `ENCRYPTED_CONFIG` line into `index.html` and re-host, **or**
   - **Download reasons.enc.json** and overwrite the file on GitHub (the Raw link
     stays the same, so the site updates on its own).

> The messages show **one per day, in order**, looping back to the start after the
> last one. `startDate` decides which day is message #1.

### Serial numbers (so you can pick a message by number)

In `encrypt.html`, under the messages box, there's now a **Numbered list (for your
reference)** that numbers every message `1., 2., 3. …` as you type. Use those numbers
to say things like "show no. 34 today." The numbers are **only for you** — they are
never stored and never appear on the hidden page. You can even type your own numbers
in the box (like `1. …` or `2) …`) and they'll be stripped automatically when you
encrypt, so the note stays clean.

> Want an actual "choose which message shows today" control on the page? That's the
> next step we discussed — the serial numbers above are what make it easy to build.

---

## First-time setup

### 1. Choose your secret time and encrypt your notes
- Open `encrypt.html`, type your messages, pick a **secret time** (HH:MM), a
  **start date**, and hit **Encrypt**.
- Paste the resulting `ENCRYPTED_CONFIG = {...};` line into `index.html`, replacing
  `const ENCRYPTED_CONFIG = null;`.

### 2. Getting the news to load (important — read this)

A page like this runs entirely in the browser, and a browser can only read a
news source that gives it permission (a "CORS" header). Google News doesn't, so
a plain static page has to borrow a stranger's relay server — and those free
relays are unreliable and go down. That's why the feed sometimes shows
"Couldn't reach the news service." It isn't a bug in the page; it's the relays.

There are two dependable, free ways to fix it. **Pick one:**

**Option A — Cloudflare Pages (recommended, no key, most reliable).**
The project includes a tiny function (`functions/api/news.js`) that fetches the
news on the server side, where the CORS problem doesn't exist. Host on
Cloudflare Pages and it just works — nothing to paste, no key, no relays.
Steps are in "Host it for free" below. (Netlify and Vercel work too; their
equivalent files are included.)

**Option B — a free GNews key (works on any host, incl. GitHub Pages).**
- Get a free key at https://gnews.io/ (2 minutes, no card).
- Paste it into `NEWS_API_KEY = ""` near the top of `index.html`.
- This uses a real news API instead of relays, so it loads fast and reliably.

The app tries them in this order automatically: its own server function →
your GNews key → public relays as a last resort. So Option A **or** B is enough;
doing both just adds a backup.

### 3. Your own song (mp3)
- The app is already set to play a file named **`song.mp3`** that sits next to
  `index.html`. Just add your `song.mp3` to the repo and it becomes the background
  sound the moment the hidden page opens — nothing else to change.
- Prefer a different name or a link? Edit one line in `index.html`:
  `const MUSIC_URL = "song.mp3";` → your filename, or a direct `https` mp3 link.
- Leave it `""` to use the soft built-in melody instead.
- If the file is missing or can't play, it quietly falls back to the built-in melody.
- Use music you have the right to use. Needs an `https://` page to play.
- **On the hidden page** there are two audio controls at the bottom: a **stop/play**
  button (square = stop the music; it turns into a ▶ to start it again) and a
  **mute** button (silences it but keeps it running).

### 4. Host it for free

Open the app from a hosted `https://` link — **not** by double-clicking the file
(opening it as a local file blocks the news, music, and note features).

**Recommended — Cloudflare Pages (gives you the reliable no-key news):**
1. Put this whole folder in a GitHub repo you own.
2. Go to https://pages.cloudflare.com/ → **Create a project** → connect the repo.
3. Framework preset: **None**. Build command: leave blank. Output directory: `/`.
4. Deploy. You get a permanent `https://…pages.dev` link, and `/api/news` is live.

**Other options:**
- **Vercel** — import the repo at vercel.com; the included `api/news.js` serves the news.
- **Netlify** — connect the repo (or drag the folder at netlify.com/drop); the
  included `netlify.toml` + function serve the news.
- **GitHub Pages** — works, but it's static-only, so it can't run the news
  function. Use **Option B (GNews key)** above for reliable news there.

On her phone, open the link and use **Add to Home Screen** so it behaves like an app.

---

## The GitHub method (optional, for updating without re-hosting)

Instead of pasting the blob into `index.html`, you can host the encrypted file:
1. Create a GitHub repo you own, upload `reasons.enc.json`.
2. Open it → click **Raw** → copy that `raw.githubusercontent.com/...` link.
3. Paste it into `CONFIG_URL = ""` in `index.html`.

Now to update, you just overwrite `reasons.enc.json` on GitHub — no re-hosting.
Keep the repo **Public** (a private repo's Raw link needs a token and won't work).

---

## The shared note + "seen by" counter

On the secret page there's a small note you can tap to write in, plus a line that
says how many people have opened it. Both people share **one** note (last save
wins), and the count goes up **once per person**.

A count that everyone shares can't live inside the page — the browser forgets it
on every reload, and it can't even read a visitor's own IP. So one small thing on
a server has to remember it. The good news: you don't need a second service. These
features use the app's **own** `/api` backend — the same Cloudflare Pages setup you
already use for the news — plus a free key-value store (KV) that you switch on
once. Where that backend isn't present (e.g. plain GitHub Pages), the note quietly
falls back to this-device-only and the counter stays hidden.

### One-time setup on Cloudflare (~2 minutes, free, no card)

1. In the Cloudflare dashboard: **Workers & Pages → KV → Create a namespace**.
   Name it anything (e.g. `briefing`).
2. Open your Pages project → **Settings → Functions → KV namespace bindings →
   Add binding**. Set **Variable name** to exactly `DATA` and pick the namespace
   from step 1. Save.
3. **Deployments → Retry/redeploy** (bindings apply on the next deploy). Done —
   the note now syncs between phones and the counter shows.

That's it. The note is encrypted on the phone before it's sent, so the server only
ever stores unreadable text; the counter stores a **hash** of each visitor's IP,
never the IP itself.

> **How the count treats people:** it's by IP. Two phones on the same home Wi-Fi
> look like one person; a phone that switches between Wi-Fi and mobile data can
> look like two. For a couple's private page that's usually fine — if you'd rather
> it count strictly once per *device* instead, that's a one-line change, just ask.

## Good to know

- **Nothing is stored on the device**, and the message screen leaves no browser
  history — closing it returns to the news with no trace.
- **The secret time is never written in the code.** It's the decryption key: the
  right time decrypts the notes, a wrong time simply fails.
- **Honest limit:** a time is only 1,440 possibilities, so a technically skilled
  person who downloads the encrypted file could brute-force it. It's very effective
  against casual snooping and reading the GitHub file, but not against a determined
  expert. Want it stronger? Ask for the "time + secret word" unlock.

---

## Quick reference

- **Read notes:** open `reasons.json`, or unlock the blob in `encrypt.html`.
- **Add notes:** `encrypt.html` → load → edit → Encrypt → replace the blob.
- **Change the secret time:** re-encrypt with a new time in `encrypt.html`.
- **Turn on the shared note + counter:** add a Cloudflare KV binding named `DATA` (see above).
- **Write the note:** unlock the page → tap the note line → type → **save**.
- **Demo unlock time (current build):** `17:10`.
