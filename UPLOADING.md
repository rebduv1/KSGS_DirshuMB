# Uploading a shiur — Khal Sunset Grove Sefard

Uploading the recording is the only step. The website updates itself within about a minute.

## 1. Name the file

The name tells the website which shiur it is and what to show. Use hyphens instead of spaces, and write dates as **year-month-day**.

| Shiur | Pattern | Example |
|---|---|---|
| **Oraysa** | `ORAYSA-Masechta-DafAmud-Date.mp3` | `ORAYSA-Shabbos-024a-2026-10-20.mp3` |
| Oraysa, more than one amud | `ORAYSA-Masechta-DafAmud-to-DafAmud-Date.mp3` | `ORAYSA-Shabbos-024a-to-025b-2026-10-20.mp3` |
| **Thursday Night Vaad** | `VAAD-Date-Topic-Words.mp3` | `VAAD-2026-10-22-Hakaras-HaTov.mp3` |
| **Sunday Shiur** (R’ Pinchas Friedlander) | `FRIEDLANDER-Date-Topic-Words.mp3` | `FRIEDLANDER-2026-10-18-Emunah-and-Bitachon.mp3` |
| Dirshu Mishnah Berurah | `MB-OC-Siman-SkFrom-SkTo-Topic.mp3` | `MB-OC-445-005-012-October-16-2026-Chometz-Naaseh-Gecholim.mp3` |

- **Daf**: always three digits — `002`, `024`, `112` — followed by `a` or `b` for the amud.
- **Masechta**: in English, as normally spelled (`Shabbos`, `Bava-Kamma`, `Kesubos`, `Menachos`…). The site shows it in Hebrew.
- **Date**: `2026-10-22`, not `10-22-2026` or `October 22`.
- Avoid apostrophes and commas in topics.

## 2. No need to compress

The site compresses large recordings by itself right after upload, keeping the same name. The one limit is GitHub's: a file uploaded through the website can be at most **25 MB**. If a long recording is bigger than that, drag it onto `compress-shiur.bat` first, or send it to David.

## 3. Upload it

1. Go to **github.com/rebduv1/KSGS_DirshuMB** and open the **audio** folder.
2. Click **Add file → Upload files**.
3. Drag the mp3 in, then click **Commit changes** at the bottom. Leave the default settings as they are.

Within a minute or two the shiur appears on the site. To check, open the repo's **Actions** tab: a green check means it worked. A file whose name doesn't match a pattern above won't appear, and the Action's log will say which file it was.

## Fixing a mistake

- **Wrong name:** open the file in the audio folder, click the **…** menu → **Rename**, and fix the name.
- **Wrong recording:** open it, click **…** → **Delete file**.

The website updates itself either way.

## Getting access

Uploading requires a free GitHub account that has been added to the repository. Ask David to invite you: in the repo, **Settings → Collaborators → Add people**.
