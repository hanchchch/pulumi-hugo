---
title: ".NET Core (C#, VB, F#)"
meta_desc: An overview of how to use .NET languages like C# and F# for infrastructure as code
           on any cloud (AWS, Azure, GCP, Kubernetes, etc.).
menu:
  intro:
    parent: languages
    weight: 3

aliases: ["/dotnet/"]
---

<img src="/logos/tech/dotnet.png" align="right" width="150" style="padding:8px; margin-top: -64px">

Pulumi supports infrastructure as code using any .NET Core language. You can use your favorite .NET tools &mdash; such as editors, package managers, build systems, and test frameworks &mdash; to create, deploy, and manage infrastructure on any cloud, including Azure, AWS, and Google Cloud.

<a class="btn" href="https://dotnet.microsoft.com/download" target="_blank" title="Install .NET Core">Install .NET Core</a>

## Getting Started

The fastest way to get up and running is to choose from one of the following Getting Started guides:

<div class="tiles mt-4">
    <div class="flex-1 pb-4 md:mr-4">
        <a class="tile p-4" href="{{< relref "/docs/get-started/aws" >}}?language=csharp">
            <img class="h-8 mx-auto" src="/logos/tech/aws.svg" alt="AWS">
        </a>
    </div>
    <div class="flex-1 pb-4 md:mr-4">
        <a class="tile p-4" href="{{< relref "/docs/get-started/azure" >}}?language=csharp">
            <img class="h-8 mx-auto" src="/logos/tech/azure.svg" alt="Azure">
        </a>
    </div>
    <div class="flex-1 pb-4 md:mr-4">
        <a class="tile p-4" href="{{< relref "/docs/get-started/gcp" >}}?language=csharp">
            <img class="h-8 mx-auto" src="/logos/tech/gcp.svg" alt="Google Cloud">
        </a>
    </div>
    <div class="flex-1 pb-4">
        <a class="tile p-4" href="{{< relref "/docs/get-started/kubernetes" >}}?language=csharp">
            <img class="h-8 mx-auto" src="/logos/tech/k8s.svg" alt="Kubernetes">
        </a>
    </div>
</div>

{{% notes "info" %}}
The Getting Started guides only demonstrate C#, but will also work for F# and Visual Basic.
{{% /notes %}}

## Prerequisites

Before using Pulumi for .NET, you will need to install both Pulumi and .NET Core SDK 3.1 or later. If you follow the Getting Started guides above, they will walk you through doing this.

