[<< BACK](../../README.md)  

# Product Specification - Bug Report

**Bug:** Weekly Attendance Calendar Display  
**Product:** ACME Business Web App  

⚠️ Anonymised documentation  
Based on real business analysis work.  
Company names and systems were replaced with placeholders.  

## 1. Problem

The Weekly Attendance view presents inconsistencies in how the selected week is displayed and highlighted.

Currently:

- The system determines week boundaries based on existing shift data, not a full calendar week
- Some days within the selected week appear unmarked or missing
- The UI does not adapt to regional week definitions (locale)

This results in:

- Misleading visual representation of the selected week
- Confusion about which days are included in the report
- Risk of incorrect interpretation of attendance data

## 2. Objective

Ensure that the Weekly Attendance view:

- Always displays a complete calendar week
- Uses consistent week boundaries
- Adapts dynamically to the user’s locale settings
- Provides clear and intuitive visual feedback

## 3. Scope

**In Scope**

- Weekly calendar visualization
- Week selection behaviour
- Highlighting logic for selected week
- Locale-based week definition

**Out of Scope**

- Changes to attendance data calculation
- Backend shift assignment logic
- Reporting/export features

## 4. Current Behaviour

### 4.1 Week Selection and Display

```gherkin
Given the user selects “Week” view mode in the attendance section
When the user opens the week selection dropdown
Then the calendar shows:
  | Day           | Highlight   |
  | ------------- | ----------- |
  | Sunday        | Dark Green  |
  | Monday–Friday | Light Green |
  | Saturday      | Unmarked    |

When the user selects the current week (e.g., Week 40)
Then the calendar shows:
  | Day             | Highlight   |
  | --------------- | ----------- |
  | Sunday          | Dark Green  |
  | Monday–Thursday | Light Green |
  | Friday          | Dark Green  |
  ```

### 4.2 Observed Issues

- The week is displayed as:  
**W40 (Sun 5 Oct – Sat 11 Oct 2025)**

However:

- Saturday may appear unmarked, suggesting the week ends early
- The system uses last day with shifts instead of full week range
- The UI does not reflect user locale expectations

## 5. Expected Behaviour
   
### 5.1 Locale-Aware Week Definition

```gherkin
Scenario: User selects "Week" view and calendar adapts to locale

Given the user selects "Week" view mode in the dropdown
When the user opens the week dropdown
Then the system should determine the first and last day of the week dynamically, based on the user's browser locale
```

### 5.2 Calendar Highlighting

```gherkin
Then the calendar should highlight:
    | Day                      | Highlight   |
    | First day of the week    | Dark Green  |
    | Intermediate days        | Light Green |
    | Last day of the week     | Dark Green  |
```

### 5.3 Week Label

```gherkin
Then the calendar should highlight:
    | Day                      | Highlight   |
    | First day of the week    | Dark Green  |
    | Intermediate days        | Light Green |
    | Last day of the week     | Dark Green  |
```

### 5.4 Data Loading Behaviour

```gherkin
When the user clicks "Apply"
Then the system should load attendance data for the full week
And all days within the selected week must be displayed
And days without assigned shifts must still be included in the calendar
```
## 6. Functional Requirements

### 6.1 Week Calculation

- The system must determine:
    - First day of week
    - Last day of week

- Based on user locale

### 6.2 Calendar Rendering

- The system must:
    - Always display 7 consecutive days
    - Highlight:
        - Start of week → Dark Green
        - Middle days → Light Green
        - End of week → Dark Green

### 6.3 Data Consistency

- The displayed week must:
    - Match the date label
    - Match the data loaded
    - Not depend on shift availability

## 7. UX Requirements

- All 7 days must always be visible
- Days without shifts must:
    - Remain visible
    - Use a different font colour or style

This ensures:

- Clear distinction between:
    - No data
    - Missing/hidden days

## 8. Technical Considerations

The system should determine the week structure using browser locale:

```JavaScript
const locale = Intl.DateTimeFormat().resolvedOptions().locale;

const firstDayByLocale = {
  'en-GB': 1,
  'es-ES': 1,
  'pt-PT': 1,
  'es-CO': 0,
};

const firstDay = firstDayByLocale[locale] ?? 0;
const lastDay = firstDay === 0 ? 6 : 0;
```

## 9. Non-Functional Requirements

**Consistency**

- Calendar UI must always align with selected period

**Usability**

- Users must clearly understand which days are included

**Performance**

- Week rendering must remain responsive

## 10. Risks and Edge Cases

**Locale mismatch**

- Browser locale may not match user expectation

**Mitigation:**

- Allow future manual override

**Data gaps**

- Days without shifts may be misinterpreted as missing data

**Mitigation:**

- Visual differentiation (font/style)

## 11. Impact

If not addressed:

- Incorrect interpretation of weekly attendance
- Reduced trust in reporting
- Potential operational decision errors

## 12. Priority & Metadata
    
| Field       | Value                    |
| ----------- | ------------------------ |
| Priority    | Medium                   |
| Severity    | Low                      |
| Environment | Web App (Production)     |
| Module      | Attendance / Weekly View |
| Reported by | Victor Romero            |
| Detected in | Week 40 (2025)           |
