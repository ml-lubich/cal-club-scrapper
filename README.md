# CalClubScraper

> Scrapes all the emails of UC Berkeley's (Cal) student organizations
> (clubs). Outputs a CSV of clubs and contact email(s) per club.
> Powered by [Selenium](https://www.selenium.dev/documentation/) and
> [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup/bs4).

```mermaid
flowchart LR
    URL[("🌐 callink.berkeley.edu<br/>/organizations")]
    DRIVER["🤖 chromedriver<br/>(auto install/cleanup)"]
    SEL["🕸 Selenium<br/>page rendering"]
    BS["🍲 BeautifulSoup4<br/>HTML parse"]
    SCRAPER{{"🔁 src/scraper.py<br/>walk clubs"}}
    OUT[/"📄 clubs.csv<br/>name + email(s)"/]

    URL --> DRIVER --> SEL --> BS --> SCRAPER --> OUT

    classDef io fill:#0e1116,stroke:#2f81f7,stroke-width:1.5px,color:#e6edf3;
    classDef tool fill:#161b22,stroke:#3fb950,stroke-width:1.5px,color:#e6edf3;
    classDef brain fill:#161b22,stroke:#d29922,stroke-width:1.5px,color:#e6edf3;
    classDef out fill:#0e1116,stroke:#a371f7,stroke-width:1.5px,color:#e6edf3;
    class URL io;
    class DRIVER,SEL,BS tool;
    class SCRAPER brain;
    class OUT out;
```

## Table of contents

- [Output sample](#output-sample)
- [Scrape loop (sequence)](#scrape-loop-sequence)
- [Parse algorithm](#parse-algorithm)
- [Runtime Environment](#runtime-environment)
- [Installation Steps](#installation-steps)
- [Running the Scraper](#running-the-scraper)
- [Sources](#sources)
- [🗺️ Repository map](#️-repository-map)

## Parse algorithm

```mermaid
flowchart LR
    A([profile HTML])
    B["BeautifulSoup(html)"]
    C["find name node"]
    D["find contact section"]
    E["regex emails<br/>[A-Z0-9._%+-]+@[A-Z0-9.-]+"]
    F{"emails found?"}
    G["len > 1?"]
    H["row = (name, [emails])"]
    I["row = (name, email)"]
    J["row = (name, '')"]
    Z([append to clubs.csv])
    A --> B --> C
    B --> D --> E --> F
    F -- yes --> G
    G -- yes --> H --> Z
    G -- no  --> I --> Z
    F -- no  --> J --> Z
    C --> H
    C --> I
    C --> J
```

## Scrape loop (sequence)

```mermaid
sequenceDiagram
    participant S as scraper.py
    participant CD as chromedriver
    participant CL as callink.berkeley.edu
    participant BS as BeautifulSoup
    participant CSV as clubs.csv

    S->>CD: install + launch headless
    S->>CL: GET /organizations
    CL-->>S: club index HTML
    loop per club link
        S->>CL: GET /organization/(id)
        CL-->>S: profile HTML
        S->>BS: parse(html)
        BS-->>S: name + email(s)
        S->>CSV: append row
    end
    S->>CD: cleanup driver
```

## Output sample

![](screenshots/demo.png)

*Note: If there are more than 1 email, the entry is returned as a list of emails.*

---
## Runtime Environment
CalClubScraper runs using `pip3` packages. You also would need Python 3.6+ `chromedriver` may be flagged by the security system of platforms like MacOS, causing the program to crash. All you need to do: go to Security Preferences and click "Allow" to open the chromedriver executable. *Note: `chromedriver` is installed and deleted automatically by the program at runtime.

---
## Installation Steps 
1. If you have not already, install [`Python 3.6+`](https://www.python.org/downloads/)
2. Install all `pip3` required packages by running `pip3 install -r requirements.txt` in command line.

---
## Running the Scraper
To run the scraper, you can run the `scraper.py` script by typing `python3 src/scraper.py` in your terminal (from the root directory). Follow the status messages! *It may take a while*

Can change the endpoint URL being scraped. Simply go to https://callink.berkeley.edu/organizations and select an option (in the dropdown to the left) to filter the clubs you would like to scrape.

---
## Sources
* BeautifulSoup4 documentation: https://www.crummy.com/software/BeautifulSoup/bs4/doc/ 
* Selenium documentation: https://selenium-python.readthedocs.io/ 
* Website scraped: https://callink.berkeley.edu/ 


## 🗺️ Repository map

Top-level layout of `cal-club-scrapper` rendered as a Mermaid mindmap (auto-generated from the on-disk tree).

```mermaid
mindmap
  root((cal-club-scrapper))
    screenshots/
      demo.png
    files
      LICENSE
      README.md
      requirements.txt
```
