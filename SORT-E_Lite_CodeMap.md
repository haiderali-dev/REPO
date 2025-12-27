# SORT-E Lite – Code Map (auto-generated)
Generated from: /mnt/data/SORT-E_SCD_PROJECT/SORT-E_SCD_PROJECT
Total Python files: 19
## File list and LOC
| File | Lines | Code est. | Bytes |
|---|---:|---:|---:|
| `config/database.py` | 128 | 106 | 4782 |
| `config/settings.py` | 43 | 34 | 1055 |
| `controllers/auth_controller.py` | 14 | 9 | 436 |
| `controllers/email_controller.py` | 427 | 346 | 15609 |
| `controllers/tag_rule_controller.py` | 29 | 21 | 972 |
| `main.py` | 15 | 10 | 240 |
| `models/attachment.py` | 166 | 139 | 5470 |
| `models/email.py` | 201 | 181 | 7337 |
| `models/email_account.py` | 197 | 171 | 6740 |
| `models/tag_rule.py` | 249 | 197 | 8869 |
| `models/user.py` | 98 | 77 | 3355 |
| `tests/__init__.py` | 0 | 0 | 0 |
| `tests/test_auth_controller.py` | 38 | 29 | 1195 |
| `tests/test_imap_date.py` | 14 | 9 | 307 |
| `tests/test_rule_matching.py` | 32 | 25 | 1159 |
| `tests/test_tag_rules.py` | 52 | 41 | 2013 |
| `views/app.py` | 51 | 40 | 1539 |
| `views/dashboard_view.py` | 642 | 506 | 24931 |
| `views/login_view.py` | 63 | 49 | 2316 |

## `config/database.py`
> SQLite database utilities. - init_db(): creates tables if missing + performs lightweight migrations - get_connection(): returns a connectio…

- **func** `get_connection()` (L17-L33): Return a SQLite connection. The DB file and attachments folder are created inside the project root (see config/settings.py).
- **func** `_column_exists()` (L36-L41)
- **func** `init_db()` (L44-L128)

## `config/settings.py`
> Application settings (single source of truth). Keep this file simple and explicit. No side effects.


## `controllers/auth_controller.py`
> Auth controller: imperative, small, testable.

- **class** `AuthController` (L9-L14)
  - `AuthController.signup()` (L10-L11)
  - `AuthController.login()` (L13-L14)

## `controllers/email_controller.py`
> Email controller: IMAP sync, header fetch, preview, attachment download. Changes in v3: - Tag rules are automatically applied to NEW emails…

- **func** `_utc_now_iso()` (L28-L29)
- **func** `_decode_header()` (L32-L43)
- **func** `_parse_from()` (L46-L47)
- **func** `_imap_date()` (L50-L51)
- **func** `_strip_html()` (L54-L58)
- **class** `FetchResult` (L62-L65)
- **class** `EmailPreview` (L69-L75)
- **class** `EmailController` (L78-L427)
  - `EmailController.__init__()` (L79-L84)
  - `EmailController.add_account()` (L87-L107)
  - `EmailController.list_accounts()` (L109-L110)
  - `EmailController.start_auto_sync()` (L113-L137)
  - `EmailController.stop_auto_sync()` (L139-L140)
  - `EmailController._connect_imap()` (L142-L152)
  - `EmailController.fetch_new_headers()` (L154-L169)
  - `EmailController.fetch_headers_since()` (L171-L242)
  - `EmailController.fetch_old_headers()` (L244-L261)
  - `EmailController.fetch_email_preview()` (L264-L328)
  - `EmailController.download_and_save_attachments_for_tagged_emails()` (L331-L427)

## `controllers/tag_rule_controller.py`
> Tag rule controller. Note: rules are auto-applied to NEW emails during sync (see EmailController).

- **class** `TagRuleController` (L12-L29)
  - `TagRuleController.create_rule()` (L13-L19)
  - `TagRuleController.apply_rule_to_existing_emails()` (L21-L23): Apply a newly created rule to already-fetched emails in the local DB.
  - `TagRuleController.list_rules()` (L25-L26)
  - `TagRuleController.delete_rule()` (L28-L29)

