# Drivecare Customer Feedback Automation

An n8n workflow that scores customer feedback with AI and routes it automatically. Built as a capstone project, with a demo feedback form made in Lovable.

## How it works

1. **Intake:** the Lovable form posts to an n8n webhook (`/feedback-form`); fields are normalized and junk or near-empty submissions are logged and discarded.
2. **AI scoring:** GPT-4o-mini rates sentiment, severity, and confidence.
3. **Human escalation:** legal threats, safety issues, abuse, vague text, or confidence below 0.5 go to a human review queue. The team is emailed and the customer gets an acknowledgement.
4. **History check:** the customer's email is looked up in the master log to flag repeat negative feedback.
5. **Routing:**

| Outcome | Action |
|---|---|
| Positive | Logged as "ready to post" for marketing |
| Mild negative | GPT-4o drafts a reply, saved to a private queue for review |
| Severe or repeat negative | Draft reply generated, manager alerted by email |

6. **Reporting:** every stage is logged to Google Sheets and shown in a Looker Studio dashboard, filterable by location.

## Design choices
- Draft replies never invent facts, refunds, deadlines, or staff names.
- Low-confidence AI results go to a person instead of being guessed at.
- The history check returns a single item to prevent duplicate emails.

## Stack
n8n · OpenAI (GPT-4o, GPT-4o-mini) · Google Sheets · Gmail · Looker Studio · Lovable

## Setup
1. Import `drivecare_feedback_automation.json` into n8n.
2. Connect your own OpenAI, Google Sheets, and Gmail credentials.
3. Create a Google Sheet with tabs: Master Feedback, Log positive, Log need human, Log manager alert, Log private queue/draft response.
4. Replace `YOUR_GOOGLE_SHEET_ID`, the `example.com` emails, and the dashboard link.
5. Point your form at the webhook URL.
