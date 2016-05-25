---
layout: post
title: Using Bitbucket Pipelines with ASP.NET Core
date: '2016-05-25T10:00:00.000+02:00'
author: Dave Van den Eynde
tags: 
---
Yesterday Atlassian announced [BitBucket Plugins](https://bitbucket.org/product/features/pipelines"), 
a new feature of BitBucket that will replace Bamboo Cloud (which has an EOL of 2017). 
I was immediately drawn because of three reasons:

1. We use BitBucket as our source control host
2. I like the idea of having my pull requests built and tested before I decide to merge
3. I never really got the integration to work with TeamCity so that it reported back succesful builds

Bamboo never worked for me either as we're building an ASP.NET Core app (it's a brave new world) so when
Pipelines was announced I immediately signed up for the beta, got the invite two minutes later and ...

... discovered that it doesn't support .NET Core "out of the box".

Which is fine, it's in beta but moreso because a quick mail to their staff got me an even faster response
that it's all Docker anyway and that there's an [offical Docker image for .NET Core](https://hub.docker.com/r/microsoft/dotnet/).

Long story short, this is what my bitbucket-pipelins.yml file looks like:

```yml
image: microsoft/dotnet

pipelines:
  default:
    - step:
        script:
          - dotnet restore
          - dotnet build ProjectName
```

This causes `dotnet` to be run in the root of your working copy causing all packages to be restored for all projects 
(and `project.json.lock` to be written...) and then build the project in folder `ProjectName` (obviously a placeholder 
for you, my dear reader).

Now only if I could get it to run NPM...


