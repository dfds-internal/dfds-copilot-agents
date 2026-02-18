# DFDS Copilot Agents

Standardized GitHub Copilot Agent definitions and prompt patterns for DFDS engineering teams. This repository provides production-ready agent configurations designed to accelerate development while maintaining our engineering excellence standards.

## Purpose

This repository is an **internal engineering enablement library** that standardizes how DFDS teams leverage GitHub Copilot Agents across different technology stacks. It's not an application—it's a collection of best-practice agent definitions and usage patterns that help teams:

- Build production-ready code faster
- Maintain consistent engineering standards
- Embrace secure-by-default development practices
- Accelerate hackathons and tribe events while keeping quality high

## Target Audience

This library is designed for DFDS engineers working across our technology ecosystem:

- **Backend Engineers**: Building APIs and services with C#/.NET
- **Frontend Engineers**: Creating modern web applications with Next.js and React
- **Python Engineers**: Developing data pipelines, automation, and cloud infrastructure
- **Cloud Engineers**: All teams building cloud-native solutions on AWS/Azure

## How to Use Copilot Agents

GitHub Copilot Agents are custom AI assistants that understand your specific technology stack and follow your organization's standards. To use them in your project:

### 1. Create the `.github/agents` folder

In your project repository, create a `.github/agents` directory:

```bash
mkdir -p .github/agents
```

### 2. Copy relevant agent definitions

Copy the agent definitions you need from this repository's `/agents` folder to your project's `.github/agents` folder:

```bash
# Example: Adding the C# expert to your project
cp agents/CSharpExpert.DFDS.agent.md your-project/.github/agents/
```

### 3. Use the agent in GitHub Copilot

Once configured, you can invoke the agent in:
- **GitHub Copilot Chat** (VS Code, Visual Studio, or GitHub.com)
- **Pull Request reviews** with `@copilot` mentions
- **Code generation workflows**

For detailed instructions, see [Agents.md](./Agents.md).

## Engineering Principles

All agent definitions in this repository are built around DFDS engineering principles:

### 🔒 Secure by Default
- Security is not optional—it's built in from the start
- Secrets management, input validation, and least privilege access
- OWASP Top 10 awareness in all code generation

### ☁️ Cloud-Native Architecture
- Design for AWS/Azure from day one
- Containerization, orchestration, and serverless patterns
- Observability, resilience, and cost optimization

### 🏭 Production-Ready Mindset
- Code must be maintainable, testable, and observable
- Structured logging with correlation IDs
- Health checks, metrics, and distributed tracing
- Graceful degradation and error handling

### 📊 Testing Discipline
- Unit tests are non-negotiable
- Integration tests for critical paths
- Test-driven development when appropriate

### 🚀 Modern Best Practices
- Follow language-specific idioms and patterns
- Leverage framework capabilities fully
- Keep dependencies up-to-date and minimal

## Available Agents

| Agent | Technology | Purpose |
|-------|-----------|---------|
| [CSharpExpert.DFDS.agent.md](./agents/CSharpExpert.DFDS.agent.md) | C#/.NET | Backend APIs, microservices, Azure Functions |
| [NextJsExpert.DFDS.agent.md](./agents/NextJsExpert.DFDS.agent.md) | Next.js/React | Frontend applications, SSR, API routes |
| [PythonExpert.DFDS.agent.md](./agents/PythonExpert.DFDS.agent.md) | Python | Data pipelines, automation, cloud infrastructure |

## Examples

Check the [/examples](./examples) folder for practical demonstrations of how to use each agent effectively:

- **[CSharp Examples](./examples/CSharp/ExamplePrompts.md)**: API endpoints, validation, testing, performance optimization
- More examples coming soon for Next.js and Python

## Contributing

We welcome contributions from all DFDS engineering teams! Here's how you can help:

### Adding New Agents

1. **Fork this repository** and create a feature branch
2. **Create a new agent definition** in the `/agents` folder following the naming convention: `[Technology]Expert.DFDS.agent.md`
3. **Ensure your agent includes**:
   - Clear role definition
   - DFDS engineering standards
   - Security requirements
   - Testing expectations
   - Technology-specific best practices
4. **Add usage examples** in the `/examples` folder
5. **Submit a Pull Request** with a clear description

### Improving Existing Agents

- Spotted a gap in coverage? Submit a PR with improvements
- Found a better pattern? Share it with the community
- Have feedback? Open an issue for discussion

### Guidelines

- **Keep it professional**: This is internal documentation representing DFDS engineering
- **Keep it practical**: Focus on real-world scenarios our teams face
- **Keep it current**: Use modern versions and best practices
- **Keep it safe**: Security and quality are non-negotiable

## Getting Help

- **Questions?** Open a GitHub issue
- **Need support?** Reach out to your platform team
- **Want to contribute?** See guidelines above

## License

This repository is for internal DFDS use only. Not licensed for external distribution.

---

**Maintained by DFDS Platform Engineering**  
*Empowering teams to build better software, faster.*
