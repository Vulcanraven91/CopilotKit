<div align="center">
  <a href="https://copilotkit.ai" target="_blank">
    <img src="./assets/banner.png" alt="CopilotKit Logo">
  </a>

  <br/>

  <strong>
    CopilotKit is the open-source framework for integrating powerful AI Copilots into any application. Easily implement custom AI Chatbots, AI Agents, AI Textareas, and more.
  </strong>
</div>

<br/>

<div align="center">
  <a href="https://www.npmjs.com/package/@copilotkit/react-core" target="_blank">
    <img src="https://img.shields.io/npm/v/%40copilotkit%2Freact-core?logo=npm&logoColor=%23FFFFFF&label=Version&color=%236963ff" alt="NPM">
  </a>
  <img src="https://img.shields.io/github/license/copilotkit/copilotkit?color=%236963ff&label=License" alt="MIT">
  <a href="https://discord.gg/6dffbvGU3D" target="_blank">
    <img src="https://img.shields.io/discord/1122926057641742418?logo=discord&logoColor=%23FFFFFF&label=Discord&color=%236963ff" alt="Discord">
  </a>
</div>
<br/>

<div align="center">
  <a href="https://discord.gg/6dffbvGU3D?ref=github_readme" target="_blank">
    <img src="./assets/btn_discord.png" alt="CopilotKit Discord" height="40px">
  </a>
  <a href="https://docs.copilotkit.ai?ref=github_readme" target="_blank">
    <img src="./assets/btn_docs.png" alt="CopilotKit GitHub" height="40px">
  </a>
  <a href="https://cloud.copilotkit.ai?ref=github_readme" target="_blank">
    <img src="./assets/btn_cloud.png" alt="CopilotKit GitHub" height="40px">
  </a>
  
</div>

<br/>
<div align="center">
  <a href="https://www.producthunt.com/posts/copilotkit" target="_blank">
    <img src="https://api.producthunt.com/widgets/embed-image/v1/top-post-badge.svg?post_id=428778&theme=light&period=daily">
  </a>
</div>

<br />

# Demos

