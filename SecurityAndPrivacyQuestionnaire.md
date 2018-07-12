From https://www.w3.org/TR/security-privacy-questionnaire/#questions (questions deemed irrelevant omitted)

### 3.1. Does this specification deal with personally-identifiable information?

Yes, potentially - since inline replies allows a free-form text box to be added to notifications,
this feature could be used to collect PII, like any form input element on a webpage. 

E.g. A site could could ask users to sign up to a mailing list by entering an email address in
the notification. 

However, we note that on platforms which support inline notification replies natively, e.g. Android,
we have not seen them being used for this purpose.


### 3.2. Does this specification deal with high-value data?

Yes, potentially - this feature could be used to solicit high-value data such as passwords or credit card numbers.

However, since masking is not enabled in the reply field, this feature is not expected to be used for
soliciting passwords. 

Again, this is not a use-case we have observed on platforms which already support
notification inline replies.


### 3.9 Does this specification allow an origin access to aspects of a user’s local computing environment?

Sites will be able to detect whether users are on a system that supports inline replies, by checking whether
the `reply` property on the notificationclick event is non-null. Generally this corresponds to OS and OS
version (e.g. on Android they are supported from Nougat), but on Linux there are multiple notification managers
available which may differ in their support.


### 3.11. Does this specification allow an origin some measure of control over a user agent’s native UI?

Yes, since it allows input fields to appear in web notifications, which are part of native UI.
