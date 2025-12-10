# Challenge 0 - Environment Creation and Resources Deployment

**Expected Duration:** 30 minutes

Welcome to your very first challenge! Your goal in this challenge is to create the services and environment necessary to conduct this hackathon. You will deploy the required resources in Azure, create your development environment and all the assets necessary for the subsequent challenges. By completing this challenge, you will set up the foundation for the rest of the hackathon. 

If something is not working correctly, please do let your coach know!


## 1.1 Fork the Repository

Before you start, please fork this repository to your GitHub account by clicking the `Fork` button in the upper right corner of the repository's main screen (or follow the [documentation](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/fork-a-repo#forking-a-repository)). This will allow you to make changes to the repository and save your progress.


## 1.2 Development Environment

GitHub Codespaces is a cloud-based development environment that allows you to code from anywhere. It provides a fully configured environment that can be launched directly from any GitHub repository, saving you from lengthy setup times. You can access Codespaces from your browser, Visual Studio Code, or the GitHub CLI, making it easy to work from virtually any device.

To open GitHub Codespaces, click on the button below:

[![Open in GitHub Codespaces](https://github.com/codespaces/badge.svg)](https://codespaces.new/)

Please select your forked repository from the dropdown and, if necessary, adjust other settings of GitHub Codespace.


> [!NOTE]
> If GitHub Codespaces is not enabled in your organization, you can enable it by following the instructions [here](https://docs.github.com/en/codespaces/managing-codespaces-for-your-organization/enabling-or-disabling-github-codespaces-for-your-organization), or, if you cannot change your GitHub organization's settings, create a free personal GitHub account [here](https://github.com/signup?ref_cta=Sign+up&ref_loc=header+logged+out&ref_page=%2F&source=header-home). The Github Free Plan includes 120 core hours per month, equivalent to 60 hours on a 2-core machine, along with 15 GB of storage.


## 1.3 Resource Deployment Guide
The first step on this hackathon will be to create the resources we will use throughout the day. You can deploy using either the one-click button or manual method below.

Before anything else, let's log in into the CLI with our account. Please paste the code underneath and follow the necessary instructions.

```bash
az login --use-device-code
```

In this lab you will be provided with a user like `userX` where X is a number.

The complete user login is: `userX@MngEnvMCAP088700.onmicrosoft.com`

Password will be provided by your coach.

Use this username and password to log in into the link provided by the CLI. Setup your Microsoft Authenticator app if needed.

Once login is successful, you can check your resource groups with:

```bash
az group list -o table
```

## 1.3.1 Resources Deployment

Now, time to deploy our resources to Azure!

Create a temporal variable to store your resource group name:

```bash
export RG="userX"
```

Create a new Microsoft Foundry resource in your resource group by running the following command in your terminal:

```bash
az cognitiveservices account create --name hack25-${RG} --resource-group ${RG} --kind AIServices --sku S0 --location swedencentral --yes
```

## 1.4 Verify the creation of your resources

Go to your [Azure Portal](https://portal.azure.com/) and find your `Resource Group`that should by now contain a Microsoft Foundry resource named `hack25-userX` (where X is your assigned number).


## 1.5 Create a Foundry project

Let's start by creating a Foundry project.

1. In a web browser, open the [Foundry portal](https://ai.azure.com) at `https://ai.azure.com` and sign in using your Azure credentials. Close any tips or quick start panes that are opened the first time you sign in, and if necessary use the **Foundry** logo at the top left to navigate to the home page, which looks similar to the following image (close the **Help** pane if it's open):

    ![Screenshot of Foundry portal.](./Media/ai-foundry-home.png)

    > **Important**: Make sure the **New Foundry** toggle is *Off* for this lab.

1. In the home page, select **Create an agent**.
1. When prompted to create a project, enter a valid name for your project and expand **Advanced options**.
1. Confirm the following settings for your project:
    - **Foundry resource**: *The name of the Foundry resource you created earlier (e.g., `hack25-userX`)*
    - **Subscription**: *Your Azure subscription*
    - **Resource group**: *The resource group where your Foundry resource is located (e.g., `userX`)*
    - **Region**: *Select `Sweden Central`*\*

    > \* Some Azure AI resources are constrained by regional model quotas. In the event of a quota limit being exceeded later in the exercise, there's a possibility you may need to create another resource in a different region.

1. Select **Create** and wait for your project to be created.
1. If prompted, deploy a **gpt-4o** model using either the *Global Standard* or *Standard* deployment option (depending on your quota availability).

    >**Note**: If quota is available, a GPT-4o base model may be deployed automatically when creating your Agent and project.

1. When your project is created, the Agents playground will be opened.

1. In the navigation pane on the left, select **Overview** to see the main page for your project; which looks like this:

    ![Screenshot of a Foundry project overview page.](./Media/ai-foundry-project.png)

1. Copy the **Foundry project endpoint** values to a notepad, as you'll use them to connect to your project in a client application.

## Create an agent client app

Now you're ready to create a client app that uses an agent. Some code has been provided for you in a GitHub repository.

In codespaces terminal, move to the `challenge0` folder:

```bash
cd challenge0
```

### Configure the application settings

1. In the cloud shell command-line pane, enter the following command to install the libraries you'll use:

    ```
   python -m venv labenv
   ./labenv/bin/Activate.ps1
   pip install -r requirements.txt azure-ai-projects azure-ai-agents
    ```

1. Enter the following command to edit the configuration file that has been provided:

    ```
   code .env
    ```

    The file is opened in a code editor.

1. In the code file, replace the **your_project_endpoint** placeholder with the endpoint for your project (copied from the project **Overview** page in the Foundry portal) and ensure that the MODEL_DEPLOYMENT_NAME variable is set to your model deployment name (which should be *gpt-4o*).
1. After you've replaced the placeholder, use the **CTRL+S** command to save your changes and then use the **CTRL+Q** command to close the code editor while keeping the cloud shell command line open.

### Write code for an agent app

> **Tip**: As you add code, be sure to maintain the correct indentation. Use the comment indentation levels as a guide.

1. Enter the following command to edit the code file that has been provided:

    ```
   code agent.py
    ```

1. Review the existing code, which retrieves the application configuration settings and loads data from *data.txt* to be analyzed. The rest of the file includes comments where you'll add the necessary code to implement your data analysis agent.
1. Find the comment **Add references** and add the following code to import the classes you'll need to build an Azure AI agent that uses the built-in code interpreter tool:

    ```python
   # Add references
   from azure.identity import DefaultAzureCredential
   from azure.ai.agents import AgentsClient
   from azure.ai.agents.models import FilePurpose, CodeInterpreterTool, ListSortOrder, MessageRole
    ```

1. Find the comment **Connect to the Agent client** and add the following code to connect to the Azure AI project.

    > **Tip**: Be careful to maintain the correct indentation level.

    ```python
   # Connect to the Agent client
   agent_client = AgentsClient(
       endpoint=project_endpoint,
       credential=DefaultAzureCredential
           (exclude_environment_credential=True,
            exclude_managed_identity_credential=True)
   )
   with agent_client:
    ```

    The code connects to the Foundry project using the current Azure credentials. The final *with agent_client* statement starts a code block that defines the scope of the client, ensuring it's cleaned up when the code within the block is finished.

1. Find the comment **Upload the data file and create a CodeInterpreterTool**, within the *with agent_client* block, and add the following code to upload the data file to the project and create a CodeInterpreterTool that can access the data in it:

    ```python
   # Upload the data file and create a CodeInterpreterTool
   file = agent_client.files.upload_and_poll(
        file_path=file_path, purpose=FilePurpose.AGENTS
   )
   print(f"Uploaded {file.filename}")

   code_interpreter = CodeInterpreterTool(file_ids=[file.id])
    ```
    
1. Find the comment **Define an agent that uses the CodeInterpreterTool** and add the following code to define an AI agent that analyzes data and can use the code interpreter tool you defined previously:

    ```python
   # Define an agent that uses the CodeInterpreterTool
   agent = agent_client.create_agent(
        model=model_deployment,
        name="data-agent",
        instructions="You are an AI agent that analyzes the data in the file that has been uploaded. Use Python to calculate statistical metrics as necessary.",
        tools=code_interpreter.definitions,
        tool_resources=code_interpreter.resources,
   )
   print(f"Using agent: {agent.name}")
    ```

1. Find the comment **Create a thread for the conversation** and add the following code to start a thread on which the chat session with the agent will run:

    ```python
   # Create a thread for the conversation
   thread = agent_client.threads.create()
    ```
    
1. Note that the next section of code sets up a loop for a user to enter a prompt, ending when the user enters "quit".

1. Find the comment **Send a prompt to the agent** and add the following code to add a user message to the prompt (along with the data from the file that was loaded previously), and then run thread with the agent.

    ```python
   # Send a prompt to the agent
   message = agent_client.messages.create(
        thread_id=thread.id,
        role="user",
        content=user_prompt,
    )

   run = agent_client.runs.create_and_process(thread_id=thread.id, agent_id=agent.id)
    ```

1. Find the comment **Check the run status for failures** and add the following code to check for any errors.

    ```python
   # Check the run status for failures
   if run.status == "failed":
        print(f"Run failed: {run.last_error}")
    ```

1. Find the comment **Show the latest response from the agent** and add the following code to retrieve the messages from the completed thread and display the last one that was sent by the agent.

    ```python
   # Show the latest response from the agent
   last_msg = agent_client.messages.get_last_message_text_by_role(
       thread_id=thread.id,
       role=MessageRole.AGENT,
   )
   if last_msg:
       print(f"Last Message: {last_msg.text.value}")
    ```

1. Find the comment **Get the conversation history**, which is after the loop ends, and add the following code to print out the messages from the conversation thread; reversing the order to show them in chronological sequence

    ```python
   # Get the conversation history
   print("\nConversation Log:\n")
   messages = agent_client.messages.list(thread_id=thread.id, order=ListSortOrder.ASCENDING)
   for message in messages:
       if message.text_messages:
           last_msg = message.text_messages[-1]
           print(f"{message.role}: {last_msg.text.value}\n")
    ```

1. Find the comment **Clean up** and add the following code to delete the agent and thread when no longer needed.

    ```python
   # Clean up
   agent_client.delete_agent(agent.id)
    ```

1. Review the code, using the comments to understand how it:
    - Connects to the AI Foundry project.
    - Uploads the data file and creates a code interpreter tool that can access it.
    - Creates a new agent that uses the code interpreter tool and has explicit instructions to use Python as necessary for statistical analysis.
    - Runs a thread with a prompt message from the user along with the data to be analyzed.
    - Checks the status of the run in case there's a failure
    - Retrieves the messages from the completed thread and displays the last one sent by the agent.
    - Displays the conversation history
    - Deletes the agent and thread when they're no longer required.

1. Save the code file (*CTRL+S*) when you have finished. You can also close the code editor (*CTRL+Q*); though you may want to keep it open in case you need to make any edits to the code you added. In either case, keep the cloud shell command-line pane open.


















