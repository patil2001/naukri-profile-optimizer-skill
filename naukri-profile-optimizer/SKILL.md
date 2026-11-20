---
name: naukri-profile-optimizer
description: Audit and optimize the user's Naukri.com profile (headline, key skills, IT skills table, employment, profile summary, career profile, projects) so recruiter Resdex searches for Java/Spring Boot backend roles surface it. Use when the user says "optimize my naukri profile", "naukri keywords", "update naukri headline/key skills", or wants more relevant recruiter calls from Naukri. Reuses resume-ats-optimizer, job-description-analyzer, resume-tailor, resume-bullet-writer, resume-quantifier and linkedin-profile-optimizer methods.
---

# Naukri Profile Optimizer

Goal: profile ranks for the searches recruiters actually type in Resdex, and every claim is backed by the resume.

## Inputs
- Live profile: built-in browser at `https://www.naukri.com/mnjuser/profile` (user already signed in; never sign out or change credentials).
- Resume: newest resume PDF in your Downloads folder (or the one uploaded on Naukri).
- Target role: default Java / Spring Boot backend developer, 3 YOE, Pune + Bangalore.

## Step 1 — Mine current JDs (job-description-analyzer method)
Open in the browser (Naukri blocks plain fetchers):
`https://www.naukri.com/java-spring-boot-developer-jobs-in-pune?experience=3` (repeat for bangalore).
Run in page:
```js
const c=[...document.querySelectorAll('.srp-jobtuple-wrapper')];const f={};
c.forEach(x=>[...x.querySelectorAll('.tag-li')].forEach(t=>{const k=t.innerText.trim().toLowerCase();f[k]=(f[k]||0)+1}));
Object.entries(f).sort((a,b)=>b[1]-a[1]).slice(0,50)
```
Also collect titles. Open 3-5 full JDs for "must have" phrases. Keep a ranked keyword list.

Baseline (Nov 2026, Pune, 3 yrs, 21 JDs): Spring Boot, Microservices, Java, REST API, SQL, Hibernate, AWS, Maven, Web Services, Kafka, Docker, PostgreSQL, JPA, Spring MVC, Multithreading, Agile, Jenkins, DevOps, Oracle. Titles: "Java Spring Boot Developer", "Java Backend Developer", "Java Developer (Spring Boot, Microservices)".

## Step 2 — Gap analysis (resume-ats-optimizer method)
Keyword in JD list AND in resume but missing from profile = add. In JD but NOT in resume = do not add; tell user it is a gap to learn. Never invent skills or metrics.

## Step 3 — Section rules
| Section | Rule |
|---|---|
| Resume headline | ≤250 chars. `Role | YOE | top 6-8 JD keywords | cert | notice`. Use exact recruiter phrasing ("Spring Boot", "Microservices", "REST API"). No stale dates, no contradictions with notice field. |
| Key skills | Naukri allows many; keep 20-30 exact-phrase skills, highest JD frequency first, no duplicates/case variants, drop off-target ones (e.g. frontend for backend search). Use Naukri's suggested canonical spellings. |
| IT skills table | Each core skill with version (Java 17/21) and last used = current year. Matches key skills. |
| Employment | One entry per stint (remove duplicates). Description = 4-6 bullets from resume (resume-bullet-writer + resume-quantifier: action + tech + metric). Fill "key skills" per job. |
| Profile summary | ≤1000 chars. Line 1 = title + YOE + stack keywords. Then 3-4 sellable resume achievements with numbers, then domain (telecom/BSS), then target role + notice. |
| Projects | Add 2-3 resume projects with tech tags (extra keyword surface). |
| Career profile | Job role/role category matching target (Backend / Java Developer, not Full Stack if targeting backend). Expected salary filled (₹0 hides from salary-filtered searches). Preferred locations ≤10, target cities first. |
| Accomplishments | Certifications (Azure AZ-900, AZ-204) with IDs; GitHub / portfolio links. |
| Freshness | Re-save one section daily (recruiters sort by recently updated). |

## Step 4 — Apply
Draft all changes in chat first as a before/after table. Editing the profile changes the user's account: get explicit yes, then edit each section via the browser (click edit pencil, form_input, Save). Re-read page to verify. Report what changed.

## Step 5 — Track
Save the keyword list and changes to Obsidian (obsidian-vault skill) with date, so next run can compare recruiter views/search appearances.
