# Microsoft Foundry Agents Workshop

Welcome to the Microsoft Foundry Agents Workshop! 🤖 This hands-on workshop will guide you through building intelligent AI agents using the Microsoft Agent Framework and Microsoft Foundry. Get ready to create sophisticated agent systems that can analyze data, interact with users, and solve real-world problems!

## Introduction

In this workshop, you'll master the fundamentals of building AI agents with Microsoft Foundry, progressing from basic single-agent applications to sophisticated multi-agent orchestrations. You'll learn how to create agents that can use both built-in and custom tools, integrate with external services through Model Context Protocol (MCP), process data, maintain conversation context, and deliver intelligent responses.

Starting with the Microsoft Foundry Agent Service SDK, you'll develop agents that leverage built-in tools like code interpreters for dynamic data analysis. You'll then advance to creating multi-agent systems with connected tools, integrating cloud-hosted MCP servers for external data access, and building sophisticated agent pipelines using the Microsoft Agent Framework SDK. By the end of this workshop, you'll have hands-on experience with multiple agent architectures and patterns, from stateful conversations and agent orchestration to sequential workflows and custom tool development.

## Learning Objectives 🎯

By participating in this workshop, you will learn how to:

- **Master Microsoft Foundry** by setting up projects, deploying models, managing AI resources, and configuring development environments.
- **Build Single-Agent Applications** using the Microsoft Foundry Agent Service SDK with proper authentication, configuration, and built-in tool integration like Code Interpreter.
- **Create Multi-Agent Systems** by developing specialized agents that work together through connected agent tools for coordinated problem-solving.
- **Integrate External Services** by connecting agents to cloud-hosted Model Context Protocol (MCP) servers for accessing real-time documentation and external data sources.
- **Develop Custom Agent Tools** using the Microsoft Agent Framework SDK to create specialized functions that extend agent capabilities for domain-specific tasks.
- **Implement Sequential Orchestration** by building agent pipelines where multiple agents process information in stages to produce comprehensive results.
- **Manage Conversation Context** through thread-based interactions that maintain stateful conversations with full conversation history.
- **Apply Best Practices** including environment configuration, credential management, proper error handling, and clean resource lifecycle management.

## Requirements

To successfully complete this workshop, you will need the following:

- **GitHub account** to access the repository and run GitHub Codespaces.
- **Familiarity with Python programming**, including working with environment variables, making API calls, and basic object-oriented concepts.
- **Basic understanding of AI concepts** such as large language models, prompts, and conversational AI.
- **An active Azure subscription** with permissions to create resources.
- **Ability to provision resources** in Sweden Central or [another supported region](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/concepts/models).

## Challenges

- **Challenge 01**: [Environment Creation and Resources Deployment](./challenge01/readme.md) - Set up your development environment, deploy Microsoft Foundry resources, create your first project, and build a client application that uses an AI agent with Code Interpreter capabilities to analyze data and perform statistical analysis.

- **Challenge 02**: [Support Ticket Triage](./challenge02/readme.md) - Build a multi-agent system that triages customer support tickets by creating specialized agents for priority assessment, team assignment, and effort estimation, orchestrated through connected agent tools.

- **Challenge 03**: [Connect AI agents to tools using Model Context Protocol (MCP)](./challenge03/readme.md) - Connect an AI agent to a cloud-hosted MCP server to enable access to Microsoft's official documentation through AI-powered search, allowing agents to provide real-time, accurate technical guidance.

- **Challenge 04**: [Develop a chat agent with Microsoft Agent Framework](./challenge04/readme.md) - Create an AI agent that processes expense claims using the Microsoft Agent Framework SDK, implementing custom tools and functions to enable agents to perform specialized tasks like sending emails.

- **Challenge 05**: [Develop a multi-agent solution with Microsoft Agent Framework](./challenge05/readme.md) - Build a sequential orchestration of multiple agents that work together to process customer feedback, practicing how to create agent pipelines that summarize, classify, and recommend actions.
