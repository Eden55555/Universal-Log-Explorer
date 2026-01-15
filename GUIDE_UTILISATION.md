# User Guide
## Universal Log Explorer - Enhanced Edition

Quick guide to use the application efficiently.

---

## 🚀 Quick Start

### 1. Open the Application

1. Open the file `index_improved.html` in your browser
2. The interface displays with a loading zone

### 2. Load a Log File

**Method 1: Drag & Drop**
- Drag your log file onto the "Click or drag a log file here" zone
- The file loads automatically

**Method 2: Click**
- Click on the loading zone
- Select your file in the file explorer

**Supported formats**: `.log`, `.txt`, `.json`

---

## 📋 Main Interface

### Header

```
┌─────────────────────────────────────────────────────────────┐
│ [Drop Zone]  [Encoding ▼]  [Level ▼]  [Search]  [Filters]   │
│ [Export]  [Stats]  [🌙]                                      │
└─────────────────────────────────────────────────────────────┘
```

**Elements**:
- **Drop Zone**: Drag & drop or click to load
- **Encoding**: Choose file encoding (UTF-8, Windows-1255, etc.)
- **Level Filter**: Filter by level (ERROR, WARN, INFO, DEBUG)
- **Search**: Text search in logs
- **ID Filter**: Filter by user ID (e.g., "1551de:287")
- **Source Filter**: Filter by source/service name
- **IP Filter**: Filter by IP address
- **Export**: Export filtered logs
- **Stats**: Show statistics
- **🌙/☀️**: Toggle dark/light theme

### File Path Display

- **Location**: Below header, above table
- **Content**: Full file path (when available)
- **Format**: `File: C:\path\to\file.log`

### Filter Badges

- **Location**: Below file path, above table
- **Display**: Badges for each active filter
- **Removal**: Click × on badge to remove filter
- **Auto-update**: Badges update automatically

### Log Table

```
┌────┬──────────┬──────────────┬──────┬────────┬──────────────┬──────────┐
│ ⭐ │ Date     │ Level        │ ID   │ Source │ Message/Path │ IP       │
├────┼──────────┼──────────────┼──────┼────────┼──────────────┼──────────┤
│ ☆  │ 2024-... │ INFO         │ 1551 │ Bed... │ GET /api...  │ 192.168..│
│ ⭐ │ 2024-... │ ERROR        │ 1552 │ Auth.. │ POST /err..  │ 10.0.0.1 │
└────┴──────────┴──────────────┴──────┴────────┴──────────────┴──────────┘
```

**Columns**:
- **⭐/☆**: Bookmark button (click to bookmark)
- **Date / Time**: Date and time
- **Level**: Log level (ERROR, WARN, INFO, etc.)
- **ID**: User ID (e.g., "1551de:287" or "1551de:---")
- **Source**: Source/service name
- **Message / Path**: Message or request path
- **IP / Source**: IP address or source

**Color codes**:
- 🔴 **Red**: Errors (4xx, 5xx, ERROR, CRITICAL, etc.)
- 🟠 **Orange**: Warnings (WARN)
- 🟢 **Green**: Success (2xx, OK)

**Zebra striping**: Alternating row colors for better readability

---

## 🔍 Search and Filtering

### Text Search

1. **Activate**: Search bar appears after loading
2. **Type**: Your search term
3. **Result**: Real-time filtering (300ms debounce)
4. **Highlighting**: Search matches are highlighted in yellow

**Searched in**:
- Message/Path
- IP/Source
- Method/Level
- User ID
- Source

**Example**:
```
Search: "error"
→ Shows all logs containing "error" (case-insensitive)
→ Highlights "error" in yellow
```

### Multiple Filters

1. **Search Filter**: General text search
2. **Level Filter**: Select level from dropdown (All Levels, ERROR, WARN, INFO, DEBUG)
3. **ID Filter**: Enter user ID (e.g., "1551de")
4. **Source Filter**: Enter source/service name
5. **IP Filter**: Enter IP address

**Combination**: All filters can be combined for precise filtering.

**Badges**: Active filters appear as badges above the table.

### URL Filter Sharing

- **Shareable**: Copy URL to share filters with others
- **Auto-load**: Filters are loaded from URL on page load
- **Format**: `?search=term&level=ERROR&id=1551de&source=Service&ip=192.168.1.1`

### Column Sorting

1. **Click**: On column header
2. **First click**: Ascending sort (A→Z, 0→9)
3. **Second click**: Descending sort (Z→A, 9→0)
4. **Indicator**: Arrow in header indicates direction

**Example**:
```
Click on "Level" → Sorts by level (DEBUG, INFO, WARN, ERROR)
Click again → Reversed sort (ERROR, WARN, INFO, DEBUG)
```

---

## ⌨️ Keyboard Navigation

### Available Shortcuts

