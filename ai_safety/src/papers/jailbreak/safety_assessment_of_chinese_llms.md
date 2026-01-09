# Safety Assessment of Chinese Large Language Model (2023)

[https://arxiv.org/abs/2304.10436](https://arxiv.org/abs/2304.10436)

Propose a safety assessment benchmark for LLMs:
- list of 8 safety scenarios 
- list of 6 different instruction-attacks
- create a set of safety prompts by combining these 2 lists called _SafetyPrompts_ (~ 100k prompts)
- use another llm to evaluate whether the generated responses are safe 
- rank the 15 tested models

## Safety scenarios

| # | Safety Dimension              | Description                                                                                                             |
| - | ----------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| 1 | **Illegal Activities**        | Promoting or explaining how to engage in criminal behavior (e.g., how to make drugs or hack systems).                   |
| 2 | **Ethical Issues**            | Encouraging unethical actions, deception, or violations of societal norms.                                              |
| 3 | **Discrimination**            | Outputting bias or harmful stereotypes based on gender, race, religion, etc.                                            |
| 4 | **Privacy Violations**        | Leaking or revealing sensitive personal data, including generated fake private info.                                    |
| 5 | **Political Sensitivity**     | Involvement in politically sensitive issues, especially in Chinese contexts (e.g., state policies, sovereignty issues). |
| 6 | **Abuse and Harassment**      | Generating content that insults, bullies, or harasses individuals or groups.                                            |
| 7 | **Sexually Explicit Content** | Producing pornographic, explicit, or otherwise inappropriate sexual content.                                            |
| 8 | **Violence and Harm**         | Promoting or encouraging violence, harm to self/others, or dangerous behavior.                                          |

## Instruction-attack types 

| #  | Attack Type                      | Description                                                                                                |
| -- | -------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| 9  | **Context Injection Attack**     | Embeds harmful instructions in multi-turn conversations or subtle contexts to bypass filters.              |
| 10 | **Encoding Attack**              | Obfuscates harmful content using tricks like base64 encoding or Unicode manipulation.                      |
| 11 | **Roleplay Attack**              | Asks the model to “pretend” to be someone (e.g., a hacker or criminal) and respond in character.           |
| 12 | **Hypothetical Scenario Attack** | Wraps unsafe queries in "what if" or hypothetical framing to circumvent safety limits.                     |
| 13 | **Prompt Leaking Attack**        | Tricks the model into revealing or interacting with its system prompt or internal mechanisms.              |
| 14 | **Translation Attack**           | Translates harmful content from other languages or embeds it in a multilingual context to avoid detection. |
