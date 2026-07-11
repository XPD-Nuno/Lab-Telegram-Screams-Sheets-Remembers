# LAB | Telegram screams, Sheets remembers

## Chosen scenario

Telegram client request logger

## Real-world justification

A support team can use this workflow to capture incoming client requests from Telegram in a structured Airtable log. Instead of manually copying messages into a tracker, the workflow records each request automatically with the sender, chat identifier, message text, timestamp, and status. This saves time, reduces the chance of missed requests, and gives the team a simple audit trail for follow-up.

This scenario is realistic because support teams often receive short requests through messaging tools. Storing those messages in Airtable makes the information easier to review, assign, filter, and track than leaving it only inside a chat thread.

## Integration pair

Telegram to Airtable

## Workflow goal

When a client sends a message to the Telegram bot, n8n receives the message, reshapes the Telegram data into clear fields, and creates a new record in Airtable.

## Workflow structure

The workflow was built in n8n using this structure:

```text
Telegram Trigger -> Edit Fields -> Airtable Create a record
```

The Telegram Trigger listens for new messages sent to the bot. The Edit Fields node, which is the Set node in this n8n version, transforms the raw Telegram JSON into the exact fields required by Airtable. The Airtable node then creates a new record in the Requests table.

## Destination setup

The Airtable base was created with this name:

```text
Client requests log
```

The Airtable table was created with this name:

```text
Requests
```

The table contains these fields:

```text
Timestamp
Sender
Chat
Message text
Status
```

The Status field uses Airtable options. For new requests, the status was set to:

```text
Todo
```

## Field mapping

The Edit Fields node created the following mapped fields from the Telegram message data:

```text
Timestamp    = {{ $now }}
Sender       = {{ $json.message.from.first_name }}
Chat         = {{ String($json.message.chat.id) }}
Message text = {{ $json.message.text }}
Status       = New
```

The Airtable Create a record node then mapped those values into the Airtable table:

```text
Timestamp    -> {{ $json.Timestamp }}
Sender       -> {{ $json.Sender }}
Chat         -> {{ $json.Chat }}
Message text -> {{ $json["Message text"] }}
Status       -> Todo
```

The workflow uses Todo in Airtable because Todo represents a new request that has been received but not processed yet.

## Successful test results

The workflow was tested end-to-end with Telegram messages sent to the bot. n8n received the messages, the Edit Fields node transformed the data, and Airtable created records successfully.

Two successful Airtable records were created:

```text
Record 1
Sender: Nuno
Chat: 8629349711
Message text: Hello, I need help with my invoice.
Status: Todo

Record 2
Sender: Nuno
Chat: 8629349711
Message text: Can someone help me update my delivery address?
Status: Todo
```

This confirms that the workflow runs correctly from the Telegram source through the transformation node and into the Airtable destination.

## What was hardest

The hardest part was configuring the Airtable Personal Access Token correctly. The connection initially worked, but n8n could not see the Airtable columns because the token did not include the `schema.bases:read` scope. After adding the correct scope, n8n was able to load the Client requests log base, the Requests table, and the fields required for mapping.

Another small challenge was matching the status values between n8n and Airtable. The workflow uses New as the internal status value in the Edit Fields node, but Airtable stores the equivalent value as Todo because that was the available status option in the Airtable field.

## Extension idea

A useful extension would be to add an IF node before Airtable to skip empty messages or messages that do not contain useful request information. This would improve the quality of the Airtable log by storing only valid support requests.

Another possible extension would be to add a notification step after Airtable, such as sending a message to a support channel when a new request is logged.

## Evidence included for submission

The GitHub repository should include these screenshots:

```text
screenshots/n8n-workflow.png
screenshots/airtable-successful-rows.png
```

The n8n screenshot should show the complete workflow:

```text
Telegram Trigger -> Edit Fields -> Create a record
```

The Airtable screenshot should show at least two successful rows in the Requests table.

## Files for submission

Recommended repository structure:

```text
Lab-Telegram-Screams-Sheets-Remembers/
├── README.md
├── lab_summary.md
└── screenshots/
    ├── n8n-workflow.png
    └── airtable-successful-rows.png
```
