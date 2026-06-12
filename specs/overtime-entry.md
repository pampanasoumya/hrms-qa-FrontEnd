# QA-301: Overtime Entry Screen — Acceptance Criteria

## Original Requirement (PM's Slack message)
"We need an overtime entry screen. Site managers should be able to log  
overtime for their workers at the end of each day — which worker,  
how many hours, what date, and a reason. Needs to work on mobile."

That's it. No wireframe. No edge cases. No error states.

---

## Questions I'd Ask Before Dev Starts

1. What is the maximum overtime hours allowed per day? Per month?
2. What happens if two site managers log overtime for the same worker same day?
3. If the phone loses connectivity mid-submission — does it save a draft or lose data?
4. Can overtime be edited after submission? Who can edit it?
5. Does HR need to approve overtime before it hits payroll?
6. What timezone does the date field use — device local or server?
7. What counts as "a reason" — free text, dropdown, or both?

**These questions are not optional.** The last two production incidents  
happened because nobody asked questions like these before building.

---

## Acceptance Criteria

### Core Flow (Launch Blocker)

GIVEN a site manager is logged in on mobile  
WHEN they select a worker, enter 4 overtime hours, today's date, and a reason  
THEN the entry saves successfully  
AND appears immediately in the worker's attendance record  
AND is included in that month's payroll calculation  

### Validation — Overtime Hours (Launch Blocker)

GIVEN a site manager enters overtime hours  
WHEN the value is negative (e.g. -2)  
THEN the system shows: "Overtime hours must be between 0 and 12"  
AND does not save the entry  

WHEN the value exceeds 12  
THEN the system shows: "Maximum 12 overtime hours per day allowed"  
AND does not save  

WHEN the value is blank  
THEN the system shows: "Please enter overtime hours"  
AND does not save  

WHEN the value contains letters (e.g. "abc")  
THEN the field rejects non-numeric input entirely  

### Monthly Cap Warning (Launch Blocker)

GIVEN a worker has already logged 55 overtime hours this month (cap: 60)  
WHEN a site manager tries to log 7 more hours  
THEN the system warns: "This worker has 5 overtime hours remaining this month"  
AND requires confirmation before saving  
AND saves maximum 5 hours, not 7  

### Duplicate Entry (Launch Blocker)

GIVEN a site manager already logged overtime for Worker A today  
WHEN another site manager tries to log overtime for Worker A today  
THEN the system shows: "Overtime already logged for this worker today by [Name]"  
AND shows the existing entry  
AND requires deliberate override — not silent overwrite  

### Network Failure (Launch Blocker)

GIVEN a site manager fills the overtime form completely  
WHEN the internet disconnects before they tap Submit  
THEN the form data is preserved when connectivity returns  
AND shows "Connection lost — your entry has been saved as draft"  
AND does NOT show a blank screen or silent failure  

GIVEN a site manager taps Submit  
WHEN the server takes more than 5 seconds to respond  
THEN a loading indicator is shown  
AND the Submit button is disabled (prevents double submission)  

### Mobile Specific (Launch Blocker)

- All form fields usable on a 5-inch Android screen, one hand
- Submit button visible without scrolling on standard mobile viewport
- All touch targets minimum 44px height
- Form does not zoom in unexpectedly on iOS when tapping fields

### Future Date (Launch Blocker)

GIVEN a site manager selects a date  
WHEN the date is in the future  
THEN the system shows: "Overtime cannot be logged for future dates"  
AND does not save  

---

## What's v2 (Not Launch Blockers)

- Bulk overtime entry for entire crew at once
- Offline mode with automatic sync when connectivity returns
- Overtime approval workflow requiring HR sign-off
- Export overtime report by site, by month

---

## Why This Matters Beyond the Spec

A site manager at the end of a 12-hour shift, on a phone,  
in fading light, entering overtime for 15 workers.  
Every validation gap is a worker whose overtime disappears silently.  
Incident 2 proved this: 47 workers' overtime was never logged  
because the endpoint returned 404 with no visible error to the user.  
The form appeared to submit. The data was gone.
