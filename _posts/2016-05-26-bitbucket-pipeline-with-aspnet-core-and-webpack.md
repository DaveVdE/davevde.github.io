---
layout: post
title: BitBucket Pipelines + ASP.NET Core: adding webpack to the mix
date: '2016-05-26T18:08:00.000+02:00'
author: Dave Van den Eynde
tags: 
---
Yesterday I blogged about using [BitBucket Plugins](https://bitbucket.org/product/features/pipelines") with
ASP.NET Core and how you can use Microsoft's public Docker image to automate your builds.

Not completely satisfied, I wanted to have my client assets built using webpack too this way. My first attempt
was doing something crazy like this:

```yml
pipelines:
  default:
    - step:
        script:
          - curl -SLO "https://nodejs.org/dist/v0.10.45/node-v0.10.45-linux-x64.tar.gz"
          - tar -zxvf "node-v0.10.45-linux-x64.tar.gz" -C /usr/local --strip-components=1
          - rm "node-v0.10.45-linux-x64.tar.gz"
          - npm install -g webpack
          - cd MyProject
          - npm update
          - webpack
          - cd ..
          - dotnet restore
          - dotnet build MyProject
```

Not pretty but it worked. It installs Node and webpack during build time. That's too slow. So I learned a thing or 
two about Docker (it's not horrible after all) and came up with [my own Docker image](https://hub.docker.com/r/davevde/dotnet-webpack/)
that includes the .NET Core 1.0 SDK (currently at RC2) and Node and webpack. So now my Pipelines config looks like this:

```yml
image: davevde/dotnet-webpack

pipelines:
  default:
    - step:
        script:
          - cd MyProject
          - npm update
          - webpack
          - cd ..
          - dotnet restore
          - dotnet build MyProject
```

There's probably still room for improvement, but it works! Never too old to learn...

