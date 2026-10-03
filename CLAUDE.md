# HobbyCon — event card procedures

Reference for adding and retiring event cards. The site is finished and correct as-is.

---

## HARD RULES — read before every task

1. **Do not refactor, restyle, tidy, or "improve" anything.** Not the CSS, not the HTML, not the indentation, not the inconsistencies. The inconsistencies listed in this file are intentional or accepted. Leave them.
2. **Do not touch existing cards** except to move a card between sections exactly as described under *Retiring an event*.
3. **`index.html` holds exactly 3 event cards. Never 2, never 4.** `.event-grid` is `grid-template-columns: repeat(3, 1fr)` (style.css:557) and the dot count is hardcoded — a 4th card breaks the layout and the mobile carousel. Adding to index.html always means *replacing* one.
3a. **Those 3 cards are the soonest 3 upcoming events, left to right in date order.** The homepage is a "what's next" shelf, not a highlights reel. When an event is retired, the replacement is the next event by date — which is usually *not* the one you just removed, and usually means **shifting the other cards left**, not swapping in place. A later event does not jump the queue. After any edit, read the three badge dates top to bottom and confirm they ascend.
4. **Never reformat a file.** No prettier, no reindenting, no collapsing multi-line class attributes. Diffs should contain only the cards being added or moved.
5. **Match the surrounding whitespace,** including the irregular leading spaces on the `<!-- Featured: ... -->` comments in events.html. They vary card to card. Copy the neighbour.
6. **Do not touch** `style.css`, `js/main.js`, `.cpanel.yml`, `.htaccess`, `vercel.json`.
7. When anything is ambiguous, ask. Do not infer event details, times, locations, or URLs.

---

## What an event needs before any editing starts

| Field | Example | Used in |
|---|---|---|
| Title | `FREE Mahjong Club` | all 4 shapes |
| Time | `6:30PM` | badge |
| Date | `July 30` / `July 30th` | badge — see suffix note below |
| Location | `Brookfield Place • Hudson Eats` | events.html, blurbs |
| What to expect | `Lessons + Open Play • American Style` | events.html only |
| Short blurb subject | `Mahjong Meetup` | all blurbs |
| Eventbrite URL | full URL including `?aff=oddtdtcreator` | all 4 shapes, twice each in B/C/D |
| Flyer filename | `jul26mahj.webp` | must already exist in `/images` |

If any field is missing, ask for it. Do not invent one.

### Flyer files

Flyers live in `/images`. Existing naming is inconsistent (`jul26mahj.webp`, `Aug26Craft.webp`, `Jun26Zumba.webp`, `YOGAflyer1.png`) — do not rename existing files. For new ones use lowercase `mmmYYkeyword.webp`, e.g. `sep26chess.webp`. Confirm the file exists before referencing it; a broken `src` is silent on the page.

`alt` text is always `{{Title without FREE}} Flyer`, e.g. `alt="Mahjong Club Flyer"`.

### Date suffix inconsistency — preserve it

- **events.html** badges use an ordinal suffix: `6:30PM • July 30th`
- **index.html** and **tickets.html** badges do not: `6:30PM • July 30`

This is not a bug to fix. Match the page you are editing.

### Character entities

events.html past cards use `&bull;` `&rsquo;` `&amp;`; newer upcoming cards use literal `•` `'` `&`. Both render fine. Match the nearest neighbouring card in the same section.

---

## Adding a new event — do all four in this order

### 1. `events.html` — upcoming card

Insert at the **top** of the grid opened at line ~213:

```html
<div class="mt-10 grid grid-cols-1 gap-6 sm:grid-cols-2 lg:grid-cols-2 items-stretch">
```

Template:

