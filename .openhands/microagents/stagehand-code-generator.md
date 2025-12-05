---
name: Stagehand Code Generator
type: knowledge
version: 1.0.0
agent: CodeActAgent
---

# Stagehand Code Generator Microagent

This microagent helps generate Stagehand V3 browser automation code based on natural language descriptions. Stagehand is an AI-powered browser automation framework that provides `act`, `extract`, `observe`, and `agent` methods.

## Core Capabilities

### 1. Basic Stagehand Setup
Generate initialization code for Stagehand instances:

```typescript
import { Stagehand } from "@browserbasehq/stagehand";

const stagehand = new Stagehand({
  env: "LOCAL", // or "BROWSERBASE"
  verbose: 2, // 0, 1, or 2
  model: "openai/gpt-4.1-mini", // or any supported model
});

await stagehand.init();
```

### 2. Browser Context and Page Management
- Access browser context: `stagehand.context`
- Get existing pages: `stagehand.context.pages()[0]`
- Create new pages: `await stagehand.context.newPage()`

### 3. Act Method - Browser Actions
Generate atomic, specific action instructions:
- ✅ Good: "Click the sign in button", "Type 'hello' into the search input"
- ❌ Bad: "Order me pizza", "Type in search bar and hit enter" (multi-step)

```typescript
// Basic action
await stagehand.act("click the sign in button");

// Action on specific page
await stagehand.act("click the sign in button", { page: page2 });
```

### 4. Extract Method - Data Extraction
Generate data extraction code with or without schemas:

```typescript
import { z } from "zod/v3";

// With schema
const data = await stagehand.extract(
  "extract all apartment listings with prices and addresses",
  z.object({
    listings: z.array(
      z.object({
        price: z.string(),
        address: z.string(),
      }),
    ),
  }),
);

// Without schema (returns { extraction: value })
const { extraction } = await stagehand.extract("extract the sign in button text");
```

### 5. Observe Method - Action Planning
Generate observation code to plan actions before execution:

```typescript
// Get candidate actions
const actions = await stagehand.observe("Click the sign in button");
// Execute the first action
await stagehand.act(actions[0]);
```

### 6. Agent Method - Autonomous Execution
Generate agent code for complex, multi-step tasks:

```typescript
// Basic agent
const agent = stagehand.agent({
  model: "google/gemini-2.0-flash",
  executionModel: "google/gemini-2.0-flash",
});

const result = await agent.execute({
  instruction: "Search for the stock price of NVDA",
  maxSteps: 20,
});

// Computer Use Agent (CUA)
const agent = stagehand.agent({
  cua: true,
  model: "anthropic/claude-sonnet-4-20250514",
  systemPrompt: "You are a helpful assistant that can use a web browser.",
});
```

## Code Generation Guidelines

When generating Stagehand code:

1. **Always import Stagehand**: Start with proper imports
2. **Initialize properly**: Include stagehand initialization with appropriate config
3. **Use atomic actions**: Break complex tasks into simple, specific actions
4. **Handle pages correctly**: Use page targeting when working with multiple pages
5. **Include error handling**: Add try-catch blocks for robust automation
6. **Use observe + act pattern**: For better reliability, observe before acting
7. **Schema validation**: Use Zod schemas for structured data extraction
8. **Proper typing**: Include TypeScript types when applicable

## Common Patterns

### Navigation and Form Filling
```typescript
const page = stagehand.context.pages()[0];
await page.goto("https://example.com");

await stagehand.act("click the login button");
await stagehand.act("type 'username' into the email field");
await stagehand.act("type 'password' into the password field");
await stagehand.act("click the submit button");
```

### Data Scraping
```typescript
const data = await stagehand.extract(
  "extract all product information",
  z.object({
    products: z.array(
      z.object({
        name: z.string(),
        price: z.string(),
        description: z.string(),
        url: z.string().url(),
      }),
    ),
  }),
);
```

### Multi-step Automation
```typescript
const agent = stagehand.agent({
  model: "google/gemini-2.0-flash",
});

await agent.execute({
  instruction: "Find and book the cheapest flight from NYC to LA for next week",
  maxSteps: 30,
});
```

## Best Practices

1. **Use specific selectors**: When targeting elements, be as specific as possible
2. **Handle dynamic content**: Use observe to handle changing DOM elements
3. **Page management**: Properly manage multiple pages and contexts
4. **Error handling**: Always include proper error handling and timeouts
5. **Model selection**: Choose appropriate models for different tasks
6. **Verbose logging**: Use verbose mode for debugging complex automations

## Limitations

- Actions should be atomic and specific
- Complex multi-step instructions should use the agent method
- Always handle potential failures and timeouts
- Be mindful of rate limits and website policies
- Test automations thoroughly before production use

This microagent helps translate natural language requirements into proper Stagehand V3 automation code, following best practices and patterns for reliable browser automation.