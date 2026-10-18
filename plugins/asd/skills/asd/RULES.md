# STE writing rules (summary)

This file is a summary of the ASD-STE100 writing rules, Issue 9. The section numbers follow the order of the standard. The text is a paraphrase, not the official wording. For the official rules, get the standard from https://www.asd-ste100.org.

## 1. Words

- **1.1** Use approved words only in their approved meaning and approved part of speech. Example: "close" is a verb ("Close the valve."). Do not use it as an adjective meaning "near".
- **1.2** You can use **technical names** (names of parts, tools, software, commands, units) and **technical verbs** (verbs for manufacturing or computing actions, for example "compile", "deploy", "merge") that are not in the dictionary. Use them only in their technical meaning.
- **1.3** Use one word for one meaning. Do not use synonyms for variety. If you call a thing "server" once, do not call it "host" or "machine" later.
- **1.4** Use only these verb forms:
  - infinitive ("to install")
  - imperative ("Install")
  - simple present ("installs")
  - simple past ("installed")
  - simple future ("will install")
  - past participle as an adjective only ("the installed package")
- **1.5** Do not use the "-ing" form of a verb, except in technical names ("logging level", "load balancing").
- **1.6** Do not use helping verbs to make complex tenses ("has been installed", "should have been").
- **1.7** Use "can" for ability and "must" for an obligation. Do not use "may", "might", "should", or "could" for instructions.

## 2. Noun clusters

- **2.1** Do not make a noun cluster of more than three words.
- **2.2** Break up a long cluster: "database connection pool timeout value" → "the timeout value of the database connection pool".
- **2.3** If a long technical name is necessary, define it once, then use a short form.

## 3. Verbs and voice

- **3.1** In procedures, use only the active voice.
- **3.2** In descriptions, use the active voice as much as possible. Use the passive voice only if the agent is not known or not important.
- **3.3** Use an action verb, not a noun made from a verb. "Do a restart of the service" → "Restart the service."
- **3.4** Do not use phrasal verbs if a single verb exists: "set up" → "install" or "configure", "find out" → "find" or "learn", "carry out" → "do", "look at" → "examine".

## 4. Sentences

- **4.1** Write one topic in each sentence.
- **4.2** Procedural sentences: maximum 20 words.
- **4.3** Descriptive sentences: maximum 25 words.
- **4.4** Use articles ("the", "a", "an") and demonstratives ("this", "these") before nouns. Do not use telegraphic style.
- **4.5** Use vertical lists to make complex text easier to read.
- **4.6** Use connecting words ("then", "but", "if", "because", "thus") to show the relation between sentences.
- **4.7** Do not use "it" or "this" if the reader cannot find the noun easily. Repeat the noun.

## 5. Procedures

- **5.1** Write one instruction in each sentence, unless two actions occur at the same time ("Hold the button and turn the dial.").
- **5.2** Use the imperative. Start the step with the verb.
- **5.3** Put a condition or a location before the instruction: "If the build fails, examine the log." "On the remote computer, set the variable."
- **5.4** Put a note or explanation in a separate sentence after the instruction. Do not put it inside the instruction.
- **5.5** Write the result of a step only if the reader must verify it: "Run `tailscale status`. The status shows `active`."

## 6. Descriptions

- **6.1** Start each paragraph with a topic sentence.
- **6.2** Write maximum six sentences in each paragraph.
- **6.3** Give the most important information first.
- **6.4** Use headings and lists to separate subjects.

## 7. Safety instructions

- **7.1** Use **WARNING** for a risk of injury, loss of data, or security exposure. Use **CAUTION** for a risk of damage to equipment or systems.
- **7.2** Start the instruction with a clear, simple command: "Do not expose port 8000 to the internet."
- **7.3** Then give the risk or the reason: "There is no authentication. Any person who finds the address can use the server."
- **7.4** Put the warning before the step that it applies to.

## 8. Punctuation and word count

- **8.1** Use the colon only before a list or an example.
- **8.2** Do not use semicolons.
- **8.3** Use parentheses only for references, codes, and short identifiers. Do not put a full sentence in parentheses.
- **8.4** For word count, a number, a technical name, a code string, or a hyphenated word counts as one word.

## 9. Writing practice

- **9.1** Use the same sentence structure for the same type of information.
- **9.2** Use units and numbers that the reader knows. Write numbers as digits in procedures.
- **9.3** Do not use slang, idioms, or jargon that is not a technical name.

## Common word replacements

This short list is a guide. It does not replace the official dictionary.

| Do not use | Use |
|------------|-----|
| utilize, employ | use |
| perform, carry out, execute (not code) | do |
| commence, initiate | start, begin |
| terminate | stop, end |
| ensure | make sure |
| verify, validate | make sure, examine |
| obtain, acquire | get |
| require | need, must |
| sufficient | enough |
| approximately | about, approximately (number only) |
| prior to | before |
| subsequent to | after |
| in order to | to |
| due to | because of |
| a number of | some, many (or the number) |
| at this point in time | now |
| it is necessary to | you must |
| may (permission or possibility) | can |
| should (instruction) | must, or write the imperative |
| leverage | use |
| facilitate | help, make easier |
| issue (problem) | problem |
