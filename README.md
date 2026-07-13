# availability_completiondate

Moodle availability (access restriction) plugin: **"Restriction by activity
completion date"**.

## What this plugin does

This is a deliberate combination of the options found in
`availability_completion` (activity completion) and `availability_date`
(date restriction), but with a rule that is different from both: the date
being compared is **not today's date** (as in `availability_date`), and the
check is **not just completed/not completed** (as in
`availability_completion`). Instead, the date used in the comparison is
**the date on which the student actually completed the chosen activity**.

Example: "Only unlock resource Y if the student completed activity X by
30/07/2026" — what matters here is not whether today is before or after
30/07, but whether the *completion of X* happened by 30/07.

Available options (all combinable):

- Required completion: must be complete / must not be complete / complete
  with a passing grade / complete with a failing grade.
- Date direction (only meaningful when the required completion is not "must
  not be complete"): completed **by** a given date, or completed **on or
  after** a given date.
- As with any Moodle restriction, the condition can be inverted with the
  "eye" (NOT) and combined in AND/OR groups with other restrictions.

## How it works under the hood

The plugin does not create any new database table. It uses Moodle's
standard activity completion API (`completion_info::get_data()`), which
returns, among other fields, `completionstate` (completion state) and
`timemodified` (when that completion record was last written/updated —
used here as the "completion date"). The restriction's own configuration
(which activity, which expected state, direction, and date) is stored, like
any other availability condition, inside the `course_modules.availability`
JSON field — no `db/install.xml` is required.

## File structure

```
availability_completiondate/
├── classes/
│   ├── condition.php     # Availability logic (back-end)
│   ├── frontend.php      # Controls the "Add restriction" form
│   └── privacy/provider.php
├── lang/en/availability_completiondate.php
├── yui/
│   ├── src/form/
│   │   ├── js/form.js    # Editing form UI (source)
│   │   ├── meta/form.json
│   │   └── build.json
│   └── build/moodle-availability_completiondate-form/
│       ├── moodle-availability_completiondate-form.js
│       ├── moodle-availability_completiondate-form-debug.js
│       └── moodle-availability_completiondate-form-min.js
├── version.php
└── README.md
```

## Compatibility

`version.php` declares `$plugin->requires = 2020061500;` (Moodle 3.9.0),
since the APIs used (`completion_info::get_data()`, the `COMPLETION_*`
constants, and the `core_availability` API) have been stable since then.
It has been tested and confirmed working on Moodle 4.5.

## English name

**"Completion Date Restriction"** (Frankenstyle/technical name:
`availability_completiondate`). Alternatives: "Completion by Date" or
"Activity Completion Date Availability".

## Author

Copyright 2026 Julio Prof — licensed under the GNU GPL v3 or later.
