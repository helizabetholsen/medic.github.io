# Tasks: Care Follow-Up Workflows

## Overview

Tasks help CHWs plan their day by prompting them to complete follow-up visits and other activities. The list might include upcoming scheduled ANC or Immunization visits, treatment or referral follow-ups, or other required activities such as a household survey. 

Tasks ensure that the right actions are taken for the right people at the right time.

## Accessing

Tasks are accessed from a few different locations in the app.

On the People tab (Image 1), tasks for a particular person or place can be viewed on the contact’s profile in the “Tasks” section. Filters allow you to choose how many tasks you’d like to view for each due date.

On the Tasks tab (Image 2) is a consolidated list of tasks for all people and families that the user looks after. The task definition determines how long the task will show on this list before and after it is due.

## Main List

Each task has an icon on the left side which indicates which type of task it is. The first bold line of text is the person or family that the task is about. The second line of text is the name of the task. The due date for the task is located in the upper right hand corner. If a task is overdue, the due date will be red.

Tasks are listed in order of due date. Tasks that are past due will appear at the top of the list. CHWs should strive to complete tasks before they are overdue. Many programs add targets to track task completion and timeliness.

## Care Guides

When a CHW clicks on a task, the care guide configured for that task displays. CHWs are then guided through questions for that specific workflow.

For more information on Care Guides, see the “[Decision Support for Care Guides]()” section of this overview. 

When the user completes the care guide, the task will be cleared from the Tasks tab, and the report will be accessible from the Reports page or on the profile of the person or place whom the report is about.

<details>
  <summary><em>Details for app developers</em></summary>

> **Defining Tasks**
> 
> Tasks are configured in the `tasks.js` file. This file is a JavaScript module which defines an array of objects conforming to the Task schema detailed below. When defining tasks, all the data about contacts on the device (both people and places) along with all their reports are available. Tasks are available only for users of type "restricted to their place". Tasks can pull in fields from reports and pass data as inputs to the form that opens when the task is selected, enabling richer user experiences.
> 
> | property | type | description | required |
> |---|---|---|---|
> | `name`| `string` | A unique identifier for the task. Used for querying task completeness. | yes, unique |
> | `icon` | `string` | The icon to show alongside the task. Should correspond with a value defined in `resources.json`. | no |
> | `title` | `translation key` | The title of the task (labeled above). | yes |
> | `appliesTo` | `'contacts'` or `'reports'` | Do you want to emit one task per report, or one task per contact? This attribute controls the behavior of other properties herein. | yes |
> | `appliesIf` | `function(contact, report)` | If `appliesTo: 'contacts'`, this function is invoked once per contact and `report` is undefined. If `appliesTo: 'reports'`, this function is invoked once per report. Return true if the task should appear for the given documents. | no |
> | `appliesToType` | `string[]` | Filters the contacts or reports for which `appliesIf` will be evaluated. If `appliesTo: 'reports'`, this is an array of form codes. If `appliesTo: 'contacts'`, this is an array of contact types. For example, `['person']` or `['clinic', 'health_center']`. For example, `['pregnancy']` or `['P', 'pregnancy']`. | no |
> | `contactLabel` | `string` or `function(contact, report)` | Controls the label describing the subject of the task. Defaults to the name of the contact (`contact.contact.name`). | no |
> | `resolvedIf` | `function(contact, report, event, dueDate)` | Return true to mark the task as "resolved". A resolved task uses memory on the phone, but is not displayed. | yes |
> | `events` | `object[]` | An event is used to specify the timing of the task. | yes |
> | `events[n].id` | `string` | A descriptive identifier. Used for querying task completeness. | yes, unique |
> | `events[n].days` | `integer` | Number of days after the doc's `reported_date` that the event is due | yes, if `dueDate` is not set |
> | `events[n].dueDate` | `function(event, contact, report)` | Returns a `Date` object for the day when this event is due. | yes, if `days` is not set |
> | `events[n].start` | `integer` | Number of days to show the task before it is due. | yes |
> | `events[n].end` | `integer` | Number of days to show the task after it is due. | yes |
> | `actions` | `object[]` | The actions (forms) that a user can access after clicking on a task. If you put multiple forms here, the user will see a task summary screen where they can select which action they would like to complete. | yes |
> | `actions[n].type` | `'report'` or `'contact'` | When `'report'`, the action opens the given form. When `'contact'`, the action redirects to a contact's profile page. Defaults to 'report'. | no |
> | `actions[n].form` | `string` | The code of the form that should open when you select the action. | yes |
> | `actions[n].label`| `translation key` | The label that should appear on the task summary screen if multiple actions are present. | no |
> | `actions[n].modifyContent`| `function (content, contact, report)` | Set the values on the content object to control the data which will be passed as `inputs` to the form which opens when the action is selected. | no |
> | `priority` | `object` or `function(contact, report)` returning object of same schema | Controls the "high risk" line seen above. | no |
> | `priority.level` | `high` or `medium` | Tasks that are `high` will display a high risk icon with the task. Default: `medium`. | no |
> | `priority.label` | `translation key` | Text shown with the task associated to the risk level. | no |
> 
> To build your tasks into your app, you must compile them into app-settings, then upload them to your instance.
> `medic-conf --local compile-app-settings backup-app-settings upload-app-settings`
</details>