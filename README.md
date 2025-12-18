# GitHub Private Repository Access with SSH Key in Jenkins

This guide explains how to access a private GitHub repository using SSH keys in Jenkins.

## Overview

This tutorial walks you through the complete process of setting up SSH key authentication to access private GitHub repositories from Jenkins.

```mermaid
flowchart TD
    A[Start] --> B[Generate SSH Key Pair]
    B --> C[Add Public Key to GitHub]
    C --> D[Add Private Key as Credential in Jenkins]
    D --> E[Configure Jenkins Job with SSH URL]
    E --> F[Select Credential from Dropdown]
    F --> G[Build and Verify]
    G --> H[Success: Access Private Repo]
    
    style A fill:#e1f5ff
    style H fill:#d4edda
    style B fill:#fff3cd
    style C fill:#fff3cd
    style D fill:#fff3cd
    style E fill:#fff3cd
```

## Architecture Diagram

```mermaid
graph LR
    subgraph Local["Local Machine"]
        A[SSH Keygen] --> B[Private Key<br/>jenkins_github_key]
        A --> C[Public Key<br/>jenkins_github_key.pub]
    end
    
    subgraph GitHub["GitHub"]
        C --> D[SSH Keys Settings]
        D --> E[Private Repository]
    end
    
    subgraph Jenkins["Jenkins"]
        B --> F[Credentials Store]
        F --> G[Jenkins Job]
        G --> E
    end
    
    style A fill:#e1f5ff
    style B fill:#ffcccc
    style C fill:#ccffcc
    style E fill:#ffffcc
    style G fill:#ccccff
```

## Prerequisites

- GitHub account with a private repository
- Jenkins instance installed and running
- Command line access to your local machine (Windows, Linux, or macOS)

## Platform-Specific Notes

| Platform | SSH Key Location | Terminal | Text Editor Options |
|----------|-----------------|----------|---------------------|
| **Windows** | `C:\Users\Username\.ssh\` | Command Prompt, PowerShell | Notepad++, Notepad |
| **Linux** | `~/.ssh/` or `/home/username/.ssh/` | Terminal (bash, zsh, etc.) | nano, vim, gedit, cat |
| **macOS** | `~/.ssh/` | Terminal (bash, zsh) | nano, vim, TextEdit, cat |

> **Note**: All platforms use the same `ssh-keygen` command. The process is identical across platforms, only the file paths and editor preferences differ.

## Step 1: Generate SSH Key Pair

On your local system, SSH keys are stored in your user's `.ssh` folder:
- **Windows**: `C:\Users\YourUsername\.ssh\`
- **Linux/macOS**: `~/.ssh/` or `/home/username/.ssh/`

![SSH Key Generation](screenshots/step1-ssh-keygen.png)

### Windows
1. Open **Command Prompt** or **PowerShell**
2. Navigate to your user directory (optional, keys will be created in `.ssh` folder automatically)
3. Run the following command:

```bash
ssh-keygen
```

### Linux/macOS
1. Open **Terminal**
2. The `.ssh` folder will be created automatically in your home directory
3. Run the following command:

```bash
ssh-keygen
```

### For Both Platforms
3. When prompted, provide a filename for your key (e.g., `jenkins_github_key`)
4. Press Enter to skip the passphrase (or set one if preferred)

This will create two files in your `.ssh` folder:
- **Private key**: `jenkins_github_key` (keep this secure)
- **Public key**: `jenkins_github_key.pub` (this will be added to GitHub)

> **Note**: On Linux/macOS, you can also use `cat ~/.ssh/jenkins_github_key.pub` to display the public key directly in the terminal.

```mermaid
graph LR
    A[ssh-keygen command] --> B[Private Key<br/>jenkins_github_key]
    A --> C[Public Key<br/>jenkins_github_key.pub]
    
    style A fill:#e1f5ff
    style B fill:#ffcccc
    style C fill:#ccffcc
