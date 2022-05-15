---
title: To dockerize a visual studio project
author: ianpowell
date: 2021-12-05 14:13:00 +0100
categories: [docker]
tags: [aspnet]
---

If creating a new project, then `Enable docker support` during project creation

OR

1. Create a file in the project root called `Dockerfile` (no extension)
2. Inside the file

```dockerfile
FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /build
COPY . .
RUN dotnet restore
RUN dotnet publish -c Release -o output

FROM microsoft/aspnetcore:2.0
WORKDIR /app
COPY --from=build /build/output .
ENTRYPOINT ["dotnet", "ConferenceApp.dll"]
```

To build image (from project root)
: `docker build -t conference/api .`

To remove the image
: `docker system prune`
    
    * All Stopped containers
    * All networks not used by at least one container
    * All dangling images
    * All build cache


To run a docker image
: `docker run -d —name {chosen name} {image name:tag}`

If an image has exited with a status code of other than 0, then it has not exited properly, perhaps there’s an error
: `docker logs {container name}`
