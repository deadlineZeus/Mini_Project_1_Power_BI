# Recorded Lectures, Decoded: An EdTech Power BI Case Study

A 3,000-course dataset, one messy CSV, and a single question: where should an EdTech startup focus to grow its recorded-lecture business?

![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat&logo=powerbi&logoColor=black)
![DAX](https://img.shields.io/badge/DAX-CALCULATE%20%7C%20ALL%20%7C%20ALLEXCEPT-blue)
![Status](https://img.shields.io/badge/Status-Complete-success)

---

## The Brief

An EdTech startup wants to expand its recorded-lecture catalog, but it's sitting on raw, unstructured data scraped from multiple course platforms — and no clear view of what's actually working. As the analyst on this project, my job was to turn that raw export into a category-first dashboard that could answer ten concrete business questions: from basic catalog size and quality, down to which languages, instructors, and course formats actually drive engagement.

The brief was explicit about one thing — everything had to be sliceable by category, since category is how the business thinks about its content roadmap.

---

## The Data

The raw source (`Online_Courses.csv`) was an aggregated export from sites like Coursera — 8,800+ rows and over 45 columns, most of which were platform-specific and mostly empty (pricing tiers, access durations, FAQs, related programs, etc. that only applied to a handful of rows). Key fields that actually mattered were buried among the noise:

- `Category` / `Sub-Category` / `Course Type`
- `Language` / `Subtitle Languages` (a comma-separated multi-value field)
- `Instructors` (also multi-value, comma-separated)
- `Rating`, `Number of viewers`, `Duration`

This is a realistic "real-world scrape" problem: inconsistent formatting, multi-value text fields packed into single cells, and a long tail of sparse columns that needed to be filtered out before any real analysis could happen.

---

## The Process

**1. Cleaning and shaping (Power Query)**
Stripped the dataset down to the columns that mattered, parsed star-rating and viewer-count strings into proper numeric types, and standardized duration into hours. The raw rows were cleaned down to approximately 3,000 usable courses.

**2. Modeling for multi-value fields**
Two fields — `Subtitle Languages` and `Instructors` — held multiple comma-separated values per course, which would have broken any simple aggregation. I built separate duplicated tables in Power Query (a Subtitle Count table and a Tutors & Ratings table) so each subtitle/instructor could be unnested and counted or ranked independently, without disturbing the grain of the main course table.

**3. DAX layer**
The report leans on `CALCULATE` for context-aware aggregations (e.g., average viewers within a category), `ALL` and `ALLEXCEPT` to control exactly which filters get cleared when computing category- and language-level totals and rankings (so a category's overall average isn't accidentally sliced by a language filter, for example), and ranking logic to surface top instructors per category/sub-category.

**4. Report design**
Twelve pages: a title page, ten question-driven analysis pages, and a conclusion — with category slicers, drillthrough-style expandable matrices, and tooltips used throughout so a viewer can go from the headline number straight down to the category/sub-category/language level without leaving the page.

---

## The Dashboard, Question by Question

| # | Question | What the dashboard shows |
|---|---|---|
| Q1 | Total catalog size | Approximately 3,000 courses after cleaning — the baseline for every other metric |
| Q2 | Average course rating | 4.70 / 5 average rating across the cleaned dataset — a strong quality baseline |
| Q3 | Total and average viewer engagement | 8M total registrations, averaging 3,210 per course |
| Q4 | Course type distribution by category | "Course"-type content pulls 7.0M total viewers vs. 1.4M for Specializations and a negligible amount for Projects — but per-course, Specializations actually average higher viewership (3.4K vs 3.2K), with Business (880) and Computer Science (442) as the largest categories by course count |
| Q5 | Average enrollment by category | Data Science leads at 6.3K average viewers, with Information Technology (4.6K) and Computer Science (4.4K) close behind — Math & Logic and Physical Science trail at the bottom |
| Q6 | Language distribution | English dominates at approximately 97% of all courses, with every other language (Spanish, French, Japanese, Chinese, Portuguese, Russian) splitting the remaining 3% |
| Q7 | Language preference by category (top 5) | A drillable ranking shows Data Science has the highest average registrations per course (6,327), and within every top category, English drives the overwhelming majority of registrations — non-English language registrations drop off sharply (e.g., Spanish at 1,740 vs. English's 6,364 within Data Science) |
| Q8 | Subtitles vs. viewer engagement | A clear, if uneven, positive relationship — viewership stays low through most subtitle counts but spikes dramatically for courses offering roughly 15-25 subtitle languages (peaking near 85K average viewers) |
| Q9 | Top instructors by category/sub-category | A drillable Category → Sub-Category → Instructor matrix surfacing the top 3 ranked instructors in each niche — built as a static visual per the brief, so the list is locked for reporting/outreach purposes |
| Q10 | Course duration vs. viewership | A sharp engagement spike for very short content (under approximately 5 hours), a secondary bump around 200 hours, then a steady decline — suggesting two distinct "sweet spots" rather than a single ideal length |

Screenshots for each page live in `/screenshots` — see the Repository Structure section below.

---

## Key Findings and Business Impact

- English-first, but not English-only: approximately 97% of engagement is English-language, yet courses with broader subtitle coverage see disproportionately higher viewership — localization/subtitling looks like an underused growth lever, not a nice-to-have.
- Data Science is the engagement leader: highest average registrations per course and the strongest performer once filtered by top languages — a clear candidate for expanded investment.
- Format matters more than total volume: Specializations slightly outperform standalone Courses on a per-course basis despite a much smaller footprint, hinting that bundled/structured content may convert better than one-offs.
- There's a duration sweet spot at both ends: short-form (under 5 hours) and a secondary mid-length cluster (around 200 hours) both outperform the long tail — useful for content-length planning.
- A short list of instructors is already over-performing: the top-3-by-category ranking gives the business a ready-made outreach list for content partnerships.

---

## Tools and Tech Stack

- Power BI Desktop — data modeling and report build
- Power Query (M) — cleaning, shaping, and unnesting multi-value fields into auxiliary tables
- DAX — `CALCULATE`, `ALL`, `ALLEXCEPT`, and ranking measures for category/language/instructor-level analysis
- Slicers, drillthrough matrices, and tooltips — for interactive, category-first exploration

---

## Repository Structure

```
├── Mini_Power_BI_project.pbix     # Full Power BI report
├── Online_Courses.csv             # Raw source data
├── screenshots/                   # Page-by-page dashboard screenshots
└── README.md
```

## How to Explore

1. Clone this repo.
2. Open `Mini_Power_BI_project.pbix` in Power BI Desktop (free download from Microsoft).
3. Use the category slicers on each page to filter the analysis, and expand the matrix visuals (Q7, Q9) to drill into language- and instructor-level detail.

---

## Challenges and What I'd Explore Next

The biggest modeling challenge was the multi-value `Subtitle Languages` and `Instructors` columns — a single course row could contain a dozen comma-separated instructors, which meant standard aggregations would have double-counted or missed relationships entirely. Solving this with auxiliary unnested tables in Power Query, connected back via `ALLEXCEPT`-controlled measures, was the core technical lift of the project.

With more time, I'd want to bring in time-series data (engagement over the life of a course, not just a snapshot) and pricing data, which was present but too sparse in this export to analyze reliably — both would sharpen the "where to invest next" recommendation considerably.

---

## Connect

If you found this useful or want to talk through the approach, feel free to connect — LinkedIn (#) · GitHub (#)
