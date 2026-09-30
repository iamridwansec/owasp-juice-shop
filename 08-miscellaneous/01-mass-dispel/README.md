# Mass Dispel

## Challenge Information

| Field          | Details                                                    |
| -------------- | ---------------------------------------------------------- |
| **Category**   | Miscellaneous                                              |
| **Difficulty** | 1                                                          |
| **Challenge**  | Mass Dispel                                                |
| **Objective**  | Close multiple "Challenge solved" notifications in one go. |

![Mass Dispel Challenge Metadata](../../../images/01-mass-dispel-challenge-metadata.png)

---

## Objective

The objective of this challenge is to discover and use the convenience feature that allows multiple **"Challenge solved"** notifications to be dismissed simultaneously.

The challenge hints indicate that the easiest way to investigate this behavior is immediately after restarting the Juice Shop, when previously solved challenge notifications are restored.

---

## Reconnaissance

After restarting the Juice Shop server, multiple **"You successfully solved a challenge"** notifications appeared in the browser.

These notifications contained individual **X** buttons for closing them.

![Mass Dispel Attack Surface](../../../images/02-mass-dispel-attack-surface.png)

---

## Attack Surface Discovery

Inspecting the notification elements in the browser showed that the notifications are rendered inside the:

```text
app-challenge-solved-notification
```

component.

Each notification contains a close button:

```html
<button id="closeButton">X</button>
```

Clicking the **X** normally removes only the selected notification.

This indicated that the individual close button was not the intended solution.

---

## Backend Validation

Inspection of the Juice Shop backend revealed a WebSocket event associated with the challenge:

```text
verifyCloseNotificationsChallenge
```

The challenge is solved when the application receives more than one notification in the event data:

```javascript
Array.isArray(data) && data.length > 1
```

This confirmed that the intended behavior involves closing multiple notifications simultaneously.

---

## Exploitation

The challenge hint refers to the notification's **convenience feature**.

After restarting the server and displaying multiple challenge notifications, the bulk-close behavior was triggered by:

1. Holding the **Shift** key.
2. Clicking the **X** button on one of the challenge notifications.
3. The active challenge notifications were dismissed together.

This caused the application to send the appropriate notification data to the backend and satisfy the `verifyCloseNotificationsChallenge` condition.

---

## Exploitation Evidence

After using the bulk-close feature, the **Mass Dispel** challenge appeared as solved on the Score Board.

![Mass Dispel Exploitation Evidence](../../../images/03-mass-dispel-exploitation-evidence.png)

---

## Impact

The challenge demonstrates how a user-interface convenience feature can trigger behavior that is different from the normal action of an individual control.

In this case:

* Clicking **X** normally closes one notification.
* **Shift + X** closes multiple notifications.
* The bulk operation triggers the challenge verification mechanism.

---

## Lessons Learned

* UI elements can contain hidden convenience features that are not immediately visible.
* Browser DevTools can reveal the structure and behavior of frontend components.
* Inspecting backend event handlers can help confirm how a frontend action is validated.
* Challenge hints can point toward intended UI behavior rather than requiring a conventional HTTP exploit.
* Keyboard modifiers such as **Shift** can change the behavior of existing UI controls.

---

## Conclusion

The **Mass Dispel** challenge was solved by identifying the bulk-dismiss convenience feature for challenge notifications.

The key discovery was that **holding Shift while clicking a notification's X button closes multiple challenge notifications at once**, satisfying the backend verification for the challenge.

