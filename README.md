# Obsidian-journaling
Here is the code from my video

## Dataview code (european date DD-MM-YYYY):
```dataviewjs
// Get the current daily note filename (format: dd-mm-yyyy, e.g., "07-12-2025")
const fileName = dv.current().file.name;

// Parse the filename to extract day, month, and year components
const [day, month, year] = fileName.split('-');

// Reformat the date to ISO standard format (yyyy-mm-dd) for comparison
// This converts "07-12-2025" → "2025-12-07"
const targetDate = `${year}-${month}-${day}`;

/**
 * Normalizes various date formats to a standard string (yyyy-mm-dd)
 * This function handles different ways dates might be stored in properties:
 * - JavaScript Date objects
 * - Dataview date objects with timestamp
 * - String formats (yyyy-mm-dd or dd-mm-yyyy)
 */
function normalizeDate(dateValue) {
    // Return null if no date value is provided
    if (!dateValue) return null;
    
    // Check if the value is a Date object or Dataview date object (has .ts property)
    if (dateValue instanceof Date || dateValue.ts) {
        // Convert to standard Date object
        const d = dateValue instanceof Date ? dateValue : new Date(dateValue.ts);
        // Extract year, month, day with proper padding (e.g., "7" → "07")
        const y = d.getFullYear();
        const m = String(d.getMonth() + 1).padStart(2, '0'); // +1 because months are 0-indexed
        const dNum = String(d.getDate()).padStart(2, '0');
        // Return formatted date string
        return `${y}-${m}-${dNum}`;
    }
    
    // Handle string date values
    if (typeof dateValue === 'string') {
        // Check for ISO format (yyyy-mm-dd)
        if (dateValue.match(/^\d{4}-\d{2}-\d{2}$/)) {
            return dateValue; // Already in correct format
        }
        // Check for European format (dd-mm-yyyy) and convert it
        if (dateValue.match(/^\d{2}-\d{2}-\d{4}$/)) {
            const [d, m, y] = dateValue.split('-');
            return `${y}-${m}-${d}`; // Convert to yyyy-mm-dd
        }
    }
    
    // Return null for unsupported formats
    return null;
}

// Search all notes in the vault for those matching the target date
// - Filter notes that have a date property
// - Normalize the date to ensure consistent comparison
// - Sort by creation date (oldest first, ascending order)
const notesWithDate = dv.pages()
    .where(p => {
        // Skip notes without a date property
        if (!p.date) return false;
        // Normalize the date and check for match
        const noteDate = normalizeDate(p.date);
        return noteDate === targetDate;
    })
    .sort(p => p.file.cday); // Sort by creation day (cday)

// Display the results
if (notesWithDate.length > 0) {
    // Show list of links to matching notes
    dv.list(notesWithDate.map(p => p.file.link));
} else {
    // Show error message with date formats for debugging
    dv.paragraph(`No notes found with date ${fileName}.`);
    dv.paragraph(`Searching for: ${targetDate}`);
}
```

## Dataview code (US date MM-DD-YYYY):
```dataviewjs
// Get the current daily note filename (format: yyyy-mm-dd, e.g., "2025-12-07")
const fileName = dv.current().file.name;

// Since filename is already ISO, we use it directly as targetDate
const targetDate = fileName;

/**
 * Normalizes various date formats to a standard string (yyyy-mm-dd)
 */
function normalizeDate(dateValue) {
    if (!dateValue) return null;
    
    // Handle Date object or Dataview timestamp
    if (dateValue instanceof Date || dateValue.ts) {
        const d = dateValue instanceof Date ? dateValue : new Date(dateValue.ts);
        const y = d.getFullYear();
        const m = String(d.getMonth() + 1).padStart(2, '0');
        const dNum = String(d.getDate()).padStart(2, '0');
        return `${y}-${m}-${dNum}`;
    }
    
    // Handle string date values
    if (typeof dateValue === 'string') {
        // Check for ISO format (yyyy-mm-dd) - this is the priority match
        if (dateValue.match(/^\d{4}-\d{2}-\d{2}$/)) {
            return dateValue;
        }
        // Optional: Support for other string formats if needed, 
        // otherwise we stick to ISO as the primary format.
    }
    
    return null;
}

// Search all notes matching the target date
const notesWithDate = dv.pages()
    .where(p => {
        if (!p.date) return false;
        const noteDate = normalizeDate(p.date);
        return noteDate === targetDate;
    })
    .sort(p => p.file.cday);

// Display results
if (notesWithDate.length > 0) {
    dv.list(notesWithDate.map(p => p.file.link));
} else {
    dv.paragraph(`No notes found with date ${fileName}.`);
    dv.paragraph(`Searching for target ISO: ${targetDate}`);
}
```

## Dataview code (defailt date YYYY-MM-DD):
```dataviewjs
// Get the current daily note filename (format: yyyy-mm-dd, e.g., "2025-12-07")
const fileName = dv.current().file.name;

// Since filename is already ISO, we use it directly as targetDate
const targetDate = fileName;

/**
 * Normalizes various date formats to a standard string (yyyy-mm-dd)
 */
function normalizeDate(dateValue) {
    if (!dateValue) return null;
    
    // Handle Date object or Dataview timestamp
    if (dateValue instanceof Date || dateValue.ts) {
        const d = dateValue instanceof Date ? dateValue : new Date(dateValue.ts);
        const y = d.getFullYear();
        const m = String(d.getMonth() + 1).padStart(2, '0');
        const dNum = String(d.getDate()).padStart(2, '0');
        return `${y}-${m}-${dNum}`;
    }
    
    // Handle string date values
    if (typeof dateValue === 'string') {
        // Check for ISO format (yyyy-mm-dd) - this is the priority match
        if (dateValue.match(/^\d{4}-\d{2}-\d{2}$/)) {
            return dateValue;
        }
        // Optional: Support for other string formats if needed, 
        // otherwise we stick to ISO as the primary format.
    }
    
    return null;
}

// Search all notes matching the target date
const notesWithDate = dv.pages()
    .where(p => {
        if (!p.date) return false;
        const noteDate = normalizeDate(p.date);
        return noteDate === targetDate;
    })
    .sort(p => p.file.cday);

// Display results
if (notesWithDate.length > 0) {
    dv.list(notesWithDate.map(p => p.file.link));
} else {
    dv.paragraph(`No notes found with date ${fileName}.`);
    dv.paragraph(`Searching for target ISO: ${targetDate}`);
}
```

## Template for Templater plugin:
```text
---

date: <% tp.date.now("YYYY-MM-DD") %>

---
```
