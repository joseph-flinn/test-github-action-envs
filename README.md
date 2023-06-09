# Test GitHub Environments

## Flow Design

- Every commit to `main` auto deploys to the `staging` environment
- Every build asset gets a generated version from the baseVersion in `version.json`
- Assets are rebuilt and retested while deploying to production (we can deploy to staging here too if we want)
    - If building to deploy to staging, the short commit hash is used for the version suffix
    - If building to deploy to production, the datetime stamp is used for the version suffix
- A tag is created, detached from `main` which includes the released version with datetime stamp
- The Production tagging/release
    - is triggered manaully with a `workflow_dispatch` event
    - deploy step is protected with a manual approval setup on the GitHub Environment protection rules


## Testing

### Requirements
- pipenv
- ngrok

### Setup
```
pipenv install
```

### Process
1. start two small http servers
    ```
    # In one shell:
    pipenv shell
    uvicorn simple-server:app --port 8001

    # In another shell:
    pipenv shell
    uvicorn simple-server:app --port 8002
    ```
2. start an ngrok tunnel for each port (in two more shells)
    ```
    # In one shell:
    ngrok http 8001

    # In another shell:
    ngrok http 8002
    ```
3. grab the forwarding urls and reset the `HOSTING_URL` secret in the respective GitHub Environments
4. Validate `CI/CD - Staging`
    - push a commit to `main`
    - validate that the staging server is hit immediately
        - seeing the HTTP request come through on the server
        - seeing "pong" in the "Deploy to: staging" step in the "Deploy to staging" job
5. Validate `CI/CD - Production`
    - Manually trigger the pipeline
    - validate that the production server requires approval
        - validate approval
        - seeing the HTTP request come through on the server
        - seeing "pong" in the "Deploy to: staging" step in the "Deploy to staging" job
