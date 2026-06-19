# GTM-Engineer-Marketing-Intel

# GTM Engineering Market Intelligence Workflow

A lightweight market-intelligence workflow built with **n8n**, **Apify**, **Google Sheets**, and **LLMs** to analyze how the market is defining **GTM Engineering**.

## Overview

GTM Engineering is an emerging role category sitting at the intersection of Revenue Operations, Sales Operations, Growth, Business Systems, workflow automation, data infrastructure, and applied AI.

The problem: the market does not yet have one clear definition of what a GTM Engineer is.

This project uses public job-posting data to explore that question.

The workflow:

1. Scrapes public LinkedIn job postings related to GTM Engineering
2. Uses contrasting examples of strong and weak matches to generate a classification rubric
3. Classifies newly scraped jobs into relevance categories
4. Filters the most relevant postings
5. Trims noisy job descriptions down to role-relevant sections
6. Uses an LLM to generate a market-intelligence report

The goal is not to create a perfect labor-market study. The goal is to demonstrate how automation, structured data, and LLMs can turn noisy public information into useful market intelligence.

---

## Why I Built This

I built this project to better understand what the market is actually asking for in GTM Engineering roles.

I have experience in process engineering, Revenue Operations, GTM systems, workflow design, and cross-functional operating models. But GTM Engineering increasingly expects builders who can work across tools like **Clay**, **n8n**, **Zapier**, **Make**, APIs, data systems, and LLM workflows.

This project was designed to answer two questions:

1. What does the market mean when it says “GTM Engineer”?
2. What responsibilities, tools, systems, and technical skills appear repeatedly across public GTM Engineering job postings?

---

## Workflow Architecture

The workflow has three connected branches tied together by a Google Sheets data repository.

```text
Learn → Scrape → Classify → Filter → Trim → Analyze
```

### Branch 1: Rubric Generation

This branch creates the classification logic.

I manually selected examples of strong-fit and poor-fit roles. An LLM compared those examples and generated a reusable rubric for classifying future job postings.

The rubric classifies roles into categories such as:

* Core GTM Engineer
* Strong Adjacent - GTM Systems
* Adjacent - GTM Ops/Strategy
* Adjacent - Customer-Facing Technical
* Weak Match
* Exclude

### Branch 2: Scheduled Job Collection and Classification

This branch runs on a schedule.

n8n triggers an Apify actor to scrape public LinkedIn job postings related to “GTM Engineer.” The results are written to Google Sheets.

An LLM then applies the classification rubric to each new posting and assigns a market-fit category.

This branch is deliberately narrow: it classifies jobs, but does not analyze the market.

### Branch 3: Market Intelligence Analysis

This branch performs the actual market analysis.

It retrieves the in-scope jobs, trims the job descriptions to preserve role-relevant sections, and passes the prepared sample to an LLM for synthesis.

The output is a market-intelligence report covering:

* Role archetypes
* Common responsibilities
* Tools and technical signals
* Business outcomes
* Company and market patterns
* Experience and background patterns
* Open questions and limitations

---

## Tools Used

* **n8n** — workflow orchestration
* **Apify** — public job-posting scrape actor
* **Google Sheets** — lightweight data repository
* **OpenRouter** — access to LLM models
* **LLMs** — rubric generation, classification, and market-intelligence analysis
* **JavaScript Code Nodes** — data preparation, JSON parsing, and job-description trimming

---

## Data Repository Structure

Google Sheets acts as the system of record for the MVP.

Recommended tabs:

```text
LI_Job_Postings
GTME_Rubric
Market_Intel_Runs
Run_Log
```

### `LI_Job_Postings`

Stores raw scraped jobs and classification outputs.

Example fields:

```text
jobId
jobTitle
companyName
location
jobDescription
yearsOfExperience
marketFitBucket
classificationReason
recommendedUse
classifiedAt
modelUsed
```

### `GTME_Rubric`

Stores the active classification rubric.

Example fields:

```text
rubricId
rubricVersion
generatedAt
modelUsed
isActive
rubricJson
rubricSummary
```

### `Market_Intel_Runs`

Stores each generated market-intelligence report.

Example fields:

```text
analysisId
analysisDate
inputJobCount
includedBuckets
modelUsed
promptVersion
executiveSummary
fullReportMarkdown
notes
```

---

## Methodology

### 1. Job Collection

Public LinkedIn job postings are collected using an Apify actor. n8n triggers the actor, receives the results, and appends the jobs to Google Sheets.

### 2. Contrastive Rubric Creation

I manually selected strong and weak examples of GTM Engineering roles. An LLM analyzed the contrast between those examples and generated a classification rubric.

This was intended to reduce the amount of hard-coded personal bias in the classification process while still giving the system useful initial examples.

### 3. Job Classification

