# Rebuild the Octatrack base after a base input changes

A new event or table regenerates the base's dispatch, and the dispatch is a base input (anything `build_core.py`
hashes). Rebuild the beta base and rebind the packages bound to it in the same pull request, and again after
merging a `main` that changed the base:

1. `npm run upstream:tools`. A stale upstream checkout fails the standalone ports' recipe check with "does not
   name the exact source/image inputs".
2. Put the m68k toolchain on `PATH`: `~/.local/m68k-elf/bin` after octabam's `make setup-linux`. Without it the
   build stops on a `FileNotFoundError`.
3. `npm run octatrack:base -- --stock <your OCTATRACK_OS1.40C.bin> --beta --write`. It takes more than ten minutes.
   After a merge conflict in `base-binding.json` or `src/engine/assets/octatrack-base*`, take `main`'s side of
   those generated files first; this step writes them again.
4. `python3 -B scripts/octatrack-module-packages.py --base-only --stock <your OCTATRACK_OS1.40C.bin>`. It points each
   base-bound package's `requiredBase` in `src/engine/assets/octatrack-module-packages.json` at the new identity in
   `sdk/machines/octatrack/base-binding.json` and rebuilds the packages whose bytes depend on the base. CI's
   byte-for-byte rebuild fails otherwise.

The owner requalifies the base on a unit before testers get it.
