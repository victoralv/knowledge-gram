---
name: knowledge-gram
description: An "Instagram of Knowledge" skill. Users ask for knowledge about a specific topic, and the skill displays a scrollable feed of knowledge posts (facts or quizzes) in a webview UI. Supports static (facts) and interactive (quiz) modes.
metadata:
  homepage: https://github.com/victoralv/knowledge-gram
---

# Knowledge-Gram

An "Instagram of Knowledge" skill. Users ask for knowledge about a specific topic, and the skill displays a scrolling app (like Instagram) with knowledge posts. Supports static (facts) and interactive (quiz) modes.

## Examples
- "Show me 10 facts about quantum physics."
- "Quiz me on world history."
- "Teach me about black holes."

## Instructions

1. Extract topic, mode ("st" for facts, "in" for quiz), and number of posts (if specified) from user prompt.
	- If user requests a number of questions/facts, use that number (max 20 recommended). If not specified, default to 10.
2. Generate the requested number of posts:
	- **Be creative and avoid trivial, obvious, or repeated questions/facts.**
	- Each post must be unique, non-redundant, and not a simple rewording of another.
	- Do not include questions/facts that are extremely basic or well-known (e.g., "What is 2+2?", "The sky is blue.", "Is the sun a star?").
	- Cover a diverse range of subtopics or interesting angles within the main topic.
	- **All answers, facts, and justifications must be accurate and verifiable. Do not hallucinate or invent information. Double-check that the correct answer (`co`) and all facts are true.**
	- Static: Each post has `tx` (fact) and `ju` (explanation).
	- Interactive: Each post has `tx` (question), `op` (array of 2–4 strings), `co` (int, 0-based index), and `ju`.


3. Output a **strict, valid JSON object** (no trailing commas, all keys/strings in double quotes) for `run_js` (index.html):

{
	"ac": "pl",
	"ty": "st"|"in",
	"kp": [ ...N objects... ]
}

// N objects (N = number requested by user, or 10 if not specified):
// Static: { "tx": string, "ju": string }
// Interactive: { "tx": string, "op": [2–4 strings], "co": int, "ju": string }

**Important:**
- For interactive, `op` must be an array of 2–4 strings. `co` must be a valid 0-based index for `op`.
- Escape double quotes `"` inside any string value as `\\"` (e.g., in questions or options containing quotes).
- Always close all arrays `[]` and objects `{}` properly. Do not leave any array or object unclosed in the output.

**Note:**
Malformed JSON (missing closing brackets or braces) will not work. Double-check that every array and object is properly closed.

Example:
Input:  What's the capital of France?
Output: { "tx": "What's the capital of France?", ... }


Rules:
- For static, omit `op` and `co`.
- For interactive, include both. `op` must have 2–4 options, `co` must be a valid index.
- All posts need `ju`.

Example static:
{
	"ac": "pl",
	"ty": "st",
	"kp": [
		{ "tx": "The speed of light in vacuum is approximately 299,792 km/s.", "ju": "This is a fundamental constant in physics." }
		/*...9 more*/
	]
}


Example interactive (2 options):
{
	"ac": "pl",
	"ty": "in",
	"kp": [
		{ "tx": "Is the sun a star?", "op": ["Yes", "No"], "co": 0, "ju": "The sun is a star." },
    { "tx": "Is Mars a star?", "op": ["Yes", "No"], "co": 0, "ju": "Mars is a planet, not a star." },
		/*...8 more*/
	]
}

Example interactive (3 options):
{
	"ac": "pl",
	"ty": "in",
	"kp": [
		{ "tx": "Who developed the theory of relativity?", "op": ["Isaac Newton", "Albert Einstein", "Niels Bohr"], "co": 1, "ju": "Albert Einstein developed the theory of relativity." },
		/*...9 more*/
	]
}

Example interactive (4 options):
{
	"ac": "pl",
	"ty": "in",
	"kp": [
		{ "tx": "Which planet is known as the Red Planet?", "op": ["Earth", "Venus", "Mars", "Jupiter"], "co": 2, "ju": "Mars is called the Red Planet." },
		/*...9 more*/
	]
}

---

**Final parameter mapping:**

- ac: action (always "pl")
- ty: type ("st" for static, "in" for interactive)
- kp: knowledge_posts (array)
- tx: text (fact or question)
- ju: justification
- op: options (array of 2–4 strings, only for interactive)
- co: correct_option (int, only for interactive)

