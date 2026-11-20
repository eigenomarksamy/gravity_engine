# gravity engine plan

a 2D N-body sandbox for exploring gravitational interaction, orbits and numerical integration. users create bodies, adjust their initial conditions, and discover stable and unstable systems, with the energy drift and stability diagnostics in view. the simulation runs in C++20, compiled to WebAssembly for the browser and natively for tests and benchmarks, with no server. it is planned for `gravity.eigenomar.com`.

nothing is built yet. this plan holds the bootstrap (B1), the MVP (M1–M5) phase 2 (L1–L6), the adoption of the ecosystem's standards (DOC1, SCM1), and the shared decisions they rely on. the gravity engine is the third of three C++ labs, after `pathfinding_playground` and `signal_lab`, each in its own repository. it reuses the pathfinding playground's bootstrap approach, not its code. work across the three, such as their release order or a library they might later share, is coordinated in the ecosystem, not planned here.

## how this plan works

- **tickets.** every ticket has an id, a lane prefix and a number, never reused or renumbered. a split ticket keeps its number and adds a letter (M2a, M2b). each ticket has a stable anchor, its id in lowercase (`#m1`), above its heading, which doesn't change when the heading does.
- **lanes.**

  | prefix | lane |
  | --- | --- |
  | B | the bootstrap: native, WebAssembly and web builds, CI and the deploy |
  | M | the MVP, released at `gravity.eigenomar.com` |
  | L | phase 2: later work, after the MVP's release |
  | DOC | the lab's documents: vision, architecture, design, testing strategy, and verification and validation |
  | SCM | source control and delivery: CI, branching, protection, releases and versioning |

- **each ticket** says what to do, what it needs, and when it is done. its "done when" is its acceptance criteria. a need is a hard need: the ticket starts once it is done. a ticket in another repository is named with that repository, as `pathfinding_playground` B1.
- **size** is the index's column, on the ecosystem's scale, the same value as the ticket's issue's `Size` in the ecosystem's GitHub Project: xxs takes minutes, xs under an hour, s an hour or two, m about a session, l two or three, xl four to six and xxl more. tbd means an open decision or work not yet planned decides it. a change to one is made to the other in the same session.
- **issues.** every open ticket gets one GitHub issue in this repository. the issue's title starts with the ticket's id, and its body links the ticket's anchor here without copying it. the ticket holds the scope and history, and the issue holds status, priority and blockers.
- **shared decisions** bind this lab and other repositories. this plan states each outcome it relies on in one line and cites the decision by its id. a change to one is made in the decision first.
- **adding work.** new work goes here as a ticket in a lane. this repository is public, so confidential work never goes in this file. the ecosystem's shared rules, which `CLAUDE.md` imports, say where it goes.
- **when work ships,** update the ticket's status in the index, add a result with the commit to the ticket, and add a line to the [log](#log).

## shared decisions

the gravity engine and the creative coding gallery's simulations, such as its proposed orbital dreams, are two unrelated environments serving different purposes, which share no code and don't wait on each other: shared decision SD4.

the three C++ labs share their constraints, stack, architecture and definition of done as shared decision SD3, which makes no shared package mandatory. the outcomes this lab relies on:

