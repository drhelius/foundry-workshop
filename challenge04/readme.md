# Challenge 04 - Develop an Microsoft Foundry chat agent with the Microsoft Agent Framework SDK

In this exercise, you'll use Microsoft Foundry Agent Service and Microsoft Agent Framework to create an AI agent that processes expense claims.

If you get stuck at any point, flag it for your coach.

> **Note**: Some of the technologies used in this exercise are in preview or in active development. You may experience some unexpected behavior, warnings, or errors.

## 1. Create an agent client app

Now you're ready to create a client app that defines an agent and a custom function.

### 1.1. Prepare the workspace

1. From your Codespace terminal, switch into the challenge folder and install dependencies:

    ```bash
    cd challenge04
    python -m venv labenv04
    source ./labenv04/bin/activate
    pip install -r requirements.txt
    ```

2. Enter the following command to edit the configuration file that has been provided:

    ```
   code .env
    ```

    The file is opened in a code editor.

3. In the code file, replace the **your_project_endpoint** placeholder with the endpoint for your project (copied from the project **Overview** page in the Foundry portal) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be *gpt-4o*).

4. After you've replaced the placeholder, use the **CTRL+S** command to save your changes.

### 1.2. Write code for an agent app

> **Tip**: As you add code, maintain the indentation levels that are already in place—especially under `with agents_client:` or inside `if/else` blocks.

1. Enter the following command to edit the code file that has been provided:

    ```
   code agent-framework.py
    ```

2. Review the code in the file. It contains:
    - Some **import** statements to add references to commonly used namespaces
    - A *main* function that loads a file containing expenses data, asks the user for instructions, and and then calls...
    - A **process_expenses_data** function in which the code to create and use your agent must be added

3. At the top of the file, after the existing **import** statement, find the comment **Add references**, and add the following code to reference the namespaces in the libraries you'll need to implement your agent:

    ```python
   # Add references
   from agent_framework import AgentThread, ChatAgent
   from agent_framework.azure import AzureAIAgentClient
   from azure.identity.aio import DefaultAzureCredential
   from pydantic import Field
   from typing import Annotated
    ```

4. Near the bottom of the file, find the comment **Create a tool function for the email functionality**, and add the following code to define a function that your agent will use to send email (tools are a way to add custom functionality to agents)

    ```python
   # Create a tool function for the email functionality
   def send_email(
    to: Annotated[str, Field(description="Who to send the email to")],
    subject: Annotated[str, Field(description="The subject of the email.")],
    body: Annotated[str, Field(description="The text body of the email.")]):
        print("\nTo:", to)
        print("Subject:", subject)
        print(body, "\n")
    ```

    > **Note**: The function *simulates* sending an email by printing it to the console. In a real application, you'd use an SMTP service or similar to actually send the email!

5. Back up above the **send_email** code, in the **process_expenses_data** function, find the comment **Create a chat agent**, and add the following code to create a  **ChatAgent** object with the tools and instructions.

    (Be sure to maintain the indentation level)

    ```python
    # Create a chat agent
    async with (
        DefaultAzureCredential() as credential,
        ChatAgent(
            chat_client=AzureAIAgentClient(credential=credential),
            name="expenses_agent",
            instructions="""You are an AI assistant for expense claim submission.
                            When a user submits expenses data and requests an expense claim, use the plug-in function to send an email to expenses@contoso.com with the subject 'Expense Claim`and a body that contains itemized expenses with a total.
                            Then confirm to the user that you've done so.""",
            tools=send_email,
        ) as agent,
    ):
    ```

    Note that the **AzureCliCredential** object will allow your code to authenticate to your Azure account. The **AzureAIAgentClient** object will automatically include the Foundry project settings from the .env configuration.

6. Find the comment **Use the agent to process the expenses data**, and add the following code to create a thread for your agent to run on, and then invoke it with a chat message.

    (Be sure to maintain the indentation level):

    ```python
        # Use the agent to process the expenses data
        try:
            # Add the input prompt to a list of messages to be submitted
            prompt_messages = [f"{prompt}: {expenses_data}"]
            # Invoke the agent for the specified thread with the messages
            response = await agent.run(prompt_messages)
            # Display the response
            print(f"\n# Agent:\n{response}")
        except Exception as e:
            # Something went wrong
            print (e)
    ```

7. Review that the completed code for your agent, using the comments to help you understand what each block of code does, and then save your code changes (**CTRL+S**).

## 2. Run and Test the Agent Application

1. In the Codespaces terminal, execute:

    ```
   python agent-framework.py
    ```
    
    The application runs using the credentials for your authenticated Azure session to connect to your project and create and run the agent.

2. When asked what to do with the expenses data, enter the following prompt:

    ```
   Submit an expense claim
    ```

3. When the application has finished, review the output. The agent should have composed an email for an expenses claim based on the data that was provided.

    > **Tip**: If the app fails because the rate limit is exceeded. Wait a few seconds and try again. If there is insufficient quota available in your subscription, the model may not be able to respond.

## 3. Summary

In this exercise, you used the Microsoft Agent Framework SDK to create an agent with a custom tool. 
