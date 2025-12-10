# Challenge 03 - Connect AI agents to tools using Model Context Protocol (MCP)

In this exercise, you'll build an agent that connects to a cloud-hosted MCP server. The agent will use AI-powered search to help developers find accurate, real-time answers from Microsoft's official documentation. This is useful for building assistants that support developers with up-to-date guidance on tools like Azure, .NET, and Microsoft 365. The agent will use the available MCP tools to query the documentation and return relevant results.

If you get stuck at any point, flag it for your coach.

## 1. Connect an Microsoft Foundry Agent to a remote MCP server

In this task, you'll connect to a remote MCP server, prepare the AI agent, and run a user prompt.

### 1.1. Prepare the workspace

1. From your Codespace terminal, switch into the challenge folder and install dependencies:

    ```bash
    cd challenge03
    python -m venv labenv03
    source ./labenv03/bin/activate
    pip install -r requirements.txt
    ```

2. Enter the following command to edit the configuration file that has been provided:

    ```
   code .env
    ```

    The file is opened in a code editor.

3. In the code file, replace the **your_project_endpoint** placeholder with the endpoint for your project (copied from the project **Overview** page in the Foundry portal) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be *gpt-4o*).

4. After you've replaced the placeholder, use the **CTRL+S** command to save your changes.

### 1.2. Update the triage agent code

> **Tip**: As you add code, maintain the indentation levels that are already in place—especially under `with agents_client:` or inside `if/else` blocks.

1. Enter the following command to edit the code file that has been provided:

    ```
   code client.py
    ```

    The file is opened in the code editor.

2. Find the comment **Add references** and add the following code to import the classes:

    ```python
    # Add references
    from azure.identity import DefaultAzureCredential
    from azure.ai.agents import AgentsClient
    from azure.ai.agents.models import McpTool, ToolSet, ListSortOrder
    ```

3. Find the comment **Connect to the agents client** and add the following code to connect to the Microsoft Foundry project using the current Azure credentials.

    ```python
    # Connect to the agents client
    agents_client = AgentsClient(
            endpoint=project_endpoint,
            credential=DefaultAzureCredential(
                exclude_environment_credential=True,
                exclude_managed_identity_credential=True
            )
    )
    ```

4. Under the comment **Initialize agent MCP tool**, add the following code:

    ```python
    # Initialize agent MCP tool
    mcp_tool = McpTool(
            server_label=mcp_server_label,
            server_url=mcp_server_url,
    )
        
    mcp_tool.set_approval_mode("never")
        
    toolset = ToolSet()
    toolset.add(mcp_tool)
    ```

    This code will connect to the Microsft Learn Docs remote MCP server. This is a cloud-hosted service that enables clients to access trusted and up-to-date information directly from Microsoft's official documentation.

5. Under the comment **Create a new agent** and add the following code:

    ```python
    # Create a new agent
    agent = agents_client.create_agent(
            model=model_deployment,
            name="my-mcp-agent",
            instructions="""
            You have access to an MCP server called `microsoft.docs.mcp` - this tool allows you to 
            search through Microsoft's latest official documentation. Use the available MCP tools 
            to answer questions and perform tasks."""
    )
    ```

    In this code, you provide instructions for the agent and provide it with the MCO tool definitions.

6. Find the comment **Create thread for communication** and add the following code:

    ```python
    # Create thread for communication
    thread = agents_client.threads.create()
    print(f"Created thread, ID: {thread.id}")
    ```

7. Find the comment **Create a message on the thread** and add the following code:

    ```python
    # Create a message on the thread
    prompt = input("\nHow can I help?: ")
    message = agents_client.messages.create(
            thread_id=thread.id,
            role="user",
            content=prompt,
    )
    print(f"Created message, ID: {message.id}")
    ```

8. Find the comment **Set approval mode** and add the following code:

    ```python
    # Set approval mode
    mcp_tool.set_approval_mode("never")
    ```

    This allows the agent to automatically invoke the MCP tools without requiring user approval. If you want to require approval, you must supply a header value using `mcp_tool.update_headers`.

9. Find the comment **Create and process agent run in thread with MCP tools** and add the following code:

    ```python
    # Create and process agent run in thread with MCP tools
    run = agents_client.runs.create_and_process(thread_id=thread.id, agent_id=agent.id, toolset=toolset)
    print(f"Created run, ID: {run.id}")
    ```
    
    The AI Agent automatically invokes the connected MCP tools to process the prompt request. To illustrate this process, the code provided under the comment **Display run steps and tool calls** will output any invoked tools from the MCP server.

10. Save the code file (*CTRL+S*) when you have finished.

## 2. Run and Test the Agent Application

1. In the Codespaces terminal, execute:

    ```
   python client.py
    ```

2. When prompted, enter a request for technical information such as:

    ```
    Give me the Azure CLI commands to create an Azure Container App with a managed identity.
    ```

3. Wait for the agent to process your prompt, using the MCP server to find a suitable tool to retrieve the requested information. You should see some output similar to the following:

    ```
    Created agent, ID: <<agent-id>>
    MCP Server: mslearn at https://learn.microsoft.com/api/mcp
    Created thread, ID: <<thread-id>>
    Created message, ID: <<message-id>>
    Created run, ID: <<run-id>>
    Run completed with status: RunStatus.COMPLETED
    Step <<step1-id>> status: completed

    Step <<step2-id>> status: completed
    MCP Tool calls:
        Tool Call ID: <<tool-call-id>>
        Type: mcp
        Type: microsoft_code_sample_search


    Conversation:
    --------------------------------------------------
    ASSISTANT: You can use Azure CLI to create an Azure Container App with a managed identity (either system-assigned or user-assigned). Below are the relevant commands and workflow:

    ---

    ### **1. Create a Resource Group**
    '''azurecli
    az group create --name myResourceGroup --location eastus
    '''
    

    {{continued...}}

    By following these steps, you can deploy an Azure Container App with either system-assigned or user-assigned managed identities to integrate seamlessly with other Azure services.
    --------------------------------------------------
    USER: Give me the Azure CLI commands to create an Azure Container App with a managed identity.
    --------------------------------------------------
    Deleted agent
    ```

    Notice that the agent was able to invoke the MCP tool `microsoft_code_sample_search` automatically to fulfill the request.

4. You can run the app again (using the command `python client.py`) to ask for different information, In each case, the agent will attempt to find technical documentation by using the MCP tool.

## 3. Summary

In this challenge you connected an Microsoft Foundry agent to a remote Model Context Protocol (MCP) server that provides access to Microsoft's official documentation. You learned how to configure MCP tools, set approval modes for automatic tool invocation, and process agent runs that leverage these external tools. You also saw how the agent automatically discovers and uses the appropriate MCP tools to fulfill user requests, providing accurate and up-to-date technical information from trusted sources.
