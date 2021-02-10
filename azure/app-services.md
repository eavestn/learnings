# Working With Azure App Services

## Introduction

### Purpose

The below document is intended to help a developer working to deploy applications to Azure App Services using Azure DevOps Pipelines or Releases.

### Document Scope

#### Legacy .NET Applications

At this stage, this document only accounts for attempting to deploy a new distributable to an Azure App Service. 

### Information Quality

The information in this document is largely anecdotal - the result of the hours spent attempting to repurpose and deploy the legacy _CapCenter.OneSiteServer_ solution using Azure DevOps pipelines and releases. .NET applications are - despite using Microsoft's flagship framework - infamous for their [myriad, poorly-documented problems while being deployed to App Services](http://www.herlitz.nu/2020/01/10/web.config-transforms-not-working-in-azure-devops-pipeline/) (2020):

> This is a problem with many dimensions and can be caused by different misconfigurations. In this blog post, I will walk through the most common errors and how to fix them.

However, this document will not commonly site its source.

## Common Problems

### Project Configuration

#### `Web.config`, `Web.*.config`, and `.csprog` Files

Unfortunately, deploying a .NET-based application to Azure App Services may require interfering with default or Visual Studio-generated project configurations, structures, and relationships. Most often, project configuration changes need to happen in or to _both_ the `*.config` and `.csproj` files.

##### The Difference Between `<Content />` and `<None />` 

This is important due to Azure Pipeline's packaging of the distributable.

