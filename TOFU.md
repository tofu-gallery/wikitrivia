# Wikitrivia on Tofu

Wikitrivia is a history timeline card game by Thomas James Watson. Players put cards based on Wikidata and Wikipedia in chronological order, in daily challenges or free play. The upstream name, visual identity, favicons, bundled decks, and attribution links are retained.

## Running the app

- Tofu framework preset: `nextjs`.
- Application root: the repository root.
- Package manager: Bun 1.4.2, using the upstream `bun.lock` format.
- Install: `bun install --frozen-lockfile`.
- Build: `bun run build` (`next build`).
- Build output: `.next/`, served by Next.js. This branch does not produce the upstream `out/` static export.
- Start: `bun run start` (`next start`); the host can set `PORT`, or pass `--port`.
- Runtime: Node.js 22 was used for local verification; Next.js 16 requires Node.js 20.9 or newer.
- Required environment variables: none.
- Recommended platform setting: `NEXT_TELEMETRY_DISABLED=1` to disable Next.js build telemetry; it is not required for game functionality.
- Database: none. Scores, difficulty preferences, and daily progress are saved in the visitor's browser. They are not synchronized between devices.

The game uses committed JSON decks under `public/decks/` and bundled fonts. Build and play do not regenerate decks, call a paid API, or need an account or secret. Card images load from `upload.wikimedia.org`, so illustrations require internet access. Wikipedia/Wikidata links and the upstream project's links remain available. Wikimedia media retain their own licenses; the code's MIT license does not relicense them.

`WIKITRIVIA_CONTACT_EMAIL` in `.env.example` is only for a maintainer who intentionally runs the optional Wikimedia content-refresh tools. It is not needed for installation, building, deployment, or playing. Read `content/README.md` before updating content; this fork uses the supplied snapshots.

## Adaptations

- Removed `output: "export"` from `next.config.js` and changed the start script to `next start`, so Tofu can use its standard Next.js preset and serve game routes directly.
- Replaced the GitHub star-count widget with an ordinary link to the same upstream repository. The widget loaded executable content from unpkg and contacted GitHub just to render the footer; the ordinary link keeps the credit without those automatic requests.
- Removed the unused `react-github-btn` and static `serve` packages, retaining a Bun lockfile, and recorded the verified Bun version in `packageManager`.
- Kept Next.js's generated `next-env.d.ts` production route-types import, so a production build refers to `.next/types/routes.d.ts` instead of the upstream development-only path.
- Added this file to explain the hosting contract and the remaining external image dependency.

No analytics, advertising, tracking integration, Tofu-owned service credential, or database has been added. The normal production share links already use the current site's origin, so that code is unchanged.

## Upstream and license

- Repository: https://github.com/tom-james-watson/wikitrivia
- Upstream branch: `master`.
- Base commit: `18d12eb3e305ecce56bac5d8b3cc5939f70d7856`.
- Fork: https://github.com/tofu-gallery/wikitrivia
- `master` and `main` mirror that upstream commit; only `tofu` contains adaptations.
- Release pin: `pin/2026-10-23`, created only after local verification passes.
- Code license: MIT. `LICENSE.md` and its copyright notice are preserved verbatim.

## Verification

Verified locally on 2026-10-23 with Node.js `v22.22.3` and Bun `1.4.2`. This work does not create a Tofu app or deploy one.

Commands, with this repository as the working directory:

```sh
bun install --frozen-lockfile
bun run lint
bun test
bun run format:check
NEXT_TELEMETRY_DISABLED=1 bun run build
NEXT_TELEMETRY_DISABLED=1 bun run start --hostname 127.0.0.1 --port 4379
curl --fail --silent --show-error --max-time 15 http://127.0.0.1:4379/ --output /dev/null --write-out '%{http_code}\n'
curl --fail --silent --show-error --max-time 15 http://127.0.0.1:4379/decks/index.json --output /dev/null --write-out '%{http_code}\n'
curl --fail --silent --show-error --max-time 15 http://127.0.0.1:4379/daily --output /dev/null --write-out '%{http_code}\n'
curl --fail --silent --show-error --max-time 15 http://127.0.0.1:4379/play/history/wars --output /dev/null --write-out '%{http_code}\n'
```

Installation, lint/typechecking, formatting, and the production build passed. The upstream tests passed: 28 tests across 6 files, zero failures. Next.js generated 299 static pages and started its production server successfully. All four HTTP checks returned 200.

A real browser check covered the home page at 375 px and 1440 px widths, navigation into the daily challenge, loading its local decks and card images, and starting free play at `/play/history/wars`. A real pointer drag placed a free-play card on the timeline, increasing its card count from one to two. Each flow ended with no captured console errors. The home page made no cross-origin resource requests and had no horizontal overflow at either checked width. The smoke test did not play every deck or complete a whole game.

The verification server and browser tabs were stopped after testing.