```

## Step 2: Add Public Key to GitHub

![GitHub SSH Settings](screenshots/step2-github-ssh-settings.png)

1. Log in to your GitHub account
2. Go to **Settings** → **SSH and GPG keys**
3. Click on **New SSH key**

![Add SSH Key to GitHub](screenshots/step2-add-ssh-key.png)

4. Give your key a title (e.g., "Jenkins Access Key")
5. Open your public key file (`jenkins_github_key.pub`) in a text editor:
   - **Windows**: Notepad++, Notepad, or any text editor
   - **Linux**: `nano ~/.ssh/jenkins_github_key.pub`, `vim ~/.ssh/jenkins_github_key.pub`, or `gedit ~/.ssh/jenkins_github_key.pub`
   - **macOS**: `nano ~/.ssh/jenkins_github_key.pub`, `vim ~/.ssh/jenkins_github_key.pub`, or TextEdit
   - **Alternative (Linux/macOS)**: Use `cat ~/.ssh/jenkins_github_key.pub` to display and copy directly from terminal
6. Copy the entire content of the public key
7. Paste it into the "Key" section on GitHub
8. Click **Add SSH key**
9. Enter your GitHub password when prompted

Your SSH key has been successfully added to your GitHub account.

```mermaid
sequenceDiagram
    participant User
    participant GitHub
    participant Local as Local Machine
    
    User->>GitHub: Navigate to Settings > SSH Keys
    User->>GitHub: Click "New SSH key"
    User->>Local: Open public key file
    Local->>User: Display public key content
    User->>GitHub: Paste public key
    User->>GitHub: Click "Add SSH key"
    GitHub->>User: Request password
    User->>GitHub: Enter password
    GitHub->>User: SSH key added successfully
```

## Step 3: Add Credential in Jenkins

![Jenkins Credentials Page](screenshots/step3-jenkins-credentials.png)

1. In your Jenkins job, go to the **Credentials** section
2. Click **Add**
3. From the dropdown, select **SSH Username with private key**

![Add Credential in Jenkins](screenshots/step3-add-credential.png)

4. Fill in the following:
   - **ID**: Give it a unique identifier (e.g., `github-ssh-key`)
   - **Username**: Your GitHub username
   - **Private Key**: Select the radio button for "Enter directly"
5. Open your private key file (`jenkins_github_key`) in a text editor:
   - **Windows**: Notepad++, Notepad, or any text editor
   - **Linux**: `nano ~/.ssh/jenkins_github_key`, `vim ~/.ssh/jenkins_github_key`, or `gedit ~/.ssh/jenkins_github_key`
   - **macOS**: `nano ~/.ssh/jenkins_github_key`, `vim ~/.ssh/jenkins_github_key`, or TextEdit
   - **Alternative (Linux/macOS)**: Use `cat ~/.ssh/jenkins_github_key` to display and copy directly from terminal
6. Copy the entire content of the private key (including `-----BEGIN` and `-----END` lines)
7. Paste it into the "Private Key" field in Jenkins
8. Click **Add**

Your credential has been successfully added to Jenkins.

```mermaid
graph TD
    A[Jenkins Job] --> B[Credentials Section]
    B --> C[Click Add]
    C --> D[Select: SSH Username with private key]
    D --> E[Enter ID, Username]
    E --> F[Paste Private Key]
    F --> G[Click Add]
    G --> H[Credential Saved]
    
    style A fill:#e1f5ff
    style H fill:#d4edda
```

## Step 4: Configure Jenkins Job with SSH URL

![Jenkins Job Configuration](screenshots/step4-jenkins-job-config.png)

1. In your Jenkins job configuration, go to the **Source Code Management** section
2. Select **Git**
3. For the **Repository URL**, use the SSH URL (not the HTTPS URL):

![GitHub SSH URL](screenshots/step4-ssh-url.png)

   - On your GitHub repository page, click **Code** → **SSH**
   - Copy the SSH URL (format: `git@github.com:username/repository.git`)
4. Paste the SSH URL into the **Repository URL** field
5. From the **Credentials** dropdown, select the credential you created in Step 3
6. Click outside the field to verify - the authentication error should disappear
7. Configure the branch name (e.g., `main` or `master` depending on your repository)
8. Click **Apply** and **Save**

```mermaid
sequenceDiagram
    participant User
    participant Jenkins
    participant GitHub
    
    User->>Jenkins: Configure Job > Source Code Management
    User->>Jenkins: Select Git
    User->>GitHub: Copy SSH URL (git@github.com:...)
    User->>Jenkins: Paste SSH URL
    User->>Jenkins: Select Credential from dropdown
    Jenkins->>GitHub: Test connection with SSH key
    GitHub->>Jenkins: Authentication successful
    Jenkins->>User: Error disappears
    User->>Jenkins: Configure branch (main/master)
    User->>Jenkins: Apply and Save
