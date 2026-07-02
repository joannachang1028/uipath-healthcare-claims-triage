# Healthcare Claims Exception Triage Automation

A UiPath Studio Web RPA workflow that validates synthetic healthcare claims and routes exceptions for human review.

## Project Overview

Healthcare claims may require manual review when required information is missing or when a claim exceeds a defined cost threshold. This workflow automates the initial validation process and assigns each claim one of two statuses:

- `Ready for Processing`
- `Needs Human Review`

The project demonstrates workflow design, conditional routing, debugging, unattended execution, and deployment through UiPath Orchestrator.

## Workflow

The automation:

1. Loads five synthetic healthcare claims.
2. Iterates through each claim.
3. Checks the claim against three validation rules.
4. Assigns a processing status and review reason.
5. Records the result in UiPath execution logs.

## Decision Rules

| Condition | Status | Reason |
|---|---|---|
| Member ID is missing | Needs Human Review | Missing Member ID |
| CPT Code is missing | Needs Human Review | Missing CPT Code |
| Claim amount exceeds $1,000 | Needs Human Review | High-cost claim |
| No exception detected | Ready for Processing | — |

## Workflow Overview

![Workflow overview](screenshots/workflow-overview.png)

## Decision Logic

![Validation logic part 1](screenshots/validation-logic-part-1.png)

![Validation logic part 2](screenshots/validation-logic-part-2.png)

## Debugging and Iteration

The first version used multiple sequential `If` blocks. A later `Else` branch could overwrite a status assigned by an earlier validation rule.

For example, a claim with a missing Member ID was initially marked `Needs Human Review`, but a later CPT Code check changed it back to `Ready for Processing`.

I added diagnostic logging to inspect the actual Member ID value and string length:

```vb
"Claim ID: " + CurrentRow("Claim ID").ToString() +
" | Member ID: [" + CurrentRow("Member ID").ToString() +
"] | Length: " + CurrentRow("Member ID").ToString().Length.ToString()
```

I then restructured the validation rules as nested conditions so that each claim receives one final status without being overwritten.

## Test Results

| Claim ID | Test Scenario | Final Status | Reason |
|---|---|---|---|
| C001 | Complete claim | Ready for Processing | — |
| C002 | Missing Member ID | Needs Human Review | Missing Member ID |
| C003 | Missing CPT Code | Needs Human Review | Missing CPT Code |
| C004 | High-cost claim | Needs Human Review | High-cost claim |
| C005 | Complete claim | Ready for Processing | — |

All five test cases produced the expected results.

## Deployment

The workflow was:

- Built in UiPath Studio Web
- Published as solution version `1.0.0`
- Deployed through UiPath Orchestrator
- Executed as an unattended RPA job
- Completed successfully in approximately 5.6 seconds

![Orchestrator deployment](screenshots/orchestrator-deployment.png)

![Unattended job logs](screenshots/unattended-job-logs.png)

## Repository Structure

```text
uipath-healthcare-claims-triage/
├── README.md
├── solution/
│   └── Healthcare-Claims-Triage.uis
├── data/
│   └── synthetic-claims.csv
└── screenshots/
    ├── workflow-overview.png
    ├── validation-logic-part-1.png
    ├── validation-logic-part-2.png
    ├── orchestrator-deployment.png
    └── unattended-job-logs.png
```

## Running the Project

1. Download the `.uis` solution package from the `solution` folder.
2. Sign in to UiPath Automation Cloud.
3. Open UiPath Studio Web.
4. Import the solution package.
5. Review the synthetic claim data and validation rules.
6. Run the workflow or publish it to UiPath Orchestrator.

UiPath product availability and import options may vary by account license and tenant configuration.

## Skills Demonstrated

- UiPath Studio Web
- UiPath Orchestrator
- RPA workflow development
- Conditional exception routing
- Healthcare claims workflow modeling
- Unattended automation
- Execution logging
- Workflow debugging
- Human-in-the-loop process design

## Future Improvements

- Load claims from Google Sheets, CSV, or a claims API
- Write results to a structured output file
- Use an Orchestrator Queue for claim processing
- Add retry and exception-handling logic
- Create a human-review workflow for flagged claims
- Add configurable validation thresholds
- Track processing volume, exception rate, and processing time

## Data and Privacy

This project uses synthetic data only. It contains no real patient information, protected health information, or production credentials.
