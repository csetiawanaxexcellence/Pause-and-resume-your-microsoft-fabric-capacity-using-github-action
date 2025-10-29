# Schedule Pause and resume your Microsoft Fabric Capacity Using Github Action For FREE

Schedule Pause and resume your microsoft fabric capacity for free using github action

## 📖 About

This repository provides a GitHub Actions workflow to automatically pause and resume your Microsoft Fabric capacity on a schedule. Save costs by pausing your capacity during off-hours and automatically resuming it when needed - all completely free using GitHub Actions!

## 🚀 Quick Start

### Step 1: Clone this Repo

```bash
git clone https://github.com/csetiawanaxexcellence/Pause-and-resume-your-microsoft-fabric-capacity-using-github-action.git
cd Pause-and-resume-your-microsoft-fabric-capacity-using-github-action
```

Or if you prefer SSH:

```bash
git clone git@github.com:csetiawanaxexcellence/Pause-and-resume-your-microsoft-fabric-capacity-using-github-action.git
cd Pause-and-resume-your-microsoft-fabric-capacity-using-github-action
```

### Step 2: Set Up GitHub Secrets

Go to your GitHub repository → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

Add these secrets:

| Secret Name | Description | Example Value |
|------------|-------------|---------------|
| `AZURE_SUBSCRIPTION_ID` | Your Azure subscription ID | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` |
| `AZURE_RESOURCE_GROUP` | Resource group name | `your-resource-group-name` |
| `AZURE_CAPACITY_NAME` | Fabric capacity name | `your-capacity-name` |
| `AZURE_TENANT_ID` | Azure AD tenant ID | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` |
| `AZURE_CLIENT_ID` | Service principal client ID | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` |
| `AZURE_CLIENT_SECRET` | Service principal secret | `your-secret-here` |

#### How to Get These Values

**1. Azure Subscription ID, Tenant ID, Resource Group, and Capacity Name**

You can find these values in the Azure Portal:

- **Subscription ID**: 
  - Go to [Azure Portal](https://portal.azure.com) → **Subscriptions** → Select your subscription → Copy the **Subscription ID**
  - Or use Azure CLI: `az account show --query id -o tsv`
  
- **Tenant ID**:
  - Go to [Azure Portal](https://portal.azure.com) → **Azure Active Directory** → **Overview** → Copy the **Tenant ID**
  - Or use Azure CLI: `az account show --query tenantId -o tsv`

- **Resource Group**: 
  - Go to [Azure Portal](https://portal.azure.com) → **Resource groups** → Find your Fabric capacity → Note the resource group name
  
- **Capacity Name**:
  - Go to [Microsoft Fabric Portal](https://app.fabric.microsoft.com) → **Settings** → **Admin portal** → **Capacities** → Find your capacity name

**2. Service Principal (Client ID and Client Secret)**

You need to create a Service Principal to authenticate GitHub Actions with Azure. Here's how:

**Option A: Using Azure Portal (Recommended for beginners)**

1. Go to [Azure Portal](https://portal.azure.com) → **Azure Active Directory** → **App registrations**
2. Click **+ New registration**
3. Enter a name (e.g., "GitHubActions-FabricManager")
4. Click **Register**
5. Go to **Certificates & secrets** → **+ New client secret**
6. Add description and expiration, then click **Add**
7. **IMPORTANT**: Copy the **Value** immediately (you won't see it again) - this is your `AZURE_CLIENT_SECRET`
8. Copy the **Application (client) ID** from the Overview page - this is your `AZURE_CLIENT_ID`

**Option B: Using Azure CLI (Faster)**

```bash
# Login to Azure
az login

# Create service principal
az ad sp create-for-rbac --name "GitHubActions-FabricManager" --role contributor --scopes /subscriptions/{SUBSCRIPTION_ID}

