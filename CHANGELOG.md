## Change Log

### v1.0.6 (Jan 3, 2020)
* Added `setCustomFields(Map<String, String> customFields, SetCustomFieldHandler handler)` in Ticket.
* Added `setTicketPriority(Priority priority, final SetTicketPriorityHandler handler)` in Ticket.

### v1.0.5 (Aug 27, 2019)
* Added `setCustomerCustomFields(Map<String, String> customFields, SetCustomerCustomFieldsHandler handler)` in SendBirdDesk.
* Added `submitFeedback(UserMessage message, int score, String comment, SubmitFeedbackHandler handler)` in Ticket.

### v1.0.4 (Jul 10, 2019)
* Added `getCustomFields()` in Ticket.
* Added `enum ThreadOption { UI_THREAD, NEW_THREAD, HANDLER }` in SendBirdDesk.Options.
* Added `setThreadOption(ThreadOption threadOption, Handler handler)` in SendBirdDesk.Options.
* Deprecated `useUiThreadForCallbacks(boolean tf)` in SendBirdDesk.Options.
* Deprecated `setHandlerForCallbacks(Handler handler)` in SendBirdDesk.Options.

### v1.0.3 (Jun 21, 2019)
* Added `useUiThreadForCallbacks(boolean tf)` in SendBirdDesk.Options.
* Added `setHandlerForCallbacks(Handler handler)` in SendBirdDesk.Options.

### v1.0.2 (May 13, 2019)
* Added `reopen(ReopenHandler handler)` in Ticket.
* Added `create(String ticketTitle, String userName, String groupKey, Map<String, String> customField, CreateHandler handler)` in Ticket.
* Added `getOpenedList(int offset, Map<String, String> customFieldFilter, GetOpenedListHandler handler)` in Ticket.
* Added `getClosedList(int offset, Map<String, String> customFieldFilter, GetClosedListHandler handler)` in Ticket.

### v1.0.1 (Mar 29, 2018)
* Support SendBird Desk standalone version.

### v1.0.0-zendesk (Mar 16, 2018)
* Zendesk-integrated version release.
* UI components are all moved to sample project for full customization.

### v0.9.10 (Nov 7, 2017)
* Stabilized video player.

### v0.9.9 (Aug 1, 2017)
* First release.
