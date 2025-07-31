# LMSFinal
## 🔄 System Workflow

```mermaid
graph TD
    A[Start] --> B[Send API Request to /modules endpoint]
    B --> C[Extract module_id for each course module]
    C --> D[Generate final Moodle URL (module_id + 1)]
    D --> E[Navigate to Course Page with Playwright]
    E --> F{Login Required?}
    F -->|Yes| G[Auto-login using credentials in .env]
    G --> H[Reload Course Page]
    F -->|No| H
    H --> I[Expand All Collapsible Sections (sectiontoggler)]
    I --> J[Extract all <a href="..."> from #region-main-box]
    J --> K[Normalize, Deduplicate, Validate Links]
    K --> L[Filter Irrelevant or Blocked Links]
    
    L --> M[Classify Each Link]
    M --> N{Internal or External?}
    
    N -->|Internal| O[Get MIME Type via Playwright]
    O --> P{MIME Type?}
    P -->|text/html| Q[Queue for Recursive Crawling]
    P -->|File (e.g., PDF/DOC)| R[Download & Store if Not Malicious]
    P -->|Moodle mod/resource| S[Resolve Actual Resource URL]

    N -->|External| T[Check Accessibility via HEAD/GET]
    T --> U[Detect Paywalls (see Paywall Detection Procedure)]

    S --> V[Submit All Processed Links to /scrapedcontents/]
    R --> V
    Q --> V
    U --> V
    T --> V

    V --> W[Run downloadFilesAndCheck() on all documents]
    W --> X[Extract and Process Embedded Document Links]
    X --> Y[Submit Document Links (same filtering/classification)]

    Y --> Z[Print Crawl Summary]
    Z --> ZA[List internal pages visited]
    ZA --> ZB[List external links found]
    ZB --> ZC[Log errors and skipped resources]
    ZC --> ZD[End]
```