# Output will contain:
# - appId (this is your AZURE_CLIENT_ID)
# - password (this is your AZURE_CLIENT_SECRET)
# - tenant (this is your AZURE_TENANT_ID)
```

**3. Grant Permissions to Service Principal**

After creating the Service Principal, you need to grant it permissions on your Fabric capacity:

1. Go to [Azure Portal](https://portal.azure.com) → Find your Fabric capacity resource
2. Go to **Access control (IAM)** → **Add** → **Add role assignment**
3. Select role: **Contributor** or **Fabric Capacity Administrator**
4. Assign access to: **Service principal** → Select your service principal
5. Click **Save**

**Alternative using Azure CLI:**

```bash
az role assignment create \
  --assignee {AZURE_CLIENT_ID} \
  --role "Contributor" \
  --scope /subscriptions/{AZURE_SUBSCRIPTION_ID}/resourceGroups/{AZURE_RESOURCE_GROUP}/providers/Microsoft.Fabric/capacities/{AZURE_CAPACITY_NAME}
```

**Helpful Resources:**

- 📖 [Create Azure Service Principal - Microsoft Docs](https://learn.microsoft.com/en-us/azure/active-directory/develop/howto-create-service-principal-portal)
- 📖 [Azure CLI - Create Service Principal](https://learn.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
- 📖 [Fabric Capacity Permissions](https://learn.microsoft.com/en-us/fabric/admin/capacity-settings)
- 📖 [GitHub Secrets Documentation](https://docs.github.com/en/actions/security-guides/encrypted-secrets)

### Step 3: Configure Schedule

Edit `.github/workflows/capacity-manager.yml` and modify the cron schedule:

```yaml
schedule:
  - cron: '0 2 * * *'  # Every day at 2:00 AM UTC