- **its own app:** it works on its own at its own URL, with no account, login, payment or database, and needs no backend, external API, uploaded data or real-time third-party service.
- **usable at once:** the default experience starts with sample scenarios and sensible parameters.
- **controls:** play, pause, reset and step.
- **seeds:** anything random, such as the multi-body preset, takes a reproducible seed, and a preset gives the same outcome for the same configuration.
- **honest models:** educational approximations are clearly told apart from validated models; this lab's defaults are chosen for exploration, not scientific calibration.
- **access:** controls work by keyboard and the layout is responsive; the visualization may favor desktop, with a working mobile fallback.
- **motion:** reduced-motion preferences are honored for nonessential animation, and nothing flashes excessively or animates without a way to pause it.
- **privacy:** no analytics or tracking by default; only optional preferences are kept, locally.
- **identity:** the three labs look consistent, without a shared frontend package before one is needed.
- **footer:** the ecosystem's button to `https://eigenomar.com`, labelled `eigenomar.com`, a plain link in the app's own button style, opening in a new tab here, since leaving would lose an edited scene.
- **C++20:** the engine is C++20; C++23 features wait until the pinned compiler and Emscripten are shown to support them.
- **separation:** the engine is separate from rendering, browser events and framework code; simulation state and the numerics stay in C++, and TypeScript does the controls, state coordination and drawing.
- **the boundary:** a small, documented interface to JavaScript, through Embind at first, transferring batched snapshots rather than one call per body.
- **two builds:** one engine source compiles to a native build and a WebAssembly build.
- **C++ habits:** RAII, clear ownership, `const` correctness, explicit types, `std::span` and standard containers, without premature templates or allocations in frame-critical paths.
- **steps:** the simulation advances in fixed steps, decoupled from the visual frame rate.
- **limits:** inputs are bounded and invalid parameters handled, so no scenario can freeze the tab.
- **threads:** single-threaded at first, with a standard Web Worker for long computation if needed; C++ pthreads aren't a baseline.
- **metrics:** performance is measurable, here as execution time, simulation steps and energy drift.
- **portability:** it builds, runs and tests on Windows and Linux. there is no code yet; B1's CI on `ubuntu-latest` covers Linux from then on.
- **the stack:** CMake, Emscripten and GoogleTest with CTest for the engine; React, TypeScript and Vite with Canvas 2D for the app, WebGL2 only if profiling justifies it, tested with Vitest and Playwright; clang-format, clang-tidy, compiler warnings and sanitizers in native CI; GitHub Actions for CI and deploys; Cloudflare Workers static assets on its own subdomain for hosting.
- **dependencies:** few and pinned, through one dependency-management method chosen when implementation starts, such as CMake's `FetchContent` at pinned revisions, and no heavyweight simulation framework.
- **the architecture:** a React and TypeScript UI, over a typed Wasm adapter (input validation and conversion, batched snapshots or events, the module's lifecycle and errors), over a WebAssembly module of the C++20 engine, which a native test and benchmark executable also builds.
- **the layout:** the suggested one, with `cpp/` (include, src, bindings, tests), `web/`, `benchmarks/`, `docs/`, `CMakeLists.txt`, `CMakePresets.json`, `wrangler.jsonc` and `.github/workflows/` beside `CLAUDE.md`, `project.yaml`, `README.md`, `LICENSE` and `.gitattributes`.
- **no shared library yet:** common code is extracted only once at least two labs need it.
- **done:** each public release meets SD3's definition of done, which [M5](#m5)'s done-when cites.

every app under eigenomar.com shows the same tab icon, eigenomar.com's lambda with its two colors swapped, a light-blue tile and a near-black lambda: shared decision SD5. [B1](#b1)'s shell takes it, so the lab is never deployed without it.

## index

| ticket | title | size | needs | status |
| --- | --- | --- | --- | --- |
| [B1](#b1) | bootstrap: native, WebAssembly and web, built and deployed from GitHub Actions | l | none | todo |
| [M1](#m1) | vector and body model, units, pairwise gravitational force, unit tests | s | B1 | todo |
| [M2](#m2) | fixed-step leapfrog integration, diagnostics and snapshots | m | M1 | todo |
| [M3](#m3) | presets, body editing, trajectories, zoom and controls | l | M2 | todo |
| [M4](#m4) | numeric safeguards, timestep and body-count limits, benchmark | m | M3 | todo |
| [M5](#m5) | release: deployment, explanation of numerical tradeoffs, and README | m | M4 | todo |
| [L1](#l1) | selectable integrators, with stability comparisons | m | M5 | todo |
| [L2](#l2) | collision detection and optional body merging | m | M5 | todo |
| [L3](#l3) | Barnes-Hut, an approximate accelerated solver for larger systems | l | M5 | todo |
| [L4](#l4) | orbital elements, barycenters and further diagnostics | m | M5 | todo |
| [L5](#l5) | high-density rendering, saved scenes and shareable configuration links | l | M5 | todo |
| [L6](#l6) | optional 3D mode | tbd | L1–L5 | todo |
| [DOC1](#doc1) | the lab's documents | tbd | `meta-eigenomar` ST1 | todo |
| [SCM1](#scm1) | the ecosystem's SCM strategy | tbd | `meta-eigenomar` ST2 | todo |

## bootstrap

the builds, checks and deploy every later ticket uses, set up once in this repository. the pathfinding playground's bootstrap, `pathfinding_playground` B1, works out the approach for all three labs: this ticket reuses that approach and the lessons in its result, not its code, so nothing here depends on that repository.

<a id="b1"></a>

### B1 · bootstrap: native, WebAssembly and web, built and deployed from GitHub Actions

- **do:** the checklist below, following `pathfinding_playground` B1's approach. this ticket adds the repository's first code, its CI and its deploy.
  - **portability:** `CMakePresets.json` has native presets for Windows (MSVC or clang-cl) and Linux (GCC or Clang), and a WebAssembly preset through Emscripten. Emscripten comes from `emsdk`, which runs on Windows natively. no build step exists only as a `.sh` or `.ps1` script, and nothing needs WSL. CI runs the native tests, with sanitizers, and the WebAssembly build on `ubuntu-latest`.
  - **delivery:** Cloudflare Workers serve the static assets on `gravity.eigenomar.com`. GitHub Actions builds the WebAssembly module and deploys the result, since Cloudflare's own build doesn't run Emscripten, on every merge to `main`.
- **needs:** nothing in this repository. it comes after `pathfinding_playground` B1, whose approach it reuses: a planning dependency, so its issue lists that ticket's issue as a `planning:` line under related work, not as a blocker.
- **done when:**
  - [ ] a minimal C++20 library builds with CMake, with native tests and one benchmark target.
  - [ ] that library compiles with Emscripten, and TypeScript calls a simple function it exports.
  - [ ] a stable typed adapter handles Wasm initialization, errors and data transfer.
  - [ ] linting and formatting, native sanitizers and CI checks are configured.
  - [ ] it builds natively on Windows and in CI on Linux, with Emscripten from `emsdk` on both, and the native tests pass on Windows started from `c:\` as well as from `C:\`.
  - [ ] a minimal Vite and React shell has a canvas, basic controls and an about/source panel.
  - [ ] the shell's tab shows the ecosystem's icon (shared decision SD5), served from the app's own assets, in a light and a dark browser theme.
  - [ ] a production static deployment on Cloudflare, built and deployed from GitHub Actions, answers at `gravity.eigenomar.com`.
  - [ ] the README lists what to install on each platform: the compiler, CMake, `emsdk` and Node. `CLAUDE.md`'s commands section lists the setup, build, test and run commands, each working on Windows and Linux.
  - [ ] in the same change as the deploy, `project.yaml`'s `delivery` says `ci: github-actions`, `deployment: cloudflare` and `deploys_on: merge`, its `links.deployment` is `https://gravity.eigenomar.com`, and `CLAUDE.md`'s delivery section says what CI runs and that every merge to `main` deploys. the ecosystem's validator passes.
  - [ ] the result records where this bootstrap differs from `pathfinding_playground` B1's approach, and why, for the ecosystem's conventions on native builds.

## MVP

stage 3 of the labs' workflow: one polished, bounded release at `gravity.eigenomar.com`. the force model, the integrator and their tests come before the interface, so M1 and M2 come before M3. the MVP's acceptance criteria are spread over M1–M5's done-whens, each in the ticket that delivers it, and M5 releases the MVP once they all hold.

**scope:**

- add, move, and remove bodies with mass, position, and initial velocity.
- play, pause, step, reset, simulation speed, and camera zoom/pan.
- real-time trajectories with adjustable trail length and fading.
- presets: two-body circular-ish orbit, eccentric orbit, three-body interaction, and a seeded multi-body system.
- implement Newtonian pairwise gravity with a **fixed simulation timestep** and a **velocity-Verlet / leapfrog** integrator.
- show time elapsed, number of bodies, total energy, and relative energy drift where meaningful.
- option to toggle a softening parameter, with a visible explanation of its physical/numerical consequence.
- show orbits and body velocities visually without requiring knowledge of orbital mechanics.

**numerical and simulation constraints:**

- use a documented unit system and gravitational constant (physical SI or normalized simulation units). defaults should be chosen for exploration rather than implying scientific calibration.
- use double precision for simulation state where supported; render a converted snapshot.
- keep the core **O(N²)** pairwise calculation for the MVP. make practical body-count limits explicit.
- do not link simulation integration to monitor refresh rate. use fixed `dt` and a bounded number of updates per render frame.
- surface timestep and softening tradeoffs. with close encounters, energy drift or unstable behavior can occur; the UI should communicate this rather than silently correcting physics.
- disable collisions/merging by default for the MVP; if bodies overlap, define and document the softening behavior.
- numeric health checks should detect non-finite positions/velocities, impossible parameter values, and runaway scenarios.
- determinism is defined for the same engine build, initial state, timestep, and seed; do not promise bitwise equality across every browser/CPU.

**proposed C++ modules:**

```text
body / vector / units
initial-condition presets
pairwise gravitational force
velocity-Verlet / leapfrog integrator
fixed-step world simulation
energy and momentum diagnostics
snapshots + camera-independent rendering data
```

<a id="m1"></a>

### M1 · vector and body model, units, pairwise gravitational force, unit tests

- **do:** the 2D vector type and the body model, each body with a mass, a position and a velocity, in double precision; the documented unit system and gravitational constant, physical SI or normalized simulation units, with defaults chosen for exploration; and the Newtonian pairwise gravitational force, the O(N²) calculation, with an optional softening parameter whose behavior when bodies overlap is defined and documented. collisions and merging are off.
- **needs:** B1.
- **done when:**
  - the force between two bodies matches independently computed reference cases within a tolerance the test states.
  - for every pair, the force on one body is equal and opposite to the force on the other, and a test checks it over a seeded set of bodies.
  - with softening on, two overlapping or coincident bodies give a finite force as documented, and with softening off the case is defined and documented, and tests check both.
  - the unit system and the gravitational constant are documented in the engine's interface, and a mass that isn't positive or a non-finite position or velocity is rejected with an error.

<a id="m2"></a>

### M2 · fixed-step leapfrog integration, diagnostics and snapshots

- **do:** the velocity-Verlet / leapfrog integrator and the fixed-step world simulation: a fixed `dt`, independent of any frame rate, and a bounded number of steps per call. the diagnostics: time elapsed, number of bodies, total energy, total momentum and relative energy drift where meaningful. each step yields a rendering-neutral snapshot in camera-independent coordinates: the bodies' positions, velocities and masses, and the diagnostics.
- **needs:** M1.
- **done when:**
  - the two-body and symmetric baseline cases behave plausibly for documented settings: a circular-ish two-body orbit stays near its radius and period over a long run, and a symmetric configuration keeps its symmetry, within tolerances the tests state.
  - with softening off and no external forces, total momentum is conserved to within a tolerance the test states.
  - the relative energy drift of stable scenarios stays bounded over a long run, and a test checks it at the documented `dt`.
  - the same build, initial state, timestep and seed give identical snapshots, and stepping one step at a time gives the same state as running the same number of steps at once, and tests check both.
  - a call never runs more than its bounded number of steps, whatever it is asked for.

<a id="m3"></a>

### M3 · presets, body editing, trajectories, zoom and controls

- **do:** the MVP's scope in the app, on B1's shell: the initial-condition presets, a two-body circular-ish orbit, an eccentric orbit, a three-body interaction and a seeded multi-body system; adding, moving and removing bodies, with their mass, position and initial velocity; real-time trajectories with adjustable trail length and fading; the controls, play, pause, step, reset and simulation speed; camera zoom and pan; the softening toggle, with a visible explanation of its physical and numerical consequence; the diagnostics panel; and the bodies' velocities drawn so orbits read without knowledge of orbital mechanics. the engine's Embind interface hands the app M2's snapshots in batches, through B1's typed adapter, and the app renders a converted snapshot, never the engine's own state.
- **needs:** M2.
- **done when:**
  - each preset loads deterministically, the seeded one with its seed, and reset returns it to its original state, and tests check both.
  - paused and single-step modes give consistent, inspectable results: each step advances by exactly one fixed `dt`, and the diagnostics shown match the engine's snapshot.
  - bodies can be added, moved and removed, and their mass, position and velocity edited, only within their bounds, by pointer and by keyboard.
  - trail length and fading change the drawn trajectories, and zoom and pan move the camera without changing the simulation.
  - toggling softening changes the simulation as its explanation says.
  - snapshots cross the Wasm boundary in batches, never one call per body, the native and WebAssembly builds pass the same correctness fixtures, and invalid input comes back through the adapter as a typed error, not a crash.
  - the footer's `eigenomar.com` button opens `https://eigenomar.com` in a new tab, so an edited scene survives.

<a id="m4"></a>

### M4 · numeric safeguards, timestep and body-count limits, benchmark

- **do:** the numeric health checks, detecting non-finite positions or velocities, impossible parameter values and runaway scenarios, each surfaced in the app rather than silently corrected; the limits on `dt`, simulation speed, steps per render frame and body count; and a benchmark of the engine's step time, natively and in WebAssembly, measured apart from Canvas drawing, that sets the documented body-count target. higher counts are profiled before being enabled, and a Web Worker is used only if the steps can't finish within the frame budget.
- **needs:** M3.
- **done when:**
  - no input, preset or edited scene can start an unbounded compute loop: steps per render frame are capped, and a test checks the cap at the highest simulation speed.
  - a non-finite position or velocity, an impossible parameter or a runaway scenario is detected, the simulation pauses or stops with a visible explanation, and tests check each case.
  - close encounters that raise the energy drift are shown in the diagnostics, not hidden.
  - the benchmark runs from the repository with documented conditions, and its results set a documented default body-count target, between 20 and 100 as suggested; counts above it stay disabled until profiled.

<a id="m5"></a>

### M5 · release: deployment, explanation of numerical tradeoffs, and README

- **do:** the MVP's validation on the deployed build, the explanation of its numerical tradeoffs in the app, an accessibility pass, the app's tests, the MVP's deployment at `gravity.eigenomar.com` through B1's pipeline, and the README.
- **needs:** M4.
- **done when:**
  - shared decision SD3's definition of done for each public release holds for everything this repository delivers. its items that other repositories deliver once the demo is live, the site's software entry among them, follow this ticket.
  - the deployed demo, the public README and the source link work without a backend.
  - the default scenario renders smoothly on a typical desktop at the documented body-count target, and every performance figure the README gives names the hardware, browser and conditions it was measured under.
  - the public app explains its chosen integrator, timestep, units, softening and limitations, including that its defaults are chosen for exploration and that determinism holds for the same build, initial state, timestep and seed, not bitwise across every browser or CPU.
  - every control works by keyboard, the layout has a working mobile fallback, and a reduced-motion preference stops nonessential animation.
  - Vitest covers the app's logic and a Playwright smoke test the deployed build, both in CI.
  - M1–M4's done-whens hold on the deployed build.

## phase 2

not MVP. it starts once the MVP is released ([M5](#m5)): accelerated N-body solvers and 3D are later work by design. each ticket uses the MVP's engine, snapshots and diagnostics where it can.

<a id="l1"></a>

### L1 · selectable integrators, with stability comparisons

- **do:** selectable integrators (Euler, RK4, leapfrog), with explicit stability comparisons.
- **needs:** M5.
- **done when:** each integrator matches reference cases within a stated tolerance, tests compare their energy drift on the same stable scenario and seed, and the app runs them side by side on one scene and explains the difference.

<a id="l2"></a>

### L2 · collision detection and optional body merging

- **do:** collision detection and optional body merging, still off by default.
- **needs:** M5.
- **done when:** tests check that a collision is detected at the expected step and that a merge conserves mass and momentum. the app documents the collision rule and how it interacts with softening.

<a id="l3"></a>

### L3 · Barnes-Hut, an approximate accelerated solver for larger systems

- **do:** an approximate accelerated solver such as **Barnes-Hut** for larger systems, beside the exact O(N²) one.
- **needs:** M5.
- **done when:** tests check its forces against the exact solver within an error the test states for its opening angle, the benchmark measures both natively and in WebAssembly under documented conditions, and higher body counts are enabled only up to what that measurement supports. the app says the solver is approximate.

<a id="l4"></a>

### L4 · orbital elements, barycenters and further diagnostics

- **do:** orbital elements, barycenters, and additional scientific diagnostics.
- **needs:** M5.
- **done when:** tests check the orbital elements and the barycenter of known two-body cases, and the app shows them with their units and the conditions under which they are meaningful.

<a id="l5"></a>

### L5 · high-density rendering, saved scenes and shareable configuration links

- **do:** high-density rendering, custom saved scenes, and shareable configuration links.
- **needs:** M5.
- **done when:** a saved scene or a shared link restores the same initial state, timestep and seed, and a test checks the round trip. saved scenes stay local, with no account or backend, and dense scenes are profiled before their rendering path is enabled, with WebGL2 only if that profiling justifies it.

<a id="l6"></a>

### L6 · optional 3D mode

- **do:** an optional 3D mode, only after the 2D experience is complete.
- **needs:** L1–L5, as the 2D experience's completion.
- **done when:** the 3D mode runs the same engine's tests in three dimensions, keeps every MVP control and diagnostic, and the 2D mode stays the default.

## ecosystem standards

the ecosystem's contract, in `meta-eigenomar`, is adding two standards that every repository adopts: the documents each one keeps, and how each one manages its source and delivers it. DOC1 and SCM1 adopt them here, each once its standard is designed. until then, their issues stay in the backlog. neither needs B1, and B1 needs neither.

<a id="doc1"></a>

### DOC1 · the lab's documents

- **do:** write this lab's product vision, architecture, design, testing strategy, and verification and validation (V&V) documents, in the shape the ecosystem's document standard sets. where a file or section already covers one, name it rather than copy it. today the vision is this plan's opening, the architecture and the stack are shared decision SD3's outcomes under [shared decisions](#shared-decisions), the MVP's scope and constraints are under [MVP](#mvp), and [M5](#m5)'s done-when holds the release's checks. `CLAUDE.md` asks B1 to record the layout in `docs/architecture.md` or `CLAUDE.md`, so whichever lands second gives that document its place among the five. written before B1, the documents describe the planned lab, and B1 and the MVP keep them true.
- **needs:** the ecosystem's document standard (`meta-eigenomar` ST1).
- **done when:** each of the five documents exists, or names the file or section that holds it, and follows the standard's outline. `project.yaml` names them under the standard's keys, and the ecosystem's validator passes. nothing in them is confidential, since this repository is public.

<a id="scm1"></a>

### SCM1 · the ecosystem's SCM strategy

- **do:** adopt the ecosystem's SCM strategy through its adoption checklist: CI, branching, protection of `main`, versioning and releases, the health report and dependency updates. [B1](#b1) plans CI on GitHub Actions and a deploy on every merge to `main`. if the strategy is ready before B1 starts, B1 follows it and this ticket checks the rest. otherwise this ticket brings B1's workflows in line.
- **needs:** the ecosystem's SCM strategy (`meta-eigenomar` ST2).
- **done when:** every item on the checklist is applied here, or recorded here as an exception the strategy allows, with its reason. `project.yaml`'s `delivery` and `CLAUDE.md` say what runs, any workflow passes on `main`, and the ecosystem's validator passes.

## log

- 2026-11-20 · B1 · added the ecosystem's tab icon (shared decision SD5) to B1's checklist, at the owner's request, rather than a ticket of its own: the shell B1 builds is the first page the lab deploys. B1's size is unchanged.
- 2026-11-18 · plan · added DOC1 and SCM1, in two new lanes, at the owner's request: this repository's adoption of the ecosystem's document standard and SCM strategy, which are designed in `meta-eigenomar`. each needs its standard, so its issue waits in the backlog.
- 2026-11-14 · plan · created by the ecosystem setup session, from the three C++ labs' shared plan, where this lab's part is now a link here. the bootstrap that the labs' plan has this lab reuse from the pathfinding playground became B1, after `pathfinding_playground` B1 as a planning dependency. its section became the MVP's scope and constraints, with stage 3's five steps as M1–M5, and its phase 2 L1–L6, in the same order. the MVP's seven acceptance criteria are in M1–M5's done-whens. the shared constraints and the definition of done are shared decision SD3, and the boundary with the creative coding gallery is shared decision SD4, each cited by its id. `project.yaml` validates, with no CI, releases or deployment until B1, `CLAUDE.md` imports the ecosystem's shared rules, and `.gitattributes` holds `* text=auto eol=lf`. nothing else in the repository changed.
