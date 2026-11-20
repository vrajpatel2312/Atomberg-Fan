# Atomberg Fan — Control4 Driver

Control4 DriverWorks (IP) driver for Atomberg smart fans
(models `Efficio / Studio / Renesa`, driver version 1,
Comfort category, fan proxy on binding `5001`,
minimum OS `2.10.0` — all per `driver.xml` inside the shipped `.c4z`).

What it does (per the driver's embedded documentation block):

- Two-way local LAN control: sends JSON commands over UDP `5600`,
  listens for ASCII-JSON state broadcasts on UDP `5625`.
- Power on/off, speed control (`1`–`6`, preset `3`), LED control.
- Live status: the fan rebroadcasts state after each command and the
  driver updates the UI in real time.
- Driver properties: `Fan IP Address` and `License Key`
  (hardware-specific; enter the key provided for the unit).

## Shipped artifact

- `build/v1/encrypted/Atomberg_Fan.c4z` — the loadable packaged driver
  (a zip containing `driver.xml` + `driver.lua.encrypted`).
- The driver sources (`Atomberg_Fan/driver.xml`, `driver.lua`,
  `.c4zproj`, `build.ps1`, `license_generator.ps1`, `salt.txt`) are
  intentionally **not** committed — see `.gitignore`. A fresh clone
  carries the packaged driver, not the build inputs.

## Build / packaging flow (`driver_packager/`)

Packaging is done with the vendored Driver Packager; `dp3`
(Python 3) is the recommended and only maintained variant
(see `driver_packager/README.md`):

```sh
python3 driver_packager/dp3/driverpackager.py -v ./ <dstdir> <manifest>
# srcdir = directory holding the driver sources
# dstdir = where the .c4z is placed
# manifest = optional manifest xml file
```

- `driver_packager/createc4z/CreateC4Z.py` wraps the same flow —
  see `driver_packager/createc4z/README.md` for usage:
  `CreateC4Z c4zprojfile srcdir dstdir [-d] [-e]`.
  `-e/--encrypt` squishes and encrypts the Lua (DriverWorks
  Encryption V2; the shipped `encrypted/` build is this variant).
  `-d/--develop` adds `C4:AllowExecute(true)` plus the dev flag so the
  Lua window in Composer stays usable on an encrypted build.
- GitHub Action form (`driver_packager/action.yml`,
  invoked as `control4/drivers-driverpackager@v1`): inputs
  `projectDir`, `c4zproj`, `outputDir`, `version`,
  `updateModified`, `allowexecute` — see
  `driver_packager/README.md` and `driver_packager/entrypoint.sh`.

## Install / load pointer

1. Load `build/v1/encrypted/Atomberg_Fan.c4z` into the Control4 project.
2. Keep the controller and the fan on the same LAN.
3. Find the fan's IP (router DHCP table or the Atomberg app) and enter
   it in the driver's `Fan IP Address` property.
4. Enter the unit's `License Key` property to activate it.
5. The driver sends commands over UDP `5600` and tracks live state
   from the UDP `5625` broadcasts.

(Steps 2–4 are the setup sequence shipped inside the driver's own
documentation block in `driver.xml`; reference link listed there:
`https://github.com/vrajpatel2312/Atomberg-local`.)

## Architecture index

- `Documentation/architecture/codebase-graph.json` — generated codebase
  graph for this checkout (nodes/edges/sources/gaps/drift), built with
  `generator/project_graph.py` per `graph.config.json`.
- Freshness is gated in CI by `.github/workflows/graph-freshness.yml`
  (read-only; rebuilds to `/tmp` and byte-compares) with a local
  mirror in `.github/tests/test-graph-freshness.sh`.
- After any tracked-file change, rebuild the graph and commit it with
  the change:

```sh
python3 generator/project_graph.py build --root . --project driver-fan \
  --config graph.config.json --out Documentation/architecture/codebase-graph.json
```
