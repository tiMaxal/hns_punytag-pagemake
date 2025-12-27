# HNSell - Handshake Domain Manager

## Project Overview
HNSell is a Python/Tkinter GUI application for processing Handshake (HNS) domain CSV exports from multiple wallet sources (Bob Wallet, Namebase, Shakestation, Firewallet). The core functionality revolves around punycode validation, Unicode conversion, and HTML portfolio generation.

**Forked from**: Original punytag tools by GitHub user [@i1li](https://github.com/i1li)

## Architecture

### Core Components
- **[hnsell.py](../hnsell.py)**: Main GUI application with 3-tab interface (1088 lines)
  - Tab 1: Punytag Processor - Batch CSV processing with auto-source detection
  - Tab 2: Puny ⟷ Unicode - Bidirectional punycode/Unicode converter
  - Tab 3: PageMaker - HTML portfolio generator with marketplace linking
- **[pagemaker.py](../pagemaker.py)**: Standalone HTML generation logic (305 lines)
- **[legacy/](../legacy/)**: Original standalone processors by @i1li - `punytag_*.py` files (bob/nb variants for tr/tld)
  - Still functional but require exact export filenames (e.g., `Namebase-domains-export.csv`, `bob_tr.csv`)
  - Core logic now integrated into GUI with flexible file selection

### Data Flow
1. **CSV Detection**: Header-based source identification (bob-tr, nb-tld, ss-tld, ss-tr, fw)
   - Example: `'extra.domain'` + `'extra.action'` → nb-tr, `'domain'` + `'for_sale'` → ss-tld
2. **Punycode Processing**: Three validation levels via `punycode_convert_validate()`:
   - `PUNY_IDNA`: Strict IDNA compliance (highest level)
   - `PUNY_ALT`: Alternative punycode (may have inconsistent rendering)
   - `PUNY_INVALID`: Contains invalid/non-rendering characters
3. **Output Management**: Date-stamped files (yyyymmdd), optional `_orig` suffix, subdirectory sorting

## Critical Patterns

### CSV Source Detection Logic (lines 233-258 in hnsell.py)
```python
# Order matters - check most specific headers first
if 'extra.domain' in headers:        # Namebase transaction
    return 'nb-tr'
elif 'domain' and 'for_sale':        # Shakestation TLD
    return 'ss-tld'
elif 'time' and 'txhash' and 'domains':  # Bob transaction
    return 'bob-tr'
```
**Rule**: When adding new source types, add header checks from most-to-least specific to avoid false positives.

### Punycode Validation Hierarchy (lines 345-365)
1. Try strict IDNA decode → `PUNY_IDNA`
2. Fall back to `idna.decode()` → `PUNY_ALT`
3. Extract partial Unicode from error message → `PUNY_ALT`
4. Complete failure → `PUNY_INVALID`

**Note**: Tags are NOT mutually exclusive - domains can have both `PUNY_ALT` and `PUNY_INVALID`.

### File Processing Conventions
- **Input**: Always read from user-selected paths (no hardcoded filenames)
- **Output**: `{original_basename}.{yyyymmdd}.csv` (date appended before extension)
- **Duplicate Prevention**: Track processed files via `file_data` list with full paths
- **Encoding**: UTF-8 for all CSV/HTML operations, handle `NaN` explicitly with `math.isnan()`

## Development Workflows

### Running the Application
```powershell
# Install dependencies
pip install -r requirements.txt  # pandas>=2.2.0, idna>=3.6

# Launch GUI
python hnsell.py
```

### Testing CSV Processing
Place test files in `csv-s/` subdirectories:
- `csv-bob/csv_bob-tr/` - Bob Wallet transactions
- `csv-nb/csv_nb-tld/` - Namebase domain exports
- `csv-ss/csv_ss-tld/` - Shakestation domain listings

### HTML Portfolio Generation
- **Marketplace Linking**: Automatically routes to `namebase.io/domains/[tld]` or `shakestation.io/domain/[tld]` based on source CSV
- **Shakestation Filter**: Only includes domains where `for_sale=TRUE` column exists
- **Sort Behavior**: Cycles through Random → Alphabetical ▲ → Alphabetical ▼ via `sort_state` variable

## Project-Specific Conventions

### Naming Conventions
- Source identifiers: `bob`, `nb` (Namebase), `ss` (Shakestation), `fw` (Firewallet)
- File types: `-tr` (transactions), `-tld` (top-level domain lists)
- Example: `process_ss_tld()` processes Shakestation domain export

### Error Handling
- CSV parsing uses pandas with `on_bad_lines='skip'` and fallback `quoting=1, escapechar='\\'` for malformed Shakestation exports
- Missing columns checked via case-insensitive search: `col.lower() == 'domain'`
- Unicode regex cleanup: `re.sub(r'(?:\\x[\da-fA-F]{2})+|\\u(?:[\da-fA-F]{4})+', '', unicode_str)` removes escape sequences

### GUI State Management
- Active tab determines `process_action()` behavior (index 0/1/2)
- File lists maintain parallel structures: `file_data` (full info) + `file_listbox` (display)
- Options persist across selections: `rename_orig_var`, `sort_to_subdirs_var`, `delete_orig_var`

## Key Integration Points

### External Dependencies
- **pandas**: CSV manipulation, column operations, NaN handling
- **idna**: Punycode encoding/decoding with both strict and lenient modes
- **tkinter**: GUI framework (standard library but may need separate install on Linux)

### HTML Template Structure (pagemaker.py)
- Embeds CSS/JavaScript inline for single-file deployment
- Dark/light mode toggle via `body.dark-mode` class
- Tag-based filtering: domains tagged with multiple categories appear in multiple sections
- Navigation: `onclick="showTagSection('tag')"` dynamically shows/hides tag sections

## Common Tasks

### Adding Support for New CSV Source
1. Update `detect_csv_source()` with unique header pattern
2. Add `process_[source]_[type]()` method following existing pattern
3. Add case to `process_punytag()` switch statement
4. Update help text in `show_help()` method

### Modifying Punycode Validation
- Core logic in `punycode_convert_validate()` (lines 345-365)
- Invalid character list maintained separately in `check_and_tag_unicode()` (pagemaker.py, lines 18-32)
- Tags affect both CSV columns and HTML grouping

### Customizing HTML Output
- Modify CSS in `pagemaker.py` `css_style` string variable (lines 68-185)
- Footer/credits injected via `{footer_content}` and `{credits_content}` placeholders
- Grid layout automatically responsive via CSS Grid with `.col` class

## Current Limitations
- Puny ⟷ Unicode tab only accepts .txt files (CSV support removed per requirements)
- HTML updates only add domains, not remove (except Shakestation for_sale=FALSE)
- Recursive folder search has no depth limit (could be slow on large directory trees)
- No undo functionality - rely on `_orig` suffix backups
