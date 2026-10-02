---
name: "workday-job-application-filler"
description: "Fill out an online job application (Workday, Avature, Greenhouse, iCIMS, or any other ATS) for Chris Wood using Claude in Chrome, rewriting weak auto-filled role descriptions into strong ones, drafting the 'why this role' answer for approval at Review, and stopping before final Submit."
---

# Job Application Filler

Use when Chris gives any job application or employer-registration URL — Workday (often ending in `/apply/autofillWithResume?source=LinkedIn`), Avature, Greenhouse, iCIMS, Lever, a company's own careers site, or anything else — and wants it filled out. Not limited to Workday: apply the same standard info, answers, and care on any platform.

## Sources of truth
- **Standard info and standard answers:** the sections below in this skill.
- **Master resume:** Google Drive, **Downloads** folder (use the Drive connector: search that folder by name and read it). Used to ground role descriptions, check autofilled values, and draft the "why this role" answer.
- **Tailored resume:** the `.docx` for THIS posting (built by the `tailored-resume-builder` skill).
- If a fact is in none of these, it is unknown. Never infer or invent.

## Inputs needed
- The application (or registration) URL.
- The tailored resume `.docx`, already delivered to the outputs folder or Chris's Drive resume folder, IF the platform has a resume-upload/autofill step. If it isn't accessible to this session (browser file upload only accepts files this session can read), ask Chris to attach it to the chat — don't skip straight to manual fill without asking, since on platforms like Workday the resume upload drives autofill. On platforms with no resume-upload step, fill manually from the standard info below.
- Posting-specific details (title, salary range, location) if the form asks and Chris hasn't given them. Ask rather than guess.
- Cover letters are out of scope: skip optional cover-letter uploads. If one is required to proceed, stop and tell Chris.

