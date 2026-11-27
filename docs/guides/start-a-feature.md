# Start a Feature

Use this guide before you build any feature. It is written for people who are not sure how to describe what they want. The agent asks the questions. You answer in plain words. The agent writes the answers into a spec, and you confirm the spec before any code is written.

Use the **small** flow for one screen or one action. Use the **large** flow for anything that touches several features, adds tables, handles money, or needs more than one permission.

## Rules for the agent

1. Do not write code until the user confirms the spec with a clear "yes."
2. Ask one group of questions at a time. Never more than five questions in one message.
3. Use plain words. If you must use a technical term, explain it in one short sentence.
4. Accept "I don't know" as an answer. Write it as an open question. Do not guess.
5. Write the answers into the spec file as you go. Do not wait until the end.
6. Show the user a short summary at each checkpoint. Ask for confirmation before moving on.
7. Refuse or skip any part that depends on an open question, and say which part.

## How to tell small from large

| Signal | Small | Large |
|---|---|---|
| Screens | One | Several |
| Features touched | One | Two or more |
| Tables | Uses existing ones | Adds new ones |
| Money, points, or vouchers | No | Yes |
| Permissions | Uses existing ones | Adds new ones |
| Time to build | Hours | Days or weeks |

If any row says "Large," use the large flow.

## Small flow

Ask these five questions, one message at a time:

1. Who uses this? (for example: staff, customers, the owner)
2. What should they be able to do? (one sentence)
3. What does success look like? (a concrete example)
4. What must never happen? (for example: a customer must never see another customer's data)
5. What is already decided, and by whom? (anything the boss has said)

Then write `docs/features/<name>.md` from the template. Show it to the user. Wait for "yes."

## Large flow

A large feature is too big to specify in one go. Split it into slices first.

### Checkpoint 1: The goal

Ask:

1. What problem does this solve, in one or two sentences?
2. Who are the users? List each group.
3. How will we know it worked? Give one real example.
4. What is the first thing that must work, even if nothing else does?

Write the answers into the top of the spec. Show the summary. Wait for "yes."

### Checkpoint 2: The decided rules

Ask about each kind of rule. Accept "not decided" for any of them.

- **Who may do what?** Which roles can see, change, or delete things?
- **Money and points:** Is there any money, points, or voucher? Who may change the amounts?
- **Limits:** Is there any rule about quantity, time, or amount?
- **Data:** What must be kept forever, and what may be removed?
- **Errors:** What should the user see when something goes wrong?

For each answer:

- If the owner decided it, add a row to the **Decided rules** table, with who decided and when.
- If the owner did not decide it, add it to **Open questions**.

Show both lists. Wait for "yes."

### Checkpoint 3: The slices

Split the feature into slices. A slice is a part that can be built, tested, and shown to the user on its own. Good slices are small and do one thing.

For each slice, write:

- **Name:** a short label, such as "Create a booking."
- **Done when:** one observable result.
- **Depends on:** other slices or open questions, if any.

Put the slices in order. The first slice should be the smallest one that proves the idea. Show the list. Wait for "yes."

Write each slice as a section in the spec. Do not put all slices in one file, unless the feature is small enough.

### Checkpoint 4: Before building each slice

For the slice to be built next, confirm:

- Every rule it uses is in the Decided rules table.
- Every open question it touches is either answered or has a safe behavior (refuse or disable).
- The owner module or feature is known (see `docs/architecture/features.md`).

Then build that slice only. When it is done, go back to the user, show what works, and ask what to build next.

## What the spec must contain

Use `docs/features/_template.md`. For a large feature, the spec must have:

- Goal and non-goals
- Users and permissions
- Decided rules table
- Open questions
- One section per slice, each with its own acceptance criteria
- Data changes and delete behavior

## Starter prompts

For a small feature:

> I want to build [your idea]. Do not write code yet. Follow `docs/guides/start-a-feature.md`, small flow. Ask me the five questions one message at a time, in plain words. Then write `docs/features/<name>.md` and show it to me. Wait for my "yes."

For a large feature:

> I want to build [your idea]. Do not write code yet. Follow `docs/guides/start-a-feature.md`, large flow. Start with Checkpoint 1. Ask only the questions for the current checkpoint. Show me a summary and wait for my "yes" before the next checkpoint.

## Edge cases

- **The user changes their mind mid-feature.** Update the spec first. Show the change. Then continue. Do not change code before the spec.
- **The user says "just do it."** Explain in one sentence that the open questions must be answered first, and list them. Offer to build the slices that do not depend on them.
- **The user does not know the answer to a rule.** Write it as an open question, with the owner's name if known. This is a normal outcome, not a failure.
- **The feature grows during building.** Stop, update the spec, and confirm with the user before continuing.
