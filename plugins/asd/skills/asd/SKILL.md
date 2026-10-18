---
name: asd
description: "Explain, research, or write in ASD-STE100 Simplified Technical English (STE). Use when the user types /asd, says 'STE', 'ASD-STE100', 'Simplified Technical English', 'simplified English', or asks for an explanation, research report, procedure, README, runbook, or doc that must be clear to non-native English readers or easy to translate. Also use when the user asks to rewrite or check existing text against STE."
---

# ASD-STE100 (Simplified Technical English)

Write all output of this skill in STE. STE is a controlled language from the aerospace industry (ASD-STE100, Issue 9, January 2025). It has two parts: writing rules and a controlled dictionary. The goal is one meaning per word and one idea per sentence, so that every reader understands the text the same way.

The full rules are in [RULES.md](RULES.md). Read that file before you write.

## Step 1: Select the mode

Parse the request. Select one mode:

| Mode | Trigger | Output |
|------|---------|--------|
| **Explain** | "/asd explain X", "explain X in STE", a question | An STE explanation of a topic, code, error, or concept |
| **Research** | "/asd research X", "find out", "compare", "what is the latest" | An STE report built from sources, with citations |
| **Write** | "/asd write", "rewrite in STE", "convert this doc", "check this against STE" | New or rewritten text in STE, plus a list of changes |

If the request has no mode word, use **Explain** for a question, **Write** for text the user supplies, and **Research** for a topic that needs current facts.

## Step 2: Get the facts

Do not write until you know the facts. STE makes text clear. It does not make wrong text correct.

- **Explain:** Read the code, file, or error. Find the root cause and the purpose.
- **Research:** Search the web or the codebase. Use more than one source when possible. Record each source URL. Write down which facts are claims by a vendor and which facts are verified.
- **Write:** Read all of the source text. Identify each instruction, each description, and each warning before you change words.

## Step 3: Identify the text type

STE has different rules for each text type. Mark each part of the output as one of these:

- **Procedure** — the reader does tasks (steps, setup, runbook). Maximum 20 words in each sentence. Use the imperative ("Install the package.").
- **Description** — the reader gets information (concept, architecture, report). Maximum 25 words in each sentence. Maximum six sentences in each paragraph.
- **Safety instruction** — a warning (risk of injury, data loss, security exposure) or a caution (risk of damage to equipment or systems). Start with a clear command. Then give the risk.

## Step 4: Write the output

Apply every rule in [RULES.md](RULES.md). The rules that have the most effect are:

1. Use one word for one meaning. Use the same word for the same thing in all of the text.
2. Use the simple form of verbs: imperative, simple present, simple past, simple future. Do not use "-ing" forms as verbs.
3. Use the active voice. In procedures, always use the active voice.
4. Write one instruction in each step. If two actions occur at the same time, you can put them in one step.
5. Put a condition before the instruction: "If the server stops, restart it."
6. Do not use noun clusters of more than three words. Break them up with "of", "for", or a relative clause.
7. Use "the", "a", "this", or "these" before nouns. Do not omit articles.
8. Use vertical lists for steps, options, and parts.
9. Do not use phrasal verbs ("set up", "carry out", "look into") when a single verb exists ("install", "do", "examine").

Code, commands, file paths, product names, and error text are technical names. Keep them exactly as they are, in code format. Do not simplify them.

### Mode-specific format

**Explain**

1. Write one sentence that says what the subject is.
2. Write one sentence that says why it is important to the reader.
3. Write the details as a description (short paragraphs or a vertical list).
4. If the reader must do something, write a procedure with numbered steps.

**Research**

1. Write a summary of three sentences or fewer.
2. Write the findings. Use one heading for each subject.
3. For each fact that comes from a vendor or from one source only, say so: "The vendor states that...".
4. Write a recommendation if the user asked for a decision.
5. Write a "Sources" list with a markdown link for each source.

**Write**

1. Give the STE text.
2. Give a short table of the important changes: original text, STE text, rule.
3. If the source text has ambiguous meaning, do not guess. List the ambiguous text and ask the user.

## Step 5: Check the output

Before you send the output, do these checks:

- [ ] Count the words in each long sentence. Procedures: 20 or fewer. Descriptions: 25 or fewer. (Technical names, numbers, and code count as one word each.)
- [ ] Find all words that end in "-ing". Change each one that is a verb.
- [ ] Find all passive verbs ("is done", "was installed"). Change each one in a procedure to the active voice.
- [ ] Find all words with more than one meaning in your text ("check", "run", "set"). Make sure that each one has one meaning in all of the text.
- [ ] Make sure that each warning starts with a command.
- [ ] Make sure that code, commands, and paths are not changed.

## Limits

- The official STE dictionary has approximately 900 approved words, and it is copyright of ASD. This skill does not include the dictionary. The word list in [RULES.md](RULES.md) is a short guide only. The result is close to STE. It is not certified STE.
- A user can get the official standard free of charge from https://www.asd-ste100.org.
- STE is for technical text. If the user asks for marketing, fiction, or casual text, tell the user that STE is not a good fit, then do the task if they confirm.