```

**Cron Schedule Examples:**
- `'0 0 * * *'` - Every day at midnight UTC
- `'0 */6 * * *'` - Every 6 hours
- `'0 9 * * 1-5'` - Monday-Friday at 9:00 AM UTC
- `'0 2,14 * * *'` - Twice daily at 2:00 AM and 2:00 PM UTC
- `'0 0 * * 0'` - Every Sunday at midnight UTC

**Cron Format:** `minute hour day month weekday`
- All times are in UTC

### Step 4: Commit and Push

```bash
git add .github/workflows/capacity-manager.yml
git commit -m "Add GitHub Actions scheduler for capacity management"
git push
```

The workflow will automatically run on the scheduled time!

## 📋 Features

### ✅ Scheduled Execution
- Runs automatically on cron schedule
- All times are in UTC

### ✅ Manual Triggers
You can manually trigger the workflow from GitHub UI:
1. Go to **Actions** tab
2. Select **Fabric Capacity Manager** workflow
3. Click **Run workflow**
4. Choose action:
   - `pause-resume` - Full cycle (pause → wait → resume → wait → status)
   - `pause` - Only pause
   - `resume` - Only resume
   - `status` - Only check status
5. Optionally specify a different capacity name
6. Click **Run workflow**

### ✅ Status Monitoring
- Checks capacity status before operations
- Skips pause if already paused
- Skips resume if already active
- Waits for operations to complete (max 5 minutes)
- Final status check and summary

### ✅ Logging
- All operations logged in GitHub Actions logs
- Easy to view execution history
- Built-in notifications (email/webhook on failure)

## 📊 Monitoring

### View Workflow Runs

1. Go to **Actions** tab in GitHub
2. Click on **Fabric Capacity Manager**
3. View all runs (scheduled and manual)
4. Click any run to see detailed logs

### Workflow Status Badge

Add this to your README.md:

```markdown
![Capacity Manager](https://github.com/csetiawanaxexcellence/Pause-and-resume-your-microsoft-fabric-capacity-using-github-action/workflows/Fabric%20Capacity%20Manager/badge.svg)
```

### Notifications

GitHub Actions can send notifications:
- Email on workflow failures
- Repository webhooks
- GitHub Mobile app notifications

To enable:
1. Go to **Settings** → **Notifications**
2. Configure workflow notifications

## 🔧 Customization

### Change Default Capacity Name

Edit the workflow file:

```yaml
env:
  CAPACITY_NAME: ${{ secrets.AZURE_CAPACITY_NAME || 'your-default-capacity' }}
```

### Add Multiple Capacities

You can create a matrix strategy:

```yaml
jobs:
  manage-capacities:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        capacity: [mmfabric, capacity2, capacity3]
    steps:
      - name: Manage ${{ matrix.capacity }}
        run: |
          # Use ${{ matrix.capacity }} in your commands
```

### Custom Wait Times

Edit the workflow steps to change wait durations:

```yaml
- name: Wait 45 seconds
  run: |
    sleep 45  # Change this value
```

## 🔒 Security Best Practices

1. **Never commit secrets to the repository**
   - Always use GitHub Secrets
   - Secrets are encrypted and only visible to authorized users

2. **Limit Service Principal Permissions**
   - Grant minimum required permissions:
     - `Microsoft.Fabric/capacities/suspend/action`
     - `Microsoft.Fabric/capacities/resume/action`
     - `Microsoft.Fabric/capacities/read`

3. **Use Branch Protection**
   - Protect your main branch
   - Require reviews before merging workflow changes

4. **Audit Logs**
   - GitHub Actions logs are retained for 90 days (400 days for GitHub Enterprise)
   - Review execution history regularly

## 💰 Cost

**GitHub Actions is FREE for public repositories!**

For private repositories:
- **2,000 minutes/month free** (GitHub Free)
- **3,000 minutes/month free** (GitHub Team)
- **Additional minutes:** $0.008 per minute

The workflow typically takes 2-5 minutes per run, so:
- Free tier: ~400-1,000 runs/month
- Most use cases fit well within free limits

## 🐛 Troubleshooting

### Workflow Not Running

1. **Check cron syntax**
   - Use a cron validator: https://crontab.guru/
   - Remember: All times are UTC

2. **Check workflow file syntax**
   - Go to Actions tab → Check for syntax errors
   - Validate YAML format

3. **Verify secrets are set**
   - Go to Settings → Secrets → Actions
   - Ensure all required secrets exist

### Authentication Errors

1. **Verify service principal credentials**
   - Check all secrets match your Azure configuration
   - Verify service principal still exists and is active

2. **Check permissions**
   - Service principal needs Fabric capacity management permissions
   - Verify subscription access

### Capacity Not Changing State

1. **Check operation completion**
   - Operations use `--no-wait` flag
   - The workflow waits up to 5 minutes
   - Check Azure Portal for operation status

2. **Verify capacity name**
   - Check the capacity name matches exactly
   - Review logs for errors

### View Detailed Logs

1. Go to Actions → Select workflow run
2. Expand each step to see detailed output
3. Check for error messages
4. Review Azure CLI command outputs

## 📝 Example Cron Schedules

| Schedule | Cron Expression | Description |
|---------|----------------|-------------|
| Daily at midnight | `0 0 * * *` | Every day at 00:00 UTC |
| Daily at 2 AM | `0 2 * * *` | Every day at 02:00 UTC |
| Twice daily | `0 2,14 * * *` | 2 AM and 2 PM UTC |
| Every 6 hours | `0 */6 * * *` | Every 6 hours |
| Every hour | `0 * * * *` | Every hour at :00 |
| Weekdays only | `0 9 * * 1-5` | Monday-Friday at 9 AM UTC |
| Weekly | `0 0 * * 0` | Every Sunday at midnight |
| Monthly | `0 0 1 * *` | First day of each month at midnight |

## 🔄 Migration from Python Script

This GitHub Actions workflow replaces your Python script with the same functionality:

| Python Script | GitHub Actions |
|--------------|----------------|
| `main(command="status")` | Step: "Check capacity status" |
| `main(command="pause")` | Step: "Pause capacity" |
| `time.sleep(45)` | Step: "Wait 45 seconds" |
| `main(command="resume")` | Step: "Resume capacity" |
| `main(command="status")` | Step: "Final status check" |

All the same logic, now running on GitHub's infrastructure with scheduling built-in!

## 📞 Support

- GitHub Actions Documentation: https://docs.github.com/en/actions
- Cron Schedule Help: https://crontab.guru/
- Azure Fabric CLI: https://learn.microsoft.com/en-us/cli/azure/fabric/capacity

