# Learning Log: Cloud-Native Engineering Mastery

This file records immutable, timestamped summaries of each PRAR (Perceive, Reason, Act, Refine) cycle completed in this repository.

---

### [2026-11-09T14:52:00+03:00] - Initialization: Repository & Lab Directory Scaffolding
- **Perceive:** Analyzed `README.md` repository structure specification (Section 6) and curriculum (Section 3). The repository was empty except for LICENSE and README.md.
- **Reason:** Formulated a complete scaffolding plan covering the top-level directories (`docs/`, `labs/`, `platform/`, `applications/`, `scripts/`, `assessments/`, `blog/`, `reviews/`, `capstone/`) and all 24 weeks with 4 standardized labs per week conforming to Section 6.2 and 6.3.
- **Act:** Scaffolded configuration files, `.gitignore`, protocol documents, top-level directories, and lab templates with standardized subdirectories (`README.md`, `architecture/diagram.mmd`, `manifests/`, `scripts/`, `evidence/`, `troubleshooting.md`).
- **Refine:** Verified complete folder hierarchy, permissions, and git tracking status.

---

### [2026-11-09T15:20:00+03:00] - Refactoring: Decentralize Curriculum & Condense Root README
- **Perceive:** Identified that root `README.md` was overly verbose (1,222 lines) because it embedded granular weekly concepts, lab steps, and exit criteria directly. The user requested moving detailed lab info into their respective lab directories and keeping only general info in the root README.
- **Reason:** Designed a hierarchical documentation architecture:
  1. Root `README.md` serves as a concise platform and navigation guide with a roadmap table linking directly to modules.
  2. Month `README.md` (`labs/month-XX/`) details the monthly domain goals and deliverable gates.
  3. Week `README.md` (`labs/month-XX/week-YY/`) details the weekly research concepts, full lab syllabus, evidence requirements, and exit criteria.
  4. Lab `README.md` (`labs/.../lab-ZZ/`) encapsulates specific reproducible steps, scripts, failure injection scenarios, and evidence checklists.
- **Act:** Implemented automated parser and generator migrating detailed instructions to Month, Week, and Lab READMEs, and condensing the root `README.md` from 1,222 lines to ~506 lines with clean navigation links.
- **Refine:** Validated that all 1,200+ relative markdown links across months, weeks, and labs resolve cleanly without broken paths.

---

### [2026-11-10T23:30:00+03:00] - Refactoring: Strip Month & Week Prefixes from Directories
- **Perceive:** User requested removing `month-XX-` and `week-YY-` prefixes from directory names while preserving all `lab-0x-` prefixes in the lab directories.
- **Reason:** Designed an atomic git rename strategy:
  1. Rename week directories inside each month (e.g. `week-01-linux-primitives` -> `linux-primitives`).
  2. Rename month directories (e.g. `month-01-container-internals` -> `container-internals`).
  3. Update all cross-references and relative markdown links across root `README.md`, month `README.md`s, and `docs/backlog.md`.
- **Act:** Executed directory renames using `git mv` and updated references across all relevant markdown documentation.
- **Refine:** Validated all 1,231 markdown links across the repository with 100% resolution success (0 broken links) and confirmed clean git tracking.

---

### [2026-11-11T20:00:00+03:00] - Restructuring: Container-Internals Lab 01 (`lab-01-linux-namespaces`)
- **Perceive:** User requested updating `labs/container-internals/linux-primitives/lab-01-unshare-namespaces/` to `lab-01-linux-namespaces/` with specialized `scripts/`, `evidence/`, and `diagrams/` files, preserving `README.md` and `troubleshooting.md`.
- **Reason:** Planned atomic `git mv` rename, removal of unused `architecture/` and `manifests/` directories, creation of dedicated namespace/network scripts (`+x`), evidence logs, and Mermaid diagrams, plus updating cross-references across `README.md`, `labs/container-internals/README.md`, and `labs/container-internals/linux-primitives/README.md`.
- **Act:** Renamed directory, restructured subdirectories and files, updated internal and external references.
- **Refine:** Verified exact directory tree, executable script permissions (`-rwxr-xr-x`), and 100% link validity (1,234 links verified).

---

### [2026-11-11T20:45:00+03:00] - Review & Alignment: Container-Internals & Lab 1.1 READMEs
- **Perceive:** Audited the user's manually updated `labs/container-internals/README.md`, `labs/container-internals/linux-primitives/README.md`, and `labs/container-internals/linux-primitives/lab-01-linux-namespaces/README.md` against the actual directory layout and curriculum.
- **Reason:** Identified stale `month-01-`/`week-01-` deliverable tree paths, a cross-reference error pointing user namespaces to Lab 1.4 instead of Week 4 Lab 4.3, missing breadcrumb/artifact links in Lab 1.1, leftover `4.x` heading numbering, and Week 1 syllabus/blog title mismatches.
- **Act:** Aligned all three READMEs and root `README.md` Week 01 summary, updated cross-references (`Lab 1.3`, `Lab 1.4`, `Week 4 Lab 4.3`), and added clickable links to `diagrams/`, `scripts/`, `evidence/`, and `troubleshooting.md`.
- **Refine:** Verified 1,246 local markdown links across the repository with 0 broken links and confirmed 0 stale `month-0x`/`week-0x` paths in `labs/container-internals/`.
