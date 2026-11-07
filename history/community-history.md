# Community history: the Battle Engine Aquila Discord, 2021 to 2026

Status: active — dated history of the community Discord, written from a November 2026 reading; recollection and report, not measurement
Last updated: 2026-11-06 (written from a November 2026 reading of the server; replaces the March 2026 extract)
Summary: what the Battle Engine Aquila Discord server talked about, found and built between January 2021 and November 2026, in period and year order: the former developers who joined, the preservation finds, what players learned about the game, the community projects and the questions still open.

## How to read this

Everything here is a summary in our own words of what members said in the server. It is **recollection and discussion, not evidence**: a developer remembering a detail twenty years later, or a player describing what they saw, is a lead to check, not a measured finding. Where the readers' notes disagreed or a point could not be confirmed, the text says so. Measured findings live in the other folders of this repository; [development-sources.md](development-sources.md) says how each kind of source is weighted.

Members are credited by their Discord handle only, and roles are as the person described them. See [People](#people) and [How this was written](#how-this-was-written).

## At a glance

| When | What happened |
| --- | --- |
| Early Jan 2021 | The server starts; lore and greetings channels are the first to be used. |
| Dec 2021 | Activity picks up and the first preservation finds are shared. |
| Jun 2022 | **desimbr** joins and says he was the lead programmer; former-developer posts begin. |
| Feb 2023 | Server ownership passes to a more active account. |
| Apr 2023 – May 2024 | Discussion of the resource extractor and the in-house tools. |
| Mar 2025 | Speedrun results, and the Toolkit author joins; the 2002 conference deck is shared. |
| Apr 2025 | A second former developer joins and describes the graphics work. |
| Sep 2025 | Onslaught Career Editor v0.1.0 is published. |
| Feb – Jun 2026 | Mapping of the executable, an updated editor release (13 Mar), then the rebuild announced. |
| Oct 2026 | Onslaught CE preview releases (first preview 22 Oct). |
| 6 Nov 2026 | Deten arrives with an independent rebuild. |

## The start: January 2021 to mid-2022

**The server begins.** The server was set up around 7 to 8 January 2021. The first posts are in the lore and greetings channels, with a set of in-universe background notes on the planet, the two sides and their treaty. The first #game-dev message (8 January) asks who the original developers were and whether they were still alive; the first answer was that they probably were. Later in January a few members said they had played the game as children and had come back to it.

**Early shape of the community.** The server stayed very small: about 14 members in February 2022 (Jeppi), and about 25 in September 2025 and again in October 2026. Early activity was players comparing notes, joking in the meme and fan-art channel, and wishing for a remake, sequel or new factions. One member in January 2021 posted a video and said they had contact with a lead designer; the notes could not verify this and the claim is left unconfirmed.

**December 2021: the first finds.** Around this time Jeppi shared an archived copy of the former developer's website (5 December) and a PDF of a cross-platform console-development conference presentation (the notes give 13 September 2021 for the PDF but also place Jeppi's first #game-dev activity in December, so the date is uncertain). Jeppi also posted a diagram naming the on-screen display elements (threat circle, battle line indicator, weapon charge gauge).

**Race challenges.** From March 2021 onwards **vandal_117** and later **charmingad.** and **BermudaMaster** traded notes on the four race challenges (see [what the community learned](#what-members-learned-about-the-game)).

## 2022: a former developer joins

On 21 June 2022 **desimbr** joined and said he had been the game's lead programmer and that the company closed in 2003. He said the released game was complete, not a demo. Over the following days and months he:

- listed ideas that did not make the game (24 June): an optional third-person camera for the Battle Engine, more vegetation and creatures, a cut storyline and a friendly troop-transport vehicle. He said no single major feature was dropped;
- posted early concept sketches of a friendly troop vehicle and a hovercraft (24 June), and said the lead art direction belonged to a named artist;
- linked the 2003 post-mortem from *Game Developer* magazine on archive.org (24 June). He also mentioned a separate write-up by another programmer; no sign of it appears in the channels read;
- described the engine, the grading system and the story revisions (see below);
- said in passing that the .msl script files had accidentally shipped with the PC version (24 June). An earlier reading of the server (March 2026) adds that these were readable text but the game ran from compiled resource data, so editing the text would not change play; the 2026 notes do not repeat this, so treat that detail as less certain;
- described the debriefing-screen Easter egg of the **team's faces** (1 and 26 August), which he said was found by running the PS2 version in an emulator.

**Other 2022 threads.** In July and August **NDjeneralBN** joined and pushed for more cooperative and versus content; **BermudaMaster** explained mission rating factors; members brainstormed remaster and sequel ideas (September). The original owner account went quiet, and **Jeppi** and **Baworo** looked after the server informally (see the 2023 entry).

## 2023 to 2024: quiet years, the extractor, the in-house tools

**2023.** In January members noted the server had gone quiet. On 10 February the owner account handed the server to **Eirinn**, who posted a note that the server would be kept alive instead of left to look abandoned. Through March **desimbr** answered questions: that C++ was normal for console games of the time; that the per-mission grade thresholds are fixed in code (3 March; see the note on disagreement below); and he promised to work out tree and Battle Engine heights (no follow-up appears). On 22 May he shared a public extractor tool he wrote for reading the game's resource archives; this document does not describe or link any extracted content.

**2024.** In May **desimbr** explained again how the game was built: mostly C++ that was not on the game disc, a custom scripting language so that level designers did not need to write C++, and a console-first design (the PS2 had 32 MB of RAM). On 17 May he said the source of an old in-house level editor (C++ with MFC) might still exist, but that he was unsure a working build did (see [open questions](#open-questions)). The server was very quiet from late September 2024.

## 2025: speedruns, the second developer, the conference deck, the save editor

**January and February.** On 19 January **Eirinn** opened a #bugs channel. **BermudaMaster** reported that on the Steam version a Fenrir that should attack the main base when close enough never does (no fix recorded). On 18 January he also reported a graphics-device launch error on a handheld PC; he said on 16 February that the game ran again, without a cause.

**Speedruns.** BermudaMaster posted practice times through the spring and summer: 59:14 on 20 February, then 57:46 on 1 March, and new records on 19 and 30 August (he said a time under 56 minutes seemed possible). He also worked on a single-mission run of the Battle for Yenya (174 attempts by 19 April). The ruleset and category were not stated in the channel; the community leaderboard is the Any% page on speedrun.com (vandal_117, 22 August 2021).

**March.** Dominating David joined (15 March), introduced himself on 18 March and asked for a windowed or borderless-windowed option. On 26 to 28 March he shared the 2002 conference deck "Cross Platform Console Development" from internet archives; **Eirinn** posted an art-pipeline slide from it, and on 28 March he uploaded the original 47-slide version (its file date reads 2002; the archived copy elsewhere in this repository's [development source index](development-sources.md) carries a 2003 web-capture date, so the two dates measure different things). **desimbr** said the console builds were mostly finished by that conference and explained the project codename "Onslaught" and why a second source-control repository, "Onslaught2", existed after the first had trouble.

**April.** On 1 April a second former developer joined, who said he had been technical director at Lost Toys, in charge of graphics (see People). He listed the landscape, shadows, coastline, the sprite system for distant objects ("impostors") and the red-and-blue battle map, and said the game was made for Xbox and PS2 on PCs fitted with a GeForce3 graphics card, using USB adapters for PlayStation pads. He said he had no finished PC source and that a 2001 talk of his on procedural landscapes had vanished from the web; he thought he might still have a copy (no follow-up seen). The maintainer also posted a list of launch options (demo mode, level select, skip intro) and source-file names recovered from the executable's strings (1 to 4 April).

**Summer.** In July a long thread compared a remaster, a remake and spiritual successors; the points raised were keeping the original feel, online play, bigger battlefields and more animation. **BermudaMaster** reported small oddities (the menu opening during the victory screen within a short timing window; holding space repeatedly confirming an options prompt).

**September: the save editor.** On 8 September **superdeveloper1337** joined and asked how to read the .aya data files. On 9 September **Dominating David** published Onslaught Career Editor **v0.1.0** on GitHub, which patches a fresh save. **vandal_117** supplied the all-S-rank save used for the testing. On 10 and 11 September **desimbr** tried patched saves on later levels and reported crashes; the cause (an unlock-all that wrote through a misaligned view) was found on 23 February 2026.

**December.** On 11 December the Toolkit's author asked for source files and learned from **desimbr** that the card-id system came from another studio (Mucky Foot), that a model viewer and cutscene editor existed in development but not in retail builds, and that forced-windowed mode lives in the shell layer. Working through the executable (13 to 14 December) he found cheat strings, the save-version word and the layout of the goodies array, and on 18 December asked testers to try a set of cheat codes. He also posted Onslaught Toolkit screenshots (13 December) and noted that getting rank E for evidence meant playing a level badly on purpose (11 December).

## 2026: the Toolkit, the rebuild, Onslaught CE

**February.** On 10 February a status update said the executable was being mapped and the release had slipped. On 12 February it was reported that the binary has no deliberate obfuscation, shows developer build strings, and has flags for internal tools such as the model viewer and cutscene editor. Screenshots of a windowed-mode patch and a resolution patch followed (11 to 27 February), and **desimbr** explained the Windows startup dialogs as default DirectX 8 SDK ones. The share of the executable mapped was given as 44% on 14 February.

**March.** On 13 March an updated Onslaught Career Editor release was linked; **desimbr** built and ran it on 20 March.

**April to June.** On 10 to 11 May the maintainer announced a faithful rebuild reconstructed from the executable, with mapping progress posted nearly daily to 100% of statically found functions on 26 May and function discovery and auditing on 8 June. On 17 to 25 June a larger release was announced, with the work made public. A newcomer, **Mister S**, joined in June and sent the first outside pull request, which was merged on 21 July (mostly interface work). Two products were described: the companion app and a Godot rebuild; screenshots and clips of the rebuild's level 100 followed in July.

**Late summer.** On 2 August the game's internal logger was switched on (on-screen text plus a log file). On 30 September levels 856 and 858 were reached through launch options. About 115 of 5,482 functions remained on 2 October.

**October: Onslaught CE.** On 15 October a native port for Linux and Windows was announced. **Onslaught CE v0.1.1** (preview) was released on 22 October; the port needs the original game installed. In the days that followed members reported problems and the maintainer shipped quick fixes: a Windows crash after the title music, a download warning on 0.1.9 (29 October, no resolution seen in the channels), a music-overlap bug fixed in **0.1.12** (confirmed by Jeppi on 31 October) and **0.1.13** on 31 October with a new menu for bonus levels, settings and help. The notes disagree on some intermediate version numbers (0.1.3, 0.1.4, 0.1.5 and 0.1.9 all appear; one note also mentions a "1.0.9" that looks like a typo). Online two-player was described on 31 October as expected in 0.2.0 and not yet good. Players' requests included a Battle Engine HUD that scales with resolution, better mouse and dodge/zoom controls, custom loadouts, and better grass, trees and shadows (26 to 28 October). Commander Mode screenshots appeared on 2 November.

**November.** On 2 November the maintainer explained that the native port, built from a decompilation he described as one byte away from matching the original executable, had become the baseline for new work. The Godot rebuild continues in its own repository.

**6 November: a second rebuild.** **Deten** arrived the same day with an independent rebuild. He described running his version inside the original game one function at a time, pinning clocks to make the original deterministic, and a set of 98 recorded playthroughs with AI-only levels and a checker. Problems he listed included a black screen at launch in fullscreen (a lost graphics device that the game never reset), a fullscreen speed gap in his own version (65 against about 300 frames per second, traced to his machine-level copies of original functions), endings that changed with the frame rate, a sound that varied between runs, a dropship in level 3.11 reading uninitialised memory, a NaN compared wrongly in level 4.22, about 50 functions with rounding differences, and test runs corrupting a DirectInput table. The maintainer asked whether he would help play-test multiplayer. Deten asked about the README claim of "8 of 60 levels"; the answer was still pending when the notes end.

## What members learned about the game

Everything in this section is a member's recollection or play report, with the speaker and date in the text.

**The grading system.** **desimbr** said (26 August 2022, from memory) that a hidden score gains points for each kill, with a value that depends on the unit type, and loses points for friendly losses. It is multiplied by a time-dependent factor that starts at 1 and falls. The grade cut-offs are fixed per level. On where they are stored the notes disagree: "in the level data" (26 August 2022) versus "hardcoded in the game code, not the data files" (3 March 2023). A hearsay report says not every level can reach S. A member reported 35 S ranks after resetting the buildings on a level; whether Wake on Ventura can reach S was disputed (Jeppi said B was normally the top, BermudaMaster reported S). BermudaMaster also reported that buildings destroyed on an island stay destroyed in later missions and are sometimes replaced by carriers, which can affect rating.

**Engine facts, from the former developers.** In-house C++ with no third-party engine; DirectX 8 on PC and Xbox with PS2 libraries; the terrain system predates the project, with some code reused from a game finished in 2000; performance trouble came from high-poly models and many units; about 380,000 lines of code and about 500 game objects (both from the post-mortem, via **desimbr**). The story went through many revisions and the cut-scenes came late; split-screen play was an add-on (31 July 2022). The card-id and model-viewer remarks are under December 2025.

**Race challenges.** **vandal_117** (1 April 2021 and 14 February 2022) and **charmingad.** (23 January 2022): the speed boost applies only within a narrow band close to the ground, flying too low stops you, water is easier, and going faster than a missile is one of the checks. The last challenge is easier if the ship and buildings are destroyed first, but not required. A 2013 article had called the challenges unbeatable. What the four challenges unlock was left unspoilt on purpose.

**Mission tips and quirks.** For the Battle of Yenya boss, clear all the buildings (including a small red one) and keep health above 70% for the A/S ranks (BermudaMaster, September 2022). In "Blinding the Enemy", the other turrets reportedly leave once one is destroyed (8 May 2025; not confirmed by a second player). In co-op the red Aquila reportedly shows blue indicators (NDjeneralBN, 28 July 2022, a player observation). A speed-up after tabbing out is open: Jeppi reported on 15 May 2026 that tabbing away during a mission and back runs the game at two to three times speed on a 144 Hz display; the speed resets on a new mission. No reply was seen as of 6 November 2026.

## Community projects

- **Speedruns.** See 2025 above; results are in the Any% leaderboard on speedrun.com.
- **Save editor and Onslaught Toolkit.** Onslaught Career Editor v0.1.0 (9 September 2025), an updated release on 13 March 2026 and the Toolkit screenshots of 13 December 2025 are the same line of work, described in the Toolkit repository.
- **Godot rebuild.** Announced in May 2026; developed in [onslaught-godot](https://github.com/dlprentice/onslaught-godot).
- **Onslaught CE.** Preview from 22 October 2026, 0.1.13 on 31 October; see [onslaught-ce](https://github.com/dlprentice/onslaught-ce).
- **Deten's rebuild.** Independent; announced on 6 November 2026 and public at [deten/battle-engine-aquila-rebuild](https://github.com/deten/battle-engine-aquila-rebuild).
- **The extractor.** A public tool desimbr wrote for reading the game's resource archives. This repository's notes on the formats are under `formats/`.
- **Fan projects.** Members floated a pixel side-scroller (2021), a Unity rebuild (Mr Pluto, 2021) and an open-world Unreal project (Jeppi, March 2023). The notes do not show any of these finished.

## Open questions

- **In-house level editor.** Does a build of it exist? **desimbr** (17 May 2024) thought the source might, a working build less certain. An earlier reading recorded it as having covered landscape, trees, units and buildings.
- **The post-mortem write-up** that was promised in June 2022 was not seen.
- **The former technical director's 2001 procedural-landscape talk.** A copy may exist (1 April 2025); no follow-up.
- **Source for the finished PC version.** The former technical director said (1 April 2025) he had none, and an earlier reading of the server recorded the same recollection from the former team.
- **Whether the console port team had full source.** Asked in December 2025; no answer seen.
- **Did a third-person Battle Engine camera or a friendly troop transport exist in any build?** Only described as ideas.
- **"Death from above"**: BermudaMaster (12 May and 23 August 2025) asked what drives its behaviour.
- **Intro movie.** Whether the short film before the start menu is beta footage (Eirinn, 28 March 2025; no reply).
- **Fenrir attack on the main base** on the Steam version (19 January 2025).
- **Tab-out speed-up** (above).
- **Lore questions** asked in 2021 and 2022 and not answered: whether the island Saracen or the Muspell homeland can be visited in play, and whether the purple creature from the submarine mission appears again.
- **Deten's "8 of 60 levels" question** and the Defender flag on 0.1.9.

## People

Handles as shown in the server. Roles are as each person described them, not verified.

| Handle | Role in the history |
| --- | --- |
| desimbr | Says he was the game's lead programmer; joined 21 June 2022; answered technical questions and wrote a public extractor tool. |
| (a second former developer) | Says he was technical director at Lost Toys, in charge of graphics; joined 1 April 2025. |
| Eirinn | The server's present owner (since February 2023); asks for speedrun times; opened #bugs. |
| Jeppi | Admin and long-time organiser; asked many of the early questions, reports bugs. |
| Baworo | Admin; longest continuous member; proposed fan games and a remake. |
| Dominating David | Posts the Career Editor, Toolkit, rebuild and Onslaught CE releases described above. |
| BermudaMaster | Speedrunner; mission, grading and bug reports. |
| vandal_117 | Speedrun leaderboard poster; race-challenge expert; supplied the all-S save. |
| NDjeneralBN | 2022 member; co-op and versus requests. |
| superdeveloper1337 | Joined September 2025; data-file and crash reports. |
| Mister S | Joined June 2026; sent the first outside pull request (interface). |
| Deten | Joined 6 November 2026; independent rebuild. |
| Others | Antares, BBQ, Kostaki, Mr Pluto, charmingad., min473, Sir Catzilla, The Saltiest Dog, GoofyGodzilla, EmMo and Yonk asked questions or shared ideas. |

## How this was written

This history was summarised in our own words from the server's channels, which were read in November 2026. It is not a transcript, and the notes it rests on were first passes: where two readings disagree, the text says so or leaves the point out. Chat about personal matters was left out on purpose. Members who would rather not be named, or who want a detail changed, can ask for it by opening an issue at https://github.com/dlprentice/onslaught-preservation/issues. It replaces a shorter extract written in March 2026, which remains in this repository's git history.
