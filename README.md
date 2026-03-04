# DFDS Copilot Agents

Standardized GitHub Copilot Agent definitions and prompt patterns for DFDS engineering teams. This repository provides production-ready agent configurations designed to accelerate development while maintaining our engineering excellence standards.

## Agents vs Playbooks

This repository contains two complementary types of resources:

| | Agents | Playbooks |
|---|---|---|
| **What** | Persona-based Copilot configurations | Reusable operational templates |
| **Examples** | C#, Next.js, Python experts | CI/CD pipelines, infra, deployment prompts |
| **Where** | `/agents` → copy to `.github/agents/` | `/playbooks` → use as prompt or workflow template |
| **Purpose** | Shape *how* Copilot thinks and responds | Provide ready-made *outputs* to copy and adapt |

- **Agents** give GitHub Copilot a role, standards, and constraints so it generates code aligned with DFDS engineering practices.
- **Playbooks** are ready-to-use operational templates (e.g. GitHub Actions workflows) that you copy into your project and customise.

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

## How to Use Copilot Agents

GitHub Copilot Agents are custom AI assistants that understand your specific technology stack and follow your organization's standards. To use them in your project:

### 1. Create the `.github/agents` folder

In your project repository, create a `.github/agents` directory:

### 2. Copy relevant agent definitions

Copy the agent definitions you need from this repository's `/agents` folder to your project's `.github/agents` folder:

**For all projects**, start with the base agent:
```bash
# Base DFDS standards (recommended for all projects)
cp path/to/dfds-copilot-agents/agents/DFDS.agent.md .github/agents/
```

Then add language-specific agents as needed:
```bash
# For C#/.NET projects
cp path/to/dfds-copilot-agents/agents/CSharpExpert.DFDS.agent.md .github/agents/

# For Next.js/React projects
cp path/to/dfds-copilot-agents/agents/NextJsExpert.DFDS.agent.md .github/agents/

# For TanStack Start projects (full-stack React + SQLite)
cp path/to/dfds-copilot-agents/agents/TanStackExpert.DFDS.agent.md .github/agents/

# For Python projects
cp path/to/dfds-copilot-agents/agents/PythonExpert.DFDS.agent.md .github/agents/
```

**Note**: Language-specific agents automatically extend the base DFDS agent using the `@DFDS.agent.md` reference, so you get both company-wide and technology-specific standards.

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

## Security — CodeQL Analysis

