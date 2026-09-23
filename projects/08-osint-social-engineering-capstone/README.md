# Social-Engineering Attack Simulation & OSINT Profiling

A sequence of coursework assignments building from basic OSINT collection to a full attacker-mindset capstone: OSINT → target profiling → attack-plan hypothesis → detection/defense framework. All conducted under explicit course scope and informed consent.

> **Note on anonymization:** the real subjects of these exercises (a university lecturer, a course instructor) are referred to below by role only. No attack was ever executed — every exercise stopped at the *analysis* stage: identifying what an attacker *could* plausibly do and why, then building defenses against it.

## Building blocks

- **OSINT on an individual:** profiled a university lecturer using only public academic sources (institutional staff page, LinkedIn, ORCID, Google Scholar) to reason about predictable scheduling windows and pretext plausibility — no contact made.
- **OSINT on an organization:** mapped the university's public org structure, funding/procurement cycles, and digital tool stack, identifying predictable "stress windows" (admissions period, fiscal year-end, semester start) where social-engineering attempts are statistically more likely to succeed.
- **From OSINT to profiling:** formalized the organizational map into a layered model (governance / operations / campuses / external network), explicitly separating **verified observations** from **probabilistic assumptions** — a distinction that matters because conflating the two is how bad intelligence gets treated as certain.

## Capstone: OSINT → Profiling → Attack-Plan Hypothesis → Defense

Built a complete analytical attack-planning exercise against a consenting subject (the course instructor):

1. **Collection:** structured OSINT across LinkedIn, public code repositories, an institutional publication archive, and regional press.
2. **Profiling:** built a relationship map and career/activity timeline from the collected data.
3. **Attack-plan hypothesis (analytical only, never executed):** developed plausible pretext narratives, timing windows, and communication-channel sequencing an attacker *could* use — as an analytical model, not a real attempt.
4. **Defense framework:** translated the attack hypothesis into concrete detection signals — channel-mismatch (a request arriving through an unexpected channel), calendar mismatch (a request timed against known unavailability), and verification asymmetry (how easy it is to fake a data point vs. how easy it is to verify it).

## Also in this course: image & geolocation OSINT

A separate exercise applied OSINT techniques to visual media: EXIF metadata extraction (`exiftool`) plus multi-engine reverse image search (Google, Yandex, TinEye) and visual-clue analysis (architecture, signage, language) to geolocate a set of provided photographs — correctly identifying 5 of 6 locations to town level, including one via an architectural landmark match later confirmed by reverse search. A notable methodological finding: every one of the 6 images had at least one *incorrect* caption already circulating publicly online, and one image carried GPS metadata from a camera model that has no GPS hardware — a fabricated or artificially-added geotag. Lesson: never trust metadata at face value; corroborate independently.

## Why this is worth including

Social engineering is the highest-leverage attack vector precisely because it targets people, not systems — understanding it from the attacker's side (what information makes a pretext credible, what timing makes urgency plausible) is what makes the defensive side (detection signals, awareness training) actually effective rather than generic.
