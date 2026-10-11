## Overview and Code Flow

The script is a custom EDIFACT-to-JSON parser designed to process structured messaging data (such as COARRI container reports).

### Execution Flow

1. **File Loading**: Reads the raw EDIFACT text from `/source.EDI` using UTF-8 encoding.
2. **Main Transformation (`edi_to_json`)**:
   * Calls `read_segments()` to clean the text, detect an optional `UNA` header for custom delimiters, and split the text into a list of individual raw segments.
   * Iterates through each segment string, passing it to `parse_segment()`.


3. **Segment Parsing (`parse_segment`)**:
   * Splits the raw segment by element separators while respecting release/escape characters.
   * Maps the elements and composites against a predefined `SCHEMA` dictionary.
   * Executes helper functions (`to_iso`, `stowage_position`) to enrich and format specific tags (`UNB`, `DTM`, `LOC`).


4. **Data Aggregation**:
   * Groups repeating segments (defined in `REPEATING`) into JSON lists, while non-repeating segments are stored as direct key-value dictionaries.


5. **Output Generation**: Serializes the final Python dictionary into a formatted JSON string, prints it to standard output, and saves it to `result.json`.

---

## Function-by-Function Breakdown

### 1. `split_unescaped(text, sep, release="?")`

* **Purpose**: Splits a string by a given separator (`sep`) while ignoring any separators that are preceded by the escape/release character (`release`).
* **Logic**: Iterates character by character. If it encounters the escape character followed by another character, it groups them together into the current chunk. If it hits the unescaped separator, it flushes the current chunk into a parts list.

### 2. `unescape(value, release="?")`

* **Purpose**: Cleans up escape character pairs within parsed data values.
* **Logic**: Uses a regular expression to strip the release character from escaped sequences, leaving the literal character intact.

### 3. `read_segments(text)`

* **Purpose**: Prepares the raw document text and uncovers active delimiters.
* **Logic**: Strips leading BOM bytes and whitespace. Checks if the text starts with the `UNA` service string advice; if so, it extracts the 6 custom delimiters from indices 3 through 8 and slices them off the text. Finally, it splits the text into clean segment strings.

### 4. `to_iso(value, fmt_code)`

* **Purpose**: Normalizes EDIFACT date/time strings into standard ISO 8601 timestamps.
* **Logic**: Looks up the Python `strptime` format string using `DATE_FORMATS` based on the provided format code. Parses the date value and returns its ISO string representation, or `None` if parsing fails.

### 5. `stowage_position(value)`

* **Purpose**: Decodes marine container stowage positions into structured bay, row, and tier components.
* **Logic**: Evaluates digit-string lengths (7-digit or 6-digit formats) and slices them according to standardized maritime coordinate offsets.

### 6. `parse_segment(raw, comp_sep, elem_sep, rel)`

* **Purpose**: Translates a single raw segment string into a structured Python dictionary based on the `SCHEMA`.
* **Logic**: Extracts the segment tag (`UNB`, `EQD`, etc.) and its elements. Iterates through the schema definitions, mapping simple elements directly or splitting composite elements (`comps`) using component separators. Applies conditional post-processing enrichments for specific tags.

### 7. `edi_to_json(text)`

* **Purpose**: Coordinates the end-to-end parsing process for the entire EDIFACT document.
* **Logic**: Initializes a result dictionary, processes all individual segments via `read_segments` and `parse_segment`, uses `setdefault()` to group repeating segments into lists, and returns the complete hierarchical JSON structure.