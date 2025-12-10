# Challenge 02 - Support Ticket Triage

Welcome back! In this challenge you will compose an agent-driven workflow that triages customer support tickets by combining lightweight specialized agents. This builds on the Azure AI Agents SDK experience from the first challenge and introduces connected agents so one agent can orchestrate the others.

If you get stuck at any point, flag it for your coach.

## 1. Build the Multi-Agent Triage App

You will modify the sample triage app in the `challenge02` folder to create several small agents and one coordinating agent that uses connected tools.

### 1.1. Prepare the workspace

1. From your Codespace terminal, switch into the challenge folder and install dependencies:

    ```bash
    cd challenge02
    python -m venv labenv
    source ./labenv/bin/activate
    pip install -r requirements.txt
    ```

### 1.2. Update the triage agent code

> **Tip**: As you add code, maintain the indentation levels that are already in place—especially under `with agents_client:` or inside `if/else` blocks.

1. Enter the following command to open the triage script:

    ```bash
    code agent_triage.py
    ```

2. Review the existing file. It already loads the endpoint and deployment name from the environment and includes placeholder strings for each agent name and their instructions.

3. Find the comment **Add references** and insert the following imports so you can create agents, tools, and runs:

    ```python
    # Add references
    from azure.ai.agents import AgentsClient
    from azure.ai.agents.models import ConnectedAgentTool, MessageRole, ListSortOrder, ToolSet, FunctionTool
    from azure.identity import DefaultAzureCredential
    ```

4. Find **Connect to the agents client** and plug in the agents client with the credentials shown below:

    ```python
    # Connect to the agents client
    agents_client = AgentsClient(
        endpoint=project_endpoint,
        credential=DefaultAzureCredential(
            exclude_environment_credential=True,
            exclude_managed_identity_credential=True,
        ),
    )
    ```

5. Under the comment **Create an agent to prioritize support tickets**, paste the following code to build the priority agent and its instructions:

    ```python
    # Create an agent to prioritize support tickets
    priority_agent_name = "priority_agent"
    priority_agent_instructions = """
    Assess how urgent a ticket is based on its description.

    Respond with one of the following levels:
    - High: User-facing or blocking issues
    - Medium: Time-sensitive but not breaking anything
    - Low: Cosmetic or non-urgent tasks

    Only output the urgency level and a very brief explanation.
    """

    priority_agent = agents_client.create_agent(
        model=model_deployment,
        name=priority_agent_name,
        instructions=priority_agent_instructions,
    )
    ```

6. Find the comment **Create an agent to assign tickets to the appropriate team**, and add:

    ```python
    # Create an agent to assign tickets to the appropriate team
    team_agent_name = "team_agent"
    team_agent_instructions = """
    Decide which team should own each ticket.

    Choose from the following teams:
    - Frontend
    - Backend
    - Infrastructure
    - Marketing

    Base your answer on the content of the ticket. Respond with the team name and a very brief explanation.
    """

    team_agent = agents_client.create_agent(
        model=model_deployment,
        name=team_agent_name,
        instructions=team_agent_instructions,
    )
    ```

7. Find **Create an agent to estimate effort for a support ticket** and paste:

    ```python
    # Create an agent to estimate effort for a support ticket
    effort_agent_name = "effort_agent"
    effort_agent_instructions = """
    Estimate how much work each ticket will require.

    Use the following scale:
    - Small: Can be completed in a day
    - Medium: 2-3 days of work
    - Large: Multi-day or cross-team effort

    Base your estimate on the complexity implied by the ticket. Respond with the effort level and a brief justification.
    """

    effort_agent = agents_client.create_agent(
        model=model_deployment,
        name=effort_agent_name,
        instructions=effort_agent_instructions,
    )
    ```

8. Under **Create connected agent tools for the support agents**, add the connected tools so other agents can invoke these specialists:

    ```python
    # Create connected agent tools for the support agents
    priority_agent_tool = ConnectedAgentTool(
        id=priority_agent.id,
        name=priority_agent_name,
        description="Assess the priority of a ticket",
    )

    team_agent_tool = ConnectedAgentTool(
        id=team_agent.id,
        name=team_agent_name,
        description="Determines which team should take the ticket",
    )

    effort_agent_tool = ConnectedAgentTool(
        id=effort_agent.id,
        name=effort_agent_name,
        description="Determines the effort required to complete the ticket",
    )
    ```

