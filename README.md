# ghcr-basic
Setup for building Github Container Registry pipelines

#### 1. Generate Access Token
<b> Access Token </b> is required to login to the the ghcr.io registry before you can push the built dockerfile image.<br/>
Go to `Profile Settings > Developer Settings > Personal Access Tokens > Tokens (Classic) > Generate New Token (Classic)` to generate the token. <br/>
Remember to copy the Access Token to the clipboard before it's lost (token value would be lost forever once you close the page) </br>

#### 2.Add the token ave it as a secret in your repository
Copy the token and save it as a secret in your repository. <br/>
`[your-repository] > Settings > Security > Secrets and Variables > Actions > Repository Secrets > New Repository Secret`  </br>
Set the SECRET_NAME and add the Access_Token generate in last step.<br/>

#### 3. Add the dockerfile for your project
The dockerfile added to the project would be used to create the container image (used in the cloud servers, e.g. AWS instances) </br>
An example codebase and dockerfile would be: </br>
`entrypoint.sh` (Codebase)
```
#!/bin/bash
echo "Hello world! You are testing docker builds and pipelines!"
```
`dockerfile` for this code would be
```
FROM alpine
RUN apk add --no-cache bash
WORKDIR /app
COPY entrypoint.sh .
RUN chmod +x entrypoint.sh
ENTRYPOINT ["/app/entrypoint.sh"]
```

#### 4. Setup the Github Actions
Setup the Github Actions to automatically build the docker image and push it to the ghcr.io repository
`[your-repository] > Actions >  Set up a workflow yourself`
Add the docker commands you'll generally use to build and publish images: 
```
name: Docker Image CI for GHCR

on:
  push

jobs:
  build_and_publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build and push docker image
        run: |
          docker login -u [github_userid] -p ${{ secrets.[your_project_secret_key] }} ghcr.io
          docker build . --tag ghcr.io/[github_userid]/[your_project_image_name]:latest
          docker push ghcr.io/[github_userid]/[your_project_image_name]:latest

```
#### Save/commit the Workflow
Commit the workflow file and check the Actions tab. The docker build_and_publish workflow will now execute.!
