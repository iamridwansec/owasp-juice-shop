# OWASP Juice Shop Walkthrough: "Meta Geo Stalking (PNG Architectural Variance)"

This documentation provides an engineering breakdown of how file formatting differences (JPEG vs. PNG) affect metadata extraction techniques during forensic asset examination.

---

##  Challenge Context & Objective
If the target user image asset is exported or saved natively in a PNG container format rather than a standard JPEG, the storage mechanism for locational coordinates shifts. The objective remains the same—extracting geographic coordinates to bypass knowledge-based security vectors—but the analyst's terminal inspection methods must adapt to the underlying structural change.

---

##  Step-by-Step Procedure

### Step 1: Technical Reality of PNG Metadata Storage
Unlike JPEGs, which natively use dedicated binary blocks for EXIF infrastructure, the baseline PNG specification handles metadata using structured blocks called **chunks** (such as `tEXt`, `zTXt`, or `iTXt` text blocks). Modern platforms regularly embed standard EXIF payloads directly into a custom compressed chunk header labeled `eXIf`.

### Step 2: Adaptive Extraction Pipeline in Kali Linux
Standard basic imaging utilities might fail to parse text chunks correctly. To extract coordinates from a target PNG file, you must adapt your tactical parsing commands.

1. Open your Kali terminal interface.
2. Run your dedicated parsing scanner over the target asset (`favorite-hiking-place.png`):
   ```bash
   exiftool favorite-hiking-place.png
   ```

#### Alternative Pipeline: Native String Scanning
If an asset pipeline has stripped typical structure flags but left raw ASCII metadata strings intact within the graphic's text chunks, invoke a raw string extract sequence to pull the geolocational data fields directly out of the binary layout:
   ```bash
   strings favorite-hiking-place.png | grep -i -E "gps|lat|long|position"
   ```

#### Terminal Execution Alternative Output
The binary text scanner parses the PNG structure directly, bypassing the absence of standard JPEG formatting layers to reveal the text chunks:
```text
iTXtEXif:GPSLatitude=36.958717
iTXtEXif:GPSLongitude=-84.348217
```

### Step 3: Resolution Consistency
Once the metadata coordinates are extracted, the downstream parsing mechanics remain unchanged. The decimal degrees translate to identical points on the mapping plane, yielding the constant answer string: `Daniel Boone National Forest`.

---

##  Core Security Concepts Covered

### 1. File Format Chunk Architecture (Anatomy of a PNG)
PNG files rely on sequential chunk payloads wrapped between fixed file markers. Because text blocks can be arbitrarily injected into these chunks (`tEXt` or `iTXt`), security filters that only inspect or strip common JPEG APP1 sections will entirely miss security exposures hiding inside alternative image architectures.

### 2. False Sense of Security in Format Conversion
Users often mistakenly believe that changing an asset's file extension or saving a JPEG template out as a PNG automatically sanitizes privacy-violating telemetry. Modern imaging software carries existing properties across container formats during export processes unless explicit metadata stripping instructions are declared.

### 3. Comprehensive Sanitization Frameworks
A secure backend upload pipeline must evaluate incoming media assets based on their true magic bytes rather than simple file extension labels. Sanitize the underlying file structures comprehensively across all potential graphic architectures (including PNG text chunks) before making the assets accessible over a public web matrix.

