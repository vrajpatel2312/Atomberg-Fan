# driver-fan — Codex executors

Executors here follow /home/agent/codeman-cases/Sam/agents/executor.md and their brief; Sam orchestrates.

## What this repo is

Control4 DriverWorks (IP) driver for Atomberg smart fans
(Efficio / Studio / Renesa, Comfort category, fan proxy binding `5001`).
Two-way LAN control: JSON over UDP `5600`, state broadcasts on UDP `5625`
(power, speed 1-6, LED, live status); see `README.md` and the driver docs block.

## Layout

- `build/` — shipped packaged driver (`build/v1/encrypted/Atomberg_Fan.c4z`).
- `Documentation/` — architecture index (`architecture/codebase-graph.json`).
- `driver_packager/` — vendored Driver Packager (`dp3` maintained); see its `README.md`.
- `generator/` — codebase-graph builder (`project_graph.py` per `graph.config.json`).
- `.github/` — CI graph-freshness gate plus local mirror test.

## Build / package / test (as documented in README.md)

```sh
python3 driver_packager/dp3/driverpackager.py -v ./ <dstdir> <manifest>
python3 generator/project_graph.py build --root . --project driver-fan --config graph.config.json --out Documentation/architecture/codebase-graph.json
bash .github/tests/test-graph-freshness.sh
```

Packager variants/flags and the GitHub Action inputs are documented in
`README.md` / `driver_packager/README.md`; no other build or test commands documented.

## Graph

```sh
/home/agent/codeman-cases/Sam/scripts/sam-graph.sh find <word> --project driver-fan
/home/agent/codeman-cases/Sam/scripts/sam-graph.sh impact <paths> --project driver-fan
```

## Safety

Secrets names only; never print values. Never push to the default branch;
open a PR and let Sam merge.
