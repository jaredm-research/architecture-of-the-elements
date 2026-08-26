# Reproducibility

Everything delivered as the single HTML artifact is generated from this tree by
the commands below. No network, no package installation, no hidden state: the
build needs only Python 3 and the tests need Python 3 and Node.

## Rebuild the artifact

    python3 scripts/ingest_ame2020.py   # data/sources/ame2020 -> data/nuclides.json (anchored)
    python3 scripts/ingest_ciaaw.py     # data/sources/ciaaw -> data/abundances.json (anchored)
    python3 scripts/ingest_ame_rct.py   # rct1/rct2 -> data/reactions.json (the adversary; repo-side only)
    python3 scripts/build_data.py       # data/elements.source.csv -> src/data/elements.json
                                        #   (writes data/manifest.json: source+output SHA-256)
    python3 scripts/build.py            # bundles src/ -> dist/index.html (prints its SHA-256)
    python3 scripts/freeze_manifest.py  # data/repo.manifest.json: SHA-256 of every file

A delivered HTML is matched to the exact tree that produced it by comparing its
SHA-256 to `dist_sha256` in data/repo.manifest.json.

## Run every test

    python3 tests/test_data.py          # dataset: schema, Madelung closures, anomaly set
    python3 tests/test_zircon.py        # zircon optics figures share this repo
    python3 tests/test_nuclides.py      # AME2020 ingest: anchors, coverage, counts
    python3 tests/test_abundances.py    # CIAAW ingest + derived standard-weight reproduction
    node tests/test_slater.mjs          # Z_eff against textbook worked examples
    node tests/test_mat4.mjs            # matrix algebra
    node tests/test_orbital.mjs         # hydrogenic wavefunctions: nodes, normalization
    node tests/test_mc.mjs              # marching cubes on a known field
    node tests/test_config.mjs          # observed-configuration parser, all 118 + anomalies
    node tests/test_hund.mjs            # Hund engine vs a pinned NIST ground-term key
    node tests/test_nuclear.mjs         # derived energetics vs AME's own reaction tables (0.001 keV)
    node tests/test_chart.mjs           # Segre-chart layout, picking, CVD-certified palette
    node tests/test_dirac.mjs           # exact Dirac vs series bounds, measured 2p interval, Z^4
    node tests/test_spinwave.mjs        # dispersion exact; chain census vs the Hund engine
    node tests/test_terms.mjs           # microstate identities; two-derivation ground-term cross-check
    node tests/test_spectrum.mjs        # reduced-mass lines vs measured H and He+; the Urey shift
    node tests/test_moseley.mjs         # the K-alpha staircase vs nine anchors; the sigma lesson; Co/Ni
    node tests/test_ion.mjs             # cation rule + the full Ln3+ curve vs an independent table
    node tests/test_janet.mjs           # left-step slotting: coverage, row widths, He/Be, Lu/Y
    node tests/test_iso.mjs             # isoelectronic invariant vs the live engine; shrinking ions
    node tests/test_staggering.mjs      # pairing gap, tin sawtooth, N=82 cliff, the odd-odd four
    node tests/test_chains.mjs          # four classical series walked from masses; two-walled cutoff
    node tests/test_semf.mjs            # the liquid drop fitted live: coefficients, valley, fissility, shells
    node tests/test_scf.mjs             # SCF Phases 1-4: radial solver, helium at HF, X-alpha, the 118 sweep
    node scripts/scf_sweep.mjs 1 40     # regenerate the sweep artifact (then 41 70, 71 95, 96 118)
    node tests/test_scfui.mjs           # SCF Phases 5-5.1: panel pure layer, worker source, export scf_ref pins
    node tests/test_kinetic.mjs         # Boltzmann K1: Maxwell layer, hard-disk gas, H-theorem, Loschmidt
    node tests/test_knui.mjs            # Boltzmann K2: panel pure layer and the bundled instrument
    node tests/test_lens.mjs            # Lens L1-L2: permalink codec, sixteen lens predicates, chain set walked live
    node tests/test_ligand.mjs          # Ligand LF1: split, -4/9, quenching, ladders, Curie, Gd shielding
    node tests/test_lfui.mjs            # Ligand LF2: panel pure layer and the bundled ninth instrument
    python3 tests/test_engine_audit.py  # independent cross-language re-derivation of every engine
    node tests/test_accessibility.mjs   # palette under 3 dichromacies, grayscale, WCAG
    node tests/test_camera.mjs          # orbit/pan/zoom basis, numerically
    node tests/test_pairing.mjs         # pairing predictions vs classic chemistry

