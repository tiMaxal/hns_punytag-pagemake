# HNSell - Handshake Domain Manager

A comprehensive GUI application for managing Handshake (HNS) domain CSV exports from multiple wallet sources.

*Voding [vibe-coding] by copilot[20251227]timaxal*

## Features

### 3-Tab Interface

#### Tab 1: Punytag Processor
- **Multi-Source Support**: Automatically processes CSV files from:
  - Bob Wallet (transaction history and TLD exports)
  - Namebase.io (transaction history and domain exports)
  - Shakestation.io (domain exports)
  - Firewallet (exports)
- **Automatic Source Detection**: Identifies source format from CSV headers
- **Punycode Conversion**: Converts punycode domains (xn--) to Unicode with proper tagging
- **Batch Processing**: Select individual files or entire folders
- **Recursive Search**: Option to search subdirectories for CSV files
- **Smart Duplicate Prevention**: Skips already processed files
- **Flexible Output Options**:
  - Rename originals with '_orig' suffix
  - Sort outputs to subdirectories by source
  - Delete original files after processing
  - Automatic date stamping (yyyymmdd)

#### Tab 2: Puny ⟷ Unicode Converter
- **Bidirectional Conversion**: Convert between Punycode and Unicode
- **Multiple Format Support**:
  - TXT files: Pure conversion based on content detection
  - CSV files: Bob-TLD format with single column
- **Batch Processing**: Convert multiple files at once

#### Tab 3: PageMaker
- **HTML Portfolio Generation**: Create beautiful portfolio pages from domain lists
- **Multi-Source Compilation**: Combine domains from Namebase and Shakestation
- **Smart Linking**: Automatically links to appropriate marketplace:
  - https://www.namebase.io/domains/[tld]
  - https://shakestation.io/domain/[tld]
- **For-Sale Filter**: Only includes Shakestation domains marked 'for_sale=TRUE'
- **Flexible Sorting**: 
  - Random (default)
  - Alphabetical ascending
  - Alphabetical descending
  - Cycle through options with Sort button
- **Customization Options**:
  - Optional footer HTML
  - Optional credits HTML
- **Update Existing Pages**: Add or remove domains from existing portfolio HTML
- **Responsive Design**: Dark/light mode toggle, zoom controls, search functionality

## Installation

### Requirements
- Python 3.7+
- Required packages (install via pip):

```bash
pip install -r requirements.txt
```

Required packages:
- pandas==2.2.0
- idna==3.6
- regex==2023.12.25
- tkinter (usually included with Python)

### Running the Application

```bash
python hnsell.py
```

## Usage Guide

### Processing CSV Files (Tab 1)

1. **Select Files**:
   - Click "Select Files" to choose individual CSV files
   - OR click "Select Folder" to process all CSVs in a directory
   - Enable "Recursive Search" to include subdirectories

2. **Review Selection**:
   - Selected files appear in the list with source type detection [bob-tr], [nb-tld], etc.
   - Use "Select All" / "Select None" to manage selections

3. **Configure Options**:
   - ☑ Rename original files with '_orig' suffix
   - ☑ Sort processed files to subdirectories by source
   - ☑ Delete original files (use with caution!)

4. **Process**:
   - Click the green "Process" button
   - Files are processed with date stamp appended
   - Success message shows number of files processed

### Converting Punycode (Tab 2)

1. Click "Select Files" and choose .txt or .csv files
2. Click the green "Process" button
3. Converted files are saved with '_uni' or '_puny' suffix

### Creating Portfolio Pages (Tab 3)

1. **Select Domain CSVs**:
   - Click "Select CSV Files"
   - Choose Namebase or Shakestation domain exports

2. **Configure Sorting** (optional):
   - Click "Sort TLDs" to cycle through sort options
   - Random → Alphabetical ▲ → Alphabetical ▼

3. **Add Custom Content** (optional):
   - Select Footer HTML file
   - Select Credits HTML file

4. **Set Output**:
   - Enter desired filename (default: portfolio.html)

5. **Generate**:
   - Click the green "Process" button
   - HTML file is created in the current directory

### Updating Existing Portfolios

1. In PageMaker tab, click "Select HTML File" under "Update Existing Page"
2. Select CSV files to add/remove domains
3. Process as normal

## Button Reference

- **🟢 Process**: Execute the current tab's action
- **🟡 Help**: Display detailed usage instructions
- **🔴 Exit**: Close the application

## File Format Detection

The application automatically detects source formats based on CSV headers:

- **Bob-TR**: `time`, `txhash`, `domains` columns
- **Bob-TLD**: Single `domains` column
- **Namebase-TR**: `extra.domain`, `extra.action` columns
- **Namebase-TLD**: `name`, `tags` columns
- **Shakestation**: `domain`, `for_sale` columns
- **Firewallet**: Other formats

## Output Files

### Processed CSVs
- Format: `original_name_YYYYMMDD.csv`
- Original files renamed to: `original_name_orig.csv` (if option selected)

### Converted Files
- Punycode to Unicode: `original_name_uni.csv` or `original_name_uni.txt`
- Unicode to Punycode: `original_name_puny.csv` or `original_name_puny.txt`

### Portfolio HTML
- User-defined filename (default: `portfolio.html`)
- Includes embedded CSS and JavaScript
- Responsive design with dark mode support

## Punycode Tagging System

The processor adds tags to identify conversion methods:

- **PUNY_IDNA**: Successfully decoded using IDNA standard
- **PUNY_ALT**: Decoded using alternative method
- **PUNY_INVALID**: Contains invalid Unicode characters

## Tips

- Test with a small batch first before processing large numbers of files
- Use the "Rename original" option to preserve original files
- Enable recursive search to find all CSVs in a project directory
- Portfolio pages include search functionality and tag filtering
- Use the Help button for quick reference while working

## Generated Portfolio Features

- **Dark/Light Mode**: Toggle with 🌙 / ☀️ button
- **Zoom Controls**: +/- buttons for text size
- **Tag Navigation**: Click tags to filter domains
- **Search Function**: Real-time domain search
- **Random Colors**: Each link gets unique decoration color
- **Tooltips**: Hover over truncated names to see full text
- **External Links**: All domains link to their marketplace pages

## Troubleshooting

### Common Issues

1. **"No module named 'tkinter'"**: 
   - On Linux: `sudo apt-get install python3-tk`
   - On Mac: Tkinter should be included with Python
   - On Windows: Reinstall Python with tkinter option selected

2. **CSV not detected correctly**:
   - Check that CSV has proper headers
   - Verify file is valid CSV format

3. **Punycode conversion errors**:
   - Some punycode domains may be invalid
   - Check the PUNY_INVALID tag in output

## Author

Based on original punycode processing scripts by @i1li

## Version

1.0.0 - Initial GUI release (2025-12-26)