9. Under **Create an agent to triage support ticket processing by using connected agents**, add the primary triage agent definition:

    ```python
    # Create an agent to triage support ticket processing by using connected agents
    triage_agent_name = "triage-agent"
    triage_agent_instructions = """
    Triage the given ticket. Use the connected tools to determine the ticket's priority,
    which team it should be assigned to, and how much effort it may take.
    """

    triage_agent = agents_client.create_agent(
        model=model_deployment,
        name=triage_agent_name,
        instructions=triage_agent_instructions,
        tools=[
            priority_agent_tool.definitions[0],
            team_agent_tool.definitions[0],
            effort_agent_tool.definitions[0],
        ],
    )
    ```

10. Find **Use the agents to triage a support issue** and insert the execution logic so the triage agent can process a ticket using the connected tools:

    ```python
    # Use the agents to triage a support issue
    print("Creating agent thread.")
    thread = agents_client.threads.create()

    # Create the ticket prompt
    prompt = input("\nWhat's the support problem you need to resolve?: ")

    # Send a prompt to the agent
    message = agents_client.messages.create(
        thread_id=thread.id,
        role=MessageRole.USER,
        content=prompt,
    )

    # Run the thread using the primary agent
    print("\nProcessing agent thread. Please wait.")
    run = agents_client.runs.create_and_process(
        thread_id=thread.id,
        agent_id=triage_agent.id,
    )

    if run.status == "failed":
        print(f"Run failed: {run.last_error}")

    # Fetch and display messages
    messages = agents_client.messages.list(
        thread_id=thread.id,
        order=ListSortOrder.ASCENDING,
    )
    for message in messages:
        if message.text_messages:
            last_msg = message.text_messages[-1]
            print(f"{message.role}:\n{last_msg.text.value}\n")
    ```

11. Finally, find **Clean up** and add code that tears down every agent once the triage run is finished:

    ```python
    # Clean up
    print("Cleaning up agents:")
    agents_client.delete_agent(triage_agent.id)
    print("Deleted triage agent.")
    agents_client.delete_agent(priority_agent.id)
    print("Deleted priority agent.")
    agents_client.delete_agent(team_agent.id)
    print("Deleted team agent.")
    agents_client.delete_agent(effort_agent.id)
    print("Deleted effort agent.")
    ```

12. Save your edits with **CTRL+S**. You can keep the file open for validation or close it with **CTRL+Q** while leaving the terminal active.

## 2. Run and Test the Agent Application

1. In the Codespaces terminal, execute the triage app:

    ```bash
    python agent_triage.py
    ```

2. When prompted, describe a realistic support ticket, for example:

    ```text
    Users can't reset their password from the mobile app.
    ```

3. Observe the output—each agent should output its assessment and the cleanup messages should confirm every agent is deleted. You should see something like:

    ```output
    Creating agent thread.
    Processing agent thread. Please wait.

    MessageRole.USER:
    Users can't reset their password from the mobile app.

    MessageRole.AGENT:
    ### Ticket Assessment

    - **Priority:** High — This issue blocks users from resetting their passwords, limiting access to their accounts.
    - **Assigned Team:** Frontend Team — The problem lies in the mobile app's user interface or functionality.
    - **Effort Required:** Medium — Resolving this problem involves identifying the root cause, potentially updating the mobile app functionality, reviewing API/backend integration, and testing to ensure compatibility across Android/iOS platforms.

    Cleaning up agents:
    Deleted triage agent.
    Deleted priority agent.
    Deleted team agent.
    Deleted effort agent.
    ```

4. Try a second prompt (for example, `Investigate occasional 502 errors from the search endpoint.`) to see how the agents coordinate on different scenarios.

## 3. Summary

In this challenge you linked multiple Azure AI agents via `ConnectedAgentTool` objects and used a higher-level triage agent that orchestrates them. You also saw how to capture the run output, validate agent-generated responses, and tear down the agents when the work is done.
