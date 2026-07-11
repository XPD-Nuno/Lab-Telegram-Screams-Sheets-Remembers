# Telegram Client Requests to Airtable

## Overview

This project was created for the **LAB | Telegram screams, Sheets remembers** exercise.

The project demonstrates how n8n can connect two different services and move information between them automatically. When a client sends a message to a Telegram bot, n8n receives the message, transforms the Telegram data into a clear structure, and creates a new record in Airtable.

## Real-world scenario

A support team can use this workflow to capture incoming client requests from Telegram in a structured Airtable log. This removes the need to copy messages manually, reduces the chance of missed requests, and provides a simple record for tracking and follow-up.

## Integration pair

```text
Telegram -> Airtable
```

## Workflow structure

```text
Telegram Trigger -> Edit Fields -> Airtable Create a record
```

The **Telegram Trigger** starts the workflow when the bot receives a new message.

The **Edit Fields** node, which is the Set node in this version of n8n, transforms the source data into the fields expected by Airtable.

The **Airtable Create a record** node adds the transformed information to the Requests table.

## Airtable destination

The destination uses the following structure:

```text
Base: Client requests log
Table: Requests
```

The table contains these fields:

- `Timestamp`
- `Sender`
- `Chat`
- `Message text`
- `Status`

New requests are stored with the Airtable status `Todo`.

## Field mapping

The Edit Fields node transforms the Telegram payload using these assignments:

```text
Timestamp    = {{ $now }}
Sender       = {{ $json.message.from.first_name }}
Chat         = {{ String($json.message.chat.id) }}
Message text = {{ $json.message.text }}
Status       = New
```

The Airtable node maps the transformed output as follows:

```text
Timestamp    -> {{ $json.Timestamp }}
Sender       -> {{ $json.Sender }}
Chat         -> {{ $json.Chat }}
Message text -> {{ $json["Message text"] }}
Status       -> Todo
```

## Test results

The workflow was tested successfully with two Telegram messages. Both messages passed through the complete workflow and created correctly formatted records in Airtable.

The test messages were:

```text
Hello, I need help with my invoice.
Can someone help me update my delivery address?
```

## How to run the workflow

1. Import or recreate the workflow in n8n.
2. Create Telegram API credentials in n8n using a bot token generated through BotFather.
3. Create an Airtable Personal Access Token with these scopes:

```text
data.records:read
data.records:write
schema.bases:read
```

4. Give the Airtable token access to the `Client requests log` base.
5. Select the `Requests` table in the Airtable node.
6. Confirm the field mappings shown above.
7. Test or activate the workflow.
8. Send a message to the Telegram bot.
9. Confirm that a new record appears in Airtable.

> Never store Telegram bot tokens or Airtable access tokens in this repository. Credentials must remain inside the n8n credentials system.

## Repository structure

```text
Lab-Telegram-Screams-Sheets-Remembers/
├── README.md
├── lab_summary.md
└── screenshots/
    ├── n8n-workflow.png
    └── airtable-successful-rows.png
```

## File map

- `README.md` explains the project, setup, workflow, and repository structure.
- `lab_summary.md` contains the real-world justification, technical reflection, confirmed results, main challenge, and extension idea.
- `screenshots/n8n-workflow.png` shows the connected n8n workflow and successful execution.
- `screenshots/airtable-successful-rows.png` shows two successfully created Airtable records.

## Evidence

The evidence included in the repository demonstrates:

- A connected source, transformation, and destination workflow.
- Successful data mapping from Telegram JSON to Airtable fields.
- An error-free execution through the Airtable destination node.
- Two correctly formatted records stored in Airtable.
- Appropriate credential handling without secrets shown in screenshots.

## Possible extension

An IF node could be added before Airtable to prevent empty messages from being stored. Another possible extension would be to send a confirmation message after a request is logged successfully.

## Security notes

The repository must not contain:

- Telegram bot tokens
- Airtable Personal Access Tokens
- Credential screenshots that expose secrets
- Unrelated personal or project files
