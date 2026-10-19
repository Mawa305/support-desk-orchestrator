# Support Desk Orchestrator

A multi-agent customer support workflow in n8n. A supervisor agent classifies each message, a specialist agent drafts the reply, and a QA agent checks it before anything is sent. Low-confidence, complaint, and failed-QA cases go to a human.

## Flow

1. **Webhook** receives the customer's name, email, and message. Invalid requests get a 400 response.
2. **Respond Accepted** returns 202 right away.
3. **Supervisor Agent** classifies the message as `billing`, `booking`, `technical`, `complaint`, or `general`, with a confidence score and a reason.
4. **Parse Routing** turns the output into structured fields. Unknown categories fall back to `general`.
5. **Confident?** sends low-confidence messages (below 0.7) to a human.
6. **Route by Category** sends each message to one specialist:
   - **Billing Agent:** acknowledges the concern and promises a review, without a deadline or refund.
   - **Booking Agent:** asks for a preferred date, time, and address, without confirming a slot.
   - **Technical Agent:** gives simple safe checks and tells the customer not to open the unit.
   - **General Agent:** answers only from the company information in the prompt.
7. **QA Agent** checks the draft against fixed rules: no refund or discount promises, no confirmed dates or technicians, no invented details, and a polite tone.
8. **QA Passed?** sends approved replies to the customer and logs them.
9. **Complaints, fallbacks, low-confidence messages, and QA rejections** go to **Human Handoff Alert**, which emails the owner and logs the case.

## Required Credentials