| Key | Action |
|-----|--------|
| `↑` (Arrow Up) | Select previous log |
| `↓` (Arrow Down) | Select next log |
| `Esc` | Close details panel |
| `Ctrl+F` | Focus on search bar |
| `Tab` | Standard navigation between elements |

### Usage

1. **Select a log**: Use arrows ↑/↓
2. **Auto-scroll**: Selection stays visible automatically
3. **Details**: Details panel opens automatically
4. **Search**: `Ctrl+F` to search quickly

**Note**: Arrows don't work if search bar is active.

---

## 📊 Details Panel

### Opening

- **Click**: Click on a log row
- **Keyboard**: Use arrows ↑/↓

### Tabs

#### 1. RAW Tab

**Content**: Complete raw log line

**Actions**:
- **Expand**: Show full message
- **Collapse**: Reduce (default)

#### 2. JSON Tab

**Content**: Extracted and formatted JSON interactively

**Features**:
- **Interactive tree**: Click on ▼ to expand/collapse
- **Syntax highlighting**:
  - 🔵 Keys (purple)
  - 🔴 Strings (red)
  - 🔵 Numbers (blue)
  - 🟢 Booleans (green)
  - ⚪ Null (gray)
- **Nested JSON**: Badge "Nested JSON" for JSON in strings
- **Multiple JSON**: If multiple JSON found, numbered (#1, #2, etc.)

**Actions**:
- **Copy JSON**: Copy raw JSON
- **Copy All**: Copy all panel content

#### 3. Metadata Tab

**Content**: Structured log information
- Date, Level, ID, Source, IP
- Message length
- Line index

### Search in Details

- **Search bar**: Located in details panel
- **Real-time**: Highlights matches in active tab
- **Debounce**: 300ms
- **Restoration**: Original content restored when search cleared

### Resizing

1. **Drag**: Left border of panel (cursor ↔)
2. **Minimum width**: 300px
3. **Maximum width**: Full screen
4. **Minimize**: Button to minimize panel

### Closing

- **Button ×**: Top right
- **Esc key**: Closes panel

---

## ⭐ Bookmarks/Favorites

### Usage

1. **Bookmark**: Click star icon (☆) in first column
2. **Unbookmark**: Click again (⭐)
3. **Visual indicator**: Bookmarked rows have special styling
4. **Persistence**: Session-only (cleared on page refresh or new file)

### Notes

- Bookmarks use unique log IDs (not indices)
- Bookmarks survive sorting
- Bookmarks are cleared when a new file is loaded
- Bookmarks are not saved to localStorage (session-only)

---

## 📈 Statistics

### Display

1. **Button "Stats"**: Click in header
2. **Panel**: Displays at bottom right

### Displayed Metrics

- **Total Entries**: Total number of loaded lines
- **Filtered**: Number of lines after filtering
- **Errors**: Number of errors (red)
- **Warnings**: Number of warnings (orange)
- **Success**: Number of successes (green)
- **Top IPs**: 5 most frequent IPs

### Update

Statistics update automatically when filtering.

---

## 💾 Export

### Available Formats

1. **CSV**: Spreadsheet format (Excel, Google Sheets)
2. **JSON**: Structured JSON format
3. **TXT**: Plain text format (one line per log)

### Usage

1. **Button "Export"**: Click in header
2. **Format**: Choose format (csv, json, or txt)
3. **Download**: File downloads automatically

**Note**: Only **filtered** logs are exported (not all logs).

### CSV Example

```csv
Date,Level,ID,Source,Message,IP
2024-01-01 12:00:00,INFO,1551de:287,BedrockService,GET /api/users,192.168.1.1
2024-01-01 12:01:00,ERROR,1552de:123,AuthService,POST /api/error,10.0.0.1
```

---

## 🎨 Dark/Light Theme

### Toggle Theme

1. **Button 🌙/☀️**: Click in header
2. **🌙**: Dark mode
3. **☀️**: Light mode

### Persistence

The chosen theme is saved and restored automatically on next open.

---

## 🔧 Encoding

### Change Encoding

1. **Dropdown menu**: Select encoding in header
2. **Options**:
   - **Hebrew (Windows-1255)**: Default
   - **UTF-8 (Standard)**: Universal standard
   - **Hebrew (ISO-8859-8)**: Hebrew alternative
   - **Western (ISO-8859-1)**: Latin-1

### Reload

If a file is already loaded, changing encoding automatically reloads the file.

**When to change**:
- Characters displayed incorrectly (e.g., Hebrew, Arabic, Chinese)
- File created on different system
- Parsing errors

---

## 📝 Common Use Cases

### 1. Find All Errors

1. Load log file
2. In "Level" filter, select **ERROR**
3. Result: Only error logs

### 2. Search for Specific IP

1. Load file
2. In IP filter, enter IP (e.g., `192.168.1.1`)
3. Result: All logs from this IP

### 3. Filter by User ID

1. Load file
2. In ID filter, enter user ID (e.g., `1551de`)
3. Result: All logs for this user ID

### 4. Filter by Source

1. Load file
2. In Source filter, enter source name (e.g., `BedrockService`)
3. Result: All logs from this source

### 5. Analyze Complex JSON Log

1. Load file
2. Click on a row containing JSON
3. In details panel:
   - See raw message (RAW tab)
   - Explore structured JSON (click on ▼ in JSON tab)
   - Copy JSON if needed

### 6. Export Errors

1. Filter by **ERROR**
2. Click **Export**
3. Choose format (CSV recommended)
4. Download file

### 7. View Statistics

1. Load file
2. Click **Stats**
3. See:
   - Total number of logs
   - Number of errors/warnings
   - Top IPs

### 8. Analyze LLM Log (AWS Bedrock, Claude)

1. Load log
2. Click on a row containing LLM response
3. JSON will be automatically extracted even if it's in:
   - Markdown block ` ```json ... ``` `
   - Escaped JSON string
   - Nested JSON object
4. Explore JSON tree to see complete structure

### 9. Share Filtered View

1. Apply filters (search, level, ID, source, IP)
2. Copy URL from address bar
3. Share URL with others
4. Recipients see same filtered view

### 10. Bookmark Important Logs

1. Click star icon (☆) on important logs
2. Logs are marked with ⭐
3. Bookmarks persist during session
4. Use for quick reference

---

## ⚡ Tips and Best Practices

### Performance

- **Large files**: Application efficiently handles up to 1M+ lines
- **Filtering**: Use filters to reduce number of logs displayed
- **Search**: Search is optimized with debounce (300ms)

### Navigation

- **Keyboard arrows**: Faster than scrolling to navigate
- **Sorting**: Use sorting to organize logs (e.g., by date, by status)
- **Search + Filter**: Combine both for precise searches

### Export

- **Pre-filtering**: Filter before exporting to keep only what's needed
- **CSV format**: Ideal for analysis in Excel/Sheets
- **JSON format**: Ideal for programmatic processing

### JSON

- **Root collapsed**: Root JSON is collapsed by default (click ▼ to expand)
- **Children expanded**: Children are opened automatically
- **Nested JSON**: JSON in strings is automatically detected and parsed

### Bookmarks

- **Session-only**: Bookmarks are cleared on refresh
- **Survive sorting**: Bookmarks stay with correct log after sorting
- **Quick access**: Use bookmarks to mark logs for later review

---

## ❓ Frequently Asked Questions (FAQ)

### Q: File doesn't load

**A**: Check:
- Encoding (try UTF-8)
- File size (<200 MB recommended)
- Browser console (F12) for errors

### Q: JSON not detected

**A**: 
- Check that JSON is valid
- Look at RAW message to see raw content
- JSON may be in non-standard format (markdown, escaped, etc.)

### Q: Characters displayed incorrectly

**A**: Change encoding in dropdown (try UTF-8 or Windows-1255)

### Q: How to export only errors?

**A**: 
1. Filter by "ERROR"
2. Click "Export"
3. Choose format

### Q: Theme doesn't persist

**A**: Check that localStorage is not blocked in your browser

### Q: How to see multiple JSON in same log?

**A**: If multiple JSON are detected, they are all displayed and numbered (#1, #2, etc.)

### Q: Slow performance with large files

**A**: 
- Use filters to reduce number of logs
- Use modern browser (Chrome, Firefox, Edge)
- Reduce file size if possible

### Q: Search not working

**A**: 
- Check browser console (F12) for errors
- Verify search input is visible
- Try clearing all filters

### Q: Bookmarks not persisting

**A**: Bookmarks are session-only by design. They are cleared when:
- A new file is loaded
- The page is refreshed
- The browser tab is closed

### Q: How to share filtered view?

**A**: 
1. Apply filters
2. Copy URL from address bar
3. Share URL
4. Recipients see same filtered view

### Q: Filter badges not showing

**A**: 
- Badges appear automatically when filters are active
- Check that filters are applied (not empty)
- Refresh page if needed

---

## 🎯 Quick Shortcuts

| Action | Shortcut |
|--------|----------|
| Load file | Drag & drop or click |
| Search | `Ctrl+F` |
| Navigate | `↑` / `↓` |
| Close details | `Esc` |
| Export | Button "Export" |
| Statistics | Button "Stats" |
| Theme | Button 🌙/☀️ |
| Bookmark | Click star icon |

---

## 📚 Resources

- **Technical documentation**: See `DOCUMENTATION.md`
- **Improvements**: See `IMPROVEMENTS.md`
- **Source code**: `index_improved.html`

---

**Guide created on**: 2024  
**Version**: 2.1 Enhanced

**Happy log analysis! 🚀**
