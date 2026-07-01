# Working Critically with AI Coding Tools

## Essential Setup Instructions

There are 2 setup options for participants to run the workshop "Working Critically with AI Coding Tools":

1. **Locally on your laptop with VSCode and Docker installed** — recommended for most participants.
2. **Locally on your laptop with your own favourite IDE setup** with an AI coding agent or tool you already use — but this is unsupported by the trainers because we will not have time to help with non-standard setups and we assume you already know your own setup well.

If in doubt about which setup would be best for you please go with Option 1.

**Minimum prerequisite for both setups:**

- GitHub account

---

## Option 1: VSCode and Docker (recommended)

- Get an OpenRouter account. Sign up here: <https://openrouter.ai/> (no credit card required)
- Install VSCode (if you don't already have it): <https://code.visualstudio.com/>
- Install Docker Desktop (if you don't already have it): <https://www.docker.com/products/docker-desktop/>
- Follow the important steps in this devcontainers tutorial up to and including "Run the application" to verify your installation: <https://code.visualstudio.com/docs/devcontainers/tutorial>

> **Important:** If you get any errors or problems while following the devcontainers tutorial, you will not be ready to run the workshop repository locally on your laptop. Ask for help to troubleshoot in advance. We have a backup option available in case you cannot get VSCode and/or Docker to work on your laptop at all.

---

## Option 2: Your own setup (advanced users only)

The code repository we will use has a devcontainer configuration so you will find it easier to get set up if your IDE supports devcontainers. However, it will also be possible to install required packages manually via `pip` and `npm`.

If you are not sure how to do that then please use Option 1 (above).

As part of the devcontainer configuration it will load some AI coding tool extensions. You are welcome to use your preferred coding tool extension instead (e.g. Copilot, Claude Code). However, you will need to set that up yourself — please make sure you have sufficient subscription or credits available. Also be aware that your results may vary from those running Option 1, and come prepared to contribute to the discussion around differences in model providers!