Read resume.md and CLAUDE.md to understand the candidate's full profile — skills, experience, seniority, target roles, and preferences.
Below (after ---JOBS---) is a JSON array of scraped job postings to evaluate — this batch only, not the full dataset.

For each job in this batch, score it 0–100 based on how well it matches THIS specific candidate:

Scoring factors:
- Stack match: award high points if the job requires skills the candidate has, per resume.md and the "Stack" section of CLAUDE.md
- Seniority fit: infer the candidate's actual experience level (junior/mid/senior/staff/principal/director) from resume.md and weight roles at a matching level favorably
- Location signals: honor the remote/hybrid/location preferences in the "Preferences" section of CLAUDE.md
- Company stage: honor any company-size preference in CLAUDE.md; otherwise don't penalize based on company size
- Posting freshness: award points if the job was posted within the last 30 days (use today's date provided at the end of this prompt) and the role is still open; penalize or skip listings that are expired, closed, or posted more than 30 days ago
- Red flags: anything listed under "Avoid" in CLAUDE.md's Preferences, plus generic dealbreakers (posting closed/expired, requires relocation the candidate can't do, stack mismatch)

Include only jobs with score >= 60.

Your entire response must be only the raw JSON array — no markdown fences, no explanation, nothing before or after it:

[{
  "title": "",
  "company": "",
  "url": "",
  "score": 0,
  "verdict": "apply|review|skip",
  "match_reasons": [],
  "red_flags": [],
  "suggested_angle": ""
}]

suggested_angle: one sentence on how the candidate should frame their application for this specific role, based on their resume.

---JOBS---
