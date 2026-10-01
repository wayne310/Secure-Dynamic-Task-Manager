# Secure Dynamic Task Manager

ITP10 | Event-Driven Programming | Midterm Laboratory Activity 2

## Project Structure

```
secure-task-manager/
├── index.html
├── css/
│   └── style.css
└── js/
    └── app.js
```

## Overview

This project is a task manager built entirely with JavaScript DOM manipulation. `index.html` contains only the static controls and an empty `<ul id="taskList">`. No task items are hard-coded, so every task is created at runtime by `app.js`. Tasks can be added, completed, edited, and removed without reloading the page.

## How the Functions Work

- **`createTaskElement(taskText, taskId)`** builds one `<li class="task-item">` using `document.createElement()`. It sets `data-task-id` and `data-state="pending"` through `dataset`, and the text goes into a `<span class="task-text">` through `textContent`. It also creates the Complete, Edit, and Remove buttons. It returns the element without attaching it to the list.
- **`addTask(taskText)`** trims and validates the text. If it is blank, it shows "Task cannot be empty" in `#taskMessage`. Otherwise it generates a unique ID (`task-1`, `task-2`, ...), calls `createTaskElement()`, appends the result to `#taskList`, clears the input and message, and updates the counts.
- **`toggleTaskComplete(taskItem)`** uses `classList.toggle("completed")` and sets `dataset.state` to `"completed"` or `"pending"` to match.
- **`beginTaskEdit(taskItem)`** replaces the task text span with an `<input class="edit-input">` holding the current text, and changes the Edit button to Save.
- **`saveTaskEdit(taskItem)`** validates the input. If it is valid, it creates a new `.task-text` span with `textContent`, swaps it in for the input, and changes Save back to Edit.
- **`removeTask(taskItem)`** calls `taskItem.remove()` and updates the counts. It only removes that one task and does not rebuild the list.
- **`updateTaskCounts()`** reads the live DOM. It counts the `.task-item` elements and checks each one's `data-state`, so the numbers always match what is on screen and nothing is hard-coded.
- **`handleTaskListClick(event)`** is the single delegated click handler for Complete, Edit/Save, and Remove.
- **`loadSampleTasks()`** creates the three required sample tasks, puts them in a `DocumentFragment`, and appends the fragment to `#taskList` once.

## Event Delegation

Only one click listener is attached, on `#taskList`, and it runs `handleTaskListClick(event)`. Inside it, `event.target.closest(".task-item")` finds which task owns the clicked button, and `event.target.matches()` identifies which button was clicked (`.complete-btn`, `.edit-btn`, or `.remove-btn`). Click events bubble up from a button to the list, so one listener handles every button, including those on tasks created after the page loaded. That is why there are no per-button listeners.

## Security

User input is never turned into HTML. The code never uses `innerHTML`, `insertAdjacentHTML`, `document.write`, or inline `onclick`. Task and edited text is always set through `textContent`, which treats it as plain text. When `<img src=x onerror=alert(1)>` is added as a task, it is displayed as literal text. No `<img>` element is created and no script runs.

## Performance

Using a `DocumentFragment` for the sample tasks means the three items are assembled off-screen and inserted into the live DOM in one operation. This causes one reflow instead of three.

## Testing Results

| Test | Result |
|---|---|
| Page load | 0 task items, counts 0 / 0 / 0 |
| Add "Finish Module 4" | 1 pending task, Total 1, Pending 1 |
| Add a blank task | No task created, "Task cannot be empty" shown |
| Add `<img src=x onerror=alert(1)>` | Displayed as text, no `img` element created |
| Click Complete | `completed` class added, `data-state="completed"`, counts update |
| Click Complete again | Returns to pending, counts restored |
| Edit then Save | Edit input shows current text; Save updates only that task |
| Blank Save | Shows "Task cannot be empty", keeps editing |
| Remove | Deletes only that task, counts update |
| Load Sample Tasks | Adds exactly the 3 required tasks |
| Dynamic tasks | All buttons work on tasks created after load |
| Console | No JavaScript errors |

## How to Run

Open `index.html` in any modern browser. No build step or server is needed.
