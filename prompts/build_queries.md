Read the files resume.md and CLAUDE.md to extract a job search configuration for the candidate.

Output ONLY a valid JSON object — no markdown fences, no explanation, no extra text — in this exact shape:

{
  "target_roles": [],
  "key_skills": [],
  "search_queries": []
}

Rules:
- target_roles: 4–6 job title variants based on the candidate's actual experience and the "Target roles" section in CLAUDE.md
- key_skills: the top 6–8 technologies/competencies the candidate is strongest in, extracted from their skills section and work experience (prioritize what appears in both resume.md and the "Stack" section of CLAUDE.md)
- search_queries: 8–10 Firecrawl-ready search strings combining role titles and skills with site: prefixes. Use OR operators for breadth. Cover at minimum: linkedin.com/jobs, indeed.com, glassdoor.com, wellfound.com. Also include 3 Reddit queries using the groups below. Follow the location/remote preferences in the "Preferences" section of CLAUDE.md (e.g. include "remote" and/or specific cities/countries as appropriate).

Reddit subreddits to always include (produce exactly 3 grouped queries, one per group):
  - Job boards group: r/jobbit, r/remotejobs, r/WorkOnline
  - Freelance/gig group: r/freelance, r/forhire
  - Field-specific community group: pick 2–3 subreddits relevant to the candidate's field based on their resume (e.g. r/webdev for web developers, r/devops for infrastructure, r/marketing for marketers)

Reddit query format — group subreddits with OR, add "hiring" for community groups:
  "(site:reddit.com/r/jobbit OR site:reddit.com/r/remotejobs OR site:reddit.com/r/WorkOnline) (<role keywords>) (<skill keywords>) remote"
  "(site:reddit.com/r/freelance OR site:reddit.com/r/forhire) (<role keywords>) (<skill keywords>) remote"
  "(<field-specific subreddits>) hiring (<role keywords>) remote"

Example non-Reddit query format:
  "site:linkedin.com/jobs (<role keywords>) (<skill keywords>) (remote OR hybrid)"

Your entire response must be only the raw JSON object — nothing before or after it.