1. [Install Pulumi]({{< relref "/docs/get-started/install" >}})
1. [Install .NET Core SDK 3.1](https://dotnet.microsoft.com/download)

## Example

{{% choosable language csharp %}}
For example, this C# program provisions an Azure resource group and storage account:

```csharp
using System.Threading.Tasks;

using Pulumi;
using Pulumi.Azure.Core;
using Pulumi.Azure.Storage;

class Program
{
    static Task<int> Main() => Deployment.RunAsync<MyStack>();
}

public MyStack : Stack
{
    public MyStack()
    {
        // Create an Azure Resource Group
        var resourceGroup = new ResourceGroup("resourceGroup");

        // Create an Azure Storage Account
        var storageAccount = new Account("storage", new AccountArgs
        {
            ResourceGroupName = resourceGroup.Name,
            AccountReplicationType = "LRS",
            AccountTier = "Standard",
        });

        // Export the connection string for the storage account
        this.ConnectionString = storageAccount.PrimaryConnectionString;
    }

    [Output] public Output<string> ConnectionString { get; set; }
}
```

{{% /choosable %}}

{{% choosable language fsharp %}}
For example, this F# program provisions an Azure resource group and storage account:

```fsharp
module Program

open Pulumi.FSharp
open Pulumi.Azure.Core
open Pulumi.Azure.Storage

let infra () =
    // Create an Azure Resource Group
    let resourceGroup = new ResourceGroup "resourceGroup"

    // Create an Azure Storage Account
    let storageAccount =
        new Account("storage",
            new AccountArgs
               (ResourceGroupName = io resourceGroup.Name,
                AccountReplicationType = input "LRS",
                AccountTier = input "Standard"))

    // Export the connection string for the storage account
    dict [("connectionString", storageAccount.PrimaryConnectionString :> obj)]

[<EntryPoint>]
let main _ =
  Deployment.run infra
```

{{% /choosable %}}

{{% choosable language visualbasic %}}
For example, this Visual Basic program provisions an Azure resource group and storage account:

```vb
Imports System.Threading.Tasks
Imports Pulumi
Imports Pulumi.Azure.Core
Imports Pulumi.Azure.Storage

Module Program
    Public Function Run() As IDictionary(Of String, Object)
        ' Create an Azure Resource Group
        Dim resourceGroup = New ResourceGroup("resourceGroup")

        ' Create an Azure Storage Account
        Dim storageAccount = New Account("storageAccount", New AccountArgs With {
            .ResourceGroupName = resourceGroup.Name,
            .AccountReplicationType = "LRS",
            .AccountTier = "Standard"
        })

        ' Export the connection string for the storage account
        Return New Dictionary(Of String, Object) From {
            {"connectionString", storageAccount.PrimaryConnectionString}
        }
    End Function

    Sub Main()
        Deployment.RunAsync(AddressOf Run).Wait()
    End Sub

End Module
```

{{% /choosable %}}

## C\#, F\#, and VB Templates

As of version 1.5, Pulumi supports .NET Core 3.1. You can write Pulumi programs in your favorite .NET language to get additional verification and tooling benefits. The fastest way to get started is to use a template. The template will autogenerate a set of files and initialize a Pulumi project. The getting started guides shown above will help do this on your cloud of choice, but this section describes doing so independently.

{{< chooser language "csharp,fsharp,visualbasic" >}}

{{% choosable language csharp %}}
You can write Pulumi programs in **C#**. From an empty directory, create a new C# project:

```bash
$ mkdir myproject && cd myproject
$ pulumi new csharp
```

This will create a `Pulumi.yaml` [project file]({{< relref "../concepts/project" >}}) containing some minimal metadata about your project (including a name and description which you may wish to change), an `myproject.csproj` file that holds references used by the project, a `Program.cs` file, containing the program entry point, and a `MyStack.cs` file with resource definitions. The name of the directory is used as the project name in `Pulumi.yaml` and as the `csproj` file name.

To deploy your infrastructure run `pulumi up` and the Pulumi engine automatically runs `dotnet build` as part of the deployment. Pulumi will perform the operations needed to deploy the infrastructure you have declared.

This `csharp` template is cloud agnostic, and you will need to install NuGet packages for the cloud provider of your choice. Additional templates are available:

* `pulumi new aws-csharp`: creates a starter AWS C# project
* `pulumi new azure-csharp`: creates a starter Azure C# project
* `pulumi new gcp-csharp`: creates a starter Google Cloud C# project
{{% /choosable %}}

{{% choosable language fsharp %}}
You can write Pulumi programs in **F#**. From an empty directory, create a new F# project:

```bash
$ mkdir myproject && cd myproject
$ pulumi new fsharp
```

This will create a `Pulumi.yaml` [project file]({{< relref "../concepts/project" >}}) containing some minimal metadata about your project (including a name and description which you may wish to change), an `myproject.fsproj` file that holds references used by the project, and a `Program.fs` file, containing your program. The name of the directory is used as the project name in `Pulumi.yaml` and as the `fsproj` file name.

To deploy your infrastructure run `pulumi up` and the Pulumi engine automatically runs `dotnet build` as part of the deployment. Pulumi will perform the operations needed to deploy the infrastructure you have declared.

This `fsharp` template is cloud agnostic, and you will need to install NuGet packages for the cloud provider of your choice. Additional templates are available:

* `pulumi new aws-fsharp`: creates a starter AWS F# project
* `pulumi new azure-fsharp`: creates a starter Azure F# project
* `pulumi new gcp-fsharp`: creates a starter Google Cloud F# project
{{% /choosable %}}

{{% choosable language visualbasic %}}
You can write Pulumi programs in **Visual Basic**. From an empty directory, create a new Visual Basic project:

```bash
$ mkdir myproject && cd myproject
$ pulumi new visualbasic
```

This will create a `Pulumi.yaml` [project file]({{< relref "../concepts/project" >}}) containing some minimal metadata about your project (including a name and description which you may wish to change), an `myproject.vbproj` file that holds references used by the project, a `Program.vb` file, containing the program entry point, and a `MyStack.vb` file with resource definitions. The name of the directory is used as the project name in `Pulumi.yaml` and as the `vbproj` file name.

To deploy your infrastructure run `pulumi up` and the Pulumi engine automatically runs `dotnet build` as part of the deployment. Pulumi will perform the operations needed to deploy the infrastructure you have declared.

This `visualbasic` template is cloud agnostic, and you will need to install NuGet packages for the cloud provider of your choice. Additional templates are available:

* `pulumi new aws-visualbasic`: creates a starter AWS Visual Basic project
* `pulumi new azure-visualbasic`: creates a starter Azure Visual Basic project
* `pulumi new gcp-visualbasic`: creates a starter Google Cloud Visual Basic project
{{% /choosable %}}

{{< /chooser >}}

## .NET Core Tools

Pulumi packages are distributed on [NuGet for download](https://www.nuget.org/packages?q=pulumi).

Although you can use any editor, [Visual Studio Code](https://code.visualstudio.com/download), [Visual Studio](https://visualstudio.microsoft.com/downloads/), or [Rider](https://www.jetbrains.com/rider/) will deliver full tooling support for .NET Core out-of-the-box, including auto-completion, red error markers and build errors.

![VSCode](/images/docs/quickstart/vscode-dotnet.png)

## Pulumi Programming Model

The Pulumi programming model defines the core concepts you will use when creating infrastructure as code programs using
Pulumi. [Architecture & Concepts]({{< relref "/docs/intro/concepts" >}}) describes these concepts
with examples available in Python. These concepts are made available to you in the Pulumi SDK.

The Pulumi SDK is available to .NET Core developers as a Nuget package. To learn more,
[refer to the Pulumi SDK Reference Guide](/docs/reference/pkg/dotnet/Pulumi/Pulumi.html).

The Pulumi programming model includes a core concept of `Input` and `Output` values, which are used to track how outputs of one resource flow in as inputs to another resource.  This concept is important to understand when getting started with .NET and Pulumi, and the [Inputs and Outputs]({{< relref "/docs/intro/concepts/inputs-outputs" >}}) documentation is recommended to get a feel for how to work with this core part of Pulumi in common cases.

## Continuous Delivery

In addition to the CLI-driven workflows shown above, you can continuously deploy your infrastructure using .NET by integrating with your CI/CD provider of choice. This ensures automated deployments triggered by events such as commits to your Git repo.

### Azure DevOps Pipelines

<img src="/logos/tech/azuredevops.png" align="right" width="100" style="padding:0 0 16px 32px">

Pulumi can deploy infrastructure changes from your Azure DevOps Pipelines. This enables easy integration with your existing automation while using .NET Core for your infrastructure as code, leveraging the Pulumi task in the Visual Studio Marketplace.

To learn more, [see the Pulumi Azure DevOps user guide]({{< relref "/docs/guides/continuous-delivery/azure-devops" >}}).

### GitHub Actions

<img src="/logos/tech/githubactions.png" align="right" width="120" style="padding:0 0 16px 32px">

Pulumi can deploy infrastructure using GitHub Actions, making Git-driven deployments of your infrastructure as code straightforward. To learn more, [see the Pulumi GitHub Actions user guide]({{< relref "/docs/guides/continuous-delivery/github-actions" >}}).

There is also a [Pulumi GitHub App]({{< relref "/docs/guides/continuous-delivery/github-app" >}}) that integrates with Pull Requests so that you get previews of deployments before they are merged inline in your PRs where it's easy to comment and collaborate.

### Other CI/CD Integrations

If you don't use Azure DevOps or GitHub Actions, Pulumi also supports a number of other [CI/CD integrations]({{< relref "/docs/guides/continuous-delivery" >}}).
