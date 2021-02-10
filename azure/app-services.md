# Working With Azure App Services

## Introduction

### Purpose

The below document is intended to help a developer working to deploy applications to Azure App Services using Azure DevOps Pipelines or Releases.

### Document Scope

#### .NET Applications

At this stage, this document only accounts for attempting to deploy a .NET-based web distributable to an Azure App Service. 

### Information Quality

The information in this document is largely anecdotal - the result of the hours spent attempting to repurpose and deploy a legacy solution using Azure DevOps pipelines and releases. .NET applications are - despite using Microsoft's flagship framework - infamous for their [myriad, poorly-documented problems while being deployed to App Services](http://www.herlitz.nu/2020/01/10/web.config-transforms-not-working-in-azure-devops-pipeline/) (2020):

> This is a problem with many dimensions and can be caused by different misconfigurations. In this blog post, I will walk through the most common errors and how to fix them.

However, this document may not commonly cite its source.

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

##### Apparent Default Impact on Azure Pipelines 

Both `None` and `Content` support a series of configurable attributes that can be included in the `.csproj` XML; however, it appears most challenges occur when considering the stated purposes' (_table above_) impact on an Azure Pipeline build. Said differently: `None` and `Content`'s purposes contribute to your Azure Pipeline's success (go figure).

By default, files marked with `Content` _are included with the output of the Azure Pipeline build process_. 

Files marked with `None` _don't do anything_.

