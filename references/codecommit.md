# AWS CodeCommit Reference

AWS CodeCommit is a secure, highly scalable, managed Git source control service.

## CodeCommit Setup Commands

### Create Repository

```bash
# Create new repository
aws codecommit create-repository \
  --repository-name <PROJECT_NAME> \
  --repository-description "Project <PROJECT_NAME>"

# List repositories
aws codecommit list-repositories

# Get repository URL
aws codecommit get-repository \
  --repository-name <PROJECT_NAME>
```

### Git Configuration

```bash
# Configure Git credential helper
git config --global credential.helper "!aws codecommit credential-helper $@"
git config --global credential.UseHttpPath true

# Add remote
git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/<PROJECT_NAME>

# Push to CodeCommit
git push -u origin main

# Push new branch
git push -u origin <branch-name>
```

### IAM Permissions

Required IAM policy for CodeCommit:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:CreateRepository",
        "codecommit:GetRepository",
        "codecommit:ListRepositories"
      ],
      "Resource": "*"
    }
  ]
}
```

For existing repository:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "codecommit:GitPull",
        "codecommit:GitPush"
      ],
      "Resource": "arn:aws:codecommit:us-east-1:<account-id>:<repository-name>"
    }
  ]
}
```

## HTTPS vs SSH

### HTTPS (Recommended)
```bash
# Configure credential helper
git config --global credential.helper "!aws codecommit credential-helper $@"

# No SSH key setup needed
```

### SSH
```bash
# Generate SSH key
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"

# Add public key to IAM
aws iam upload-ssh-public-key --ssh-public-key-body "$(cat ~/.ssh/id_rsa.pub)"

# Get SSH key ID
aws iam get-ssh-public-key --ssh-public-key-id <key-id> --status Active
```

## Branch Management

```bash
# Create feature branch
git checkout -b 001-feature-name

# Push branch
git push -u origin 001-feature-name

# Switch branch
git checkout main
git pull

# List branches
git branch -a
```

## Spec-Kit + CodeCommit Workflow

Since spec-kit requires git, use CodeCommit as the remote:

```bash
# 1. Create CodeCommit repo
aws codecommit create-repository --repository-name my-app --repository-description "My App"

# 2. Initialize locally
mkdir my-app && cd my-app
git init

# 3. Configure remote
git remote add origin https://git-codecommit.us-east-1.amazonaws.com/v1/repos/my-app

# 4. Create initial commit
echo "# My App" > README.md
git add . && git commit -m "Initial commit"

# 5. Push to CodeCommit
git push -u origin main

# 6. Continue with spec-kit (it will use existing git repo)
# spec-kit will create branches like 001-feature-name automatically
```
