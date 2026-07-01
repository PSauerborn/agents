# Quality Report: [Feature Name]

Work Plan ID: WP-[0-9]{3}
Created Date: YYYY-MM-DD

## Coding Standards Review

(List applicable standards files here used in review)
Applicable Standards:

- GENERAL.md
- [Standards file 1]
- [Standards file 2]
- [Standards file N]

### Coding Standards Violations

(Metrics on coding standards violations)
Total Violations: [Count of number of violations]

(List all standards/rule violations within applicable standards files and document adherence)

| Standards File | Rule ID    | File Path     | Description                                           |
|----------------|------------|---------------|-------------------------------------------------------|
| GENERAL.md     | GEN-001    | `cmd/main.go` | Log level not configured via environment var          |
| go/API.md      | GO-API-003 | `cmd/api.go`  | Invalid response structure on `GET /example` endpoint |
