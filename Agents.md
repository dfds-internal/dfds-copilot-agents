# GitHub Copilot Agents Guide

## What are GitHub Copilot Agents?

GitHub Copilot Agents are specialized AI assistants that provide context-aware code suggestions and guidance tailored to specific technologies, frameworks, and organizational standards. Think of them as expert teammates who understand both your technology stack and your company's engineering practices.

Unlike generic AI coding assistants, Copilot Agents can be customized with:
- **Technology expertise**: Deep knowledge of specific languages and frameworks
- **Organizational standards**: Your company's coding conventions and best practices
- **Security policies**: Built-in security awareness and compliance requirements
- **Architecture patterns**: Preferred design patterns and architectural approaches

## How Copilot Agents Work

Copilot Agents are defined using markdown files (`.agent.md`) that contain:

1. **Role Definition**: What the agent specializes in
2. **Context**: Relevant documentation, standards, and patterns
3. **Instructions**: How to approach code generation and suggestions
4. **Constraints**: What to avoid and requirements to follow

When you interact with GitHub Copilot in your IDE or on GitHub.com, these agent definitions guide the AI to provide responses aligned with your organization's standards.

## Setting Up Agents in Your Project

### Step 1: Create the Agents Directory

In your project repository, create a `.github/agents` folder:

```bash
mkdir -p .github/agents
```

> **Important**: The folder must be `.github/agents` (not just `agents`) for GitHub Copilot to recognize it.

### Step 2: Add Agent Definitions

Copy the relevant agent definition files from this repository:

```bash
# For C#/.NET projects
cp path/to/dfds-copilot-agents/agents/CSharpExpert.DFDS.agent.md .github/agents/

# For Next.js/React projects
cp path/to/dfds-copilot-agents/agents/NextJsExpert.DFDS.agent.md .github/agents/

# For Python projects
cp path/to/dfds-copilot-agents/agents/PythonExpert.DFDS.agent.md .github/agents/
```

You can include multiple agents in a single project if you have a polyglot codebase.

### Step 3: Commit the Agents

Add and commit the `.github/agents` folder to your repository:

```bash
git add .github/agents
git commit -m "Add DFDS Copilot Agent definitions"
git push
```

### Step 4: Use the Agents

Once configured, the agents are automatically available in:

#### In Your IDE (VS Code, Visual Studio)
- Open GitHub Copilot Chat
- Start coding or ask questions—the agent context is automatically applied
- The agent will provide suggestions following DFDS standards

#### On GitHub.com
- In Pull Requests, mention `@copilot` to get agent-powered reviews
- In Issues, use `@copilot` for implementation suggestions

#### Example Interactions
```
You: "Create a new API endpoint for user registration"
Agent: [Generates code following DFDS patterns with validation, logging, error handling]

You: "Add unit tests for the registration endpoint"
Agent: [Creates comprehensive tests using xUnit with proper mocking and assertions]

You: "Review this code for security issues"
Agent: [Analyzes code against OWASP Top 10 and DFDS security standards]
```

## Using Agents Effectively

### Best Practices

**Be Specific**
- ❌ "Make this better"
- ✅ "Add input validation and improve error handling for this endpoint"

**Provide Context**
- ❌ "Create a service"
- ✅ "Create a service that fetches user data from DynamoDB with retry logic and structured logging"

**Iterate**
- Ask follow-up questions to refine the output
- Request explanations for suggested patterns
- Ask for alternatives when needed

### Common Use Cases

**Code Generation**
```
"Create a new controller for managing customer orders with CRUD operations"
"Generate a React component for displaying a product list with filtering"
"Write a Python function to process CSV files from S3"
```

**Testing**
```
"Add unit tests for the OrderService class"
"Create integration tests for the payment endpoint"
"Generate test data fixtures for the User entity"
```

**Refactoring**
```
"Refactor this method to use async/await"
"Extract this logic into a separate service class"
"Optimize this database query for better performance"
```

**Security & Compliance**
```
"Review this code for security vulnerabilities"
"Add authentication and authorization to this endpoint"
"Ensure this function handles PII data according to GDPR"
```

## Extending and Customizing Agents

You can customize agents for your specific team or project needs:

### Creating a Custom Agent

1. **Copy an existing agent** as a template
2. **Modify the role definition** to match your specialization
3. **Add your team's standards** and specific patterns
4. **Include examples** relevant to your use cases
5. **Save as `YourExpert.DFDS.agent.md`** in `.github/agents`

### Customization Example

```markdown
# My Team's API Agent

You are an expert in building APIs for the Payment Team at DFDS.

## Standards
- Use Stripe SDK v15+
- All payment operations must be idempotent
- Implement webhook signature verification
- Log all transactions with correlation IDs
...
```

### Contributing Back

If you create a useful agent that could benefit other DFDS teams:
1. Submit a PR to this repository
2. Add documentation and examples
3. Share with the engineering community

## Advanced Features

### Multiple Agents

You can have multiple agents in your project. Copilot will use the most relevant agent based on:
- File type and location
- Context of your question
- Current workspace

### Agent Hierarchies

Agents can reference other agents or build upon them:
- Create a base `DFDS.agent.md` with company-wide standards
- Create specific agents that extend the base (e.g., `CSharpExpert.DFDS.agent.md`)

### Version Control

Keep your agent definitions in Git:
- Track changes over time
- Review updates through PRs
- Maintain different versions for different projects

## Troubleshooting

**Agent not being used**
- Ensure `.github/agents` folder exists (not just `agents`)
- Check that agent files have `.agent.md` extension
- Verify files are committed and pushed to the repository
- Try reloading your IDE window

**Inconsistent suggestions**
- Be more specific in your prompts
- Check that the agent definition is clear and unambiguous
- Ensure there are no conflicting instructions in multiple agents

**Agent not following standards**
- Review the agent definition for clarity
- Make standards explicit rather than implicit
- Provide examples in the agent definition

## Resources

- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [DFDS Engineering Standards](https://github.com/dfds-internal) *(internal)*
- [Agent Examples](https://github.com/dfds-internal/dfds-copilot-agents/tree/main/agents))

## Support

- **Questions?** Open an issue in this repository
- **Bug reports?** File an issue with reproduction steps
- **Feature requests?** Discuss in GitHub Discussions

---

**Keep learning, keep building, keep shipping!** 🚀
