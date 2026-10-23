# Actigraphy Integration and Sleep Feature Engineering

How do incompatible sleep records become one trustworthy dataset?

This is a public documentation repository for a connected research workstream in Raee Bhagat’s portfolio. It describes methods and decisions from reviewed project materials. It does not contain the original analysis implementation, participant data, or internal research files.

[Portfolio](https://raeeb.dev/) · [Workstream in the project library](https://raeeb.dev/project-library#P02) · [Detailed case study](https://raeeb.dev/projects/sleep-data)

## Research context

Integrated wearable actigraphy exports with flight records and sleep logbook information to derive analysis-ready sleep variables. The scripts parse heterogeneous exports, align sleep episodes to trips, distinguish total sleep time from time spent in a sleep episode, and calculate sleep inside windows relative to flight events. This work demonstrates the data engineering behind the statistical analyses: reconciling formats, maintaining trip keys, handling midnight boundaries, and delivering reusable features to downstream models.

## What the workflow produces

Reusable trip-level features from wearable files, sleep logs, and flight timestamps.

## Methods and decisions

- Data integration.
- temporal joins.
- interval overlap.
- timestamp normalization.
- repeated-trip alignment.
- derived features.
- missing-data handling.

## Libraries and tools

`R`, `dplyr`, `tidyr`, `purrr`, `lubridate`, `stringr`, `openxlsx`, `CSV`, `Excel`.

These tools are associated with the reviewed workstream. This repository is documentation, so it does not install or execute those libraries.

## Analytical structure

**Unit of analysis:** A sleep episode linked to participant × route × outbound trip date; features are aggregated to trip level.

**Inputs:** Heterogeneous wearable CSV exports, sleep logbooks, flight records, episode start/end times, and available wake-after-sleep-onset measures.

**Output:** Trip-level sleep summaries over 24-, 48-, and 72-hour windows, with data needed to check alignment and allocation.

### Robust parsing

The processing code locates the statistics section inside actigraphy exports instead of assuming one fixed header row. This is a practical defense against changes in export structure, but malformed files still require explicit handling and review.

### Composite trip keys

Participant identifiers alone are insufficient when someone contributes several trips. Route and outbound-date context help associate an episode with the correct trip. Matching logic should distinguish no match, one match, and multiple candidates before aggregation.

### Normalize before subtracting

Date parsing and time-zone interpretation precede duration calculations. A cross-midnight episode is an interval, not a negative clock-time difference. Flight-relative windows make the feature definition comparable across routes.

### Separate opportunity from sleep

Time spent in a sleep episode or in bed is distinct from estimated total sleep time. The TST workflow incorporates wake after sleep onset where available and retains verification variants for comparison.

## Validation and interpretation

- **Boundary behavior:** Check an episode fully outside, fully inside, or partly overlapping a window, including equality at a boundary.

- **Key integrity:** Reconcile unique trip keys before and after joins; review duplicated matches instead of silently summing them.

- **Physical plausibility:** Review negative durations, implausibly long episodes, and sleep totals exceeding the available observation window.

- **Feature consistency:** Investigate missing windows and unexpected decreases between nested 24-, 48-, and 72-hour summaries.

**Model or calculation:** overlap = max(0, min(episode end, window end) − max(episode start, window start))

When total episode sleep is allocated in proportion to overlap, the estimate assumes sleep is distributed uniformly within the episode. It cannot recover minute-by-minute sleep states.

## Where the methods transfer

Longitudinal sensor integration, temporal joins, feature preparation, and scientific ETL.

## Evidence and boundaries

This summary describes analytical work and its interpretation. It is not a certification, a causal claim, or evidence of a deployed production service.

The portfolio contains twenty connected workstreams. Several share datasets, preparation logic, research programs, or legacy code. Separate documentation repositories do not imply twenty independent completed studies or exclusive authorship of all underlying research.

## Privacy and reproducibility

- Original datasets, participant identifiers, internal file paths, workbooks, and unpublished source code are not included.
- Public interactive examples on the portfolio use synthetic teaching data and are labeled accordingly.
- There is no install command, runnable research pipeline, or reproducibility claim for this documentation repository.
- No institutional endorsement or ownership of shared research materials is implied.

## Author and contact

Raee Bhagat · [Portfolio](https://raeeb.dev/) · [LinkedIn](https://www.linkedin.com/in/raee-suresh-bhagat-53168890/) · [Email](mailto:raeesbhagat@gmail.com)