This repository uses [GitHub CodeQL](https://codeql.github.com/) for automated security analysis. CodeQL is GitHub's static-analysis engine that treats code as data: it compiles the source into a queryable database and then runs hundreds of security and quality queries against it to surface vulnerabilities before they reach production.

### What CodeQL does

- Detects common vulnerability classes such as SQL injection, cross-site scripting (XSS), path traversal, insecure deserialization, and more (aligned with OWASP Top 10).
- Runs data-flow and taint-tracking analyses that follow untrusted input all the way to a dangerous sink — catching issues that simpler pattern-matching tools miss.
- Reports findings directly in the repository's **Security → Code scanning alerts** tab, and as annotations on Pull Requests.

### Prerequisites — enabling Code Scanning

Before the CodeQL workflow can upload results, **Code Scanning must be enabled** in the repository settings:

1. Go to **Settings → Security → Code security and analysis**.
2. Under **Code scanning**, click **Set up** (or **Enable**).
3. Choose **Set up with a workflow** if you are bringing your own workflow file (as this repository does).

> **Note**: Only repository admins can enable Code Scanning. Without this step the `analyze` step will fail with:
> *"Code scanning is not enabled for this repository. Please enable code scanning in the repository settings."*

### How we use it

The workflow is defined in [`.github/workflows/security-codeql.yml`](.github/workflows/security-codeql.yml) and runs:

| Trigger | When |
|---------|------|
| **Push** | On every push to `main` / `master` |
| **Pull Request** | On every PR targeting `main` / `master` |
| **Schedule** | Every Monday at 03:00 UTC |

Analyzed languages:

| Language | Build mode |
|----------|-----------|
| C# | `autobuild` |
| JavaScript / TypeScript | `none` (no build required) |
| Python | `none` (no build required) |
| Java / Kotlin | `autobuild` |
| Go | `autobuild` |
| Ruby | `none` (no build required) |

### Acting on alerts

1. Open **Security → Code scanning alerts** in the repository.
2. Review the alert details, including the data-flow path CodeQL traced.
3. Fix the vulnerability in your code (or dismiss it with a justification if it is a false positive).
4. The alert will automatically close once a fixed commit is pushed and the analysis re-runs.

### Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| *"Code scanning is not enabled for this repository"* | Code Scanning is disabled in repo settings | Enable it under **Settings → Security → Code security and analysis** (admin required) |
| *"This run of the CodeQL Action does not have permission to access the CodeQL Action API endpoints"* | Workflow running on a PR from a **fork** — fork PRs cannot write to the Code Scanning API | Expected behaviour; the workflow skips SARIF upload automatically for fork PRs. Results are still analysed locally. Merge the PR and the next push/schedule run will upload results. |
| *"Please ensure the workflow has at least the 'security-events: read' permission"* | Missing permission in the workflow | The workflow already declares `security-events: write`. If you see this on a non-fork PR, confirm Code Scanning is enabled in repository settings. |

## Available Agents

### Base Agent
| Agent | Purpose |
|-------|---------|
| [DFDS.agent.md](./agents/DFDS.agent.md) | Base standards for all DFDS engineering (security, logging, testing, cloud-native) |

### Language-Specific Agents
All language-specific agents extend the base DFDS agent and add technology-specific best practices.

| Agent | Technology | Purpose |
|-------|-----------|---------|
| [CSharpExpert.DFDS.agent.md](./agents/CSharpExpert.DFDS.agent.md) | C#/.NET | Backend APIs, microservices |
| [NextJsExpert.DFDS.agent.md](./agents/NextJsExpert.DFDS.agent.md) | Next.js/React | Frontend applications, SSR, API routes — includes DFDS Navigator/shadcn setup |
| [TanStackExpert.DFDS.agent.md](./agents/TanStackExpert.DFDS.agent.md) | TanStack Start | Full-stack React + SQLite, DFDS design system, hackathons |
| [PythonExpert.DFDS.agent.md](./agents/PythonExpert.DFDS.agent.md) | Python | Data pipelines, automation, cloud infrastructure |

## Skills

Skills are domain-specific knowledge files that agents load on demand. They cover topics that apply across multiple agents — avoiding duplication in each agent definition.

| Skill | When to use |
|---|---|
| [dfds-navigator-ui](./skills/dfds-navigator-ui/SKILL.md) | Add the DFDS header, Navigator tokens, and shadcn/ui with DFDS colours to any React project |
| [dfds-shadcn-theme](./skills/dfds-shadcn-theme/SKILL.md) | Complete shadcn/ui DFDS theme — CSS variables, Tailwind v4 `@theme inline` bridge, and CVI form overrides |
| [dfds-npmrc-setup](./skills/dfds-npmrc-setup/SKILL.md) | Authenticate with the `@dfds-frontend` GitHub Packages registry (local, Docker, CI) |

Both the Next.js and TanStack Start agents reference these skills directly.

## Playbooks

Playbooks are reusable operational templates that you copy into your project and customise. Unlike agents (which shape Copilot's behaviour), playbooks are concrete, ready-to-use outputs.

### DevOps

| Playbook | Purpose |
|----------|---------|
| [github-actions-ecr-push.md](./playbooks/devops/github-actions-ecr-push.md) | GitHub Actions workflow — build & push Docker image to AWS ECR |

### How to use a playbook

1. Open the relevant playbook file.
2. Copy the workflow / template it contains into your project.
3. Replace the placeholder values (clearly marked as `ChangeThisTo…`) with your actual configuration.
4. Follow the **Setup Instructions** section in the playbook for any additional steps (e.g. adding GitHub Secrets).

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

### Adding New Playbooks

1. **Fork this repository** and create a feature branch
2. **Create a new playbook** under `/playbooks/<category>/` (e.g. `/playbooks/devops/`) as a Markdown file
3. **Ensure your playbook includes**:
   - A ready-to-use template (workflow YAML, script, etc.) with clearly marked placeholder values
   - A **Setup Instructions** section explaining how to adapt and use it
   - A **Notes** section covering key design decisions and security considerations
4. **Register it** in the Playbooks table in `README.md`
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
