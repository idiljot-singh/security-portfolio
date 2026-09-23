# Self-OSINT / Breach Exposure Audit

An OSINT self-audit combining search-engine dorking with breach-database cross-referencing, to test how much of a personal digital footprint is discoverable — and where each method's blind spots are.

**Tools:** Google search operators (`site:`, `filetype:`, `intitle:`, boolean `OR`, quoted phrases, exclusion `-`), [haveibeenpwned.com](https://haveibeenpwned.com)

## Method

1. Ran 14 structured queries across 6 different Google search operators against my own name and email address, to see what a purely open-web search surfaces.
2. Cross-checked the same email address against HaveIBeenPwned — a breach-intelligence source that doesn't appear in web search results at all.

## Finding

The web-search phase alone surfaced very little of substance. The breach-database check told a different story: **9 confirmed data-breach exposures** across multiple services (2019–2024), including sites using weak, unsalted MD5 password storage — none of which had shown up anywhere in the search-engine phase.

## Why this matters

A single method, even done thoroughly, misses real exposure. Open-web OSINT and breach-intelligence sources answer different questions, and skipping either one leaves a meaningful blind spot — a lesson that generalizes directly to assessing an organization's exposure, not just an individual's.
