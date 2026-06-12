# Negative Testing Report — What Breaks When You Try to Break It

**Tested by:** Soumya P  
**App:** Employee Management System (fenil29/employee-management-system)  
**Testing approach:** Exploratory — clicked through every form trying  
to break it the way a tired site manager or a curious attacker would.

---

## Test Area 1: Employee Creation Form

| Input Tried | Expected | Actual | Bug? |
|---|---|---|---|
| Blank name field → Submit | Validation error shown | TBD after running app | TBD |
| Name: `<script>alert('xss')</script>` | Input sanitised, no alert | TBD | TBD |
| Salary: -1 | Reject negative salary | TBD | TBD |
| Salary: 0 | Warning or rejection | TBD | TBD |
| Salary: 999999999 | Accept or cap | TBD | TBD |
| Email: "notanemail" | Format validation error | TBD | TBD |
| Duplicate employee ID | Reject with error | TBD | TBD |
| All fields blank → Submit | Multiple validation errors | TBD | TBD |

## Test Area 2: Attendance / Overtime

| Input Tried | Expected | Actual | Bug? |
|---|---|---|---|
| Overtime: -5 | Reject negative | TBD | TBD |
| Overtime: 999 | Reject, show max | TBD | TBD |
| Date: tomorrow's date | Reject future date | TBD | TBD |
| Date: blank | Validation error | TBD | TBD |
| Submit same attendance twice | Duplicate warning | TBD | TBD |

## Test Area 3: Payroll / Salary Generation

| Input Tried | Expected | Actual | Bug? |
|---|---|---|---|
| Generate payslip, no attendance data | Block with error | TBD | TBD |
| Generate payslip, salary = 0 | Warning shown | TBD | TBD |
| Change salary mid-month | Recalculates correctly | TBD | TBD |

---

## Note on TBD Entries
App was being set up locally during this assignment window.  
The test cases above are designed from code review and  
the two known production incidents. Results will be updated  
as soon as the app runs locally.

## What the Incidents Tell Us About Negative Test Priority

**Incident 1** tells us: test what happens when config is wrong  
before a single user action is taken.

**Incident 2** tells us: test that every API the frontend calls  
actually exists and returns the right status code — not just 200,  
but the right response body too.

These two test categories are higher priority than UI validation  
because they already caused real worker harm.