```html
   <!-- Featured: {{NAME}} -->
  <div class="hc-card bg-white border border-slate-200 rounded-3xl shadow-sm p-6 md:p-7">
    <div class="flex items-center justify-between gap-4">
      <div class="min-w-0">
        <div class="text-sm text-slate-500">Featured Experience</div>
        <div class="mt-1 text-xl font-bold text-slate-900">
         {{TITLE}}
        </div>
      </div>

      <div
        class="shrink-0 px-3 py-1 rounded-full bg-purple-50 border border-purple-200 text-purple-700 text-xs font-semibold"
      >
        {{TIME}} • {{MONTH}} {{DAY_ORDINAL}}
      </div>
    </div>

    <p class="mt-4 text-sm text-slate-600 leading-relaxed">
      We're hosting a <span class="font-semibold">{{BLURB_SUBJECT}}</span> in NYC!
    </p>

    <a
      href="{{EVENTBRITE_URL}}"
      target="_blank"
      class="mt-5 block overflow-hidden rounded-2xl border border-slate-200 bg-slate-50 hover:shadow-lg transition-shadow"
    >
      <img
        src="images/{{FLYER}}"
        alt="{{ALT}}"
        class="w-full h-auto object-cover"
        loading="lazy"
      />
    </a>

    <div class="mt-6 grid gap-3">
      <div class="rounded-2xl bg-slate-50 border border-slate-200 p-4">
        <div class="text-xs text-slate-500">LOCATION</div>
        <div class="mt-1 font-semibold text-slate-800">{{LOCATION}}</div>
      </div>

      <div class="rounded-2xl bg-slate-50 border border-slate-200 p-4">
        <div class="text-xs text-slate-500">WHAT TO EXPECT</div>
        <div class="mt-1 font-semibold text-slate-800">
          {{WHAT_TO_EXPECT}}
        </div>
      </div>
    </div>

    <a
      href="{{EVENTBRITE_URL}}"
      class="mt-6 w-full inline-flex items-center justify-center gap-2
             rounded-full px-6 py-3 font-semibold whitespace-nowrap
             border-2 border-purple-400 text-purple-700
             transition-all duration-300
             hover:bg-purple-500 hover:text-white hover:border-purple-500
             hover:shadow-lg"
    >
      Sign Up Free <i data-feather="arrow-right" class="w-4 h-4"></i>
    </a>
  </div>
```

Indent the whole block to match its neighbours (10 spaces on the `<div class="hc-card ...">` line).

### 2. `tickets.html` — ticket card

Insert at the **top** of the grid at line ~188 (`<div class="grid grid-cols-1 gap-6 sm:grid-cols-2">`), inside `<section id="upcoming-events">`.

```html
<!-- Event: {{NAME}} ({{MONTH}} {{DAY}}) -->
<div class="hc-card bg-white border border-slate-200 rounded-3xl shadow-sm overflow-hidden">
  <a href="{{EVENTBRITE_URL}}" target="_blank" class="block bg-slate-50 border-b border-slate-200">
    <img src="images/{{FLYER}}" alt="{{ALT}}" class="w-full aspect-[4/5] object-cover" loading="lazy" />
  </a>
  <div class="p-6 md:p-7">
    <div class="flex flex-col gap-2">
      <div class="flex items-center justify-between gap-2 flex-wrap">
        <div class="text-sm text-slate-500">Featured Experience</div>
        <div class="px-3 py-1 rounded-full bg-purple-50 border border-purple-200 text-purple-700 text-xs font-semibold whitespace-nowrap">{{TIME}} • {{MONTH}} {{DAY}}</div>
      </div>
      <h3 class="text-xl font-bold text-slate-900">{{TITLE}}</h3>
    </div>
    <p class="mt-4 text-sm text-slate-600 leading-relaxed">{{BLURB_SUBJECT}} hosted at {{LOCATION}}!</p>
    <a href="{{EVENTBRITE_URL}}" class="mt-6 w-full inline-flex items-center justify-center gap-2 rounded-full px-6 py-3 font-semibold whitespace-nowrap border-2 border-purple-400 text-purple-700 transition-all duration-300 hover:bg-purple-500 hover:text-white hover:border-purple-500 hover:shadow-lg">
      Sign Up Free <i data-feather="arrow-right" class="w-4 h-4"></i>
    </a>
  </div>
</div>
```

