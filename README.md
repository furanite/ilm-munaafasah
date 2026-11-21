# Munaafasatul Ilm (منافسة العلم)

The Islamic edition of Get Grilled, a quiz-board game for team competitions, families, classes and masjid
events. This site shows only the Islamic and Kids collections. The code is the same as the Get Grilled
app; `npm run build:islamic` builds the edition (it sets `PACK_EDITION=islamic` and the Islamic themes).

Live site: https://furanite.github.io/ilm-munaafasah/ (host edition at `/host/`).

**This repository's `main` is a mirror.** App and question changes are made on the Get Grilled branch
`claude/project-thread-1dkosk` (repository `furanite/get-grilled`) and synced here; direct changes to
app files are overwritten by the next sync.

## What's in it

- **47 categories:** 40 Islamic (Qur'an, Hadith, Prophets, Aqidah, Fiqh including the four schools and food
  and drink, Islamic history, Tazkiyah, Arabic, and Nasheeds, Ilahis and Naats) and 7 Kids categories.
- **Three languages:** every category, the tips, the rules and the themed nights in English, Arabic and
  Urdu, with an Urdu interface and Urdu Terms and Privacy (the Arabic Terms are binding). Calligraphic
  lettering per theme (Ruqaa, Kufic styles, Nastaliq for Urdu).
- **School of fiqh picker:** fiqh lines are tagged by school (`scripts/schools.mjs`); untagged lines are
  agreed by the four schools and always shown.
- **Themes:** 24 themes from Islamic art, the prayer times and Muslim cultures, plus Eid of the Mu'mineen
  (Thursday Maghrib to Friday Maghrib) and Blessed Mawlid (Sunday Maghrib to Monday Maghrib). Maghrib is
  computed for the host's own location (asked only after an explanation, rounded to 0.1°, kept on the
  device) or the device's time zone (`src/lib/place.ts`).
- **Sounds:** breeze, birdsong and a deep daff only, with no melodic instruments (`src/lib/sound-isl.ts`).
- **10 themed nights**, including Ramadan Special, Hajj and Eid al-Adha, Blessed Mawlid Night and a night
  for each school of fiqh (`questions/nights-islamic.json`).
- **Shared with Get Grilled:** golden, steal and audience tiles, streaks, comeback, lightning round, final
  question, tie-breaker, tournaments, category vote, special rounds (where the chosen categories offer
  them), team introductions, time limits, game log and judges' sheet, backup sheet, bracket, waiting
  screen, practice, question files, display options, offline use, buzzers and the Raspberry Pi version.
- **Read-aloud** (off by default) never reads a clue that names a Prophet or Companion, and never
  machine-recites Qur'anic text.
- **Scholar review page** at `#review`; status in `questions/REVIEW-STATUS.md`.

## Run it

    npm ci
    npm run dev            # local development
    npm run build:islamic  # the Islamic edition, app and host edition, in site/ and site/host/
    npm run build:pi       # the Islamic site plus the Raspberry Pi server files

## Hosting

Every push to `main` builds and publishes both versions to GitHub Pages (`.github/workflows/deploy.yml`):
the app at `/` and the host edition at `/host/`. Settings > Pages > Source must be **GitHub Actions**.
The site keeps its own browser storage keys (`gg.<name>.ilm.v1`), separate from Get Grilled on the same
domain.

## Raspberry Pi and buzzers

The Pi version uses `http://ilm.local`, the `ilm` command, and releases from this repository (needs its own
`PI_SIGNING_KEY` secret before the first release). It runs on its own microSD card, so one Pi can serve both
games by swapping cards. See `pi/README.md`. The buzzer box (Raspberry Pi Pico) is described in `buzzer/`
and in the printable picture guide `public/buzzer-guide.html`. Neither has been tried on real hardware yet.

## Questions

Built-in questions live in `questions/*.txt`, one file per category:

    id: kids-prophets
    name: Stories of the Blessed Prophets ﵈
    nameAr: قصص الأنبياء ﵈
    nameUr: انبیاءِ کرام ﵈ کے قصے
    group: kids
    ## 100
    Clue text :: Answer text || H: source

Arabic and Urdu go in `questions/ar/` and `questions/ur/` (one line per English question, same order).

A line carries a source tag after ` || `: `H:` historical (give the source), `D:` dramatised, `F:`
fictional. Add `?` (for example `H?:`) to hold a line back until someone checks it; held lines are listed in
`questions/TO-VERIFY.md`. Every line in the Islamic and Kids collections must carry a tag. Read
`questions/ISLAMIC-STYLE.md` before adding to them: it sets the honorifics (ﷺ with "our Beloved and our
master", ﵇ for Prophets and angels, ﵁ for a Companion, رضوان الله عليهم written out for several, رحمة الله
عليه for scholars and awliya'), "passed away", "Blessed Seerah" and "Blessed Mawlid", and what is never
shown. The honorific and translation tests enforce these rules.

`npm run packs` checks every file (duplicates, missing answers, empty levels, source tags, translations) and
writes `public/packs/`. A game draws one question per level from each pool and avoids repeats until a pool
runs out. Every question must stand alone, because packs are drawn at random.

## Tests

    npm test               # unit tests (game logic, packs, honorifics, translations, Terms, storage)
    npm run e2e            # browser tests: full games, every theme, Arabic, builder, phone width
    npm run e2e:host       # host edition and audience window
    npm run e2e:extras     # special tiles and rounds
    npm run build:pi && npm run e2e:pi   # Pi server, devices, audience phones
