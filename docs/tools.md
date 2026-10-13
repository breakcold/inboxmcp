# Tool catalog

Snapshot of the public runtime documentation on 2026-10-13. The catalog contains 50 tools; individual connections discover tools according to connected providers and permissions. See [live documentation](https://inbox-mcp.com/docs) for current behavior.

Full input schemas and annotations are in [tools.json](../tools.json).

## linkedin_status

Call before planning several LinkedIn actions. Says whether the account can act now (blocked_reason), how many actions are left today (profile_views → linkedin_get_profile, searches → linkedin_search_people, messages → linkedin_send_message, new_conversations → linkedin_start_chat, invitations → linkedin_send_invitation, posts → linkedin_create_post; invitations also have a weekly limit), and ready_in_seconds for each: the wait before that action can run. Reading the inbox has a high limit and is not listed. No account or a disconnected one: use linkedin_connect_account. Plan or seats: use inboxmcp_billing. For WhatsApp, Telegram and Instagram accounts, use inbox_status.

- Group: Account & plan
- Read-only annotation: true

## inbox_status

Status and limits of one connected account, any provider (LinkedIn, WhatsApp, Telegram, Instagram). Call before planning several actions. Says whether the account can act now (blocked_reason), how many actions are left today (messages → *_send_message, new_conversations → *_start_chat, profile_views → *_get_profile, posts → linkedin_create_post / instagram_create_post; on LinkedIn also searches and invitations), and ready_in_seconds for each. Reading has a high limit and is not listed. No account or a disconnected one: use connect_account. Plan or seats: use inboxmcp_billing.

- Group: Account & plan
- Read-only annotation: true

## connect_account

Get a link for the user to connect a LinkedIn, WhatsApp, Telegram or Instagram account, or to reconnect one that was logged out. Before giving the link, tell the user what the result says about the free trial or price. They log in on our connect page in their browser (LinkedIn and Instagram: their login; WhatsApp and Telegram: scan a QR code). Never ask for passwords or codes in the chat. The link works for 30 minutes. Afterwards, call inbox_list_accounts to confirm.

- Group: Account & plan
- Read-only annotation: true

## linkedin_connect_account

Get a link for the user to connect a LinkedIn account, or to reconnect one LinkedIn logged out. Before giving the link, tell the user what the result says about the free trial or price. They log in to LinkedIn in their browser on our connect page (never ask for their LinkedIn password or codes in the chat). The link works for 30 minutes. Afterwards, call linkedin_status to confirm.

- Group: Account & plan
- Read-only annotation: true

## inboxmcp_billing

The user's inbox MCP plan: free trial (3 days, starts when the first account is connected) or subscription ($10/month per connected account, any provider), with a link to our billing page to subscribe, update their card, get invoices, or cancel (payment happens on Stripe, in their browser). The link works for 30 minutes. Use it when the tools say the trial or subscription ended.

- Group: Account & plan
- Read-only annotation: true

## inbox_list_accounts

The LinkedIn, WhatsApp, Telegram and Instagram accounts this connection can use, with their account_id, provider, name and status. Pass account_id as `account` to the inbox_* and provider tools (linkedin_*, whatsapp_*, telegram_*, instagram_*) when more than one is connected.

- Group: Inbox
- Read-only annotation: true

## inbox_list_chats

Conversations across every connected inbox (LinkedIn, WhatsApp, Telegram, Instagram), merged and sorted most recent first; account or provider narrows it. Use unread=true to triage. Each item has chat_id, its account_id and provider (pass account_id as account to inbox_get_messages / inbox_send_message when several accounts are connected), and person_id for 1-to-1 chats. Accounts that can't be read right now are listed in skipped, with what to do.

- Group: Inbox
- Read-only annotation: true

## inbox_get_messages

Messages in one conversation, most recent first. 'from' is 'me' (the account owner) or 'them'.

- Group: Inbox
- Read-only annotation: true

## inbox_send_message

Reply in an existing conversation, on any connected provider. Plain text. Show the user the exact text before calling this.

- Group: Inbox
- Read-only annotation: false

## inbox_start_chat

Message someone you have no conversation with yet. `to`: on LinkedIn the person_id (1st-degree connections; for InMail use linkedin_start_chat), on WhatsApp a phone number with country code, on Telegram the user id, on Instagram the user id. New conversations have a low daily limit, especially on WhatsApp, where cold messages get new accounts banned. Show the user the exact text before calling this.

- Group: Inbox
- Read-only annotation: false

## inbox_mark_read

Marks a conversation read (or unread), on any connected provider.

- Group: Inbox
- Read-only annotation: false

## linkedin_list_chats

Conversations in the LinkedIn inbox, most recent first. Use unread=true to triage. Returns chat_id values for linkedin_get_messages / linkedin_send_message and person_id for 1-to-1 chats. With several LinkedIn accounts, lists them all merged (each item has its account_id: pass it as account to the other linkedin_* tools); account narrows to one.

- Group: LinkedIn
- Read-only annotation: true

## linkedin_get_messages

Messages in one conversation, most recent first. 'from' is 'me' (the account owner) or 'them'.

- Group: LinkedIn
- Read-only annotation: true

## linkedin_send_message

Reply in an existing conversation. Plain text. Show the user the exact text before calling this.

- Group: LinkedIn
- Read-only annotation: false

## linkedin_start_chat

Message someone you are connected with (1st degree). For others, set inmail=true (needs a LinkedIn premium plan with InMail credits). Get the person_id from linkedin_search_people or linkedin_get_profile.

- Group: LinkedIn
- Read-only annotation: false

## linkedin_mark_read

Marks a conversation read (or unread).

- Group: LinkedIn
- Read-only annotation: false

## linkedin_search_people

Search LinkedIn members by keywords, or paste a LinkedIn people-search URL copied from the browser (keeps all its filters). Each page costs one LinkedIn search. Returns person_id values for other tools.

- Group: LinkedIn
- Read-only annotation: true

## linkedin_get_profile

Full profile by person_id or public identifier (the part after linkedin.com/in/). Counts as a profile view on LinkedIn.

- Group: LinkedIn
- Read-only annotation: true

## linkedin_send_invitation

Invite someone to connect, optionally with a note (max 300 characters, 200 on free LinkedIn accounts, which can add a note to about 15 invitations a week). Limited per day and per week, and LinkedIn actions are spaced about a minute apart; check linkedin_status first.

- Group: LinkedIn
- Read-only annotation: false

## linkedin_list_invitations

Pending invitations you sent or received.

- Group: LinkedIn
- Read-only annotation: true

## linkedin_create_post

Publish a text post on the account's feed. A link can be appended; LinkedIn builds the preview from the first URL in the text. Public and irreversible from here: confirm the final text with the user first.

- Group: LinkedIn
- Read-only annotation: false

## linkedin_schedule_outreach

Queue up to 50 messages or invitations. They are sent one by one with random gaps of a minute or more, respecting daily limits. An invitation's text is its note (max 300 characters, 200 on free LinkedIn accounts). Returns a job_id; poll with linkedin_get_job. Confirm the list with the user first.

- Group: LinkedIn
- Read-only annotation: false

## linkedin_get_job

Progress and per-item results of a job from linkedin_schedule_outreach.

- Group: LinkedIn
- Read-only annotation: true

## whatsapp_list_chats

WhatsApp chats (1-to-1 and groups), most recent first. Use unread=true to triage. Returns chat_id values for whatsapp_get_messages / whatsapp_send_message, and person_id (the contact's WhatsApp id, <digits>@s.whatsapp.net) for 1-to-1 chats; for a group's members use whatsapp_list_group_members. With several WhatsApp accounts, lists them all merged (each item has its account_id: pass it as account to the other whatsapp_* tools); account narrows to one.

- Group: WhatsApp
- Read-only annotation: true

## whatsapp_get_messages

Messages in one WhatsApp chat, most recent first. 'from' is 'me' (the account owner) or 'them'. message_id values work with whatsapp_react.

- Group: WhatsApp
- Read-only annotation: true

## whatsapp_send_message

Reply in an existing WhatsApp chat (1-to-1 or group). Plain text. WhatsApp actions are spaced 10–20 s apart. Show the user the exact text before calling this.

- Group: WhatsApp
- Read-only annotation: false

## whatsapp_start_chat

Message a phone number you have no WhatsApp chat with yet. phone: international format with its country code, like +33 6 12 34 56 78. WhatsApp bans accounts that send cold messages (new accounts after 2–3), so only message people who expect it; at most 10 new chats a day. Show the user the exact text before calling this.

- Group: WhatsApp
- Read-only annotation: false

## whatsapp_mark_read

Marks a WhatsApp chat read (or unread).

- Group: WhatsApp
- Read-only annotation: false

## whatsapp_react

Add an emoji reaction to a message (message_id from whatsapp_get_messages). Any emoji works; a new reaction replaces yours.

- Group: WhatsApp
- Read-only annotation: false

## whatsapp_get_profile

A WhatsApp user's profile: name, username, phone when WhatsApp shares it, bio, followers and picture. Counts as a profile view.

- Group: WhatsApp
- Read-only annotation: true

## whatsapp_list_group_members

Members of a WhatsApp group chat (chat_id from whatsapp_list_chats), with their user_id for whatsapp_get_profile and their phone number. Group chats only.

- Group: WhatsApp
- Read-only annotation: true

## telegram_list_chats

Telegram chats (private chats and groups), most recent first. Use unread=true to triage. Returns chat_id values for telegram_get_messages / telegram_send_message, and person_id (the contact's Telegram user id) for private chats; for a group's members use telegram_list_group_members. With several Telegram accounts, lists them all merged (each item has its account_id: pass it as account to the other telegram_* tools); account narrows to one.

- Group: Telegram
- Read-only annotation: true

## telegram_get_messages

Messages in one Telegram chat, most recent first. 'from' is 'me' (the account owner) or 'them'. message_id values work with telegram_react.

- Group: Telegram
- Read-only annotation: true

## telegram_send_message

Reply in an existing Telegram chat (private or group). Plain text. Show the user the exact text before calling this.

- Group: Telegram
- Read-only annotation: false

## telegram_start_chat

Message a Telegram user you have no chat with yet, by numeric user id (a chat's person_id, or a member from telegram_list_group_members). Telegram restricts accounts that message many strangers, so only message people who expect it; at most 10 new chats a day. Show the user the exact text before calling this.

- Group: Telegram
- Read-only annotation: false

## telegram_mark_read

Marks a Telegram chat read (or unread).

- Group: Telegram
- Read-only annotation: false

## telegram_react

Add an emoji reaction to a message (message_id from telegram_get_messages). Telegram accepts only its standard reaction emojis (👍 ❤️ 🔥 🎉 😁 👏 …); others fail.

- Group: Telegram
- Read-only annotation: false

## telegram_get_profile

A Telegram user's profile: name, username, phone when Telegram shares it, bio, followers and picture. Counts as a profile view.

- Group: Telegram
- Read-only annotation: true

## telegram_list_group_members

Members of a Telegram group chat (chat_id from telegram_list_chats), with their user_id for telegram_get_profile. Group chats only.

- Group: Telegram
- Read-only annotation: true

## instagram_list_chats

Instagram direct messages, most recent first. Use unread=true to triage. Returns chat_id values for instagram_get_messages / instagram_send_message, and person_id (the contact's Instagram user id) for 1-to-1 chats. With several Instagram accounts, lists them all merged (each item has its account_id: pass it as account to the other instagram_* tools); account narrows to one.

- Group: Instagram
- Read-only annotation: true

## instagram_get_messages

Messages in one Instagram conversation, most recent first. 'from' is 'me' (the account owner) or 'them'. message_id values work with instagram_react.

- Group: Instagram
- Read-only annotation: true

## instagram_send_message

Reply in an existing Instagram direct conversation. Plain text. Show the user the exact text before calling this.

- Group: Instagram
- Read-only annotation: false

## instagram_start_chat

Send a direct message to an Instagram user you have no conversation with yet, by user id (a chat's person_id, or from instagram_get_profile). Messages to people who don't follow the account land in their message requests, and Instagram restricts accounts that message many strangers: only message people who expect it; at most 10 new conversations a day. Show the user the exact text before calling this.

- Group: Instagram
- Read-only annotation: false

## instagram_mark_read

Marks an Instagram conversation read (or unread).

- Group: Instagram
- Read-only annotation: false

## instagram_react

Add an emoji reaction to a message (message_id from instagram_get_messages). Instagram keeps one reaction per person: a new one replaces yours.

- Group: Instagram
- Read-only annotation: false

## instagram_get_profile

A Instagram user's profile: name, username, phone when Instagram shares it, bio, followers and picture. Counts as a profile view.

- Group: Instagram
- Read-only annotation: true

## instagram_list_posts

Posts of an Instagram user, most recent first: the account's own by default, or someone else's with user_id. Returns caption, link, date, likes and comments.

- Group: Instagram
- Read-only annotation: true

## instagram_create_post

Publish a photo on the account's Instagram feed: image_url is an https link to a JPEG, PNG or WebP (at most 8 MB), text is the caption. Public and irreversible from here: confirm the image and the caption with the user first.

- Group: Instagram
- Read-only annotation: false

## search

Search LinkedIn people by keywords. Returns ids usable with fetch.

- Group: ChatGPT deep research
- Read-only annotation: true

## fetch

Fetch a full record by id from search: person:<id> or chat:<id>.

- Group: ChatGPT deep research
- Read-only annotation: true
