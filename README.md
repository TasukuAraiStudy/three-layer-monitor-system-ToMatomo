# Three-Layer Monitor To Matomo 🍅

[![Project status: early development](https://img.shields.io/badge/status-early%20development-orange.svg)](#development-status)

**Matomo Three-Layer Monitor** is an open-source research software project for
evaluating the observational quality of event measurement in Matomo. The
project is intended to identify where an observed web event is retained or
lost by recording and comparing the same event at three successive layers of
the measurement pipeline.

> **Important:** This repository is in early development. The architecture,
> data model, command-line interface, and installation procedure described
> below are planned and may change. The software is not yet ready for research
> or production use.

## Why this project is needed

Web analytics interfaces normally show only events that reached the final
reporting layer. When an expected event is absent, the final count alone cannot
determine whether the event:

1. failed to occur in the browser;
2. occurred but was not transmitted to, or received by, the analytics system;
3. was received but was not retained or exposed in the final reporting layer.

This ambiguity limits the interpretation of web analytics data as observational
research data. Matomo Three-Layer Monitor is intended to make these stages
separately observable and to provide reproducible comparisons between them.

The project extends the three-layer monitoring approach described in:

> Arai, T. (2026). *Establishing a web site environment-controlled three-layer
> monitoring system for observational quality assessment of web analytics
> measurement*. PeerJ Computer Science, 12, e4045.
> <https://doi.org/10.7717/peerj-cs.4045>

The published study examined a Google Analytics 4 measurement pipeline. This
repository develops a separate, locally reproducible implementation for
Matomo. Results obtained with Matomo have not yet been established and will be
reported separately from the software description.

## Intended users

The planned software is intended for:

- researchers studying web analytics measurement quality;
- analysts who need to distinguish event-generation failures from downstream
  measurement loss;
- Matomo administrators validating an instrumentation pipeline;
- instructors demonstrating how an observed interaction becomes an analytics
  record.

## Three-layer model

The exact observation method for each layer will be validated during
development. The initial model is:

| Layer | Planned observation | Question answered |
| --- | --- | --- |
| Layer 1: browser event | A locally recorded interaction and its unique event identifier | Did the event occur in the browser? |
| Layer 2: tracking request | A record that the corresponding Matomo tracking request was sent or received | Did the event reach the collection boundary? |
| Layer 3: Matomo record | The corresponding record retrieved from Matomo through a documented interface | Did the event become available in Matomo? |

Each experimental event will be assigned a unique identifier where technically
appropriate. The matching procedure, timing tolerance, duplicate handling, and
classification of delayed records will be specified before empirical results
are interpreted.

## Planned capabilities

The minimum planned research workflow is:

1. generate or register uniquely identifiable test events;
2. record Layer 1 observations in the browser or test runner;
3. record Layer 2 Matomo tracking requests;
4. retrieve Layer 3 observations from Matomo;
5. match records across the three layers;
6. classify the stage at which each event became unobservable;
7. calculate layer-to-layer retention and discrepancy measures;
8. export event-level results and a machine-readable analysis summary;
9. reproduce a documented example in a local Matomo environment.

The project is intended to provide reusable monitoring and comparison logic,
not only a script tied to one experiment.

## Planned output

The event-level output is expected to contain fields equivalent to the
following. Names and definitions remain provisional until the data schema is
validated.

| Field | Planned meaning |
| --- | --- |
| `event_id` | Identifier used to match an experimental event across layers |
| `layer1_observed` | Whether the browser event was observed |
| `layer2_observed` | Whether the tracking request was observed |
| `layer3_observed` | Whether the corresponding Matomo record was observed |
| `loss_stage` | First transition at which the event became unobservable |
| `browser` | Browser and version used in the observation |
| `operating_system` | Operating-system environment |
| `event_timestamp` | Event time in a documented time standard |
| `retrieval_timestamp` | Time at which the final-layer data were retrieved |

Aggregate output is expected to include the number of eligible events, matched
and unmatched records, duplicates, exclusions, and retention estimates for
each layer transition.

## Development status

- [x] Define the research problem and initial project scope.
- [x] Publish the initial repository documentation.
- [ ] Finalize the observation boundary for each layer.
- [ ] Specify the event-level data schema and matching rules.
- [ ] Implement Layer 1 logging.
- [ ] Implement Layer 2 request observation.
- [ ] Implement Layer 3 Matomo retrieval.
- [ ] Implement cross-layer matching and metrics.
- [ ] Add unit and integration tests.
- [ ] Provide a locally reproducible example environment.
- [ ] Conduct and document an initial research application.
- [ ] Prepare a stable software release.

Development decisions and unresolved questions should be documented in public
GitHub Issues. Features will be added through traceable commits and versioned
releases rather than as a single code dump.

## Planned repository structure

The repository is expected to use the following structure. Entries marked as
planned may not exist yet.

```text
matomo-three-layer-monitor/
├── README.md
├── LICENSE
├── CHANGELOG.md
├── CONTRIBUTING.md
├── AI_USAGE.md
├── pyproject.toml
├── src/
│   └── matomo_three_layer/
├── tests/
├── examples/
├── docs/
│   └── design.md
├── paper/
│   ├── paper.md
│   └── paper.bib
└── .github/
    └── workflows/
        └── tests.yml
```

### Contents planned for each file and directory

| Path | Planned contents and role |
| --- | --- |
| `README.md` | Project overview, statement of need, intended users, current status, installation instructions, minimal usage example, and links to detailed documentation. This file is the entry point for users and reviewers. |
| `LICENSE` | Full text of an OSI-approved open-source license. The intended license is MIT, subject to confirmation before the first software release. |
| `CHANGELOG.md` | Version-by-version record of added, changed, fixed, deprecated, and removed functionality. It will distinguish unreleased work from tagged releases. |
| `CONTRIBUTING.md` | Instructions for reporting bugs, requesting features, preparing a development environment, running tests, and proposing code changes. It will also state the available support pathway. |
| `AI_USAGE.md` | Development log describing any generative-AI assistance used for code, tests, documentation, or manuscript preparation, together with the corresponding human review and validation. |
| `pyproject.toml` | Python package metadata, supported Python versions, runtime and development dependencies, command-line entry points, build settings, and test configuration. |
| `src/matomo_three_layer/` | Installable Python package containing the core domain model, data ingestion, Matomo communication, cross-layer matching, measurement-quality metrics, export functions, and command-line interface. Modules will be separated according to validated responsibilities rather than placed in one experiment-specific script. |
| `tests/` | Automated unit, integration, and regression tests. Planned cases include missing records, duplicated identifiers, delayed Layer 3 availability, malformed input, zero denominators, and known example outputs. |
| `examples/` | Small, non-sensitive example configuration files, input data, expected output, and commands that allow users and reviewers to exercise the core workflow. Empirical research data will be separated from synthetic test fixtures where necessary. |
| `docs/design.md` | Detailed software design: layer definitions, system boundary, data flow, event identity strategy, matching algorithm, time handling, exclusions, architectural trade-offs, security considerations, and known limitations. |
| `paper/paper.md` | Planned Journal of Open Source Software manuscript describing the software's need, state of the field, software design, and demonstrated research impact. It will not be used as a substitute for user documentation or as the primary report of new empirical findings. |
| `paper/paper.bib` | BibTeX references cited by the JOSS manuscript, including related research, software, Matomo documentation, and the methodological foundation of the project. |
| `.github/workflows/tests.yml` | GitHub Actions workflow that installs the package in a clean environment and automatically runs the test suite on relevant pushes and pull requests. |

Additional files may be introduced when justified, including a Docker Compose
configuration for local reproducibility, a machine-readable citation file, and
security or governance documentation.

## Installation

The package is not yet installable. The planned installation interface is:

```bash
pip install .
```

A development installation is expected to use:

```bash
pip install -e ".[test]"
```

These commands are placeholders until `pyproject.toml`, dependency constraints,
and supported environments have been implemented and tested.

## Usage

The command-line interface has not yet been implemented. A future minimal
workflow may resemble:

```bash
matomo-three-layer validate-config examples/sample_config.yml
matomo-three-layer collect examples/sample_config.yml
matomo-three-layer analyze run-output/
```

The final commands will be documented only after their behavior and outputs are
covered by tests.

## Testing and reproducibility

The project will prioritize tests for conclusions derived from the monitoring
data, particularly record matching, denominator construction, retention
calculation, duplicate handling, and delayed availability. A reviewer should
eventually be able to run a small example locally and compare the generated
results with documented expected outputs.

The target reproducible environment will include a locally controlled Matomo
instance and test website. Containerization is under consideration; the final
choice will be documented with its portability, maintenance, and computational
cost trade-offs.

## Scope and limitations

This project will not assume that absence from a Matomo report proves permanent
data loss. Retrieval timing, processing delay, configuration, consent settings,
network behavior, and identifier availability are possible alternative
explanations and must be recorded or tested.

The software is not intended to:

- evaluate individual website visitors;
- bypass consent, privacy, or access controls;
- treat Matomo output as ground truth without upstream comparison;
- claim equivalence between GA4 and Matomo measurement behavior;
- generalize from a single local experiment to all deployments.

## Contributing and support

Until `CONTRIBUTING.md` is added, bugs, reproducibility problems, and narrowly
defined feature requests may be submitted through the repository's GitHub Issue
tracker. Reports should avoid credentials, personal data, production tracking
data, and other confidential information.

## AI usage

Generative AI may be used to assist with scaffolding, refactoring, test design,
documentation drafting, and language editing. The human author is responsible
for the research problem, layer definitions, architectural decisions,
validation criteria, source review, software behavior, and all published
claims. AI-assisted work will be recorded in `AI_USAGE.md` and disclosed in any
JOSS submission in accordance with the journal's policy.

## Citation

There is no stable software release to cite yet. Citation metadata and an
archived software DOI will be added when a validated release is available.

For the methodological background, cite the PeerJ Computer Science article
linked above.

## License

An OSI-approved license file will be added before the software is released. The
current intention is to use the MIT License. Until the license file is present,
no open-source permission should be inferred from this README alone.

## Name and affiliation disclaimer

Matomo is a product and trademark of its respective owners. This independent
research software project is not affiliated with or endorsed by Matomo or
InnoCraft.
