# CLAUDE.md

## Project Overview

**Nyquist's Ranked Choice Voting Tabulator** is a Python-based tool for analyzing ranked choice voting (RCV) election results. It runs as a Jupyter notebook (designed for Google Colab) and provides vote tabulation, visualization, narrative generation, and non-monotonicity detection.

## Quick Start

```bash
# Run in Google Colab (recommended)
# Open: https://githubtocolab.com/KalebNyquist/ranked-choice-tabulator/blob/main/tabulator.ipynb

# Or run locally with Jupyter
pip install pandas numpy parse num2words matplotlib
jupyter notebook tabulator.ipynb
```

## Project Structure

```
ranked-choice-tabulator/
├── tabulator.ipynb           # Main source code (all functions in one notebook)
├── README.md                 # Project documentation
├── LICENSE                   # Apache 2.0 license
├── election_data/            # (gitignored) Put election data files here
├── read_cfgs.json           # (gitignored) Saved import configurations
└── example_outputs/
    ├── Burlington 2009 Mayor.md   # Example analysis
    └── assets/                    # Output visualizations
```

## Architecture

The codebase is organized into functional sections within the notebook:

### Configuration & Data Loading
- `cfg_wizard()` - Interactive setup for importing election data
- `load_cfgs()` / `save_cfg()` - Configuration persistence (JSON)
- `load_election_data()` - Pandas-based import (CSV, Excel, TXT/PRM)

### Data Structures Supported
Three "record structures" for ballot data:
- **field**: Columns like "1st Place", "2nd Place" with candidate IDs as values
- **value**: Paired columns for ranking + candidate ID
- **array**: Single column with delimited array like `"CandA[1],CandB[2]"`

### Election Engine
- `contest_election()` - Main instant-runoff voting simulation
- `contest_round()` - Per-round winner/loser determination
- `get_top_choice()` - Determines voter's next valid preference

### Output Generation
- `get_horizontal_bar_graph_of_rounds()` - Matplotlib visualization
- `narrate_results()` - Plain English narrative for each round
- `monotonicity_check()` - Detects Arrow's Impossibility Theorem violations

## Key Global Variables

The notebook uses several global variables set during execution:
- `read_cfg` - Current import configuration dict
- `ballots` - List of processed ballot dicts
- `name_map` - Candidate ID to display name mapping
- `rank_columns`, `cand_columns` - Column mapping dicts
- `rank_to_cand_map`, `cand_to_rank_map` - Bidirectional lookups

## Testing

No automated tests exist. Manual testing uses the Burlington 2009 Mayor election as a canonical case (famous non-monotonic RCV election).

## Common Tasks

### Adding support for new data formats
Modify the `generate_by_*_columns_of_interest()` functions in the notebook.

### Changing visualization style
Edit `get_horizontal_bar_graph_of_rounds()` and `get_color_to_candidate_map()`.

### Modifying election rules
The instant-runoff logic is in `contest_round()`. Win threshold is `floor((total_votes / 2) + 1)`.

## Dependencies

- pandas, numpy - Data manipulation
- parse - Template string parsing
- num2words - Number-to-English conversion
- matplotlib - Visualization

## Known Limitations

- Only supports instant-runoff (IRV) single-winner elections
- Non-monotonicity detection incomplete for complex multi-candidate scenarios
- No support for ballot weights
- Global variables create tight coupling between functions
- No unit tests or CI/CD

---

## Strategic Vision: Rapid Response Election Analysis Tool

### The Opportunity

There's a gap in the RCV ecosystem for **independent, objective, rapid** non-monotonicity analysis:

- **FairVote** (primary RCV advocacy org) actively downplays monotonicity as "of little import" and has incorrectly claimed elections like Burlington 2009 had no monotonicity failure
- **Academic researchers** publish rigorous analysis but years after elections (Graham-Squire & McCune 2023 found 3.8% of competitive RCV elections are non-monotonic)
- **Election officials** release CVRs but don't analyze for pathologies
- **No neutral arbiter exists** for rapid post-election analysis

### Why Now

1. **RCV is expanding rapidly** - 7 states voted on RCV measures in 2024
2. **CVR data increasingly available** - Major jurisdictions (Alaska, Maine, NYC, Minneapolis, SF) now publish within 24-48 hours
3. **3.8% failure rate** in competitive elections = roughly 1-in-26 elections
4. **Unique positioning** - The "both-and" approach (detecting issues BUT providing upper bounds) offers credibility that pure advocacy orgs lack

### Target Jurisdictions for CVR Analysis

| Jurisdiction | CVR Availability | Format Notes |
|--------------|------------------|--------------|
| Alaska | Quick release, public | Statewide RCV |
| Maine | Public release | Statewide RCV |
| NYC | Public release | Municipal primaries |
| Minneapolis | ~24 hours post-election | Municipal |
| San Francisco | With each results release | Municipal |
| Cambridge, MA | Public release | Municipal |

---

## Development Roadmap

### Phase 1: Foundation

**Goal**: Make the tool production-ready

| Task | Priority | Notes |
|------|----------|-------|
| Refactor into Python package | Critical | Extract core logic from notebook, enable CLI/API usage |
| Add `requirements.txt` + packaging | Critical | Enable `pip install` |
| Write unit tests | Critical | Use Burlington 2009 as ground truth |
| Support NIST CVR format | High | Standard format used by many jurisdictions |
| Support Universal RCV Tabulator JSON | High | Interoperability with RCTab |
| Fix multi-candidate non-monotonicity | High | Currently "halts analysis" in complex cases |

### Phase 2: Rapid Response Infrastructure

**Goal**: Enable same-day analysis when CVRs drop

| Task | Priority | Notes |
|------|----------|-------|
| Pre-built importers for major jurisdictions | Critical | Alaska, Maine, NYC, Minneapolis, SF |
| Batch processing mode | High | Analyze all races in a CVR dump |
| Automated report generation | High | Markdown/PDF with visualizations |
| Performance optimization | Medium | Permutation approach won't scale to 10+ candidates |
| CI/CD pipeline | Medium | Automated testing |

### Phase 3: Distribution & Credibility

**Goal**: Become the trusted source journalists/researchers cite

| Task | Priority | Notes |
|------|----------|-------|
| Public results archive | High | Host analyzed elections online |
| Methodology documentation | High | Peer-reviewable algorithm explanation |
| Condorcet winner detection | Medium | "Would a different winner beat all others head-to-head?" |
| Spoiler effect quantification | Medium | Measure vote-splitting impact |
| API for researchers | Low | Let academics query the database |

### Phase 4: Expansion

| Task | Notes |
|------|-------|
| Multi-winner (STV) support | City council elections |
| Historical election database | Backfill all available CVR data |
| Comparative analysis | "If this were Approval/STAR/Condorcet, who wins?" |

---

## Competitive Landscape

| Project | Strength | Gap This Project Fills |
|---------|----------|------------------------|
| RCTab (BrightSpots) | Certified for official tabulation | No pathology detection |
| open-rcv | Clean Python library | No non-monotonicity analysis |
| RCVis | Beautiful visualizations | No analytical features |
| FairVote data | Large election database | Conflict of interest on monotonicity |
| **This project** | Non-monotonicity detection + narrative output | Unique combination |

---

## References

- Graham-Squire & McCune (2023): "An Examination of Ranked-Choice Voting in the United States, 2004–2022" - Found 3.8% non-monotonicity rate
- NIST SP 1500-103: Cast Vote Records Common Data Format Specification
- FairVote Best Practices: "Best Practices for Releasing RCV Election Results"
