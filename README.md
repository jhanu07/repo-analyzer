# Repository Analyzer

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)

A production-grade tool for analyzing GitHub repositories using AI models (Claude, GPT, Gemini) to generate comprehensive build commands, development workflows, and architectural insights. Supports monorepos, multi-language projects, and provides detailed analysis of project structure and build processes.

## 🚀 Features

- **AI-Driven Analysis**: Uses Claude, GPT-4, or Gemini to understand repository structure and generate accurate build commands
- **Multi-Language Support**: Handles JavaScript, Python, Java, Go, Rust, and more
- **Monorepo Compatible**: Detects and handles monorepo patterns with workspace-specific commands
- **Comprehensive Output**: Generates detailed JSON with build commands, setup instructions, and architectural insights
- **CI/CD Ready**: Reusable GitHub Actions workflow for automated analysis and building
- **Smart Tool Installation**: Automatically installs required tools (JDK, Maven, Node.js, npm, etc.) based on analysis


## 📋 Quick Start

### Prerequisites

- Python 3.8+
- At least one AI API key: `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, or `GOOGLE_API_KEY`
- Optional: `GITHUB_TOKEN` for higher GitHub API rate limits

### Installation

```bash
# Clone the repository
git clone https://github.com/bd-Jahnavi/repo-analyzer.git
cd repo-analyzer

# Set up virtual environment
python -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Usage

```bash
# Basic analysis (defaults to claude-sonnet)
python repo_analyzer.py https://github.com/facebook/react

# Specify AI model
python repo_analyzer.py https://github.com/microsoft/vscode gpt-4

# Use custom configuration
python repo_analyzer.py https://github.com/golang/go claude-sonnet config.toml
```

## 🛠️ CI/CD Integration

### Reusable Workflow

The repository includes a reusable GitHub Actions workflow for automated analysis and building:

```yaml
# In your repository's .github/workflows/build_project.yml
name: Build Project

on:
  workflow_dispatch:
    inputs:
      repo_url:
        default: ${{ github.repository }}
      model:
        default: 'sonnet'

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: bd-Jahnavi/repo-analyzer/.github/workflows/analyze_repo.yml@main
        with:
          repo_url: https://github.com/${{ inputs.repo_url }}
          model: ${{ inputs.model }}
        secrets:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}

      - name: Run build
        run: |
          # Automatically extracts and runs the build command from analysis
          # Supports Java, Node.js, Python, Go, Rust, and more
```

### Calling from Another Repository

```yaml
name: Analyze and Build
on: [workflow_dispatch]

jobs:
  analyze-build:
    uses: bd-Jahnavi/repo-analyzer/.github/workflows/analyze_repo.yml@main
    with:
      repo_url: https://github.com/facebook/react
      model: sonnet
    secrets:
      ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
```

## 📊 Output Format

The analyzer generates comprehensive JSON output with the following structure:

```json
{
  "repository_analysis": {
    "primary_technology": "JavaScript",
    "technology_stack": ["Node.js", "React", "Webpack", "TypeScript"],
    "architecture_type": "single-project"
  },
  "build_ecosystem": {
    "package_managers": ["npm"],
    "build_tools": ["webpack"]
  },
  "commands": {
    "environment_setup": "nvm install 18 && nvm use 18",
    "install_dependencies": "npm install",
    "build_production": "npm run build",
    "test_all": "npm test",
    "start_development": "npm start"
  },
  "development_workflow": {
    "setup_steps": ["Clone repo", "Install Node.js", "Run npm install", "Start development server"]
  }
}
```

## 🔧 Configuration

Create a `config.toml` file to customize analysis behavior:

```toml
# Analysis settings
analysis_depth = "comprehensive"
max_files_to_analyze = 75
max_file_size = 15000

# Command categories to analyze
command_categories = [
    "setup", "install", "build", "test", "dev", "production"
]

# API configuration
[api_base_urls]
claude = "https://api.anthropic.com"
gpt = "https://api.openai.com/v1"

# File filtering
excluded_directories = ["node_modules", ".git", "dist", "build"]
```

## 🏗️ Architecture

```
repo-analyzer/
├── .github/
│   ├── workflows/
│   │   └── analyze_repo.yml      # Reusable GitHub Actions workflow
│   └── scripts/
│       ├── repo_analyzer.py      # Main Python analyzer
│       └── run_repo_analyzer.sh  # Shell script wrapper
├── src/                          # Source code
│   ├── analyzer.py              # Core analysis logic
│   ├── ai_client.py             # AI model integration
│   └── github_api.py            # GitHub API interactions
├── tests/                        # Unit tests
├── docs/                         # Documentation
├── requirements.txt              # Python dependencies
├── config.toml                   # Default configuration
└── README.md                     # This file
```

## 🔍 Supported Technologies

| Language/Framework | Build Tools | Package Managers |
|--------------------|-------------|------------------|
| **JavaScript/TypeScript** | Webpack, Vite, Rollup | npm, yarn |
| **Python** | setuptools, poetry | pip, conda |
| **Java** | Maven, Gradle | Maven, Gradle |
| **Go** | go build | go mod |
| **Rust** | Cargo | Cargo |
| **Ruby** | Rake | Bundler |
| **PHP** | Composer | Composer |

## 🌟 Examples

### Analyze React Repository
```bash
python repo_analyzer.py https://github.com/facebook/react sonnet
# Output: npm install && npm run build
```

### CI/CD for Java Project
```yaml
# .github/workflows/ci.yml
name: CI
on: [push]
jobs:
  build:
    uses: bd-Jahnavi/repo-analyzer/.github/workflows/build_project.yml@main
    secrets: inherit
```

### Monorepo Analysis
```bash
python repo_analyzer.py https://github.com/microsoft/vscode claude-sonnet
# Detects multiple packages and generates workspace-specific commands
```

## 🔒 Security

- API keys loaded from environment variables only
- No sensitive data logged or stored
- GitHub token support for authenticated API calls
- Input validation for repository URLs

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Anthropic](https://www.anthropic.com/) for Claude AI models
- [OpenAI](https://openai.com/) for GPT models
- [Google Cloud](https://cloud.google.com/) for Gemini models
- [GitHub API](https://docs.github.com/en/rest) for repository access

## 📞 Support

- **Issues**: [GitHub Issues](https://github.com/bd-Jahnavi/repo-analyzer/issues)
- **Discussions**: [GitHub Discussions](https://github.com/bd-Jahnavi/repo-analyzer/discussions)
- **Email**: thadiparthijahnavi@gmail.com

---

⭐ **If you found this useful, please give it a star!** ⭐
