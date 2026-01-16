# AI Development Team 🤖

This repository contains the configuration for a complete, autonomous AI software development team in Google Antigravity. This team consists of **12 specialized agents** and strict workflows to build high-quality software from idea to production.

Note that the architecture pieces (Github, Netlify, Supabase, etc.) may need adjustment or additions for your infrastructure.

## Team Roster

### Base Agents
| Agent | Role | Focus |
|-------|------|-------|
| **Product Manager** | Requirements | PRDs, User Stories, Features |
| **Data Analyst** | Analytics | Metrics, KPI Definition, Dashboards |
| **Technical Architect** | System Design | Architecture, Tech Stack, Security |
| **Database Admin** | Data Layer | Schema, SQL, RLS Policies |
| **Security Engineer** | Security | Audits, Threat Modeling, Pen Testing |
| **UX/UI Designer** | Design | Design Systems, Component Specs |
| **Infrastructure** | Environment | Supabase/Netlify Setup |
| **Engineering Manager**| Planning | Implementation Plans, Work Slicing |
| **Developer** | Implementation | Coding, Testing, Refactoring |
| **QA Engineer** | Verification | Testing, Bug Reporting |
| **Technical Writer** | Documentation | Guides, API Docs, READMEs |
| **DevOps** | Deployment | CI/CD, Load Testing, Monitoring |

### Specialized Teams
| Specialization | Command | Focus |
|----------------|---------|-------|
| **SaaS Team** | `/start-saas-project` | Multi-tenancy, RLS, Subscriptions |
| **Shopify Team** | `/start-shopify-project` | App Ecosystem, Billing API |
| **Mobile Team** | `/start-mobile-project` | React Native, Expo, Offline-first |
| **Chrome Ext.** | `/start-chrome-extension-project` | Manifest v3, Content Scripts |
| **AI App Team** | `/start-ai-app-project` | RAG, Vector DBs, LLM Integration |
| **E-commerce** | `/start-ecommerce-project` | Cart, Checkout, GMV Optimization |

## Configuration

To ensure the agents function correctly, you must configure the environment:

1.  **Environment Variables**:
    Copy the provided template and fill in your keys:
    ```bash
    cp .env.template .env
    ```
    *   `GITHUB_TOKEN`: Required for all agents to read/write code and issues.
    *   `PROJECT_TYPE`: Critical for specialization (e.g., set to `saas` to load SaaS context).
    *   `SUPABASE_URL` / `NETLIFY_...`: Required for DBA, Architect, and DevOps agents.

2.  **MCP Servers (Tool Dependencies)**:
    Ensure your Antigravity instance has access to these tools (or equivalent MCP servers):
    *   `filesystem` (Built-in)
    *   `github` (Built-in or MCP)
    *   `supabase` (MCP recommended for DBA/DevOps)
    *   `netlify` (MCP recommended for DevOps)

## Installation

### Option 1: Quick Start (Installer Script)
If you just want to add the team to your existing project:

1.  Download `install.sh` from the releases page (or this repo).
2.  Run it in your project root:
    ```bash
    chmod +x install.sh
    ./install.sh
    ```

### Option 2: Clone & Customize (Recommended)
If you want to maintain your own version of the team:

1.  Clone this repository:
    ```bash
    git clone https://github.com/your-username/ai-dev-team.git
    cd ai-dev-team
    ```
2.  Customize the agents in `agents/configs` or `agents/prompts`.
3.  (Optional) Regenerate the installer script if you want to distribute your version:
    ```bash
    python3 generate_installer.py
    ```
4.  Copy the `agents/` and `.agent/` folders to your actual project workspace.

## Usage

The team is operated via slash commands in the chat.

### New Projects
*   **Standard Project**: `/start-project` (Follows the full 12-agent waterfall)
*   **Specialized Projects**: Use the specific commands listed above.

### Existing Codebase (Brownfield)
*   **New Feature**: `/feature-request` (Update docs -> Plan -> Code)
*   **Refactor**: `/refactor` (Audit -> Plan -> Code)
*   **Bug Fix**: `/bug-fix` (Reproduce -> Fix -> Verify)

### Individual Roles
Run any agent individually for specific tasks:
*   `/run-pm` - Requirements
*   `/run-data-analyst` - Metrics & Dashboards
*   `/run-security` - Security Audits
*   `/run-tech-writer` - Documentation
*   `/run-devops` - Deployment & Load Testing
*   ...and all other agents.

## Directory Structure

The setup script creates the following structure:

```
.
├── .agent/
│   └── workflows/       # Workflow definitions (.md)
├── agents/
│   ├── configs/         # Agent JSON configurations
│   ├── prompts/         # System prompts (Markdown)
│   └── specializations/ # Context files for all project types
└── docs/                # Output directory for all artifacts
```
