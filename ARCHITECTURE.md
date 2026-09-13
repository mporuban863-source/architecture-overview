# GitHub App Authentication Architecture

This document outlines the different authentication methods available for GitHub Apps.

## Authentication Overview

GitHub Apps support three primary authentication methods:

1. **App Authentication** - Authenticate as the GitHub App itself using JWT
2. **Installation Authentication** - Authenticate as an app installation using installation access
3. **User Authentication** - Authenticate on behalf of a user using user access

## Architecture Diagram

```mermaid
graph TB
    GH["GitHub Platform"]
    
    subgraph "Authentication Methods"
        JWT["JWT Authentication<br/>(App as itself)"]
        IAT["Installation Access<br/>(App Installation)"]
        UAT["User Access<br/>(On Behalf of User)"]
    end
    
    subgraph "Use Cases"
        JWT_UC["• Generate installation access<br/>• Manage app resources<br/>• List installations"]
        IAT_UC["• Automation workflows<br/>• No user input required<br/>• Attribute to app"]
        UAT_UC["• User-initiated actions<br/>• Attribute to user<br/>• User-specific permissions"]
    end
    
    subgraph "Access Scope"
        JWT_SCOPE["App-level<br/>resources"]
        IAT_SCOPE["User/Org owned<br/>resources"]
        UAT_SCOPE["User/Org owned<br/>resources"]
    end
    
    GH -->|generates| JWT
    GH -->|generates| IAT
    GH -->|generates| UAT
    
    JWT --> JWT_UC
    IAT --> IAT_UC
    UAT --> UAT_UC
    
    JWT --> JWT_SCOPE
    IAT --> IAT_SCOPE
    UAT --> UAT_SCOPE
```

## Authentication Methods Details

### 1. App Authentication (JWT)

**Use this when:**
- Your app needs to generate installation access
- Your app needs to manage its own resources
- Your app needs to list accounts where it is installed

**Authentication Type:** JSON Web (JWT)

**Key Features:**
- Authenticates the app itself
- Used to generate other access types
- App-level scope

### 2. Installation Authentication

**Use this when:**
- You want to perform automation workflows
- User input is not required
- You want to attribute actions to the app

**Authentication Type:** Installation Access

**Key Features:**
- Authenticates as an app installation
- Access to user/organization owned resources
- Ideal for automated workflows

### 3. User Authentication

**Use this when:**
- You want to attribute actions to a specific user
- You want to ensure only user-authorized actions are performed
- You need user-specific permissions

**Authentication Type:** User Access

**Key Features:**
- Authenticates on behalf of a user
- Access to user/organization owned resources
- User-level accountability

## Flow Diagram

```mermaid
sequenceDiagram
    participant App as GitHub App
    participant GH as GitHub API
    participant User as User/Org
    
    Note over App,User: JWT Flow
    App->>GH: Authenticate with JWT
    GH-->>App: Acknowledge
    
    Note over App,User: Installation Access Flow
    App->>GH: Request installation access (using JWT)
    GH-->>App: Return installation access
    App->>GH: Make API calls (using access)
    GH->>User: Perform action as app
    
    Note over App,User: User Access Flow
    App->>GH: Redirect user to GitHub
    GH->>User: Request authorization
    User-->>GH: Grant authorization
    GH-->>App: Return user access
    App->>GH: Make API calls (using access)
    GH->>User: Perform action as user
```

## References

- [Authenticating as a GitHub App](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/authenticating-as-a-github-app)
- [Authenticating as a GitHub App Installation](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/authenticating-as-a-github-app-installation)
- [Authenticating with a GitHub App on behalf of a user](https://docs.github.com/en/apps/creating-github-apps/authenticating-with-a-github-app/authenticating-with-a-github-app-on-behalf-of-a-user)
