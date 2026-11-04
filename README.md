# THUG2 THAW Level Converter

> **Not affiliated with or endorsed by Activision, Neversoft or any other rights holder.** A non-commercial fan project
> for preservation, interoperability and education. No game files are included: the tools work only on copies of the
> games you own. Game names are trademarks of their owners. See LICENSE (MIT for the project's own work; no licence for material
> from the games); third-party parts (downloaded tools) are listed in THIRD_PARTY_NOTICES.md. Rights holders: open an
> issue and it will be dealt with promptly.

Converts the classic levels and the story's areas of your THAW (1.01) install from your own copy of Tony Hawk's American Wasteland (PC) to the formats of Tony Hawk's Underground 2 (PC), for its level
select. Nothing of either game is in this repository: the files are made from your copies each time. How it works,
what is converted and what isn't: [thaw_levels/README.md](thaw_levels/README.md).

- `thaw/` - Tony Hawk's American Wasteland's formats: its level files (`thaw.ts`: archives, scenes, textures, collision, QB node arrays), its level scripts in THUG2's free skate (`thaw_scripts.ts`) and its node flags (`node_flags.ts`).
- `thaw_levels/` - the converter (`thaw_levels.ts`: `convert`, `selfcheck`, `list`) and the THUGPRO comparisons.

## Dependencies

Each pinned to a tagged commit in package.json (`git+https://git@github.com/...#<commit>`; one copy of each: every repository that uses one pins the same commit):

- [thug2-level-converter-core](https://github.com/CanuckAussieKev/thug2-level-converter-core): THUG2's formats (scenes, collision, textures, archives, scripts), the level port's common steps. THAW's own formats are read here (`thaw/`).
- [thug2-thugpro-compat](https://github.com/CanuckAussieKev/thug2-thugpro-compat): the THUGPRO compatibility profile (`--compat`): the register's THAW data - level names, frames, spawns, node and geometry edits, THUGPRO's own content by reference - and its port changes; `thaw_levels/thugpro_compare.ts --extract-thugpro` rewrites it (in a clone of that repository).

Used by: Project 8 is built on THAW's formats, so [thug2-thp8-level-converter](https://github.com/CanuckAussieKev/thug2-thp8-level-converter) (and through it Proving Ground's) reads its node arrays, level scripts and node flags with `thaw/`; the content pipeline's THAW BMX tools read THAW's files with it too.

## Public API

What other repositories may import - package.json's `exports`, nothing else (`import ... from "thug2-thaw-level-converter/thaw/thaw.ts"`):

- `thaw/thaw.ts` - THAW's level files read: archives, scenes, textures, collision and QB (THAW's token form).
- `thaw/thaw_scripts.ts` - THAW's level scripts ported to THUG2's free skate (people, cars, rails, particles) - Project 8's too.
- `thaw/node_flags.ts` - THAW's node flags (the non-story node sets) as THUG2 scripts.
- `thaw_levels/thaw_levels.ts` - the converter's steps (materials, zones, frame shifts): the content pipeline's THAW BMX tools use them.

Commands (package.json's `commands`: the tools with a command line - importing a module never runs one): `thaw_levels/migrate_layouts.ts`, `thaw_levels/thaw_levels.ts`, `thaw_levels/thugpro_compare.ts`, `thaw_levels/thugpro_inventory.ts`.

## Usage

Needs Node 24 and NeverScript's `ns` (the QB script compiler): `npx thug2-neverscript fetch` downloads it for your
system (Windows, Linux, macOS - from [our NeverScript fork](https://github.com/CanuckAussieKev/NeverScript)'s release,
SHA-256 checked) and the converter finds it there; or pass `--ns <ns>` / set `NEVERSCRIPT=<ns>`. Inside the
[THUG2 decompilation](https://github.com/CanuckAussieKev/thug2-decomp) its own build is used
(`tools\build_neverscript.ps1`). The converter needs only THUG2's data files, not its exe.

    git clone https://github.com/CanuckAussieKev/thug2-thaw-level-converter
    cd thug2-thaw-level-converter
    npm ci
    npx thug2-neverscript fetch
    node thaw_levels/thaw_levels.ts convert Z_CH --thaw "C:\Games\THAW" --thug2 C:\Games\THUG2
    node thaw_levels/thaw_levels.ts selfcheck --thug2 C:\Games\THUG2

`convert` writes `<thug2>\thug2-decomp-thaw\` (not read by the game) - or `--out <folder>`, which must be new, empty or an earlier output (any other folder is refused,
nothing in it touched); naming levels converts only those and keeps the others; without level names it converts the default
set (thaw_levels/README.md). The [THUG2 content pipeline](https://github.com/CanuckAussieKev/thug2-content-pipeline)'s
`menu_level/make_menu_level.ts` then installs every converted game's levels into the game's data overlay and level
select. In the decomp, where the pipeline installs this converter, the same commands are

    node tools/pipeline/run.ts thaw_levels/thaw_levels.ts convert --thug2 C:\Games\THUG2
    node tools/pipeline/menu_level/make_menu_level.ts --game C:\Games\THUG2

Checks: `npm run typecheck`, `npm run lint`, `npm test` (the tests that need a game install skip without it;
`package.test.ts` checks package.json's exports, commands and pins). GitHub Actions runs them on every push
(`.github/workflows/ci.yml`).

## Versions

Releases are tagged `v<major>.<minor>.<patch>`; this is `v1.4.1` (core v5.2.1 / compat v1.0.7 (a partial convert keeps the other levels' people in peds.json); behaviour unchanged); `v1.4.0` (core v5.2.0: a kill only while one of THAW's goals is on isn't a kill zone - Downtown's Line03 and photo-goal walls no longer reset the skater in free skate); `v1.3.1` (imports from core v5.0.0's numbers.ts / text.ts / json.ts / cli.ts (its py.ts split, names for what they do); behaviour unchanged); `v1.3.0` (paths written with / (path.join) instead of pyJoin and \-strings; core v4.0.0); `v1.2.1` (core v3.1.0: runs on Linux and macOS with the same output as on Windows; CI on all three); `v1.2.0` (ns found or fetched without the decomp, safe output folders, partial converts keep the other levels; core v3.0.0); `v1.1.0` (THUGPRO's profile from thug2-thugpro-compat; core v2.0.0). The repositories that use it pin the tagged commit.
