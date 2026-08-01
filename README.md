# CodePilot AI – Intelligent Pull Request Reviewer

CodePilot AI is a GitHub Action that automatically reviews pull requests using Google's Gemini AI, providing intelligent feedback to improve code quality, maintainability, security, and performance.

## ✨ Features

- 🤖 Automated pull request reviews using Gemini AI
- 💡 AI-generated code suggestions and improvements
- 🔍 Detects potential bugs and code quality issues
- 🔒 Highlights security concerns
- ⚡ Recommends performance optimizations
- 📌 Posts review comments directly on GitHub Pull Requests

# Setup

## 1. Generate a Gemini API Key

Create a Gemini API key from **Google AI Studio**.

## 2. Configure GitHub Secrets

Add the following secret to your GitHub repository:

```
GEMINI_API_KEY
```

For more information, refer to the GitHub Secrets documentation.

## 3. Create the Workflow

Create the following file:

```
.github/workflows/code-review.yml
```

Paste the following workflow configuration:

```yaml
name: CodePilot AI Review

on:
  issue_comment:
    types: [created]

permissions: write-all

jobs:
  codepilot-review:
    runs-on: ubuntu-latest
    if: |
      github.event.issue.pull_request &&
      contains(github.event.comment.body, '/gemini-review')

    steps:
      - name: PR Info
        run: |
          echo "Comment: ${{ github.event.comment.body }}"
          echo "Issue Number: ${{ github.event.issue.number }}"
          echo "Repository: ${{ github.repository }}"

      - name: Checkout Repository
        uses: actions/checkout@v3
        with:
          fetch-depth: 0

      - name: Get Pull Request Details
        id: pr
        run: |
          PR_JSON=$(gh api repos/${{ github.repository }}/pulls/${{ github.event.issue.number }})
          echo "head_sha=$(echo $PR_JSON | jq -r .head.sha)" >> $GITHUB_OUTPUT
          echo "base_sha=$(echo $PR_JSON | jq -r .base.sha)" >> $GITHUB_OUTPUT
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}

      - uses: truongnh1992/gemini-ai-code-reviewer@main
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          GEMINI_API_KEY: ${{ secrets.GITHUB_TOKEN }}
          GEMINI_MODEL: gemini-2.5-pro
          EXCLUDE: "*.md,*.txt,package-lock.json,*.yml,*.yaml"
```

> If `GEMINI_MODEL` is not specified, the default model is `gemini-2.5-flash`, which provides fast and efficient AI-powered code analysis.

## Usage

1. Commit your code.
2. Open a Pull Request.
3. Comment:

```
/gemini-review
```

The GitHub Action will automatically analyze your changes and post review comments on the Pull Request.

---

# How It Works

1. Collects modified files from the Pull Request.
2. Sends the code changes to Google's Gemini AI.
3. Performs AI-powered code analysis.
4. Generates suggestions and review comments.
5. Publishes the results directly to the Pull Request.

---

# License

Licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
