## Demo on creating a snake game, based on youtube tutorial

### install opencode from https://opencode.ai/download

npm i -g opencode-ai

### Initial file structure

```bash
AGENTS.MD
/tests
/docs
```

### Agents.md content
```bash
A snake game written in html + ts + css

### Coding rules:
- Use npm or node, whatever easier
- Don't write inline documentation
- After every new feature update the /docs folder
- For each new feature implement a unit test in the tests folder
- Before witing code, always plan first
```

### Prompt
```bash
Create a minimal snake game, just basic controls, basic visuals, keep track of scores
```

### Execution

The agent planned it

Agree and prompt 'proceed'

The agent built it

### The agent executed it

```bash
All done. Here's what was built:
File
index.html
style.css
src/engine.ts
src/main.ts
tests/game.test.ts
docs/architecture.md

Run with: npm run dev (dev server) or npx vite build (prod build) or npm test (9/9 passing).

Controls: Arrow keys. Snake starts center, eats red food (+10), grows. Game over on wall or self collision.
```

### Success! The game runs and the tests pass
