# Start a Feature

Use this guide before you build any feature. It is written for people who are not sure how to describe what they want. The agent asks the questions. The user answers in plain words, or with a letter from a list. The agent writes the answers into a spec, and the user confirms the spec before any code is written.

Use the **small** flow for one screen or one action. Use the **large** flow for anything that touches several features, adds tables, handles money, or needs more than one permission.

## Rules for the agent

1. Do not write code until the user confirms the spec with a clear "yes."
2. Ask one checkpoint at a time. Never more than five questions in one message.
3. Use plain words. If you must use a technical term, explain it in one short sentence.
4. Ask one open question first, then offer choices. Every question has a "something else" choice and a "not sure yet" choice. Do not offer choices alone, because they can lead the answer.
5. Mark who decides each question: the owner (boss) for product and permission rules, the user for choices about what they want to see first, and the agent for technical details that follow our standards.
6. Accept "not sure yet" as an answer. Write it as an open question. Do not guess.
7. Write the answers into the spec file as you go. Do not wait until the end. Create the file at Checkpoint 1 with `Status: draft`.
8. Show a short summary at each checkpoint. Wait for a clear "yes" before the next checkpoint.
9. Refuse or skip any part that depends on an open question, and say which part.
10. At the end of every checkpoint, and at the end of the small flow, ask: "Is there anything else I should know?" Write any answer into the spec before the next step.

## What the agent may and may not decide

| The agent may decide | The agent must not decide |
|---|---|
| Which tools and folders to use | Who can read or write |
| How code is organized, following our standards | Whether a reader needs an account |
| How to test a part | What the product is allowed to show |

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

## Question format

Every question follows this format. Use it in every checkpoint.

```text
N. The open question, in plain words.
   Why we ask: one sentence on what this decides.
   Who decides: owner, user, or agent.
   Describe it in your own words, or pick one:
   a) First choice, in plain words.
   b) Second choice, in plain words.
   c) Something else. Describe it.
   d) Not sure yet. We'll leave it open.

Example reply: "1a, 2b, 3d."
```

The open question always comes first, so the user can answer in their own words. The choices are a guide, not the only answer. Always keep "something else" and "not sure yet."

## When the owner has not answered

If the user cannot answer a question that the owner decides, the agent writes a short message for the owner, using the same questions and choices. The user sends it and pastes the reply back.

```text
Hi, I'm starting the [feature] work. Please answer these quick questions so I can build the right part first:
1. [question] a) [choice] b) [choice] c) not sure
2. [question] a) [choice] b) [choice] c) not sure
```

## Small flow

Ask these five questions in the question format. Keep them in one message.

1. Who uses this? Choices: staff, customers, the owner, or something else.
2. What should they be able to do? Choices: one sentence each, from the user's answer.
3. What does success look like? Ask for one concrete example.
4. What must never happen? Choices: a short list of examples, plus "not sure yet."
5. What is already decided, and by whom? Ask the user to name the owner.

Then write `docs/features/<name>.md` from the template with `Status: draft`. Show it to the user. Wait for "yes." After "yes," set the status to `confirmed`, with the date and who confirmed it.

## Large flow

A large feature is too big to specify in one go. Split it into slices first.

### Checkpoint 1: The first part to build

Ask three questions in the question format:

1. Who writes or changes the data? Who decides: owner.
2. Who can see the data? Who decides: owner.
3. What should work first? Choices: the smallest working part, such as "one item is created." Who decides: user and owner together.

Create `docs/features/<name>.md` with `Status: draft`. Write the Problem, Goal, users, and first part into it. Show a summary, such as "Goal, users, and first part." Wait for "yes."

### Checkpoint 2: The decided rules

Ask about each kind of rule, in the question format. Include "not sure yet" for each.

- **Who may do what?** Which roles can see, change, or delete things? Who decides: owner.
- **Money and points:** Is there any money, points, or voucher? If not, say so, and skip this question. Who decides: owner.
- **Limits:** Is there any rule about quantity, time, or amount? Who decides: owner.
- **Data:** When something is removed, should it be hidden or deleted for good? Who decides: owner.
- **Errors:** What should the user see when something goes wrong? Who decides: user.

For each answer:

- If the owner decided it, add a row to the **Decided rules** table, with who decided and when.
- If the owner did not decide it, add it to **Open questions**.

Show both lists. Ask: "Is there anything else I should know?" Wait for "yes."

### Checkpoint 3: The slices

Split the feature into slices. A slice is a part that can be built, tested, and shown to the user on its own. Good slices are small and do one thing.

For each slice, write:

- **Name:** a short label, such as "Create a booking."
- **Done when:** one observable result.
- **Acceptance criteria:** six to nine checks that confirm the slice works. If a slice needs ten or more, split it into two slices.
- **Depends on:** other slices or open questions, if any.

Put the slices in order. The first slice should be the smallest one that proves the idea. Show the list. Ask: "Is there anything else I should know?" Wait for "yes."

Write the slices in the **Slices** section of the spec, in order. Write each slice's acceptance criteria under a heading for that slice, in Given / When / Then form. Keep everything in one spec file. Split it only if the spec goes past one page.

### Checkpoint 4: Definition of Ready (before building each slice)

For the slice to be built next, confirm:

- Every rule it uses is in the Decided rules table.
- Every open question it touches is either answered or has a safe behavior (refuse or disable).
- The owning feature is known (see `docs/architecture/features.md`).

Then build that slice only. When it is done, set its status to `shipped` with the date, update `Last updated`, go back to the user, show what works, and ask what to build next.

Review this checklist after each feature. If the same information keeps being missing, add it to the checklist, so the next feature asks for it at the start.

## What the spec must contain

Use `docs/features/_template.md`. For a large feature, the spec must have:

- Status, owner, and last updated date in the header
- Problem, goal, and non-goals
- Users and permissions
- Decided rules table
- Open questions
- Slices, in build order, each with its status
- Acceptance criteria under a heading for each slice, in Given / When / Then form
- Data changes and delete behavior, at the contract level (no code)
- Owner of the spec, and when it is updated (for example, after each slice ships). A spec that nobody keeps up to date drifts from the code.

Status moves in one direction at each step: `draft`, then `confirmed` after the user says "yes," then `shipped` for each slice. If the scope changes after confirmation, set the status back to `draft` until the user confirms again.

## Starter prompts

For a small feature:

> I want to build [your idea]. Do not write code yet. Follow `docs/guides/start-a-feature.md`, small flow. Ask me the five questions in the question format, one message at a time, in plain words. Then write `docs/features/<name>.md` and show it to me. Wait for my "yes."

For a large feature:

> I want to build [your idea]. Do not write code yet. Follow `docs/guides/start-a-feature.md`, large flow. Start with Checkpoint 1. Ask only the questions for the current checkpoint, in the question format. Show me a summary and wait for my "yes" before the next checkpoint.

## Edge cases

- **The user changes their mind mid-feature.** Update the spec first, set the status back to `draft`, show the change, and wait for "yes." Then continue. Do not change code before the spec.
- **The user says "just do it."** Explain in one sentence that the open questions must be answered first, and list them. Offer to build the slices that do not depend on them.
- **The user does not know the answer to a rule.** Write it as an open question, and write the message for the owner. This is a normal outcome, not a failure.
- **The owner gives very little context.** Ask the owner the same questions in the message format. Do not fill the gap by guessing.
- **The feature grows during building.** Stop, update the spec, and confirm with the user before continuing.
