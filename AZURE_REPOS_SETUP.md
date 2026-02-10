# Azure Repos "Create Repository" Permission (Fix TF401027)

The pipeline creates the Azure Repos repository **automatically** if it doesn't exist. For that to work, the **Project Build Service** must have the **Create repository** permission. This is a **one-time setup** in Azure DevOps.

## Error you're fixing

```
TF401027: You need the Git 'CreateRepository' permission to perform this action.
Details: identity 'Build\<project-id>', scope 'project'.
```

## One-time steps in Azure DevOps

1. Open your project: **https://dev.azure.com/SonuKumar0171/IFI-Terraform-Templates**
2. Go to **Project settings** (bottom-left gear).
3. Under **Repositories**, select **Repositories** (or **Repos**).
4. Open the **Security** tab (or click **Git repositories** for project-level Git permissions).
5. Click **+ Add** (or find the existing entry) and add:
   - **ProjectName Build Service (OrganizationName)**  
     e.g. **IFI-Terraform-Templates Build Service (SonuKumar0171)**
   - Or search for **Build** and select the project Build Service.
6. For that identity, set **Create repository** to **Allow**.
7. Save/apply the changes.

## Alternative path (project-level Git permissions)

1. **Project settings** → **Repositories** → **Git repositories** (top-level entry).
2. Click **Security**.
3. Add **IFI-Terraform-Templates Build Service (SonuKumar0171)** if not listed.
4. Set **Create repository** = **Allow** for that identity.

After this, re-run the pipeline. The **Create Azure Repos Repository (if needed)** stage will create the `demo-repos` repository when it doesn’t exist, with no manual repo creation.

## Pipeline behavior

- **Parameter `azureReposName`** (default: `demo-repos`): name of the repo the pipeline ensures exists.
- The **Create_Repo** stage runs first; if the repo exists, it does nothing; if not, it creates it via the REST API using the build’s OAuth token.
