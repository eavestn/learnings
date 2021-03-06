# Working With Azure App Services

## Introduction

### Purpose

Finding good, comprehensive documentation on errors commonly associated with deploying a .NET-based application to Azure App Services is hard. The below document is intended to help a developer working to deploy applications to Azure App Services using Azure DevOps Pipelines or Releases. This document assumes an eventual architecture for both building and deploying your ASP .NET Web Application to Azure App Services.

### Document Scope

#### Assumptions

You've got a correctly configured App Service with necessary RGs and SCs and are familiar with how those things work

#### .NET Applications

At this stage, this document only accounts for attempting to deploy a .NET-based web distributable to an Azure App Service. 

### Information Quality

The information in this document is largely anecdotal - the result of the hours spent attempting to repurpose and deploy a legacy solution using Azure DevOps pipelines and releases. .NET applications are - despite using Microsoft's flagship framework - infamous for their [myriad, poorly-documented problems while being deployed to App Services](http://www.herlitz.nu/2020/01/10/web.config-transforms-not-working-in-azure-devops-pipeline/) (2020):

> This is a problem with many dimensions and can be caused by different misconfigurations. In this blog post, I will walk through the most common errors and how to fix them.

However, this document may not commonly cite its source.

## Architecture

### Architecture Motivation

Easiest to do (this document does not cover any of the necessary configuration of the app service you might need to make)

### High Level

<img align="left" width="500" height="200" src="./assets/azure-pipelines.png">

This diagram is a very crude representation of the high-level archicture of our deployment strategy: circles represent artifacts or environments; rectangles different "actions" performed by the pipeline. It is _very_ simple: [**1**] pull a copy of the code from the repository; [**2**] build the solution; and [**3**] drop the package in a location to be used by later stages.

[**4**] The Release then picks up the package and [**5**] deploys it to the environment for which the Release is responsible. Transitions between various steps in this pipeline are "triggered" - or will happenly automatically. 

### Steps (Outline; Brief Description)

#### Service Connections


### Head Nod to YAML


## Common Problems

### Project Configuration

#### `Web.config`, `Web.*.config`, and `.csprog` Files

Unfortunately, deploying a .NET-based application to Azure App Services may require interfering with default or Visual Studio-generated project configurations, structures, and relationships. Most often, project configuration changes need to happen in or to _both_ the `*.config` and `.csproj` files.

##### The Difference Between `<Content />` and `<None />` 

:warning: This is important due to Azure Pipeline's packaging of the distributable.

[You can get pretty nerdy with this, pretty quick](https://stackoverflow.com/questions/41754602/whats-the-difference-between-content-and-none-when-always-copy-to-output-direc); however, a superficial understanding should suffice for getting you unstuck. The purpose of each tag as broken down by [Microsoft](https://docs.microsoft.com/en-us/visualstudio/msbuild/common-msbuild-project-items?view=vs-2019#none):

| Config. Tag | Micrsoft Purpose Description |
| :----       | :---- |
| `None`      | Represents files that should have no role in the build process. |
| `Content`   | Represents files that are not compiled into the project, but may be embedded or published together with it. |

`None` makes more sense in relationship to `Content`. `Content` is a file that has no influence on the build process of the project (it's not compiled _into_ the executable artifact); however, it may be included in the output of the build. `None`is literally "none": it doesn't do anything or need to go anywhere once the project is ready for distribution. A great example of `None` would be a `README.md` file or a `.gitignore` file.

:heavy_check_mark: **TLDR;** Checking that you are using the right tag for your `Web.*.config` files is the first step to resolving frequently-encountered Azure Pipeline errors. Keep reading to learn more about which tag should be used and how it should be used.

##### Apparent Default Impact on Azure Pipelines 

Both `None` and `Content` support a series of configurable attributes that can be included in the `.csproj` XML; however, it appears most challenges occur when considering the stated purposes' (_table above_) impact on an Azure Pipeline build. Said differently: `None` and `Content`'s purposes contribute to your Azure Pipeline's success (go figure).

By default, files marked with `Content` _are included with the output of the Azure Pipeline build process_. `Content`-tagged resources will appear in the destination App Service.

Files marked with `None` _don't do anything_. These files will not appear in the destination App Service. Additionally, any releationship expressed as a child of `None` seems to cause Azure DevOps Pipelines to complain (the errors for which are completely misleading and absolutely unrelated; the generated error - for which there is _no_ documentation on the internet other than sparse foreign-language forums - leads the reader to believe the issue is Git and image related).

Our `Web.*.config` files fall into this category: files we - ultimately - want distributed as a part of the deployment _and_ that should have no impact on the build, itself.

:heavy_check_mark: **TLDR**; don't use `None` for resources you either [1] want to include in the build output or [2] with to express a relationship. Use `<Content />`.

##### `<Content />`: To Add Relationships Or Not

The motivation for moving our build and publication to the cloud is to completely divesting Visual Studio of its responsibility for building and deploying an application to a higher environment. We no longer want to do this manually - or, ideally, will no longer _need_ to (outside of local development).

There is "debate" (or, rather, contradicting resources) on the interwebs: we know _not_ to use `None`; but what tag should we use and how should we use it?

Some articles say that `<Content />` tags should be used in addition to expressing relationships between various `<Content />`-tagged resources:

```xml
  <Content Include="Web.Dev.config">
	  <DependentUpon>Web.config</DependentUpon>
  </Content>
```

The result is certainly a cleaner experience in the Visual Studio IDE; however, it is unclear whether Azure DevOps actually supports these relationships. The relationship being expressed here is that `Web.Dev.config` needs to be applied to (merged with) `Web.config`. 

However, what _is_ clear is that we can do these transformations using Azure DevOps Pipelines and do not half to rely upon an expressed relationship to do so (to be covered later). All you need to do is list each `*.config` file as its own `<Content />` tag (self closing is fine; order does not matter). Again, as we are taking away the relationship-expression resonsibilty from the IDE, we will fully rely upon Azure DevOps Pipelines and Releases for application of the configurations:

```xml
	<Content Include="Web.config"></Content>
	<Content Include="Web.Dev.config"></Content>
```

:heavy_check_mark: **TLDR;** Do not express `<DependentUpon />` relationships in your `<Content />` XML nodes.