```

## Step 5: Build and Verify

1. Click **Build Now** in your Jenkins job

![Build Success](screenshots/step5-build-success.png)

2. If you encounter branch-related errors, go back to **Configure** and ensure the branch name matches your repository's default branch (usually `main` instead of `master`)
3. Once the build succeeds, you can verify by checking the **Workspace** - all files from your GitHub repository should be available

![Workspace Files](screenshots/step5-workspace-files.png)

```mermaid
flowchart LR
    A[Build Now] --> B{Build Status}
    B -->|Success| C[Check Workspace]
    B -->|Branch Error| D[Update Branch Name]
    D --> A
    C --> E[Files Available]
    
    style A fill:#e1f5ff
    style E fill:#d4edda
    style B fill:#fff3cd
```

## Troubleshooting

### Authentication Error
- Ensure the public key is correctly added to your GitHub account
- Verify the private key content is complete (including BEGIN/END markers)
- Check that the correct credential is selected in the Jenkins job

### Branch Not Found
- Verify your repository's default branch name (`main` vs `master`)
- Update the branch name in Jenkins job configuration accordingly

### Permission Denied
- **Linux/macOS**: Ensure the SSH key has correct permissions:
  ```bash
  chmod 600 ~/.ssh/jenkins_github_key
  chmod 644 ~/.ssh/jenkins_github_key.pub
  ```
- Verify the GitHub username matches the one used in Jenkins credentials
- On Windows, permission issues are less common, but ensure the key file is readable

## Complete Workflow Diagram

```mermaid
graph TB
    subgraph Step1["Step 1: Generate Keys"]
        A1[Run ssh-keygen] --> A2[Private Key Created]
        A1 --> A3[Public Key Created]
    end
    
    subgraph Step2["Step 2: GitHub Setup"]
        A3 --> B1[Add Public Key to GitHub]
        B1 --> B2[GitHub Authenticated]
    end
    
    subgraph Step3["Step 3: Jenkins Credentials"]
        A2 --> C1[Add Private Key to Jenkins]
        C1 --> C2[Credential Stored]
    end
    
    subgraph Step4["Step 4: Job Configuration"]
        B2 --> D1[Add SSH URL to Job]
        C2 --> D1
        D1 --> D2[Select Credential]
        D2 --> D3[Configure Branch]
    end
    
    subgraph Step5["Step 5: Build"]
        D3 --> E1[Build Now]
        E1 --> E2[Access Private Repo]
    end
    
    style A1 fill:#e1f5ff
    style B2 fill:#ccffcc
    style C2 fill:#ccccff
    style D3 fill:#ffffcc
    style E2 fill:#d4edda
```

## Summary

By following these steps, you can:
- Generate SSH key pairs for secure authentication
- Add public keys to GitHub for repository access
- Configure Jenkins to use private SSH keys for authentication
- Access private GitHub repositories in Jenkins jobs

This setup provides a secure way to integrate your private GitHub repositories with Jenkins using SSH key authentication.

## Security Best Practices

```mermaid
mindmap
  root((SSH Key Security))
    Private Key
      Never commit to repo
      Store securely
      Use passphrase if possible
    Public Key
      Safe to share
      Add to GitHub only
    Credentials
      Use Jenkins credential store
      Rotate keys periodically
      Limit key permissions
```

- **Never commit private keys** to version control
- **Use strong passphrases** for production environments
- **Rotate SSH keys** periodically
- **Limit key permissions** in GitHub (if using deploy keys, limit to specific repositories)
- **Use Jenkins credential store** instead of hardcoding keys

