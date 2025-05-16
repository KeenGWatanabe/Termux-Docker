# Termux-Docker Manually trigger the workflow in GitHub to run Docker commands remotely.
 GitHub Actions workflow YAML file to run your 4 Docker commands:
Here's the corrected and improved GitHub Actions workflow YAML file to run your 4 Docker commands:

```yaml
name: Build and Push to ECR

on: [workflow_dispatch]

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v2
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: us-east-1

      - name: Login to Amazon ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v1

      - name: Build Docker image
        run: docker build -t nodejs-app .

      - name: Tag Docker image
        run: |
          docker tag nodejs-app:latest 255945442255.dkr.ecr.us-east-1.amazonaws.com/nodejs-app:latest

      - name: Push Docker image
        run: |
          docker push 255945442255.dkr.ecr.us-east-1.amazonaws.com/nodejs-app:latest
```

### Key Improvements:

1. **Added AWS Authentication**:
   - Uses the official AWS credentials action
   - Stores sensitive AWS keys in GitHub Secrets (more secure than hardcoding)

2. **Better ECR Login**:
   - Uses the dedicated `amazon-ecr-login` action instead of manual CLI login

3. **Separate Steps**:
   - Each major operation is now its own step for better logging and debugging

4. **Code Checkout**:
   - Added checkout step to ensure your code is available for the build

### Before Using This Workflow:

1. Set up these GitHub Secrets in your repository:
   - `AWS_ACCESS_KEY_ID`
   - `AWS_SECRET_ACCESS_KEY`

2. Ensure your AWS IAM user has permissions to:
   - ECR: GetAuthorizationToken, Push/Pull images
   - (Optional) ECR repository creation rights if the repo doesn't exist

3. Make sure your Dockerfile is in the root of your repository or adjust the build context path accordingly.

This workflow will:
1. Authenticate with AWS
2. Log in to ECR
3. Build your Docker image
4. Tag it properly
5. Push it to your ECR repository

The workflow can be triggered manually (via workflow_dispatch) or you can modify the `on:` section to trigger on other events like pushes to certain branches.
