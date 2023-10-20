# Changelog

### v1.1.4 (Oct 20, 2023) with Chat SDK v4.9.1
* Added  `getFirstResponseTime()` , `getIssuedAt()` in `Ticket` class.

### v1.1.3 (Mar 3, 2023) with Chat SDK v4.2.1
* Connection error fixed.
* Improved stability.

~~### v1.1.2 (Jan 19, 2023) with Chat SDK v4.2.1~~
* **Deprecated as this version would cause connection failure to the desk server**
* Added  `getStatus2()` , `getAllTicketList(int, Map, GetTicketListHandler)`, `getTicketList(int, Map, List, GetTicketListHandler)` in `Ticket` class.
* Deprecated `getStatus()` in `Ticket` class.

### v1.1.1 (Oct 11, 2022) with Chat SDK v4.0.9
* Connection error fixed.
* Improved stability.

~~### v1.1.0 (Sep 6, 2022) with Chat SDK v4.0.8~~
* **Deprecated as this version would cause connection failure to the desk server**
* Requirements changes
    * Android 5.0 (API level 21) or higher
    * Java 8 or higher
    * Support androidx only
    * Android Gradle plugin 4.0.1 or higher
    * Sendbird Chat SDK for Android 4.0.3 and later
* Artifact type has been changed
    * jar → aar

### v1.0.14 (Jun 2, 2021)
* Renamed parameter name of `Ticket#cancel` from `groupKey` to `groupKeyForTransfer`.

### v1.0.13 (May 21, 2021)
* Added supports for BotKey.
    * Added option to specify botKey when creating `Ticket`.
* Added `cancel(String transferGroupKey, Ticket.CancelHandler handler)`.
* Added support for FAQ.
    * Added `selectQuestion(long faqFileId, String question, Ticket.SelectQuestionHandler handler)`.
    * Added `FAQData` and `FAQResult`.

### v1.0.12 (Mar 2, 2021)
* Improved handling of error code.

### v1.0.11 (Dec 4, 2020)
* Improved stability.

### v1.0.10 (Nov 25, 2020)
* Added `close(String comment, CloseHandler handler)` in Ticket.

### v1.0.9 (Oct 22, 2020)
* Minor bugfix.

### v1.0.8 (Aug 20, 2020)
* Improved handling of error code and error messages on API failure.

### v1.0.7 (Apr 27, 2020)
* Added `create(String ticketTitle, String userName, String groupKey, Map<String, String> customFields, Priority priority, List<String> relatedChannelUrls, CreateHandler handler)` in Ticket.
* Added `setRelatedChannelUrls(List<String> relatedChannelUrls, SetRelatedChannelUrlsHandler handler)` in Ticket.
* Added `interface SetRelatedChannelUrlsHandler` in Ticket.
* Added `getRelatedChannels()` in Ticket.
* Added `class RelatedChannel` in Ticket.

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
