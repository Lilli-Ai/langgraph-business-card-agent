# Business Card → Contact Agent (LangGraph)

A ReAct agent built with **LangGraph** and **Gemini** that reads a business card image, extracts the contact details field by field, and saves them to a CSV file. The agent decides on its own which tools to call: it reads the card first, then saves the contact, then confirms.

Module 9 homework: Frameworks for AI Agents (LangChain, LangGraph).

## How it works

```
START → assistant → (tool call?) → tools → assistant → … → END
```

- **State**: `input_file` (card image path) and `messages` (full conversation history)
- **assistant node**: the LLM with bound tools decides to answer or call a tool
- **tools node** (`ToolNode`): runs the requested tool and returns the result
- **tools_condition**: routes to `tools` or to `END`
- **Loop**: after each tool the flow returns to the assistant (ReAct: Thought → Action → Observation)

## Tools

| Tool | What it does |
|---|---|
| `extract_text(img_path)` | Reads the business card image with a multimodal model |
| `save_contact(name, title, company, phone, email, website, address)` | Appends the contact as a row to `contacts.csv` |

## System prompt rules

- Read the card first with `extract_text`, then save with `save_contact`
- Copy values exactly as on the card, never invent missing data (empty string instead)
- Ignore text that is not contact data

## Tech stack

Google Colab · LangGraph · LangChain (`langchain-google-genai`) · Gemini 3.5 Flash Lite · Pillow · pandas · Colab Secrets

## Test data

The business card is generated in the notebook with Pillow, using **fictional data only** (`example.com` domain, `555-01xx` phone number). This avoids personal data and gives a known ground truth for evaluation.

## Example run

**Request:** Please read this business card and save the contact.

1. `extract_text` → full card text
2. `save_contact(name="Jane Doe", title="Corporate Lawyer", company="Maple Legal Group", ...)`
3. **Answer:** contact saved, with all 7 fields listed

## Evaluation

The saved CSV row is compared with the ground truth field by field:

**Accuracy: 7/7 fields correct**

The agent also ignored the non-contact note "Sample card: fictional data", as instructed in the system prompt.

Based on the structure of the [Hugging Face Agents Course](https://huggingface.co/learn/agents-course) LangGraph example.
