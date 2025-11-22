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
