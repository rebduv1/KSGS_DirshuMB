# KSGS Shul Shiurim — instructions for Claude

Shiur recording site for **Khal Sunset Grove Sefard**, run by David Klein (R' Dovid Klein).
Live: **https://ksgs-shul-shiurim.vercel.app** (old address ksgs-dirshu-mb.vercel.app still works).
GitHub `rebduv1/KSGS_DirshuMB` → Vercel deploys every push to `main` automatically.

Your usual job: David attaches a recording and you **upload it to `audio/`**. The file name is
the only metadata — the site reads everything from it — so most of the work is fixing the name.

## Upload procedure

1. Inspect the attachment: `file` + `ffprobe` (duration, bitrate). Attachments arrive with an
   8-hex-digit prefix (`7412b491-…`) — strip it. If there is no extension, detect it with `file`
   and add `.mp3` / `.m4a`. Keep the original format; never convert.
2. Normalize the name (rules below). Tell David in one line what you changed and why.
3. `git fetch origin main && git merge --ff-only origin/main`, copy to `audio/<NAME>`, commit,
   push straight to `main`. Uploads do not need a branch or approval.
   - Commit identity: `git -c user.name=Claude -c user.email=noreply@anthropic.com commit …`
     (the stop hook rejects other identities). Message: `Add <Shiur>: <ref>, <YYYY-MM-DD>`.
4. Verify, then report:
   - The **Update shiur list** Action run succeeds
     (`gh api repos/rebduv1/KSGS_DirshuMB/actions/runs?per_page=1`).
   - The bot's follow-up commit added the file to the right `*.json` list.
   - Vercel status on `main` is `success`
     (`gh api repos/rebduv1/KSGS_DirshuMB/commits/main/statuses`).
   - Report back in 1–3 lines: what went live, where it shows, any name fixes, compressed size.
5. Do **not** compress by hand. The Action re-encodes anything above ~80 kbps to 64 kbps mono,
   keeping the same name.
   It gets ffmpeg from PyPI (`imageio-ffmpeg`); apt-get hung on the runners twice, so it isn't used.
   If the run log says "Could not get ffmpeg", the shiur still goes live uncompressed; re-run the
   workflow (`gh api -X POST …/actions/workflows/update-shiurim.yml/dispatches -f ref=main`).
   Always check the uploaded size: still >80 kbps means compression was skipped.

## File-name rules (all four prefixes uppercase, hyphens only)

| Shiur (home-page order) | Maggid shiur | Pattern | List file |
|---|---|---|---|
| Oraysa | R' Yisroel Jankelowitz | `ORAYSA-Masechta-###a-YYYY-MM-DD` · range `ORAYSA-Yevamos-120a-to-121b-…` | `oraysa.json` |
| Mishnayos | R' Yisroel Jankelowitz | `MISHNA-Masechta-PPP.MMM-YYYY-MM-DD` (perek.mishna) · range `001.001-to-001.004`, across `001.006-to-002.002` · whole perek `001` | `mishnayos.json` |
| Thursday Night Vaad | R' Yisroel Jankelowitz | `VAAD-YYYY-MM-DD-Topic-Words` | `vaad.json` |
| Sunday Shiur | R' Pinchas Friedlander (Hebrew: ר׳ פנחס פרידלאנדער) | `FRIEDLANDER-YYYY-MM-DD-Topic-Words` | `sunday.json` |
| Dirshu Mishnah Berurah | R' Dovid Klein (David) | `MB-OC-SSS-KKK-KKK-YYYY-MM-DD-Topic-Words` · across simanim `MB-OC-444-030-to-445-005-…` | `shiurim.json` |

Fixes you will routinely make (all seen in practice):
- **Capitalize**: prefix in caps (`mb-oc` → `MB-OC`, `oraysa` → `ORAYSA`, `friedlander` →
  `FRIEDLANDER`); topic words Title-Case (`tairuves-chozer-vniur` → `Tairuves-Chozer-vNiur`,
  matching older names like `vDorshin`, `vAchar`); masechta capitalized (`Yevamos`).
- **Two-digit month and day**: `2026-11-1` → `2026-11-01`. Without this the shiur is skipped.
- **Three-digit numbers**: daf `2a` → `002a`; siman, se'if katan, perek and mishna are all three digits.
- **A single se'if katan is written twice** (`MB-OC-448-012-012-…`); with only one, the `202` of the year
  is read as the end of the range. The site shows a matching pair as one s"k.
- **No underscores, spaces or stray periods**: `MB-OC-_447` → `MB-OC-447`; `Tairuves_Cont.` →
  `Tairuves-Continued` (use "Continued", as in older names). The only period allowed is the
  Mishnayos `PPP.MMM`.
- **Impossible dates** (e.g. `2026-20-25`): use the date the shiur was clearly given (usually the
  upload day; the Vaad is Thursday night, Friedlander is Sunday) and say so.
- **Obvious typos** in recurring words: `Taituves` → `Tairuves`. Say so.
- **Flag, but still upload**, anything that looks off: an overlapping s"k range against the
  previous MB shiur, a date that doesn't fit the weekday, a daf out of sequence. Renaming later
  is cheap. Recent MB sequence is in `shiurim.json`.
- If a name can't be resolved (siman/daf/date missing or ambiguous), ask instead of guessing.

Masechta spellings are Litvish, in English (`Shabbos`, `Kesubos`, `Bava-Kamma`); the site maps
them to Hebrew via the `MASECHTOS` table in `series.html`. Add any missing ones there.

## How the site is built (read before changing anything)

- `index.html`: home page, one card per shiur in the order above, each showing its latest recording.
- `mb.html`: Mishnah Berurah player (David's locked design: limestone/petrol-teal theme, Hebrew
  arch with סימן/ס״ק in gematria, logo base64-embedded). Chelek groups: 1–127, 128–241, 242–344,
  345–428, 429–529, 530–697.
- `series.html?s=oraysa|mishnayos|vaad|sunday`: one shared player; `SERIES` and `VIEW` in the
  script define each series. Vaad/Sunday arches show the Hebrew date (month spellings fixed to
  חשון, סיון).
- Both players draw their own dropdown over a hidden `<select id="pick">`. Android's native
  picker scrambles mixed Hebrew/English, so don't revert that.
- `*.json` lists are **rebuilt by the Action** (`.github/workflows/update-shiurim.yml`) on every
  push touching `audio/`. Don't hand-edit them, except the object form
  `{ "file": …, "date": …, "topic": … }`, which overrides display (used for the 2006-typo files).
- Home page: maggid names in English. Series pages: `מגיד שיעור:` + Hebrew name.
- Hebrew: Litvish pronunciation in transliteration (Shabbos, not Shabbat).

## Rules for changes beyond uploads

- Site/code changes: work on a branch, check at phone width (390px) with Playwright, push, and
  give David the Vercel preview
  (`https://ksgs-dirshu-mb-git-<branch>-rebduv1s-projects.vercel.app`, needs Vercel login).
  Merge only when he says so, unless he asked for the exact change and it can't affect existing
  sections.
- Never rename or delete existing recordings without asking. Never force-push.
- Keep `UPLOADING.md` (for human uploaders) in sync when naming rules change.

## Known quirks

- **Vercel occasionally lags 10–15 min** behind a push. Check `gh api …/deployments?per_page=3`
  before assuming something broke. If nothing arrives after ~15 min, push an empty commit to
  re-trigger.
- `git push` prints "This repository moved" (the repo name's case changed). Harmless.
- GitHub's web uploader caps files at 25 MB; uploads through you have no such limit.
- In shell scripts, don't `pkill -f "http.server…"`: it matches its own command line and kills
  the shell.
