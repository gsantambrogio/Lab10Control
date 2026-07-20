# Lab10 Telegram Notification System

This document covers the **Lab10** Telegram bot and its alarm-push endpoint,
both hosted in this repository on `molchip.lens.unifi.it`. The repo also
hosts unrelated sibling bots (`local/`+`push/`, `local_LENS/`+`push_LENS/`,
`LENS.php`, `tel.php`) — same code pattern, different bot tokens/databases —
which are out of scope here and only mentioned at the bottom so they aren't
confused with the Lab10 pieces.

## Overview

There are two integration points:

1. **The Lab10 Telegram bot** — a normal bot users add on Telegram and talk
   to. Every user who has ever messaged it is auto-registered as a recipient
   for *all* alarm broadcasts (see below — there's no per-alarm opt-in).
2. **`push_lab10/push.php`** — an HTTP endpoint (Basic Auth protected) that
   external monitoring systems hit with a GET request to broadcast an alarm
   message to every registered chat, without any Telegram-side interaction.

## Directory layout

```
telegram/
├── manager.php            Telegram webhook for the Lab10 bot (receives updates)
├── local_lab10/            shared includes, not web-accessible ("Deny from all")
│   ├── db.php               mysqli connection to the `lab10` database (gitignored)
│   ├── definitions.php      LAB name + BOT_TOKEN (gitignored)
│   └── tel_functions.php    all bot logic: send helpers, alarm senders, message processing
└── push_lab10/             external push endpoint for triggering alarms
    ├── .htaccess             HTTP Basic Auth (AuthUserFile /var/www/local/.htpasswdLAB10)
    └── push.php              parses the query string, dispatches to a sendXAlarm() function
```

`db.php`, `db.php~`, `definitions.php`, `definitions.php~` are excluded from
git everywhere in the tree (see `.gitignore`), since they hold the DB
password and the bot token. Read them directly on the server if you need the
actual values — they are intentionally not reproduced in this document.

## The bot side (`manager.php`)

- Webhook URL: `https://molchip.lens.unifi.it/telegram/manager.php`
- Registering/removing the webhook: run `php manager.php` from the CLI
  (`php manager.php delete` clears it) — this calls Telegram's `setWebhook`.
- Telegram POSTs a JSON update to `manager.php`, which decodes it and calls
  `processMessage()` (in `tel_functions.php`) when it contains a `message`.
- `processMessage()`:
  - Calls `checkNew()`, which `INSERT`s the chat into the `manager` MySQL
    table the first time it sees a `chatid`. **This is how the alarm
    recipient list builds itself** — anyone who has ever messaged the bot
    receives every future alarm, forever, with no way to unsubscribe from
    the bot side.
  - Sends `"Welcome!\n/help for help."` on first contact.
  - `/temp` → `sendTemp()`: replies with the latest row of the `temperature`
    table.
  - `/help` → `sendHelp()`: lists available commands.
  - Any other text → replies `"Cool"`.

## Database (`lab10` MySQL database)

**`manager`** — the alarm recipient list.
| column | type | notes |
|---|---|---|
| id | int, PK | |
| chatid | bigint, unique | Telegram chat id |
| telname | varchar(40) | Telegram username at registration time |
| messageid | int | id of the message that triggered registration |

Populated only by `checkNew()`. Every `sendXxxAlarm()` function does
`SELECT DISTINCT chatid FROM manager` and pushes to all of them — there is
no per-alarm-type subscription table.

**`temperature`** — read-only from this codebase's perspective.
| column | type | notes |
|---|---|---|
| id | int, PK | |
| gdate | timestamp | auto-set on insert/update |
| water | float | |
| lab | float | |
| lasertable | float | |

Read by `/temp` (`sendTemp()`). Nothing in this repo writes to it — a
separate logging job (outside this project) is presumably responsible.

## Outgoing alarms — `push_lab10/push.php`

Base URL: `https://molchip.lens.unifi.it/telegram/push_lab10/push.php`

Access requires HTTP Basic Auth, enforced by Apache via `.htaccess`
(`AuthUserFile /var/www/local/.htpasswdLAB10`) *before* PHP runs — `push.php`
itself does not re-check credentials.

Every alarm type shares the same delivery mechanism: a `sendXxxAlarm()`
function in `tel_functions.php` builds a text string, runs
`SELECT DISTINCT chatid FROM manager`, and calls `pushMessage($chat_id, $text)`
for each row with a `sleep(1)` between sends (simple pacing against
Telegram's API — not batching, not deduplication). `pushMessage()` hits
Telegram's `sendMessage` via a raw curl GET to
`api.telegram.org/bot<BOT_TOKEN>/sendMessage`.

`push.php` itself just checks which `$_GET` params are present and
dispatches — it does no validation of values beyond that.

### CTC temperature alarm

```
GET push.php?CTCAlarm={1|2}&T1={t1}&T2={t2}&T3={t3}&T4={t4}
```
- Upstream trigger (EPICS, not in this repo): any of 4 PVs
  `CTC:Temp1:STATUS` … `CTC:Temp4:STATUS` goes non-zero.
- Status codes: `1` = Underrange, `2` = Overrange.
- Handler: `sendCTCAlarm($status, $t1, $t2, $t3, $t4)`.
- Message: `Temperature {Underrange|Overrange}. T1: X K; T2: X K; T3: X K; T4: X K`

### CC1 (cold cathode gauge) alarm

```
GET push.php?CC1Alarm=YES
```
- Upstream trigger: PV `CC1:STATUS.STAT` severity == MAJOR (2), gated by
  `CC1:notify.VAL == 1`.
- Handler: `sendCC1Alarm()` — the value of `CC1Alarm` isn't inspected, only
  its presence.
- Message: fixed text `"Cold cathode gauge OVERRANGE"`.

### Water alarm

```
GET push.php?waterAlarm={severity}&waterTemp={temperature}
```
- Handler: `sendWaterAlarm($temperature, $severity)`.
- Message: `"{severity} Alarm: Water Temp Dept Physics\nWater temperature exceeded {temperature} C"`

### Cryostat compressor alarm

```
GET push.php?CryoAlarm={ALARM|WARNING|MAINTENANCE}&Msg={text}
```
- Upstream source: `TelAlarms.py`, a daemon on a Raspberry Pi (`cryo`)
  running the cryostat's EPICS IOC, watching a Cryomech CPA1114/CP1100-series
  compressor.
  - `ALARM` — fires when PV `cryo:alarms` (decoded Alarm-register fault
    text, e.g. `"High Pressure High, Motor Current High"`) changes; `Msg` is
    that text, or literal `CLEARED` once it resolves.
  - `WARNING` — same mechanism on PV `cryo:warnings` (non-fatal register).
  - `MAINTENANCE` — fires once when PV `cryo:hours` crosses 20,000 (adsorber
    replacement interval per the Cryomech manual for this model).
  - Edge-triggered upstream (Python caches the last value and only sends on
    a real transition), so `push.php`/`tel_functions.php` do not need to
    deduplicate repeated identical alarms.
- Handler: `sendCryoAlarm($kind, $msg)`.
- Message:
  - `ALARM` → `🚨 Cryostat ALARM: {Msg}`, or `✅ Cryostat alarm CLEARED` if
    `Msg == CLEARED`.
  - `WARNING` → `⚠️ Cryostat warning: {Msg}`, or `✅ Cryostat warning CLEARED`
    if `Msg == CLEARED`.
  - `MAINTENANCE` → `🔧 Cryostat maintenance due: {Msg}`.
- Verified 2026-07-20 with a live curl request against the production
  endpoint (200 OK, message delivered to the then-3 subscribed chats).

## Known gaps

- All alarm types broadcast to every subscriber in `manager` — there is no
  per-alarm-type channel or opt-out.
- `push.php` performs no auth of its own; it fully relies on the Apache
  Basic Auth in front of it.
- No rate limiting beyond the 1-second `sleep()` between per-recipient
  sends inside each `sendXxxAlarm()`.
- Editor backup files (`db.php~`, `tel_functions.php~`, etc.) scattered
  through the tree are not used by any code path.

## Sibling bots in this repo (not Lab10, mentioned for orientation only)

- `local/` + `push/` + `tel.php`, auth file `.htpasswdSTD`
- `local_LENS/` + `push_LENS/` + `LENS.php`, auth file `.htpasswdLENS`

Same webhook/push pattern as Lab10, but separate bot tokens, database
connections, and recipient tables.
