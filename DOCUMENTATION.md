# Complete Technical Documentation
## Universal Log Explorer - Enhanced Edition

**Version**: 2.1 Enhanced  
**Date**: 2024  
**Type**: Single-File Application (SFA)  
**License**: Open Source

---

## Table of Contents

1. [Overview](#overview)
2. [Technical Architecture](#technical-architecture)
3. [Detailed Features](#detailed-features)
4. [JSON Extraction System](#json-extraction-system)
5. [Internal API](#internal-api)
6. [Configuration and Customization](#configuration-and-customization)
7. [Performance and Optimizations](#performance-and-optimizations)
8. [Browser Compatibility](#browser-compatibility)
9. [Troubleshooting](#troubleshooting)

---

## 1. Overview

### 1.1 Description

Universal Log Explorer is a single-page web application designed for technical analysis of complex log files. It is particularly optimized for:

- **LLM Developers**: Analysis of logs from services like AWS Bedrock, Claude, etc.
- **DevOps**: Quick inspection of server logs (Apache, Nginx, applications)
- **Backend Developers**: Debugging of structured JSON logs
- **Analysts**: Exploration of large log data

### 1.2 Main Features

- ✅ **Zero dependencies**: No external libraries required
- ✅ **Virtual Scrolling**: Efficient handling of millions of lines
- ✅ **Advanced JSON extraction**: Automatic detection of nested JSON
- ✅ **Multi-encoding**: Support for UTF-8, Windows-1255, ISO-8859-8, etc.
- ✅ **Dark/Light theme**: Adaptive interface
- ✅ **Multi-format export**: CSV, JSON, TXT
- ✅ **Advanced statistics**: Detailed metrics on logs
- ✅ **Accessibility**: Complete ARIA support, keyboard navigation
- ✅ **Search with highlighting**: Visual highlighting of search matches
- ✅ **Multiple filters**: Filter by ID, Source, IP with badges
- ✅ **URL filter persistence**: Shareable filter URLs
- ✅ **Tabbed details panel**: RAW/JSON/Metadata tabs
- ✅ **Search in details**: Search within the details panel
- ✅ **Zebra striping**: Alternating row colors for readability
- ✅ **Bookmarks/Favorites**: Mark important logs (session-only)

### 1.3 Supported Log Formats

- **Standard Web Logs**: Apache/Nginx format (Common Log Format, Combined Log Format)
- **JSON Logs**: Simple or complex JSON objects
- **Structured Logs**: Logs with metadata (IP, dates, levels)
- **Raw Logs**: Free text with heuristic extraction
- **Multi-line Logs**: Support for logs spread across multiple lines
- **MatrixLog Format**: Custom format with ID and Source fields

---

## 2. Technical Architecture

### 2.1 Technology Stack

```
┌─────────────────────────────────────┐
│   HTML5 (Structure)                  │
│   CSS3 (Variables + Grid/Flexbox)   │
│   JavaScript ES6+ (Vanilla)        │
└─────────────────────────────────────┘
```

**No external dependencies** - The application works entirely with web standards.

### 2.2 Code Structure

```
index_improved.html
├── <head>
│   ├── Meta tags (charset, viewport)
│   └── <style> (Embedded CSS)
├── <body>
│   ├── Header (Controls)
│   ├── File Path Display
│   ├── Filter Badges
│   ├── Table Header (Columns)
│   ├── Viewport (Virtual Scrolling)
│   ├── Details Overlay (Side Panel with Tabs)
│   └── <script> (Embedded JavaScript)
│       ├── Application state
│       ├── Regex patterns
│       ├── Log parsers
│       ├── Virtual Scrolling
│       ├── JSON extraction
│       ├── JSON Tree rendering
│       ├── Filter management
│       ├── Bookmark management
│       └── Event handlers
```

### 2.3 Rendering Engine

#### Virtual Scrolling

The application uses a custom **virtual scrolling** system:

```javascript
const ROW_HEIGHT = 40;        // Fixed height per row (px)
const VISIBLE_COUNT = 40;     // Number of visible rows simultaneously
```

**Principle**:
1. Calculate total height: `totalHeight = filteredLogs.length * ROW_HEIGHT`
2. Calculate visible window: `start = floor(scrollTop / ROW_HEIGHT)`
3. Render only visible rows: `slice(start, start + VISIBLE_COUNT)`
4. CSS translation: `transform: translateY(start * ROW_HEIGHT)`

**Advantages**:
- Constant performance even with 1M+ lines
- Optimized memory (only ~40 DOM elements)
- Smooth scrolling

### 2.4 Theme System

Uses **CSS Custom Properties** (CSS Variables):

```css
:root {
    --bg-color: #f8f9fa;
    --text-main: #333;
    /* ... */
}

[data-theme="dark"] {
    --bg-color: #1a1a1a;
    --text-main: #e6edf3;
    /* ... */
}
```

**Persistence**: Preferences are saved in `localStorage`.

---

## 3. Detailed Features

### 3.1 Data Loading

#### Loading Methods

1. **Drag & Drop**
   - Drag a file onto the drop zone
   - Visual feedback (green border, colored background)
   - Multi-file support (first file selected)

2. **Click on Zone**
   - Opens native file selector
   - Automatic filter: `.log`, `.txt`, `.json`

3. **Encoding**
   - **Default**: Windows-1255 (Hebrew)
   - **Alternatives**: UTF-8, ISO-8859-8, ISO-8859-1
   - **Dynamic change**: Automatic reload if file already open

#### Loading Process

```javascript
loadFile() {
    1. Display loading indicator
    2. Read file with FileReader
    3. Apply selected encoding
    4. Split lines (\n)
    5. Filter empty lines
    6. Parse each line
    7. Update interface
    8. Cache status classes
    9. Display full file path
    10. Clear bookmarks
}
```

### 3.2 Parsing System

#### Available Parsers

1. **JSON Parser**
   - Detection: Line starts with `{` or `[` and ends with `}` or `]`
   - Extraction: `JSON.parse()`
   - Automatic mapping:
     - `ip` → `j.ip || j.remote_addr`
     - `date` → `j.time || j.timestamp || j.date`
     - `userId` → `j.userId || j.user_id || j.id || extracted from raw line`
     - `source` → `j.source || j.service || extracted from raw line`
     - `method` → `j.level || j.method`
     - `path` → `j.message || j.msg || j.url`
   - **Unique ID**: Generated for each log entry

2. **Standard Web Parser**
   - Format: `IP user-ident userid [date] "method path protocol" status size`
   - Regex: `/^(\S+) \S+ \S+ \[(.*?)\] "(\S+) (\S+) \S+" (\d{3}) (\d+|-)/`
   - Direct extraction of groups
   - Extracts userId and source from raw line if present

3. **MatrixLog Format Parser**
   - Format: `[date] level userId source message`
   - Regex: `/\[(.*?)\]\s+(\w+)\s+([a-f0-9]{5,6}:[-\d]+)\s+([A-Z][a-zA-Z0-9]*)\s+(.+)/i`
   - Extracts: date, level, userId, source, message

4. **Heuristic Parser**
   - Used as last resort
   - Extraction by regex patterns:
     - IP: `/(\d{1,3}\.){3}\d{1,3}/`
     - Date: Multiple patterns (ISO, Apache, Custom)
     - Level: `/(ERROR|WARN|INFO|DEBUG|CRITICAL|SEVERE|EXCEPTION|FATAL)/i`
     - UserId: `/([a-f0-9]{5,6}:[-\d]+)/i` (e.g., "1551de:287" or "1551de:---")
     - Source: `/\s+([A-Z][a-zA-Z0-9]*Service|[A-Z][a-zA-Z0-9]*)\s+/`

### 3.3 Filtering and Search

#### Text Search

- **Searched fields**:
  - Message/Path
  - IP/Source
  - Method/Level
  - User ID
  - Source
- **Case sensitivity**: Case-insensitive
- **Debounce**: 300ms (performance optimization)
- **Real-time**: Instant filtering
- **Highlighting**: Search matches are highlighted with `<mark>` tags

#### Multiple Filters

1. **Search Filter**: General text search
2. **Level Filter**: Filter by log level (ERROR, WARN, INFO, DEBUG)
3. **ID Filter**: Filter by user ID (e.g., "1551de:287")
4. **Source Filter**: Filter by source/service name
5. **IP Filter**: Filter by IP address

#### Filter Badges

- **Visual indicators**: Badges appear above the table for active filters
- **Removal**: Click × on badge to remove filter
- **Auto-update**: Badges update automatically when filters change

#### URL Filter Persistence

- **Shareable URLs**: Filters are saved in URL parameters
- **Auto-load**: Filters are loaded from URL on page load
- **Format**: `?search=term&level=ERROR&id=1551de&source=Service&ip=192.168.1.1`

#### Column Sorting

- **Sortable columns**: All columns (Date, Level, ID, Source, Message, IP)
- **Direction**: Ascending / Descending (alternating click)
- **Visual indicator**: Arrow in header
- **Numeric sort**: Automatic for numeric fields

### 3.4 Keyboard Navigation

| Key | Action |
|-----|--------|
| `↑` (Arrow Up) | Select previous entry |
| `↓` (Arrow Down) | Select next entry |
| `Esc` | Close details panel |
| `Ctrl+F` | Focus on search bar |
| `Tab` | Standard navigation between elements |

**Behavior**:
- Auto-scroll to keep selection visible
- Automatic opening of details panel
- Disabled if search is active

### 3.5 Details Panel

#### Tabs

1. **RAW Tab**
   - Complete raw log line
   - Collapsible by default (space saving)
   - Expand/Collapse button

2. **JSON Tab**
   - Automatically detected JSON
   - Interactive tree display
   - Support for multiple JSON per log
   - Numbering if multiple JSON found

3. **Metadata Tab**
   - Structured log information
   - Date, Level, ID, Source, IP
   - Message length
   - Line index

#### Search in Details

- **Search bar**: Located in details panel
- **Real-time**: Highlights matches in active tab
- **Debounce**: 300ms
- **Restoration**: Original content restored when search cleared

#### Actions

- **Copy JSON**: Copy raw JSON
- **Copy All**: Copy all panel content
- **Minimize/Restore**: Minimize/restore panel
- **Resize**: Drag left border

#### Resizing

- **Minimum width**: 300px
- **Maximum width**: 100vw (full screen)
- **Default width**: 600px
- **Maximize mode**: Saves original width

### 3.6 File Path Display

- **Location**: Below header, above table
- **Content**: Full file path (when available)
- **Display**: Only shown after file is loaded
- **Format**: `File: C:\path\to\file.log`

### 3.7 Bookmarks/Favorites

- **Button**: Star icon (☆/⭐) in first column
- **Toggle**: Click to add/remove bookmark
- **Visual indicator**: Bookmarked rows have special styling
- **Persistence**: Session-only (not saved to localStorage)
- **Clearing**: Bookmarks cleared when new file loaded
- **Tracking**: Uses unique log ID (not index) to survive sorting

### 3.8 Zebra Striping

- **Alternating rows**: Even rows have different background color
- **Theme support**: Works in both light and dark themes
- **Readability**: Improves visual separation of rows

### 3.9 Export Logs

#### Supported Formats

1. **CSV** (Comma-Separated Values)
   - Headers: Date, Level, ID, Source, Message, IP
   - Encoding: UTF-8 with BOM
   - Escaping: Double quotes for values

2. **JSON**
   - Format: Array of objects
   - Indentation: 2 spaces
   - Encoding: UTF-8

3. **TXT** (Plain Text)
   - Format: One line per log (raw)
   - Encoding: UTF-8

#### Process

```javascript
exportLogs() {
    1. Ask user for format
    2. Filter logs (exports only filtered logs)
    3. Generate content according to format
    4. Create Blob
    5. Download via <a> with download attribute
    6. Display success notification
}
```

### 3.10 Statistics

#### Calculated Metrics

- **Total**: Total number of loaded lines
- **Filtered**: Number of lines after filtering
- **Errors**: Number of error logs
- **Warnings**: Number of warning logs
- **Success**: Number of success logs (2xx)
- **Top IPs**: 5 most frequent IPs
- **Status Codes**: Distribution of HTTP codes

#### Display

- Floating panel (bottom right)
- Toggle on/off with "Stats" button
- Automatic update on filtering

---

## 4. JSON Extraction System

### 4.1 Multi-Level Architecture

The JSON extraction system uses a cascade approach:

```
1. Direct Parsing
   ↓ (failure)
2. Markdown Extraction (```json ... ```)
   ↓ (failure)
3. Code Block Extraction (``` ... ```)
   ↓ (failure)
4. Recursive Search in JSON Objects
   ↓ (failure)
5. Extraction by Balanced Braces
```

### 4.2 `extractAllJsonFromString()` Function

#### Algorithm

```javascript
function extractAllJsonFromString(str) {
    const results = [];
    const seen = new Set(); // Avoid duplicates
    
    // 1. Direct parsing attempt
    try {
        const parsed = JSON.parse(str);
        results.push({ parsed, raw: str, source: 'direct' });
        return results;
    } catch (e) { /* Continue */ }
    
    // 2. Search in markdown blocks
    const markdownRegex = /```json\s*([\s\S]*?)```/gi;
    // ... extraction ...
    
    // 3. Search in code blocks
    const codeBlockRegex = /```\s*([\s\S]*?)```/g;
    // ... extraction ...
    
    // 4. Recursive search in JSON objects
    try {
        const parsed = JSON.parse(str);
        const foundJsons = findAllJsonInObject(parsed);
        results.push(...foundJsons);
    } catch (e) { /* Continue */ }
    
    // 5. Extraction by balanced braces
    // ... matching algorithm ...
    
    return results.length > 0 ? results : null;
}
```

### 4.3 Nested JSON Detection

#### Use Cases

1. **JSON in JSON String**
   ```json
   {
     "response": "{\"status\": \"ok\"}"
   }
   ```

2. **JSON in Markdown**
   ```
   Response: ```json
   {"status": "ok"}
   ```
   ```

3. **JSON in Log Property**
   ```json
   {
     "content": [{
       "type": "text",
       "text": "```json\n{\"key\": \"value\"}\n```"
     }]
   }
   ```

#### `findAllJsonInObject()` Function

Recursive search with depth limit (5 levels):

```javascript
function findAllJsonInObject(obj, depth = 0) {
    if (depth > 5) return []; // Infinite recursion protection
    
    if (typeof obj === 'string') {
        // JSON detection in string
        // Markdown extraction
        // Balanced braces extraction
    } else if (Array.isArray(obj)) {
        // Recursive traversal
    } else if (obj !== null && typeof obj === 'object') {
        // Recursive traversal of properties
    }
    
    return results;
}
```

### 4.4 Balanced Braces Algorithm

#### `findBalancedBraces()` Function

```javascript
function findBalancedBraces(str, startPos) {
    let depth = 0;
    let inString = false;
    let escapeNext = false;
    
    for (let i = startPos; i < str.length; i++) {
        const char = str[i];
        
        // Escape handling
        if (escapeNext) {
            escapeNext = false;
            continue;
        }
        
        if (char === '\\') {
            escapeNext = true;
            continue;
        }
        
        // String handling
        if (char === '"' && !escapeNext) {
            inString = !inString;
            continue;
        }
        
        if (inString) continue; // Ignore in strings
        
        // Brace counting
        if (char === '{') depth++;
        else if (char === '}') {
            depth--;
            if (depth === 0) {
                return { start: startPos, end: i };
            }
        }
    }
    
    return null; // Not balanced
}
```

**Advantages**:
- Respects JSON strings (doesn't count `{` in strings)
- Handles escapes (`\"`, `\\`)
- Returns exact position of balanced braces

### 4.5 JSON Tree Rendering

#### `createJsonNode()` Function

```javascript
function createJsonNode(key, value, forceCollapse = false) {
    // Nested JSON detection in string
    // Toggle creation (▼)
    // Span creation (key, value)
    // Recursion for objects/arrays
    // Collapse/expand handling
}
```

#### Rendering Behavior

1. **Root Node**: Always `collapsed` by default (`forceCollapse = true`)
2. **Child Nodes**: Always `expanded` by default (`forceCollapse = false`)
3. **Nested JSON Strings**: Automatic detection and parsing

#### JSON Styles

- **Keys**: Purple (`--json-key: #881391`)
- **Strings**: Red (`--json-string: #c41a16`)
- **Numbers**: Blue (`--json-number: #1c11ee`)
- **Booleans**: Green (`--json-bool: #0d904f`)
- **Null**: Gray italic

---

## 5. Internal API

### 5.1 Global Variables

```javascript
const ROW_HEIGHT = 40;              // Row height (px)
const VISIBLE_COUNT = 40;            // Number of visible rows
let allLogs = [];                    // All loaded logs
let filteredLogs = [];               // Logs after filtering
let currentFile = null;              // Current file
let selectedIndex = -1;              // Selection index
let sortColumn = null;               // Sort column
let sortDirection = 'asc';            // Sort direction
let statusClassCache = new Map();     // Status class cache
let bookmarkedLogs = new Set();      // Bookmarked log IDs (session-only)
let activeFilters = {                // Active filters
    search: '',
    level: '',
    id: '',
    source: '',
    ip: ''
};
let currentDetailsTab = 'raw';       // Current details tab
```

### 5.2 Regex Patterns

```javascript
const patterns = {
    ip: /(\d{1,3}\.){3}\d{1,3}/,
    date: /(\d{4}-\d{2}-\d{2}[T ]\d{2}:\d{2}:\d{2})|(\d{2}\/\w{3}\/\d{4}:\d{2}:\d{2}:\d{2})|(\d{2}\/\d{2}\/\d{4} \d{2}:\d{2}:\d{2}(\.\d+)?)/,
    level: /(ERROR|WARN|INFO|DEBUG|CRITICAL|SEVERE|EXCEPTION|FATAL)/i,
    httpStatus: / (20[0-6]|40[0-9]|50[0-9]) /,
    userId: /([a-f0-9]{5,6}:[-\d]+)/i,
    source: /\s+([A-Z][a-zA-Z0-9]*Service|[A-Z][a-zA-Z0-9]*)\s+/
};
```

### 5.3 Main Functions

#### Loading and Parsing

- `loadFile()`: Load and parse a file
- `processFileContent(lines)`: Process log lines
- `smartRawParse(line)`: Heuristic parsing

#### Rendering

- `render()`: Virtual scrolling and row rendering
- `createJsonNode(key, value, forceCollapse)`: Create JSON node
- `showDetails(index)`: Display details panel
- `switchDetailsTab(tabName)`: Switch between detail tabs
- `searchInDetails(searchTerm)`: Search within details panel

#### JSON Extraction

- `extractAllJsonFromString(str)`: Extract all JSON
- `extractJsonFromString(str)`: Extract first JSON (compatibility)
- `findAllJsonInObject(obj, depth)`: Recursive search
- `findBalancedBraces(str, startPos)`: Brace matching
- `findBalancedBrackets(str, startPos)`: Bracket matching

#### Filtering

- `applyFilters()`: Apply all active filters
- `updateFilterBadges()`: Update filter badge display
- `createFilterBadge(label, value, filterType)`: Create filter badge
- `removeFilter(filterType)`: Remove specific filter
- `updateURLFilters()`: Update URL with current filters
- `loadFiltersFromURL()`: Load filters from URL

#### Bookmarks

- `toggleBookmark(logId)`: Toggle bookmark for a log entry

#### Utilities

- `debounce(func, wait)`: Debounce for optimizations
- `copyText(text, btn)`: Copy to clipboard
- `showToast(message, duration)`: Toast notification
- `escapeHtml(text)`: XSS protection
- `escapeRegex(str)`: Escape regex special characters
- `highlightText(text, searchTerms)`: Highlight search matches
- `getStatusClass(log)`: Status CSS class (with cache)

#### Export and Statistics

- `exportLogs()`: Multi-format export
- `calculateStats()`: Calculate statistics
- `toggleStats()`: Show/hide stats panel

---

## 6. Configuration and Customization

### 6.1 Row Height

**CSS**:
```css
:root {
    --row-height: 40px;  /* Modify this value */
}
```

**JavaScript**:
```javascript
const ROW_HEIGHT = 40;  /* Modify this value */
```

**Important**: Both values must be synchronized.

### 6.2 Number of Visible Rows

```javascript
const VISIBLE_COUNT = 40;  /* Increase for more visible rows */
```

**Recommendation**: Keep between 30-50 for optimal performance.

### 6.3 Adding Encodings

In HTML:
```html
<select id="encoding-select">
    <option value="windows-1255">Hebrew (Windows-1255)</option>
    <option value="UTF-8">UTF-8 (Standard)</option>
    <!-- Add here -->
    <option value="ISO-8859-15">Western (ISO-8859-15)</option>
</select>
```

### 6.4 Modifying Regex Patterns

```javascript
const patterns = {
    ip: /(\d{1,3}\.){3}\d{1,3}/,
    date: /your-pattern-here/,
    level: /(ERROR|WARN|INFO|DEBUG|CUSTOM)/i,
    httpStatus: / (20[0-6]|40[0-9]|50[0-9]) /,
    userId: /([a-f0-9]{5,6}:[-\d]+)/i,
    source: /\s+([A-Z][a-zA-Z0-9]*Service|[A-Z][a-zA-Z0-9]*)\s+/
};
```

### 6.5 Adding Custom Parsers

```javascript
const mainParsers = [
    {
        name: 'Standard Web',
        regex: /^(\S+) \S+ \S+ \[(.*?)\] "(\S+) (\S+) \S+" (\d{3}) (\d+|-)/,
        map: (m) => ({ 
            id: `log_${Date.now()}_${Math.random().toString(36).substr(2, 9)}`,
            ip: m[1], 
            date: m[2], 
            method: m[3], 
            path: m[4], 
            raw: m[0] 
        })
    },
    // Add your parser here
    {
        name: 'Custom Format',
        regex: /your-regex/,
        map: (m) => ({ /* mapping with id */ })
    }
];
```

### 6.6 Color Customization

**Light Theme**:
```css
:root {
    --bg-color: #f8f9fa;
    --header-bg: #2c3e50;
    --text-main: #333;
    --error-color: #e74c3c;
    --warning-color: #f39c12;
    --success-color: #27ae60;
}
```

**Dark Theme**:
```css
[data-theme="dark"] {
    --bg-color: #1a1a1a;
    --header-bg: #0d1117;
    --text-main: #e6edf3;
    /* ... */
}
```

### 6.7 Search Debounce

```javascript
searchInput.oninput = debounce(() => {
    applyFilters();
}, 300);  // Modify delay (ms)
```

---

## 7. Performance and Optimizations

### 7.1 Virtual Scrolling

**Complexity**: O(1) for rendering (constant)

**Memory**: ~40 DOM elements maximum, regardless of number of logs

**Performance**: 60 FPS even with 1M+ lines

### 7.2 Status Class Cache

```javascript
let statusClassCache = new Map();

function getStatusClass(log) {
    const cacheKey = `${log.method}-${log.path}-${log.userId || ''}-${log.source || ''}`;
    if (statusClassCache.has(cacheKey)) {
        return statusClassCache.get(cacheKey);
    }
    // ... calculation ...
    statusClassCache.set(cacheKey, statusClass);
    return statusClass;
}
```

**Advantage**: Avoids repeated recalculations during scroll.

### 7.3 Search Debounce

**Delay**: 300ms

**Effect**: Reduces filtering calculations during fast typing.

### 7.4 DocumentFragment

```javascript
const fragment = document.createDocumentFragment();
// ... element creation ...
content.appendChild(fragment);
```

**Advantage**: Batch DOM updates, reduces reflows.

### 7.5 Performance Limits

| Metric | Recommended Limit | Maximum Limit |
|--------|------------------|---------------|
| Log lines | 100,000 | 1,000,000+ |
| File size | 50 MB | 200 MB |
| Nested JSON | 5 levels | 10 levels |
| Visible rows | 40 | 100 |

---

## 8. Browser Compatibility

### 8.1 Supported Browsers

| Browser | Minimum Version | Status |
|---------|----------------|--------|
| Chrome | 90+ | ✅ Complete |
| Firefox | 88+ | ✅ Complete |
| Edge | 90+ | ✅ Complete |
| Safari | 14+ | ✅ Complete |
| Opera | 76+ | ✅ Complete |

### 8.2 APIs Used

- **FileReader API**: File reading
- **Clipboard API**: Copy (with `document.execCommand` fallback)
- **CSS Custom Properties**: CSS variables
- **CSS Grid / Flexbox**: Layout
- **ES6+ Features**: Arrow functions, const/let, Map, Set, etc.

### 8.3 Fallbacks

- **Clipboard API**: Fallback to `document.execCommand` for older browsers
- **localStorage**: No fallback (required for theme)

---

## 9. Troubleshooting

### 9.1 Common Issues

#### File doesn't load

**Possible causes**:
- Incorrect encoding
- File too large (>200 MB)
- Unsupported file format

**Solutions**:
1. Try another encoding
2. Split file into multiple parts
3. Check console for errors

#### JSON not detected

**Possible causes**:
- Malformed JSON
- JSON in non-standard format
- JSON too deeply nested

**Solutions**:
1. Check JSON validity
2. Use RAW display to see raw content
3. Check depth limits (5 levels by default)

#### Slow performance

**Possible causes**:
- Too many logs (>1M)
- Complex filters
- Old browser

**Solutions**:
1. Reduce number of logs (pre-filtering)
2. Use modern browser
3. Reduce `VISIBLE_COUNT` if necessary

#### Theme doesn't persist

**Cause**: localStorage disabled or blocked

**Solution**: Allow cookies/localStorage in browser settings

#### Search not working

**Possible causes**:
- JavaScript errors in console
- Filters not applied correctly

**Solutions**:
1. Check browser console (F12)
2. Verify filter inputs are visible
3. Try clearing all filters

#### Bookmarks not persisting

**Note**: Bookmarks are session-only by design. They are cleared when:
- A new file is loaded
- The page is refreshed
- The browser tab is closed

### 9.2 Debug

#### Console Debug Mode

Open console (F12) and use:

```javascript
// See all logs
console.log(allLogs);

// See filtered logs
console.log(filteredLogs);

// See statistics
console.log(calculateStats());

// Test JSON extraction
console.log(extractAllJsonFromString("your-text"));

// See active filters
console.log(activeFilters);

// See bookmarks
console.log(bookmarkedLogs);
```

#### Pattern Verification

```javascript
// Test a pattern
const testLine = "192.168.1.1 - - [01/Jan/2024:12:00:00] \"GET / HTTP/1.1\" 200 1234";
const match = testLine.match(mainParsers[0].regex);
console.log(match);
```

---

## 10. Changelog

### Version 2.1 Enhanced (2024)

**New features**:
- ✅ File path display (full path)
- ✅ Search with highlighting
- ✅ Multiple filters (ID, Source, IP) with badges
- ✅ URL filter persistence (shareable)
- ✅ Tabbed details panel (RAW/JSON/Metadata)
- ✅ Search in details panel
- ✅ Zebra striping for readability
- ✅ Bookmarks/Favorites (session-only)
- ✅ Column reordering (ID, Source added)
- ✅ MatrixLog format parser

**Improvements**:
- 🔧 JSON extraction improved (nested JSON handling)
- 🔧 Virtual scrolling optimized
- 🔧 Error handling improved
- 🔧 UX improved (user feedback)
- 🔧 Bookmark tracking uses unique IDs (survives sorting)

**Fixes**:
- 🐛 Fixed JSON extraction in strings
- 🐛 Fixed JSON detection in markdown
- 🐛 Fixed performance issues with large files
- 🐛 Fixed bookmark persistence (now session-only)
- 🐛 Fixed search highlighting
- 🐛 Fixed filter badge display

### Version 2.0 Enhanced (2024)

**New features**:
- ✅ Multi-level JSON extraction (markdown, code blocks, nested)
- ✅ Multi-format export (CSV, JSON, TXT)
- ✅ Advanced statistics
- ✅ Dark/Light theme
- ✅ Column sorting
- ✅ Level filters
- ✅ Toast notifications
- ✅ Loading indicator
- ✅ Complete ARIA accessibility
- ✅ XSS protection
- ✅ Performance optimizations (debounce, cache)

---

## 11. Support and Contribution

### 11.1 Bug Reports

In case of issues, provide:
- Browser version
- Log file size
- Log format
- Console error message (F12)

### 11.2 Improvement Suggestions

Suggestions welcome for:
- New log formats
- New features
- Performance improvements
- Accessibility improvements

---

**Documentation generated on**: 2024  
**Last update**: Version 2.1 Enhanced