No indentation — these cards sit flush at column 0. Match that.

### 3. `index.html` — swap, never append

Still exactly 3 cards, and they must end up in **ascending date order**.

Work it out before editing, not after:

1. List every upcoming event across the site with its date (`tickets.html` is
   the most complete list).
2. Sort by date. The soonest 3 are what belongs on the homepage.
3. Compare against what is there now, and make the cards match that list in
   that order.

This usually means **shifting cards left**, not editing one in place. If the
retired event was in slot 1, slots 2 and 3 move up and the new event lands in
slot 3. Dropping a later event straight into slot 1 leaves the homepage out of
order — an easy mistake, because the diff looks small and clean.

An event further out does not go on the homepage just because it was the most
recently created. It waits its turn.

Leave the three `<button class="event-dot">` elements at line ~315 completely
alone — the count never changes.

**Check when done:** read the three badge dates top to bottom. They must
ascend. If they do not, the order is wrong regardless of how tidy the diff is.

```html
    <a href="{{EVENTBRITE_URL}}"
      target="_blank" rel="noopener"
      class="hc-card bg-white border border-slate-200 rounded-3xl shadow-sm overflow-hidden block">
      <img src="images/{{FLYER}}" alt="{{ALT}}" class="w-full aspect-[4/5] object-cover" loading="lazy" />
      <div class="p-6">
        <div class="flex items-center justify-between gap-2 flex-wrap">
          <div class="text-sm text-slate-500">Featured Experience</div>
          <div class="px-3 py-1 rounded-full bg-purple-50 border border-purple-200 text-purple-700 text-xs font-semibold whitespace-nowrap">{{TIME}} • {{MONTH}} {{DAY}}</div>
        </div>
        <h3 class="mt-2 text-xl font-bold text-slate-900">{{TITLE}}</h3>
        <p class="mt-3 text-sm text-slate-600">{{BLURB_SUBJECT}} hosted at {{LOCATION}}!</p>
        <div class="mt-5 w-full inline-flex items-center justify-center gap-2 rounded-full px-6 py-3 font-semibold border-2 border-purple-400 text-purple-700">
          Sign Up Free <i data-feather="arrow-right" class="w-4 h-4"></i>
        </div>
      </div>
    </a>
```

Note: the CTA here is a `<div>`, not an `<a>` — the whole card is already the link. Do not change it to an `<a>`.

### 4. Report back

List the files changed and the exact card that was displaced from index.html, so it can be confirmed before pushing.

---

## Retiring an event

### `events.html` — move to Previous Events

1. Cut the card from the upcoming grid.
2. Add `past-event` to its class list, immediately after `hc-card`:
   `class="hc-card past-event bg-white border border-slate-200 rounded-3xl shadow-sm p-6 md:p-7"`
3. Paste it into the Previous Events grid (line ~695, `<div class="grid gap-6 md:grid-cols-2 items-start">`), at the **top**, below the `<!-- paste your hc-card divs here -->` comment.
4. Change nothing else inside the card. Keep the Eventbrite links — `.past-event` sets `pointer-events: none` (style.css:614) so they are already unclickable.

Newest past event goes first.

### `tickets.html` and `index.html` — delete

Remove the card outright, including its `<!-- Event: ... -->` comment. index.html must be back to exactly 3 cards afterwards — so its retirement and its replacement happen in the same edit.

---

## Verify before committing