## `main.py`
> SORT-E Lite entry point.

- **func** `main()` (L8-L11)

## `models/attachment.py`
> Attachment model + saver process. - AttachmentSaverProcess uses multiprocessing to write files without blocking UI. - Attachment model stor…

- **func** `_utc_now_iso()` (L18-L19)
- **class** `Attachment` (L23-L62)
  - `Attachment.create()` (L33-L41)
  - `Attachment.list_for_email()` (L44-L62)
- **class** `AttachmentView` (L66-L71)
- **func** `list_for_user_view()` (L74-L100): List all downloaded attachments for a user, with email subject + tag.
- **class** `AttachmentSaverProcess` (L103-L166): A simple multiprocessing-based file writer. Send tasks: (email_id, folder, filename, bytes_payload, content_type) Receive results on result_queue: ("ok"|"error…
  - `AttachmentSaverProcess.__init__()` (L110-L113)
  - `AttachmentSaverProcess.start()` (L115-L119)
  - `AttachmentSaverProcess.stop()` (L121-L128)
  - `AttachmentSaverProcess._safe_filename()` (L131-L137)
  - `AttachmentSaverProcess._run()` (L140-L166)

## `models/email.py`
> Email model (stored headers + tags). - We store headers during sync. - Email body is fetched on-demand for preview (Gmail-like open on clic…

- **func** `_utc_now_iso()` (L15-L16)
- **func** `_split_tags()` (L19-L20)
- **class** `Email` (L24-L201)
  - `Email.upsert_header()` (L35-L57)
  - `Email.id_for_account_uid()` (L60-L66)
  - `Email.get_by_id()` (L69-L87)
  - `Email.list_recent_by_user()` (L90-L114)
  - `Email.total_count_for_user()` (L116-L126): Total stored emails for user (not limited).
  - `Email.remove_tag_from_user_emails()` (L129-L155): Remove a single tag from all emails for a user. Returns number of affected emails.
  - `Email.list_by_tag()` (L157-L165)
  - `Email.tag_counts_for_user()` (L168-L173)
  - `Email.set_tags()` (L176-L179)
  - `Email.append_tag()` (L182-L195)
  - `Email.mark_has_attachments()` (L198-L201)

## `models/email_account.py`
> Email account model. Note: - OAuth is out of scope for this lite version. - Many providers require 'App Passwords' (especially Gmail/iCloud…

- **func** `_utc_now_iso()` (L21-L22)
- **func** `_try_keyring_set()` (L25-L34)
- **func** `_try_keyring_get()` (L37-L45)
- **class** `EmailAccount` (L49-L197)
  - `EmailAccount.add()` (L64-L126)
  - `EmailAccount.list_for_user()` (L128-L151)
  - `EmailAccount.get_by_id()` (L154-L175)
  - `EmailAccount.get_password()` (L178-L191)
  - `EmailAccount.update_last_sync()` (L194-L197)

## `models/tag_rule.py`
> Tag rules model. Rules match ONLY on: - email subject - sender email address / from header Used in two ways: 1) Automatic tagging of NEW em…

- **func** `_utc_now_iso()` (L21-L22)
- **func** `_norm()` (L25-L26)
- **func** `_split_tags()` (L29-L30)
- **class** `TagRule` (L34-L249)
  - `TagRule.matches()` (L42-L51)
  - `TagRule.create()` (L55-L69)
  - `TagRule.list_for_user()` (L72-L89)
  - `TagRule.list_active_for_user()` (L92-L93)
  - `TagRule.delete()` (L96-L119): Delete a rule. If this was the last rule for its tag_name, also remove that tag from emails.
  - `TagRule.get_by_id()` (L123-L139)
  - `TagRule.matched_tags()` (L143-L150): Return the tag names whose rules match this email header.
  - `TagRule.apply_to_all_emails()` (L154-L175)
  - `TagRule.apply_rule_to_existing_emails()` (L178-L249): Apply ONE rule to all stored emails for this user. Why this exists: - New emails get auto-tagged during sync (fast path in EmailController) - But when a rule i…

## `models/user.py`
> User model (signup/login). Passwords are stored using PBKDF2-HMAC-SHA256 with a random salt.

- **func** `_utc_now_iso()` (L18-L19)
- **func** `_hash_password()` (L22-L25)
- **class** `User` (L29-L98)
  - `User.create()` (L35-L62)
  - `User.authenticate()` (L65-L79)
  - `User.get_by_id()` (L82-L90)
  - `User.update_attachments_dir()` (L93-L98)

## `tests/__init__.py`

## `tests/test_auth_controller.py`
- **class** `TestAuthController` (L10-L34)
  - `TestAuthController.setUp()` (L11-L18)
  - `TestAuthController.tearDown()` (L20-L21)
  - `TestAuthController.test_signup_and_login()` (L23-L29)
  - `TestAuthController.test_duplicate_username()` (L31-L34)

## `tests/test_imap_date.py`
- **class** `TestImapDate` (L7-L10)
  - `TestImapDate.test_imap_date_format()` (L8-L10)

## `tests/test_rule_matching.py`
- **class** `TestRuleMatching` (L10-L28)
  - `TestRuleMatching.setUp()` (L11-L17)
  - `TestRuleMatching.tearDown()` (L19-L20)
  - `TestRuleMatching.test_matched_tags()` (L22-L28)

## `tests/test_tag_rules.py`
- **class** `TestTagRules` (L11-L48)
  - `TestTagRules.setUp()` (L12-L33)
  - `TestTagRules.tearDown()` (L35-L36)
  - `TestTagRules.test_rule_apply_counts_and_filter()` (L38-L48)

## `views/app.py`
> Tkinter app shell (frame switching).

- **class** `App` (L14-L51)
  - `App.__init__()` (L15-L28)
  - `App._set_frame()` (L30-L34)
  - `App.show_login()` (L36-L37)
  - `App.show_dashboard()` (L39-L40)
  - `App._on_close()` (L42-L51)

## `views/dashboard_view.py`
> Main dashboard view (Tkinter) with: - Gmail-like open-on-click preview - Tag counts + tag filtering - Automatic tagging: rules apply to NEW…

- **class** `DashboardView` (L24-L642)
  - `DashboardView.__init__()` (L25-L53)
  - `DashboardView.destroy()` (L55-L60)
  - `DashboardView._build()` (L63-L217)
  - `DashboardView._auto_refresh()` (L220-L222)
  - `DashboardView.set_status()` (L224-L226)
  - `DashboardView._update_attachment_action_state()` (L228-L239): Enable attachment fetch only when a specific tag is selected.
  - `DashboardView._logout()` (L242-L244)
  - `DashboardView.choose_folder()` (L246-L256)
  - `DashboardView._provider_changed()` (L258-L264)
  - `DashboardView.refresh_accounts()` (L267-L275)
  - `DashboardView._on_account_select()` (L277-L282)
  - `DashboardView.add_account()` (L284-L309)
  - `DashboardView._on_fetch_mode_changed()` (L311-L321)
  - `DashboardView.fetch_emails()` (L323-L349)
  - `DashboardView.refresh_tags()` (L352-L393)
  - `DashboardView._on_tag_select()` (L395-L407)
  - `DashboardView.refresh_emails()` (L410-L422)
  - `DashboardView._on_email_select()` (L424-L432)
  - `DashboardView._set_preview_text()` (L434-L438)
  - `DashboardView._load_preview_async()` (L440-L455)
  - `DashboardView._format_preview()` (L457-L468)
  - `DashboardView.refresh_rules()` (L471-L475)
  - `DashboardView.create_rule()` (L477-L508)
  - `DashboardView.delete_rule()` (L510-L522)
  - `DashboardView._open_in_os()` (L525-L538)
  - `DashboardView.view_attachments()` (L540-L627)
  - `DashboardView.save_attachments()` (L629-L642)

## `views/login_view.py`
> Login/Signup view (Tkinter).

- **class** `LoginView` (L12-L63)
  - `LoginView.__init__()` (L13-L19)
  - `LoginView._build()` (L21-L45)
  - `LoginView._submit()` (L47-L63)

