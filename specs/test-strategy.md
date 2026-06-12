# Test Strategy — Construction HRMS Payroll

## This is NOT a generic test strategy

A generic strategy says: "test critical flows based on risk assessment."  
This strategy names the exact screens, exact calculations,  
and exact people who get hurt when something breaks.

---

## The One Output Everything Protects

**The payslip.** Every feature in this system — employee onboarding,  
attendance logging, overtime entry, salary calculation — exists  
for one reason: to generate a correct payslip for a construction worker.

If the payslip is wrong, the system has failed.  
Not technically. Humanly.

---

## Who Actually Uses This System

| User | Device | Context | What failure means for them |
|---|---|---|---|
| Site Manager | Mobile phone | Dusty construction site, end of 12hr shift | Overtime entry lost → worker underpaid |
| Payroll Operator | Desktop | Month-end, processing 200 payslips | One wrong number × 200 = mass underpayment |
| HR Admin | Desktop | Office | Wrong employee data → wrong salary base |
| Construction Worker | None — never logs in | Waiting for payslip | Late or wrong payslip = can't pay rent |

**The worker never touches the system. They just receive the output.  
They have no way to know if something went wrong until it's too late.**

---

## What I Test First — Prioritised by Real Incidents

### Priority 1 — The Two Things That Already Broke

**Incident 1: Env variable crash**
- Test: server fails loudly on startup if DB config is missing
- Why: 4 hours downtime, 200 payslips failed, 38 workers paid late
- How: remove DB_HOST from .env → verify server refuses to start with clear error

**Incident 2: API route contract**
- Test: /api/attendance returns 200, not 404
- Why: 47 workers' overtime never logged, 12 excluded from payslips
- How: hit every frontend-facing endpoint directly, verify none return 404

### Priority 2 — Salary Calculation (Zero Tolerance)

Every calculation that touches a worker's pay:
- Base salary × days worked = correct gross pay
- Overtime hours × overtime rate = correct overtime pay
- Gross pay + overtime - deductions = correct net pay
- Each formula tested independently with boundary values

Test cases:
- 0 overtime hours → no overtime added
- 1 overtime hour → minimum overtime calculated
- Maximum overtime hours → cap enforced, warning shown
- Negative overtime entered → rejected, not saved
- Zero base salary → blocked, error shown

### Priority 3 — Data Entry Validation (Site Manager on Mobile)

Site managers log data on phones at construction sites:
- Fat finger errors: 999 overtime hours instead of 9
- Wrong date: future dates, past month dates
- Duplicate entries: same worker logged twice same day
- Network drop mid-submission: data not silently lost

### Priority 4 — New Developer Safety Net

The new dev broke overtime calculation touching the salary endpoint.  
The senior dev is the only one who understands it.  
Tests here are documentation — they show what the system must do:

- Changing salary calculation formula → existing tests fail immediately
- Renaming an API route → contract test fails, PR blocked
- Missing a required field → validation test fails

---

## What I Test Manually vs Automatically

| Automated | Manual / Exploratory |
|---|---|
| Salary calculations | UI on mobile screen sizes |
| API route existence | Form usability at end of shift |
| Env variable validation | Error messages — are they human-readable? |
| Duplicate entry rejection | Edge cases I discover by clicking |
| Negative/zero value rejection | How the app behaves on slow network |

---

## CI Pipeline — Every PR Must Pass

No code merges to main without:
1. All API route tests passing
2. All salary calculation tests passing  
3. Env variable validation check passing

This directly addresses the Dev Lead's concern:  
*"I've seen teams where the test suite takes 20 minutes and  
nobody wants to push code."*

These tests run in under 2 minutes. Fast enough that  
nobody resents them. Strict enough to catch both incidents.

---

## Addressing the Team

**To the PM:** These tests make you faster — bugs caught in CI  
take 5 minutes to fix. Bugs caught in production took 4 hours  
of downtime and a midnight reconciliation session.

**To the Dev Lead:** The test suite runs in 2 minutes.  
It catches the exact two failure modes that already hit production.  
Code review catches logic errors. CI catches integration failures.  
Both are needed.

**To the Senior Dev:** Your knowledge of the payroll module  
is the most valuable thing on the team. These tests preserve it.  
When you're in meetings, the new dev can make changes safely  
because the tests describe exactly how salary calculation works.

**To the New Dev:** The tests are your safety net.  
If your change breaks overtime calculation, the test fails  
before your code reaches production. You'll know immediately,  
not when the payroll operator calls at midnight.