<details open>
  <summary>Spreadsheet App</summary>
  
  ![image](https://github.com/user-attachments/assets/9c6ebced-1439-49bb-811b-1f74fe749ab5)

  Check out the repo [here](https://github.com/CopilotKit/demo-presentation)
</details>

<details>
  <summary>Banking App</summary>
  
  ![image](https://github.com/user-attachments/assets/ecddcf4c-cfe3-45ba-848d-03f94902475f)

  Check out the repo [here](https://github.com/CopilotKit/demo-banking)
</details>

<details>
  <summary>Travel App</summary>

  ![image](https://github.com/user-attachments/assets/09dc873b-b263-40a3-8577-1414d0837510)

  Check out the repo [here](https://github.com/CopilotKit/CopilotKit/tree/main/examples/coagents-travel)
</details>

<details>
  <summary>ToDo App</summary>
  
  ![image](https://github.com/user-attachments/assets/7cafb021-98e0-44b5-b3b7-232b0c3ae35b)
  
  Check out the repo [here](https://github.com/CopilotKit/example-todos-app)
</details>

Get started in minutes, not hours!

```ts
useCopilotReadable(.
useCopilotSuggestion(...)
```

```ts
const { state, setState} = useAgent(...)

useAgentStateRender(...)
```

# Getting Started
Check out our [quickstart documentation](https://docs.copilotkit.ai/quickstart)

# Code Samples

### Chat UI
```ts
// Headless UI with full control
const { visibleMessages, appendMessage, setMessages, ... } = useCopilotChat();

// Pre-built components with deep customization options
<CopilotPopup 
  instructions={"You are assisting the user as best as you can. Answer in the best way possible given the data you have."} 
  labels={{ title: "Popup Assistant", initial: "Need any help?" }} 
/>

// ...
```

### RAG (Retrieval-Augmented Generation)
```ts
// Frontend RAG
useCopilotReadable({
  description: "The current user's colleagues",
  value: colleagues,
});

// knowledge-base integration
useCopilotKnowledgebase(myCustomKnowledgeBase)
```

### Actions
```ts
// Frontend actions + generative UI
useCopilotAction({
  name: "appendToSpreadsheet",
  description: "Append rows to the current spreadsheet",
  parameters: [
    { name: "rows", type: "object[]", attributes: [{ name: "cells", type: "object[]", attributes: [{ name: "value", type: "string" }] }] }
  ],
  render: ({ status, args }) => <Spreadsheet data={canonicalSpreadsheetData(args.rows)} />,
  handler: ({ rows }) => setSpreadsheet({ ...spreadsheet, rows: [...spreadsheet.rows, ...canonicalSpreadsheetData(rows)] }),
});

// Note: Backend actions supported via python, typescript (extensible protocol)
```

### Intelligent Suggestions
```ts
// Suggest next steps based on context
const { suggestions } = useCopilotSuggestions(
  {
    instructions: `Autocomplete or modify spreadsheet rows. Selected cell: ${JSON.stringify(activeCell)} (${activeCellData})`,
    value: { rows: spreadsheet.rows.map((row) => ({ cells: row })) },
    enabled: !!activeCell && !spreadsheetIsEmpty,
  },
  [activeCell, spreadsheet]
);
```

# Code Samples (CoAgents)

### State Sharing and Generative UI
```ts
// Share state between app and agent
const { agentState } = useCoAgent({ 
  name: "basic_agent", 
  initialState: { input: "NYC" } 
});

// agentic generative UI
useCoagentStateRender({
  name: "basic_agent",
  render: ({ state }) => <WeatherDisplay {...state.final_response} />,
});

// Human in the Loop (Approval)
useCopilotAction({
    name: "email_tool",
    parameters: [{ name: "email_draft", type: "string", description: "The email content", required: true }],
    renderAndWaitForResponse: ({ args, status, respond }) => (
      <EmailConfirmation
        emailContent={args.email_draft || ""}
        isExecuting={status === "executing"}
        onCancel={() => respond?.({ approved: false })}
        onSend={() => respond?.({ approved: true, metadata: { sentAt: new Date().toISOString() } })}
      />
    ),
  });

// and more!
```

### Emit Intermediate Agent State

LangGraph.js
```ts
// Stream tool-call arguments as agent state
const modifiedConfig = copilotKitCustomizeConfig(config, {
  emitIntermediateState: [{ 
    stateKey: "outline", 
    tool: "set_outline", 
    toolArgument: "outline" 
  }],
});
const response = await ChatOpenAI({ model: "gpt-4o" }).invoke(messages, modifiedConfig);
```

LangGraph Python
```python
# Python equivalent:
modifiedConfig = copilotkit_customize_config(
    config,
    emit_intermediate_state=[{
        "state_key": "outline", 
        "tool": "set_outline", 
        "tool_argument": "outline"
    }]
)
```


## Contributing

Thanks for your interest in contributing to CopilotKit! 💜

We value all contributions, whether it's through code, documentation, creating demo apps, or just spreading the word.

Here are a few useful resources to help you get started:

- For code contributions, [CONTRIBUTING.md](./CONTRIBUTING.md).
- For documentation-related contributions, [check out the documentation contributions guide](https://docs.copilotkit.ai/contributing/docs-contributions?ref=github_readme).

> 💡 **NOTE:** All contributions must be submitted via a pull request and be reviewed by our team. This ensures all contributions are of high quality and align with the project's goals.

## Get in touch

You are invited to join our community on [Discord](https://discord.gg/6dffbvGU3D) and chat with our team and other community members.

## License

This repository's source code is available under the [MIT License](https://github.com/CopilotKit/CopilotKit/blob/main/LICENSE).
