# Microsoft Foundry Agents Workshop

Welcome to the Microsoft Foundry Agents Workshop! 🤖 This hands-on workshop will guide you through building intelligent AI agents using the Microsoft Agent Framework and Microsoft Foundry. Get ready to create sophisticated agent systems that can analyze data, interact with users, and solve real-world problems!

## Introduction

In this workshop, you'll master the fundamentals of building AI agents with Microsoft Foundry. You'll learn how to create agents that can use tools, process data, maintain conversation context, and deliver intelligent responses. Through practical challenges, you'll gain hands-on experience with the enterprise-grade Agent Framework SDK and build production-ready AI solutions.

Using the Microsoft Foundry Agent Service SDK, you'll develop agents that leverage built-in tools like code interpreters to perform dynamic data analysis and statistical computations. By the end of this workshop, you'll have created a complete agent application that demonstrates the power of AI-driven automation and intelligent assistance.

## Learning Objectives 🎯

By participating in this workshop, you will learn how to:

- **Master Microsoft Foundry** by setting up projects, deploying models, and managing AI resources in the Azure AI ecosystem.
- **Build Intelligent AI Agents** using the Microsoft Foundry Agent Service SDK with proper authentication, configuration, and tool integration.
- **Implement Agent Tools** by leveraging built-in capabilities like the Code Interpreter to enable agents to execute Python code dynamically for data analysis.
- **Manage Conversation Context** through thread-based interactions that maintain stateful conversations with full conversation history.
- **Apply Agent Orchestration** by creating, running, and monitoring agent workflows with proper status checking and error handling.
- **Develop Production-Ready Solutions** with environment configuration, credential management, and clean resource lifecycle management.

## Architecture

This workshop focuses on a client-driven agent architecture where applications connect to Microsoft Foundry to leverage hosted agents and models:

- **Microsoft Foundry Project**: Centralized hub for managing AI agents, model deployments, and project resources with integrated monitoring and configuration.
- **AI Agent with Code Interpreter**: Specialized agent powered by GPT-4o that analyzes data files and performs statistical analysis using Python code execution capabilities.
- **Client Application**: Python-based application using the Microsoft Foundry Agent Service SDK to interact with agents, manage threads, and process responses.
- **Stateful Conversations**: Thread-based communication maintaining conversation history, context, and multi-turn interactions for coherent agent responses.

The workflow follows a simple yet powerful pattern: authenticate → upload data → create agent → run conversation → retrieve results. This architecture ensures secure, scalable agent interactions with full observability and control over the agent lifecycle.

## Requirements

To successfully complete this workshop, you will need the following:

- **GitHub account** to access the repository and run GitHub Codespaces.
- **Familiarity with Python programming**, including working with environment variables, making API calls, and basic object-oriented concepts.
- **Basic understanding of AI concepts** such as large language models, prompts, and conversational AI.
- **An active Azure subscription** with permissions to create resources.
- **Ability to provision resources** in Sweden Central or [another supported region](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/concepts/models).

## Challenges

- **Challenge 01**: [Environment Creation and Resources Deployment](./challenge01/readme.md) - Set up your development environment, deploy Microsoft Foundry resources, create your first project, and build a client application that uses an AI agent with Code Interpreter capabilities to analyze data and perform statistical analysis.

- **Challenge 02**: [Coming Soon] - Additional challenges will be added to expand your agent capabilities.

- **Challenge 03**: [Coming Soon] - More advanced agent scenarios and patterns.
