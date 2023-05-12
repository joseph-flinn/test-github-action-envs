# Test GitHub Environments

## Testing

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
3. grab the forwarding urls and reset the `HOSTING_URL` secret in the respective GitHub Environment
4. Run the `CI/CD` pipeline
    - Validate that the staging server is hit immediately from the pipeline
    - Validate that the production server requires approval before connecting to the server
