# Azure DevOps

## Learning Goals

- Hands on experience with a simple DevOps pipeline

## Instructions

We will be going through some examples, putting together a simplistic CI/CD pipeline. This will be done in Azure DevOps, using their getting started documentation.
This is quite the condensed topic, so please ask questions if you find yourself getting stuck on any items.


## Building an Azure DevOps Pipeline


### Create a Project

Sign up for a free Azure DevOps account, using your Github account:

- [Sign up for an account](https://docs.microsoft.com/en-us/azure/devops/user-guide/sign-up-invite-teammates)

And create a new project to use for the rest of the demo. Make sure to select a Git backed and Scrum based project:

- [Create a new project](https://docs.microsoft.com/en-us/azure/devops/organizations/projects/create-project)


### Create Work Items

Chances are you will be using Azure DevOps more frequently for work planning and tracking than any of the other features here. Let's get started with a quick demo of this, but be aware
that this workflow will be heavily influenced by your own organization's project development style.

We will be using the Scrum examples going forward, but those concepts should also apply fairly well for other schemes:

- [Tracking work in Azure Boards](https://docs.microsoft.com/en-us/azure/devops/boards/get-started/plan-track-work)

Once you've read through this guide, go ahead and create a few Work Items, and assign them to yourself:

- Create Repo
- "Add Missing Earth" Pull Request 
- Create Build Pipeline
- Push to Artifact Store


### Create New Repository

We will be creating a new Git backed code repository here, and integrating our IntelliJ environment to use it.

Start with the following IntelliJ guide:

- [IntelliJ Plugin](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-repo-intellij)

It is somewhat out of date with the current Azure DevOps dashboard layout, so also look through the following if you cannot work out where all options are located:

- [Create new Repository](https://docs.microsoft.com/en-us/azure/devops/repos/git/create-new-repo)

> Notes:  
> - When you are first cloning code on the commandline, you will probably need to generate git credentials to use. Save these, as they will be needed through the rest of this lab.  
> - The demo application you are working with is fairly out of date, and most likely won't build as is. You can just ignore this, and push a broken codebase.  
> - It may take some looking to track down all the used options of the IntelliJ plugin. It seems the layout may have changed since this guide was created. 

Once this is done, close out the "Create Repo" item, and the "Add Missing Earth Pull Request" item if it hasn't automatically resolved.


### Create New Pipeline

Now that we've created a code repository in our Azure DevOps project, the next step would be to setup a Pipeline to trigger whenever a new commit is made.

We'll first need to setup a Docker Azure pipeline agent though, as Microsoft no longer has a free tier of runners by default.
Read though the following guide to generate a Personal Access Token:

- [Create Access Token](https://docs.microsoft.com/en-us/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)

Then run the following on your local machine to stand up and register an agent

``` shell
docker run --name docker-agent \
    -e AZP_AGENT_NAME=docker-agent \
    -e AZP_URL=https://dev.azure.com/<org name> \
    -e AZP_TOKEN=<token value> \
    -e JAVA_HOME_8_X64=/usr/lib/jvm/java-8-openjdk-amd64/ \
    emberstack/azure-pipelines-agent
...
>> Connect:

Connecting to server ...

>> Register Agent:

Scanning for tool capabilities.
Connecting to the server.
Successfully added the agent
Testing agent connection.
2022-08-09 18:56:27Z: Settings Saved.
2. Running Azure Pipelines agent...
Scanning for tool capabilities.
Connecting to the server.
2022-08-09 18:56:29Z: Listening for Jobs

# In another terminal
docker exec -it docker-agent /bin/bash
root@24015a1f449b:/azp# apt update
...
root@24015a1f449b:/azp# apt install maven openjdk-8-jdk
...
```

> Notes: This local Docker agent should work on local dev workstations, but it is possible that network firewalls may block this connection from occurring. 

We should be able to see the live agent at this point by navigating to "Organization Settings" -> Pipelines -> "Agent pools" -> Default -> Agents.
Select the new agent, navigate to Capabilities, and add a new capability named "maven". This is needed so our later pipeline pool selector has an agent capable for assigning work.

You can read up some more about Pipeline [agents](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/agents) if you are interested. Development teams may or may not be
responsible for maintaining their own, so it is a worthwhile, but somewhat long, read.

Let's move on to the actually pipeline definition now:

> Notes:  
> - Everywhere the following guide uses GitHub, instead use Azure Git Repos. You may need to create a new repo for this.  
> - The first build will fail due to the wrong agent pool being in use. We need to update the pipeline yaml to use `pool: Default` instead. Then select the `Rerun failed jobs` button to restart the build.  
> - After triggering the new agent pool, you may need to grant all the requested permissions if these were not selected previously.

- [Create Java Pipeline](https://docs.microsoft.com/en-us/azure/devops/pipelines/create-first-pipeline?tabs=java)


Once the build runs successfully, close the "Create Build Pipeline" feature, and move on.


### Push New Artifact

Once the Build Pipeline is actually building something successfully, we can start uploading these completed Artifacts for use further in our Testing and Release processes.

Read thought the following and implement its pipeline code snippet. You should be able to navigate to and download all the compiled Artifacts as documented:

- [Push Artifacts](https://docs.microsoft.com/en-us/azure/devops/pipelines/publish-pipeline-artifact)

Close the "Push to Artifact Store" feature.


### Next Steps

Testing and Release processes become highly customized beyond this point, so it isn't easy to demonstrate anything both generic and meaningful. Read up on some further documentation of pipeline
tasks to see how these builds and releases could be put together:

- [Pipeline Tasks](https://docs.microsoft.com/en-us/azure/devops/pipelines/tasks/)
