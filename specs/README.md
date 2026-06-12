# HRMS Payroll — QA Assignment
**Candidate:** Soumya P  
**Role:** QA Engineer — DeepThought Fellowship  
**Codebase:** Employee Management System (React + Node.js)

## Why This QA Suite Exists

Two production incidents in 30 days. Both reached workers.

- **Incident 1:** Missing env variable → server crashed on deployment  
  → 200 payslips failed → 38 construction workers paid 3 days late
- **Incident 2:** API route renamed → frontend got 404 silently  
  → 47 workers' overtime never logged → 12 entries excluded from payslips

Every test, ticket, and pipeline in this repo exists to prevent  
a specific worker from losing wages — not to hit coverage numbers.

## What's Inside

| Folder | What it contains |
|---|---|
| `specs/` | Acceptance criteria written before dev starts |
| `bug-reports/` | 5 real bugs found by testing the app |
| `test-strategy.md` | How I prioritised what to test and why |
| `negative-testing-report.md` | What breaks when you try to break it |
| `.github/workflows/` | CI pipeline — blocks PRs if tests fail |

## How to Run

### Backend
cd hrms-qa-backend
npm install
npm start

### Frontend
cd hrms-qa-frontend
npm install
npm start

Open http://localhost:3000

## QA Philosophy for This Codebase
A bug in a food delivery app means a bad experience.  
A bug in this payroll app means a construction worker's  
family doesn't have rent money this month.  
That's the bar every test is written against.
