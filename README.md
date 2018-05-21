# Notification Inline Replies Explainer

An [addition to the Notifications API](https://github.com/whatwg/notifications/compare/master...anitawoodruff:master) to
allow users to type a text reply to a notification within the notification's own UI.

## The Problem

Notifications by their nature are interruptive. For notifications that require a direct response, e.g. an urgent chat message,
users currently have no option but to fully context-switch to the website that requires their attention in order to type a
reply. This can be disruptive and time-consuming.

Many native notification centres (including those on Android N+, Mac OS and Windows) have an API for apps to display
notifications which let users type a reply from the notification UI itself without opening the app. This feature is currently
inaccessible to websites.

## Use Cases

Primarily this feature is targeted at sites already using notifications to notify users of messages, that are currently
limited to opening a tab containing an input field when the notification is clicked on. This includes:

- Messaging apps
- Web forums
- Any site that has a commenting platform

In all these cases notification inline replies would allow users to quickly type a short reply to a message they receive
with minimal friction (no need to open a tab, find the right text box, and then close the tab). 

## Proposal

Allow developers to declare notification actions that enable inline text entry during activation.

This is achieved by adding two fields to the existing notification action object allowing developers to declare this, and one
to the notification event containing the user's reply.

The two additional fields within the notification action object are:

- **type**: indicates whether the action should be treated as a regular button (type = *button*), or as an action which allows
text input during activation (type = text), e.g. an inline text input field, or a button which turns into a focused text
input field when the button is clicked on.

- **placeholder** : an optional string that represents a short hint (a word or short phrase) intended to aid the user with
data entry when the control has no value.

The addition to the notification event is:

- **reply** : a DOMString containing the user's reply, or null if the user activated a non-text action button, or was not
able to input a reply due to platform limitations - in this case the site would be expected to handle the event as it would
have prior to the introduction of this feature (e.g. by opening a tab for the user to type a reply).

## Example Code

Showing a notification that enables an inline reply:

    navigator.serviceWorker.ready.then((serviceWorker) => {
        serviceWorker.showNotification("New Message", {
      body: "Marvin says: Hello",
      actions: {
        "action": "reply",
        "type": "text",
        "title": "Reply",
        "placeholder": "Respond to Marvin..."
      }
      });
    })

Retrieving an inline reply from a message (from within a Service Worker):

    self.onnotificationclick = (event) => {
      var notification = event.notification;
      var reply = event.reply;
    }

## Appendix: alternatives considered

We considered adding a new Service Worker event for handling action button activations carrying an inline response, but didn’t
pursue this because developers are already accustomed to handling all other activations, regardless of context, as part of
`notificationclick`.

## Appendix: native notification center APIs

Native notification centers expose different APIs for application developers to opt-in to inline replies. This proposal
intends to accommodate for all of them.

### Windows

Action Center notifications can be made to accept text input by adding an <input> element to the toast’s XML template, which
also supports placeholders. This is very flexible, and user agents can decide to display the actual action buttons where they
feel that’s most appropriate.

https://docs.microsoft.com/en-us/windows/uwp/design/shell/tiles-and-notifications/adaptive-interactive-toasts#inputs 

### Mac OS

The mac OS notification center supports notifications to enable support for inline replies through a boolean property
(`hasReplyButton`) paired with an optional placeholder value (`responsePlaceholder`). Accepting inline replies may result in
other action buttons to cease being displayed, which is a platform limitation.

https://developer.apple.com/documentation/foundation/nsusernotification

### Android

Android 7.0 introduced the '[direct reply action](https://developer.android.com/training/notify-user/build-notification#reply-action)'
, which can be attached to a notification using the
[RemoteInput.Builder API](https://developer.android.com/reference/android/support/v4/app/RemoteInput.Builder). The direct
reply action appears as an additional button in the notification that opens a text input. The title of this button and
placeholder for the text input can both be customized.
