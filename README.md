# Automated Task & Deadline Reminder (n8n)

An automated task and deadline reminder system built with **n8n**, **Google Sheets**, and **Gmail**. It checks tasks daily, identifies reminder-eligible records, sends email notifications, and updates reminder tracking.

## Features

- Daily scheduled execution at 8:00 AM
- Retrieves task records from Google Sheets
- Evaluates task deadlines and reminder eligibility
- Sends reminder emails through Gmail
- Updates `last_reminded` and `reminder_status` after successful sends
- Tracks email failures with a `Failed` status
- Prevents duplicate reminders on the same day
- Skips completed tasks and tasks that are not eligible for reminders

## Workflow

1. **Schedule Trigger** — runs daily at 8:00 AM.
2. **Get All Tasks** — retrieves task records from Google Sheets.
3. **Check Task Deadlines** — calculates deadline status and eligibility.
4. **Filter Reminder Tasks** — separates eligible and ineligible tasks.
5. **Send Task Reminder** — sends the reminder email through Gmail.
6. **Update Reminder Tracking** — records the successful reminder date and `Sent` status.
7. **Update Failed Reminder** — records `Failed` when Gmail returns an error, without marking the task as sent.

## Google Sheets columns

The workflow expects task records with fields similar to:

| Column | Purpose |
|---|---|
| `task_id` | Unique task identifier |
| `task_name` | Task title |
| `description` | Task details |
| `due_date` | Task deadline |
| `priority` | Task priority |
| `email` | Recipient email address |
| `last_reminded` | Date the last reminder was successfully sent |
| `reminder_status` | Reminder result, such as `Sent` or `Failed` |
| `status` | Task completion status |

Adjust the column names in the workflow if your spreadsheet uses different headers.

## Setup

1. Import the exported workflow JSON from the `workflow/` folder into n8n.
2. Connect your Google Sheets account and select the task spreadsheet.
3. Connect your Gmail account and configure the recipient field.
4. Confirm that the spreadsheet headers match the fields used in the workflow.
5. Set the workflow timezone to `Asia/Manila`.
6. Confirm the Schedule Trigger runs daily at **8:00 AM**.
7. Test successful sends, Gmail failures, completed tasks, overdue tasks, and duplicate-reminder prevention.
8. Activate the workflow after all tests pass.

## Error handling

The Gmail node should use **Continue (using error output)** where supported. Route its error output to **Update Failed Reminder**.

- Success branch: set `last_reminded` to the current Manila date and `reminder_status` to `Sent`.
- Error branch: set `reminder_status` to `Failed` and leave `last_reminded` unchanged.

This prevents a failed email attempt from being recorded as a successful reminder.

## Testing checklist

- [ ] Upcoming deadline — email sent
- [ ] Due today — email sent
- [ ] Overdue task — email sent (if eligible under your rules)
- [ ] Completed task — skipped
- [ ] Already reminded today — skipped
- [ ] Invalid email — validation error or failed send
- [ ] Gmail failure — status becomes `Failed`, not `Sent`

## Repository structure

```text
Automated-Task-Deadline-Reminder-n8n/
├── README.md
├── workflow/
│   └── task-deadline-reminder.json
├── screenshots/
│   ├── workflow-overview.png
│   ├── google-sheets-tracking.png
│   ├── email-notification.png
│   └── execution-success.png
└── .gitignore
```

## Important

The n8n workflow export and execution screenshots were not included in this starter archive because they were not provided in this chat. Export your completed workflow from n8n and place the JSON file in `workflow/`. Add your own screenshots to `screenshots/` before publishing the repository.

## Technologies

- n8n
- Google Sheets
- Gmail
- Workflow automation
