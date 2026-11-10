# Quantum Chip Layout EDA Tool using Qiskit Metal

> **Status: 🚧 In active development (started Nov 2026).** The design and roadmap are below. Features are checked off as they land.

A Python electronic design automation (EDA) tool that generates **superconducting quantum chip layouts** from a small set of parameters. It is built on [Qiskit Metal](https://github.com/qiskit-community/qiskit-metal).

Laying out qubits, readout resonators and launch pads by hand is slow and error-prone. You describe the chip you want (number of qubits, spacing, resonator lengths), and this tool:

1. builds the layout automatically,
2. checks it against a set of design rules, and
3. exports a GDS file for fabrication or simulation.

It can be used either from a PyQt desktop UI or from the command line.

---

## Features

| Feature | Description | Status |
|---|---|---|
| **Parametric layout generator** | Generates transmon qubits (`TransmonPocket`), meandered readout resonators (`RouteMeander`) and launch pads from a YAML/JSON config | ⬜ Planned |
| **Design-rule verification (DRC) engine in Rust** | A fast geometry-checking core written in Rust and called from Python through PyO3 bindings. It checks for minimum spacing, overlapping components, out-of-bounds components and resonator length limits | ⬜ Planned |
| **GDS export** | Writes the final layout to `.gds` using Qiskit Metal's GDS renderer | ⬜ Planned |
| **PyQt desktop UI** | A form for chip parameters with *Generate*, *Run DRC* and *Export GDS* buttons, plus a list of rule violations | ⬜ Planned |
| **CLI** | `qlayout generate config.yaml --out chip.gds` for scripted and batch runs | ⬜ Planned |
| **Automated tests** | `pytest` unit tests for the generator and every DRC rule | ⬜ Planned |
| **CI/CD** | GitHub Actions runs the tests and lint on every push and pull request (Linux runners) | ⬜ Planned |

---

## Architecture

```
            ┌──────────────┐      ┌─────────────────┐
config.yaml │  CLI / PyQt  │      │  Layout builder │   Qiskit Metal
 ─────────► │   front end  │ ───► │  (qubits, CPW   │ ─► DesignPlanar
            └──────────────┘      │   resonators)   │
                                  └────────┬────────┘
                                           │
                                  ┌────────▼────────┐
                                  │  DRC engine     │ ─► violations report
                                  │  (Rust + PyO3)  │
                                  └────────┬────────┘
                                           │ pass
                                  ┌────────▼────────┐
                                  │  GDS exporter   │ ─► chip.gds
                                  └─────────────────┘
```

### Planned project layout

```
qlayout/
  config.py        # load and validate YAML/JSON chip parameters
  builder.py       # create qubits, resonators and pads in a Qiskit Metal design
  drc/
    engine.py      # Python wrapper: run the rules and collect violations
drc_core/          # Rust crate (PyO3): spacing, overlap, bounds and length rules
  Cargo.toml
  src/lib.rs
  export.py        # GDS export
  cli.py           # command-line entry point
  ui/app.py        # PyQt desktop front end
tests/             # pytest suites (one per DRC rule and builder)
examples/          # sample chip configs (2-qubit, 4-qubit, ...)
.github/workflows/ # CI pipeline
```

---

## Example config (planned format)

```yaml
chip:
  size_mm: [6, 6]
qubits:
  count: 4
  type: TransmonPocket
  spacing_um: 1500
resonators:
  type: RouteMeander
  length_mm: [6.2, 6.4, 6.6, 6.8]
drc:
  min_spacing_um: 50
```

## Usage (once released)

```bash
# install
python -m venv .venv && source .venv/bin/activate
pip install -e .

# generate a layout, run DRC and export GDS
qlayout generate examples/4q.yaml --out build/4q.gds

# launch the desktop UI
qlayout ui

# run the tests
pytest
```

---

## Roadmap

- [ ] Scaffold the package, config loader and the first sample config
- [ ] Layout builder: qubits and launch pads
- [ ] Layout builder: meandered readout resonators
- [ ] Rust DRC core (PyO3 + maturin): spacing, overlap, bounds and length rules, with `cargo test` and pytest tests
- [ ] GDS export
- [ ] CLI
- [ ] PyQt UI
- [ ] GitHub Actions CI on Linux
- [ ] Benchmarks: compare manual vs. automated layout setup time and count how many deliberately broken layouts DRC catches

## Tech stack

Python · Rust (PyO3, maturin) · Qiskit Metal · PyQt · pytest · GitHub Actions · Linux · git

## Author

**Asha Subramaniyan**, MS Computer Science, Northeastern University