## Chris's standard application info
- Name: Christopher Wood
- Email for applications: Chriswood.healthcare360@gmail.com (never baskhooper@gmail.com — that's his Claude account email only)
- Phone: 636-395-1246
- Address: 1 Coventry Ct, St. Charles, MO 63304
- LinkedIn: linkedin.com/in/chriswood10
- Education: Master of Healthcare Administration, University of Missouri-Columbia (08/2016-05/2018, GPA 3.8, Honors); Bachelor of Science in Healthcare Administration, Brigham Young University-Idaho (08/2010-07/2016, GPA 3.9)
- Work history: NAVVIS & Company (Practice Optimization Manager / Healthcare Performance Improvement Consultant, July 2024 - Sept 2026, role ended when Navvis dissolved the POM program — do NOT leave "I currently work here" checked); Washington University in St. Louis (Clinic Administrator, Director-level, July 2022 - July 2024); San Luis Valley Health, entered as TWO roles: Primary Care & Specialty Care Rural Health Clinic Manager (09/2020 - 05/2022) and Primary Care Clinic Manager (08/2019 - 09/2020); University of Missouri Hospital (Project Management Intern, May 2017 - Aug 2017) if it appears from an existing saved profile.

## Standard answers to common screening questions
1. Legal right to work in the U.S. (verification): Yes.
2. Requires visa sponsorship now or in the future: No.
3. Worked for/against a client of the company, or relatives/friends employed there: No (unless Chris says otherwise).
4. Interested in relocating: No.
5. Desired salary: enter a RANGE, never a single number, when the field is free text (e.g., posting range $120,000-$165,000 -> "$135,000 - $150,000"). If the field is a fixed dropdown of salary bands, pick the band that contains that midpoint.
6. Available start date: 7 days from today's date.
7. Location preference (checkbox lists or dropdowns): if the job is remote or hybrid, check/select any Missouri options, or "Any Location" if no Missouri option exists. For a specific in-office role, use the job's actual location.
8. Non-compete/non-solicit/NDA from a previous employer: No (unless Chris says otherwise).
9. Prior employment with this same company, or current employer being a client of it: No (unless Chris says otherwise).

Any other factual or legal screener (background check, age, referral source, etc.) not covered above: leave blank and flag. Never guess an attestation.

## Process
1. **Setup.** Load Claude in Chrome tools (ToolSearch `select:mcp__claude-in-chrome__tabs_context_mcp,mcp__claude-in-chrome__navigate,mcp__claude-in-chrome__computer,mcp__claude-in-chrome__read_page,mcp__claude-in-chrome__tabs_create_mcp,mcp__claude-in-chrome__get_page_text,mcp__claude-in-chrome__find,mcp__claude-in-chrome__file_upload,mcp__claude-in-chrome__form_input`). If they fail to load, stop and report. Call `tabs_context_mcp` first, open a NEW tab for each application (never reuse a tab another in-flight application is using), navigate to the URL, and record the tab ID + application URL.
2. **Pre-flight (ONE batched message to Chris, before entering any personal data).** Confirm: role/company/location, tailored resume path, posting-specific details (salary range, etc.), that he wants the form filled now, and whether an existing account/login should be used (never create credentials or invent passwords). Also remind: machine awake, Chrome window open and not minimized, Memory Saver off (or the ATS domain set to always-active), and only one Claude session driving the browser. Check for a prior application or saved draft for the same req on this platform; if one exists, report it instead of duplicating.
3. **Resume upload/autofill.** If the platform has an autofill step (e.g. Workday's "Autofill with Resume"), upload the tailored resume docx via `file_upload` on the file-input element (found via `find`, not by clicking — clicking opens a native picker the tool can't see). Verify the filename displays after upload. If the file path isn't uploadable (permission error), stop and ask Chris to attach the file rather than silently falling back to manual entry. If there is no such step, fill all fields manually from the standard info.
4. **Walk every section** the platform presents (typically: general/contact info, work experience, education, application/screening questions, voluntary disclosures/EEO, review), adapting to that ATS. Treat all autofilled values as untrusted: check each against the standard info and master resume (titles, employers, dates, degrees) and correct mismatches. Resolve validation errors before advancing. "Next / Save and Continue" is fine to click (and preferred, since it saves progress server-side); only the final Submit/Apply is off-limits.
5. **Role description format** (any free-text job/role-description field). Autofill parsers often do a poor job here. For EVERY job entry, replace it with an actual job description, not an accomplishment summary: a 1-2 sentence role overview (scope, team/provider/clinic scale, what the role owned), a blank line, then "Responsibilities:" followed by about 6 hyphen bullets, each starting with a strong verb. Make it sound strong, but strengthen wording and structure only: every fact, number, and scope claim must trace to the master resume. Never invent numbers; if a number isn't there, describe scope qualitatively. Respect character limits and check that bullets/line breaks survive; if the field strips them, use semicolon-separated lines.
6. **Widget handling.**
   - Dropdowns/selects: clicking an option often does not register, especially on Workday-style custom widgets. Click the dropdown, use Up/Down keys then Return, and verify the displayed value.
   - Search-type multiselects (Field of Study, Certification): type, then Return, sometimes twice.
   - Masked date fields: click, ctrl+a, BackSpace, type digits.
   - Plain HTML `<select>`: `form_input` usually works directly.
   - Scrolling: prefer scrolling to an element ref from `find`/`read_page`. Fallback: mouse wheel over the right margin (~x 1210, adjust to viewport).
   - Prefer `find`/`read_page` for checking state; screenshot to verify widgets and the Review screen.
7. **EEO / voluntary self-identification** (race, gender, veteran, disability): select "I do not wish to answer" / "Decline" where offered; if a field (e.g. gender, Hispanic/Latino) has no decline option and is optional, leave it blank — never guess.
8. **Subjective questions** ("Why this role?", motivation): leave blank while working through the form and do NOT draft yet; drafting happens at the Review stage (step 10). If a blank one blocks "Next", ask Chris whether to enter a clearly marked placeholder ("TBD") to proceed, and replace it after approval. Any other subjective question not covered above (strengths, etc.) stays blank and is flagged in the report.
9. **Consents and acknowledgments** (terms, privacy, e-signature): leave unchecked unless required to reach Review. If required, check only with Chris's okay and note it in the report.
10. **Review stage, in this order:**
    a. Reach the final Review screen (or as close to final submission as the platform allows) and run a verification pass: compare name, contact info, employers, titles, dates, and education to the standard info and master resume; list any discrepancies.
    b. **Draft the "Why this role" answer(s)** now, grounded in the job posting plus the master resume only (no invented facts or numbers), matching the field's character limit. Post the draft in chat and wait for Chris's approval or edits. Do not enter it before approval.
    c. After approval, enter the final text, replace any placeholder, and confirm it saved.
    d. Re-screenshot the Review screen. **Never click the final Submit/Apply button.** Submitting requires Chris's explicit go-ahead in chat.
11. **Connection resilience.** The Chrome extension can lose the browser/tab connection (idle tabs get discarded, the extension worker sleeps).
    - Re-verify the tab with `tabs_context_mcp` before each new section. Work in short bursts; avoid long idle waits.
    - Post a one-line progress note in chat after each section, so a reset costs one section, not the whole run.
    - On "tab not found / not connected" / tool errors: (1) call `tabs_context_mcp` and look for the existing tab by URL; (2) if found, re-attach and `read_page` to confirm what is actually filled before typing; (3) if gone, open ONE new tab to the saved-draft/application URL (not a fresh "apply" link, which can create a duplicate application) and verify what the ATS saved; (4) max 2 reconnect attempts, then stop and report the last saved section.
    - After any reconnect, compare the page to the progress notes and fill only what's missing (no duplicate job or education entries), and confirm you are still logged in.
12. **Stop-and-report conditions** (no repeated retries, apart from the 2 reconnect attempts above): CAPTCHA, account-creation/password step, email/MFA verification, session timeout/reset or login page after reconnect, upload failure, a required field with no known answer, a required cover letter, or a duplicate application.
13. **Parallel runs (e.g. subagents).** Give each one this full skill content in its prompt, its own new tab AND its own browser window (background tabs are throttled and discarded), and a unique application. Cap parallelism at 2 unless Chris says otherwise. No two runs share a login session on the same ATS tenant without Chris's okay. Each reports back in the format below.

## Report back
- **Status:** Reached Review / stopped at <step> / blocked (reason) | Submitted: NO
- **Filled:** brief list by section
- **Role descriptions used:** verbatim, per job
- **"Why this role" answer:** approved text as entered (or "pending approval")
- **Needs Chris:** blank or flagged fields (screeners, subjective questions, consents)
- **Discrepancies:** autofill versus standard info/master resume, and what was corrected
- **Issues:** disconnects, timeouts, resets, widget quirks, and how each was recovered