Each scraped posting is classified using the rubric.

The classifier is intentionally constrained. Its job is only to decide whether a posting belongs in the GTM Engineering market-analysis sample.

It does not perform the final market analysis.

### 4. Job Description Trimming

The full job descriptions were too large to pass into the market-analysis node without hitting model context limits.

To solve this, a preprocessing step trims each description to preserve role-relevant sections such as:

* Responsibilities
* Requirements
* Qualifications
* Tools and systems
* Workflow ownership
* Experience requirements
* Business outcomes

It removes or reduces lower-signal sections such as:

* About the company
* Why join us
* Benefits
* Culture language
* Legal boilerplate

### 5. Market Intelligence Analysis

The market-analysis node receives only the filtered, trimmed, in-scope jobs.

The LLM analyzes the sample to identify recurring patterns across responsibilities, tools, role archetypes, business outcomes, experience requirements, and market ambiguity.

---

## Sample Findings

The current sample suggests that GTM Engineering is a technical discipline focused on designing, building, and operating the data-driven, automation-centric infrastructure behind a company’s go-to-market motion.

Recurring patterns include:

* Workflow automation across the GTM stack
* CRM and revenue-systems integration
* API and data movement
* AI/LLM-enabled workflow design
* Internal tooling for sales, marketing, and customer-success teams
* Pipeline generation and sales productivity outcomes
* Data quality, routing, enrichment, and observability
* Blurred boundaries with RevOps, Sales Ops, Growth, and Solutions Engineering

A working definition from the analysis:

> GTM Engineering is the technical discipline of designing, building, and operating the data-driven, automation-centric infrastructure that enables a company’s go-to-market motion.

---

## What I Learned

This project became a hands-on learning exercise in automation, APIs, workflow design, data preparation, and LLM orchestration.

Key lessons included:

* How HTTP Requests differ from Webhooks
* How APIs and JSON payloads move data between systems
* How Apify actors can be triggered from n8n
* How n8n expressions, `$json`, `.first()`, and `JSON.stringify()` work
* Why some nodes should run once per item while others should aggregate many items
* How to use LLMs as specific workflow components rather than general-purpose chatbots
* Why classification and analysis should be separate workflow steps
* How token limits force better data preparation
* How Google Sheets can serve as a lightweight workflow repository
* How errors in parsing, escaping, context size, and item references reveal weak points in workflow design

The biggest takeaway: GTM Engineering is not about one specific tool. It is about owning the workflows, systems, data movement, automation, and increasingly AI-enabled infrastructure behind the go-to-market motion.

---

## Current Limitations

This is an MVP and should be interpreted as directional market intelligence, not a comprehensive labor-market study.

Known limitations:

* The sample is based on public LinkedIn job postings
* The role category is still emerging and noisy
* Some relevant jobs may not use the phrase “GTM Engineer”
* Some roles using the phrase may be adjacent or weak fits
* Compensation analysis is not fully implemented
* Job descriptions are trimmed to manage context limits
* The classification rubric should improve with more labeled examples
* The workflow does not yet include robust deduplication
* The analysis reflects a snapshot in time, not a longitudinal trend

---

## Next Improvements

Planned improvements include:

* Better deduplication across job scrapes
* Salary and OTE extraction
* Tool-frequency counting
* Company-stage segmentation
* Weekly market-intelligence snapshots
* Cleaner run logging
* Better error handling
* A more polished data model
* Comparison against adjacent roles like RevOps, Sales Ops, Growth, and Solutions Engineering
* A reusable workflow template for emerging-role market analysis

---

## Repository Structure

Suggested structure:

```text
gtm-engineering-market-intel/
│
├── README.md
├── methodology.md
│
├── prompts/
│   ├── rubric-generation-prompt.md
│   ├── classification-prompt.md
│   └── market-intel-prompt.md
│
├── workflow/
│   └── n8n-workflow-export.json
│
├── outputs/
│   └── sample-market-intel-report.md
│
├── data/
│   └── sample-classified-jobs-redacted.csv
│
└── screenshots/
    ├── workflow-overview.png
    ├── rubric-branch.png
    ├── classification-branch.png
    └── market-intel-output.png
```

---

## Privacy and Data Notes

This project uses public job-posting data.

Before publishing workflow exports or sample data, remove:

* API keys
* OpenRouter credentials
* Apify tokens
* Google credentials
* Private sheet URLs
* Personal notes
* Employer-specific or confidential information
* Any sensitive workflow metadata

The public version of this repo should contain only sanitized workflow exports, prompts, methodology, screenshots, and sample outputs.

---

## Status

Current status: **MVP / v0.1**

The workflow is functional and has produced an initial GTM Engineering market-intelligence report. The next phase is packaging, documentation, and improving repeatability.
