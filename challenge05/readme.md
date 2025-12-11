# Challenge 05 - Develop a multi-agent solution Microsoft Agent Framework

In this exercise, you'll practice using the sequential orchestration pattern in the Microsoft Agent Framework SDK. You'll create a simple pipeline of three agents that work together to process customer feedback and suggest next steps. You'll create the following agents:

- The Summarizer agent will condense raw feedback into a short, neutral sentence.
- The Classifier agent will categorize the feedback as Positive, Negative, or a Feature request.
- Finally, the Recommended Action agent will recommend an appropriate follow-up step.

You'll learn how to use the Microsoft Agent Framework SDK to break down a problem, route it through the right agents, and produce actionable results.

If you get stuck at any point, flag it for your coach.

> **Note**: Some of the technologies used in this exercise are in preview or in active development. You may experience some unexpected behavior, warnings, or errors.

## 1. Create an AI Agent client app

Now you're ready to create a client app that defines an agent and a custom function.

### 1.1. Prepare the workspace

1. From your Codespace terminal, switch into the challenge folder and install dependencies:

    ```bash
    cd challenge05
    python -m venv labenv05
    source ./labenv05/bin/activate
    pip install -r requirements.txt
    ```

2. Enter the following command to edit the configuration file that has been provided:

    ```
   code .env
    ```

    The file is opened in a code editor.

3. In the code file, replace the **your_project_endpoint** placeholder with the endpoint for your project (copied from the project **Overview** page in the Foundry portal) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be *gpt-4o*).

4. After you've replaced the placeholder, use the **CTRL+S** command to save your changes.

### 1.2. Create AI agents

> **Tip**: As you add code, maintain the indentation levels that are already in place—especially under `with agents_client:` or inside `if/else` blocks.

1. Enter the following command to edit the code file that has been provided:

    ```
   code agents.py
    ```

2. At the top of the file under the comment **Add references**, and add the following code to reference the namespaces in the libraries you'll need to implement your agent:

    ```python
    # Add references
    import asyncio
    from typing import cast
    from dotenv import load_dotenv
    from agent_framework import ChatMessage, Role, SequentialBuilder, WorkflowOutputEvent
    from agent_framework.azure import AzureAIAgentClient
    from azure.identity.aio import DefaultAzureCredential
    ```

3. In the **main** function, take a moment to review the agent instructions. These instructions define the behavior of each agent in the orchestration.

4. Add the following code under the comment **Create the chat client**:

    ```python
    # Create the chat client
    async with DefaultAzureCredential() as credential:
        async with AzureAIAgentClient(credential=credential) as chat_client:
    ```

    Note that the **AzureCliCredential** object will allow your code to authenticate to your Azure account. The **AzureAIAgentClient** object will automatically include the Foundry project settings from the .env configuration.

5. Add the following code under the comment **Create agents**:

    (Be sure to maintain the indentation level)

    ```python
            # Create agents
            summarizer = chat_client.create_agent(
                instructions=summarizer_instructions,
                name="summarizer",
            )

            classifier = chat_client.create_agent(
                instructions=classifier_instructions,
                name="classifier",
            )

            action = chat_client.create_agent(
                instructions=action_instructions,
                name="action",
            )
    ```

#### Create a sequential orchestration

1. In the **main** function, find the comment **Initialize the current feedback** and add the following code:
    
    (Be sure to maintain the indentation level)

    ```python
            # Initialize the current feedback
            feedback="""
            I use the dashboard every day to monitor metrics, and it works well overall. 
            But when I'm working late at night, the bright screen is really harsh on my eyes. 
            If you added a dark mode option, it would make the experience much more comfortable.
            """
    ```

2. Under the comment **Build a sequential orchestration**, add the following code to define a sequential orchestration with the agents you defined:

    ```python
            # Build sequential orchestration
            workflow = SequentialBuilder().participants([summarizer, classifier, action]).build()
    ```

    The agents will process the feedback in the order they are added to the orchestration.

3. Add the following code under the comment **Run and collect outputs**:

    ```python
            # Run and collect outputs
            outputs: list[list[ChatMessage]] = []
            async for event in workflow.run_stream(f"Customer feedback: {feedback}"):
                if isinstance(event, WorkflowOutputEvent):
                    outputs.append(cast(list[ChatMessage], event.data))
    ```

    This code runs the orchestration and collects the output from each of the participating agents.

4. Add the following code under the comment **Display outputs**:

    ```python
            # Display outputs
            if outputs:
                for i, msg in enumerate(outputs[-1], start=1):
                    name = msg.author_name or ("assistant" if msg.role == Role.ASSISTANT else "user")
                    print(f"{'-' * 60}\n{i:02d} [{name}]\n{msg.text}")
    ```

    This code formats and displays the messages from the workflow outputs you collected from the orchestration.

5. Use the **CTRL+S** command to save your changes to the code file.

## 2. Run and Test the Agent Application

1. In the Codespaces terminal, execute:

    ```
    python agents.py
    ```

    You should see some output similar to the following:

    ```output
    ------------------------------------------------------------
    01 [user]
    Customer feedback:
        I use the dashboard every day to monitor metrics, and it works well overall.
        But when I'm working late at night, the bright screen is really harsh on my eyes.
        If you added a dark mode option, it would make the experience much more comfortable.

    ------------------------------------------------------------
    02 [summarizer]
    User requests a dark mode for better nighttime usability.
    ------------------------------------------------------------
    03 [classifier]
    Feature request
    ------------------------------------------------------------
    04 [action]
    Log as enhancement request for product backlog.
    ```

1. Optionally, you can try running the code using different feedback inputs, such as:

    ```output
    I use the dashboard every day to monitor metrics, and it works well overall. But when I'm working late at night, the bright screen is really harsh on my eyes. If you added a dark mode option, it would make the experience much more comfortable.
    ```
    ```output
    I reached out to your customer support yesterday because I couldn't access my account. The representative responded almost immediately, was polite and professional, and fixed the issue within minutes. Honestly, it was one of the best support experiences I've ever had.
    ```

## 3. Summary

In this exercise, you practiced sequential orchestration with the Microsoft Agent Framework SDK, combining multiple agents into a single, streamlined workflow.