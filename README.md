# AI PR Summarizer

![GitHub Marketplace](https://img.shields.io/badge/GitHub-Marketplace-blue.svg)
![License](https://img.shields.io/badge/License-Proprietary-red.svg)

Automate your pull request reviews with intelligent, AI-generated summaries. The **AI PR Summarizer** leverages advanced natural language processing to analyze and summarize changes in your pull requests, saving you time and enhancing code review efficiency.

## 🚀 Features

- **Automated Summaries**: Automatically generate concise summaries of pull request changes.
- **Key Highlights**: Highlight important files and explain the purpose of each change.
- **Seamless Integration**: Easily integrate with your existing GitHub workflows.
- **Secure and Private**: Maintain code privacy while leveraging powerful AI capabilities.
- **Customizable**: Configure settings to tailor summaries to your project's needs.

## 📋 Prerequisites

Before integrating the AI PR Summarizer into your workflows, ensure you have the following:

- **GitHub Repository**: A GitHub repository where you want to use the Action.
- **GitHub Personal Access Token (PAT)**: A PAT with the following scopes:
  - `read:packages`
  - `write:packages`
  - `repo`
- **OpenAI API Key**: An API key from [OpenAI](https://openai.com/) to utilize GPT-4 capabilities.

## 🛠 Installation

### 1. Authenticate to GitHub Container Registry (GHCR)

To allow the Action to pull the private Docker image from GHCR, you need to authenticate using a GitHub PAT.

1. **Generate a GitHub PAT**:
   - Navigate to [GitHub Settings](https://github.com/settings/tokens).
   - Click on **"Generate new clasic token"**.
   - Select the necessary scopes: `read:packages`, `write:packages`, `repo`.
   - Click **"Generate token"** and save the token securely.

2. **Add the PAT as a Secret in Your Repository**:
   - Go to your repository on GitHub.
   - Click on **"Settings"** > **"Secrets and variables"** > **"Actions"**.
   - Click **"New repository secret"**.
   - Add a secret named `G_TOKEN` and paste your GITHUB PAT.

### 2. Add OpenAI API Key as a Secret

1. **Obtain OpenAI API Key**:
   - Sign up or log in to your [OpenAI account](https://platform.openai.com/account/api-keys).
   - Generate a new API key.

2. **Add the API Key as a Secret**:
   - In your repository, navigate to **"Settings"** > **"Secrets and variables"** > **"Actions"**.
   - Click **"New repository secret"**.
   - Add a secret named `OPENAI_API_KEY` and paste your API key.

## 🎬 Usage

Integrate the AI PR Summarizer into your GitHub Actions workflow by adding the following step to your workflow YAML file.

### 1. Create or Update Your Workflow

Add a new workflow file (e.g., `.github/workflows/ai-pr-summarizer.yml`) or update an existing one with the following content:

```yaml
name: 'AI PR Summarizer'

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  summarize_pr:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
      packages: read  # Essential for private container pulls
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Login to GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }} # or set this to a serviceuser
          password: ${{ secrets.G_TOKEN }}

      - name: Pull AI PR Summarizer Docker Image
        run: |
          docker pull ghcr.io/Pierre-Gode/ai-pr-summarizer:latest

      - name: Run AI PR Summarizer
        run: |
          docker run --rm \
            -v $GITHUB_EVENT_PATH:/app/event.json \
            -e GITHUB_EVENT_PATH="/app/event.json" \
            -e GITHUB_TOKEN="${{ secrets.G_TOKEN }}" \
            -e OPENAI_API_KEY="${{ secrets.OPENAI_API_KEY }}" \
            ghcr.io/your-org/ai-pr-summarizer:latest
