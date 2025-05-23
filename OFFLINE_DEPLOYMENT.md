# Offline Deployment Guide

This guide explains how to deploy LightRAG in an offline environment where external CDN resources are not accessible.

## Background

By default, LightRAG's Swagger UI interface loads CSS and JavaScript files from CDN (cdn.jsdelivr.net). In offline environments, this can cause issues with the documentation interface not loading properly.

## Solution

We've modified the application to use local static files for Swagger UI instead of loading them from CDN. The modified Docker image includes:

1. Local copies of Swagger UI files (`swagger-ui.css`, `swagger-ui-bundle.js`, and `swagger-ui-standalone-preset.js`)
2. A custom Swagger UI HTML template
3. Configuration to serve these files from the `/static` endpoint

## Manual Download Instructions

If you need to download the Swagger UI files manually:

1. Create the directory structure:
   ```bash
   mkdir -p lightrag/api/static/swagger-ui-dist
   ```

2. Download the required files:
   ```bash
   # Download CSS file
   curl -L https://cdn.jsdelivr.net/npm/swagger-ui-dist@5/swagger-ui.css -o lightrag/api/static/swagger-ui-dist/swagger-ui.css
   
   # Download main JavaScript bundle
   curl -L https://cdn.jsdelivr.net/npm/swagger-ui-dist@5/swagger-ui-bundle.js -o lightrag/api/static/swagger-ui-dist/swagger-ui-bundle.js
   
   # Download standalone preset JavaScript
   curl -L https://cdn.jsdelivr.net/npm/swagger-ui-dist@5/swagger-ui-standalone-preset.js -o lightrag/api/static/swagger-ui-dist/swagger-ui-standalone-preset.js
   ```

3. Verify the files are downloaded correctly:
   ```bash
   ls -la lightrag/api/static/swagger-ui-dist/
   ```

## Building the Docker Image

To build the Docker image for offline deployment:

```bash
# Clone the repository (if you haven't already)
git clone https://github.com/hkuds/LightRAG.git
cd LightRAG

# Build the Docker image
docker-compose build
```

## Running in Offline Mode

Once the image is built, you can run it in an offline environment:

```bash
# Start the container
docker-compose up -d
```

## Verification

After starting the container, verify that the Swagger UI is working correctly:

1. Open a web browser and navigate to `http://localhost:9621/docs`
2. The Swagger UI interface should load properly without any network errors
3. You should be able to interact with the API documentation

## Troubleshooting

If you encounter any issues:

1. Check the container logs: `docker-compose logs`
2. Verify that the static files exist in the container:
   ```bash
   docker exec -it lightrag ls -la /app/lightrag/api/static/swagger-ui-dist
   ```
3. Make sure the custom Swagger UI HTML template exists:
   ```bash
   docker exec -it lightrag cat /app/lightrag/api/static/swagger-ui.html
   ```

## Additional Notes

- This modification only affects the Swagger UI documentation interface
- The core functionality of LightRAG remains unchanged
- If you need to update the Swagger UI version, you'll need to download the new files and rebuild the Docker image 