## Where each shipped feature lives

| feature | source | verified by |
|---|---|---|
| element dataset | data/elements.source.csv -> scripts/build_data.py | test_data.py |
| helix / table geometry | src/render/geometry.js | test_data (closures) |
| orbital isosurfaces (s,p,d,f) | src/physics/orbital*.js, marching_cubes.js | test_orbital (incl. f), test_mc |
| observed configurations | src/physics/config_parse.js | test_config.mjs |
| Isotopes section (AME2020 masses) | data/sources/ame2020 -> scripts/ingest_ame2020.py -> data/nuclides.json | test_nuclides.py |
| Abundances + derived weighted mass | data/sources/ciaaw -> scripts/ingest_ciaaw.py -> data/abundances.json | test_abundances.py |
| Magnetism block + mu_eff encoding | src/physics/hund.js | test_hund.mjs |
| Nuclear energetics + B/A encoding | src/physics/nuclear.js (+ reactions.json adversary) | test_nuclear.mjs |
| Chart of nuclides (Segre) | src/render/nuclide_chart.js | test_chart.mjs |
| Relativity block + Rel. encoding | src/physics/dirac.js | test_dirac.mjs |
| Spin-wave instrument | src/physics/spinwave.js | test_spinwave.mjs |
| Term ladder (Russell-Saunders) | src/physics/terms.js | test_terms.mjs |
| Spectrum block (reduced-mass lines) | src/physics/spectrum.js (masses: data/nuclides.json) | test_spectrum.mjs |
| Moseley staircase + block | src/physics/moseley.js | test_moseley.mjs |
| Ion mode (3+ basis) | src/physics/ion.js | test_ion.mjs |
| Janet left-step arrangement | src/physics/janet.js + render/geometry.js | test_janet.mjs |
| Isoelectronic explorer | src/physics/iso.js | test_iso.mjs |
| Pairing staircase | src/physics/staggering.js | test_staggering.mjs |
| Decay chains | src/physics/chains.js | test_chains.mjs |
| SEMF fit + shell mode | src/physics/semf.js | test_semf.mjs |
| Radial solver (SCF P1) | src/physics/scf.js | test_scf.mjs |
| All-118 sweep artifact | data/scf_sweep.json | test_scf.mjs (invariants, determinism sentinels) |
| Kinetic engine (K1) | src/physics/kinetic.js | test_kinetic.mjs |
| Ligand-field engine (LF1) | src/physics/ligand.js | test_ligand.mjs |
| Cross-language engine audit | scripts/engine_probe.mjs (all engines) | test_engine_audit.py |
| per-subshell Z_eff in the panel | src/physics/slater.js | test_slater.mjs |
| Slater Z_eff | precomputed by build_data (rules in test) | test_slater.mjs |
| camera (orbit, pan, zoom, drag/click) | src/render/camera.js | test_camera.mjs |
| colour system + adaptive ink | src/render/color.js | test_accessibility.mjs |
| pairing predictions | src/physics/pairing.js | test_pairing.mjs |
| aufbau-break overlay | src/app.js (rings) + dataset cfg_real | test_data.py (set pinned) |
| JSON export schema | src/app.js buildElementRecord | docs/EXPORT_SCHEMA.md |
| UI shell, a11y wiring | src/ui/*.html/css, src/app.js | build guards + greps |

## Manifests

- data/manifest.json: the DATA provenance (source CSV SHA-256 -> output JSON
  SHA-256, derivation note). Written by build_data.py.
- data/repo.manifest.json: the TREE freeze (every file, bytes, SHA-256, and the
  dist hash). Written by freeze_manifest.py after a build.

## Delivery rule

Every delivered artifact is accompanied by the full repository (zip) whose
freeze manifest contains the artifact's hash. If the two hashes match, the HTML
in hand was built from exactly this tree.