- `events.html` upcoming grid: card count is even, or the last row will look lopsided at `sm:grid-cols-2`.
- `index.html`: exactly 3 `<a class="hc-card ...">` inside `.event-grid`, exactly 3 `.event-dot` buttons.
- `index.html`: the 3 badge dates ascend, and they are the soonest 3 upcoming events on the site. Run the order check below.
- Every `images/...` path referenced actually exists.
- Every Eventbrite URL appears twice per card in shapes B, C, D — image link and CTA — and both are identical.
- `git diff` contains only added/moved cards. Any change to `style.css`, `js/main.js`, or unrelated markup means something went wrong — revert it.

Quick check:

```bash
grep -c 'hc-card bg-white border border-slate-200 rounded-3xl shadow-sm overflow-hidden block' index.html   # must be 3
grep -c '<button class="event-dot' index.html                                                              # must be 3
```

Order check — homepage cards, in the order they appear:

```bash
sed -n '/event-grid mt-6/,/event-dot/p' index.html \
  | grep -E 'whitespace-nowrap">|<h3' \
  | sed -E 's/.*whitespace-nowrap">([^<]+).*/  DATE : \1/; s/.*<h3[^>]*>([^<]*).*/TITLE: \1/'
```

Compare against every upcoming date on the site:

```bash
grep -oE 'whitespace-nowrap">[^<]+' tickets.html | sed 's/whitespace-nowrap">//'
```

The homepage list must be the first 3 of that list, in the same order.

---

## Deploy

**Push to `main`. Vercel builds automatically.** No dashboard step, no cPanel,
nothing else to run. A deploy is usually live in under a minute.

```bash
git add -A && git commit -m "your message" && git push
```

### How the site is served

```
visitor → DNS (GoDaddy) → 192.124.249.20 Sucuri → 216.198.79.1 Vercel
```

Sucuri sits in front as a WAF/CDN; Vercel is the origin. DNS is at GoDaddy
(`ns31`/`ns32.domaincontrol.com`). Vercel project `hobby-con`, account
`hellohobbycon@gmail.com`.

`vercel.json` at the repo root replaces the old `.htaccess`. `cleanUrls: true`
handles both the `.html` → clean-URL redirect and serving `/retreats` from
`retreats.html`. The 90 vanity and typo redirects live under `redirects`.
HTTPS and the branded `404.html` are automatic. Do not edit `vercel.json`
without need — it was verified 1:1 against the old `.htaccess`.

### Git access — one key per person, per machine

Everyone pushes with their **own** GitHub account and their **own** SSH key.
Keys are never shared, and the remote URL is a per-machine setting, not a
property of the repo — so what works on one laptop will not necessarily work
on another.

Recommended setup on each machine, especially if that person also uses GitHub
for other work:

1. Generate a key used only for HobbyCon.
2. Give it a Host alias in `~/.ssh/config` with `IdentitiesOnly yes`, so the
   machine cannot fall back to a different account's key.
3. Point this repo's `origin` at that alias.

The reason is the failure mode: an HTTPS remote authenticates with whatever
single GitHub credential macOS has cached, which is shared across every repo
on the machine. On a computer with more than one GitHub account that can
push, or commit, as the wrong one.

If a push fails with a permissions error, check the key and alias on that
machine first. Do not "fix" it by switching the remote to HTTPS.

### If a change does not appear on hobbycon.com

The deploy almost certainly worked; Sucuri is caching. It has been observed
holding pages for ~21 hours.

1. Check `https://hobby-con.vercel.app/<page>` — if the change is there,
   Vercel is fine and it is purely a cache issue.
2. Request the file with a query string to bypass Sucuri's cache:
   `https://hobbycon.com/js/main.js?v=1` — a different URL is a different
   cache key, so this fetches fresh.
3. Clear the cache in GoDaddy → Website Security → Performance.

Browser cache clearing and Incognito do **not** help. The stale copy is at
Sucuri's edge, not on your machine. This has cost hours twice; check Vercel
first, always.

### Legacy files

`.cpanel.yml` and `.htaccess` are dead — cPanel no longer serves the site.
They are kept only as a reference until Sucuri is retired. Do not follow the
cPanel deploy steps anywhere; they do nothing now.
