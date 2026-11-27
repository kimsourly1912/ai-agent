# Start a Feature

Use this guide before you build any feature. It is written for people who are not sure how to describe what they want. The agent asks the questions. The user answers in plain words, or with a letter from a list. The agent writes the answers into a spec, and the user confirms the spec before any code is written.

Use the **small** flow for one screen or one action. Use the **large** flow for anything that touches several features, adds tables, handles money, or needs more than one permission.

## Rules for the agent

1. Do not write code until the user confirms the spec with a clear "yes."
2. Ask one checkpoint at a time. Never more than five questions in one message.
3. Use plain words. If you must use a technical term, explain it in one short sentence.
4. Give every question three things: why we ask, the choices, and a "not sure yet" choice. Never ask an open question with no choices.
5. Mark who decides each question: the owner (boss) for product and permission rules, the user for choices about what they want to see first, and the agent for technical details that follow our standards.
6. Accept "not sure yet" as an answer. Write it as an open question. Do not guess.
7. Write the answers into the spec file as you go. Do not wait until the end.
8. Show a short summary at each checkpoint. Wait for a clear "yes" before the next checkpoint.
9. Refuse or skip any part that depends on an open question, and say which part.

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
N. The question, in plain words.
   Why we ask: one sentence on what this decides.
   Who decides: owner, user, or agent.
   a) First choice, in plain words.
   b) Second choice, in plain words.
   c) Not sure yet. We'll leave it open.

Example reply: "1a, 2b, 3c."
```

Add a choice for "something else" only when the list might not cover the answer.

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

Then write `docs/features/<name>.md` from the template. Show it to the user. Wait for "yes."

## Large flow

A large feature is too big to specify in one go. Split it into slices first.

### Checkpoint 1: The first part to build

Ask three questions in the question format:

1. Who writes or changes the data? Who decides: owner.
2. Who can see the data? Who decides: owner.
3. What should work first? Choices: the smallest working part, such as "one item is created." Who decides: user and owner together.

Write the answers into the top of the spec. Show a summary, such as "Goal, users, and first part." Wait for "yes."

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
- The owning feature is known (see `docs/architecture/features.md`).

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

> I want to build [your idea]. Do not write code yet. Follow `docs/guides/start-a-feature.md`, small flow. Ask me the five questions in the question format, one message at a time, in plain words. Then write `docs/features/<name>.md` and show it to me. Wait for my "yes."

For a large feature:

> I want to build [your idea]. Do not write code yet. Follow `docs/guides/start-a-feature.md`, large flow. Start with Checkpoint 1. Ask only the questions for the current checkpoint, in the question format. Show me a summary and wait for my "yes" before the next checkpoint.

## Edge cases

- **The user changes their mind mid-feature.** Update the spec first. Show the change. Then continue. Do not change code before the spec.
- **The user says "just do it."** Explain in one sentence that the open questions must be answered first, and list them. Offer to build the slices that do not depend on them.
- **The user does not know the answer to a rule.** Write it as an open question, and write the message for the owner. This is a normal outcome, not a failure.
- **The owner gives very little context.** Ask the owner the same questions in the message format. Do not fill the gap by guessing.
- **The feature grows during building.** Stop, update the spec, and confirm with the user before continuing.
