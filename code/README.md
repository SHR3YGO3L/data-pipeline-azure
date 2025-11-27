# Infrastructure as Code for Data Governance Pipeline with Purview Discovery

This directory contains Infrastructure as Code (IaC) implementations for the recipe "Data Governance Pipeline with Purview Discovery".

## Available Implementations

- **Terraform**: Multi-cloud infrastructure as code

## Prerequisites

- Azure CLI installed and configured
- Azure subscription with Owner or Contributor permissions
- Terraform 1.0+ (for Terraform deployment)
- Basic understanding of data governance concepts and Azure storage
- Familiarity with Azure Synapse Analytics and data pipeline concepts
- Estimated cost: $50-100 USD for testing environment (depends on data volume and scan frequency)
- Do the pipeline before running this script

## Quick Start



### Using Terraform
```bash
# Navigate to terraform directory
cd terraform/

# Initialize Terraform
terraform init

# Review the deployment plan
terraform plan

# Apply the configuration
terraform apply
```

## Architecture Components

This IaC deployment creates the following Azure resources:

- **Azure Purview Account**: Central data governance and catalog service
- **Azure Data Lake Storage Gen2**: Hierarchical storage for enterprise data lakes
- **Azure Synapse Analytics Workspace**: Integrated analytics platform
- **Storage Containers**: Organized data containers (raw-data, processed-data, sensitive-data)
- **IAM Role Assignments**: Proper permissions for Purview-Storage integration
- **Firewall Rules**: Secure access configuration for development

## Configuration Options


### Terraform Variables

Customize `terraform/terraform.tfvars`:

```hcl
location                = "East US"
resource_group_name     = "rg-purview-governance-demo"
purview_account_name    = "purview-governance-demo"
storage_account_name    = "datalakegovernance"
synapse_workspace_name  = "synapse-governance-demo"
environment            = "demo"
```


## Post-Deployment Configuration

After the infrastructure is deployed, you'll need to complete these manual steps:

1. **Register Data Sources in Purview**:
   - Navigate to Azure Purview Studio
   - Register your Data Lake Storage account as a data source
   - Configure scan rules and schedules

2. **Configure Data Classification**:
   - Set up custom classification rules
   - Define sensitivity labels
   - Configure automated scanning policies

3. **Set Up Synapse Integration**:
   - Connect Synapse workspace to Purview
   - Configure lineage tracking
   - Deploy data pipelines for governance testing

4. **Create Sample Data**:
   - Upload sample datasets to test classification
   - Verify data discovery and cataloging
   - Test governance workflows

## Validation

### Verify Azure Purview Account
```bash
az purview account show \
    --resource-group "rg-purview-governance-demo" \
    --name "your-purview-account-name" \
    --query '{name:name,state:properties.provisioningState}' \
    --output table
```

### Verify Data Lake Storage
```bash
az storage account show \
    --resource-group "rg-purview-governance-demo" \
    --name "your-storage-account-name" \
    --query '{name:name,kind:kind,hierarchicalNamespace:isHnsEnabled}' \
    --output table
```

### Verify Synapse Workspace
```bash
az synapse workspace show \
    --resource-group "rg-purview-governance-demo" \
    --name "your-synapse-workspace-name" \
    --query '{name:name,state:provisioningState}' \
    --output table
```

## Cleanup


### Using Terraform
```bash
cd terraform/
terraform destroy
```



## Troubleshooting

### Common Issues

1. **Purview Account Name Conflicts**:
   - Purview account names must be globally unique
   - Modify the account name in your parameters

2. **Storage Account Name Conflicts**:
   - Storage account names must be globally unique and lowercase
   - Update the storage account name in your configuration

3. **Insufficient Permissions**:
   - Ensure you have Owner or Contributor permissions on the subscription
   - Verify Azure resource providers are registered

4. **Firewall Access Issues**:
   - The deployment configures firewall rules for current IP
   - Update firewall rules if accessing from different locations

### Resource Provider Registration

If you encounter resource provider errors, register them manually:

```bash
az provider register --namespace Microsoft.Purview
az provider register --namespace Microsoft.Storage
az provider register --namespace Microsoft.Synapse
az provider register --namespace Microsoft.KeyVault
```

## Cost Management

### Expected Costs

- **Azure Purview**: Consumption-based pricing for scanning and cataloging
- **Data Lake Storage Gen2**: Storage costs based on data volume
- **Synapse Analytics**: Compute costs based on usage

### Cost Optimization Tips

1. **Purview Scanning**: Schedule scans during off-peak hours
2. **Storage Tiers**: Use appropriate storage tiers for different data types
3. **Synapse Compute**: Pause compute resources when not in use
4. **Resource Cleanup**: Remove test resources promptly after validation

## Security Considerations

### Implemented Security Features

- **Managed Identity**: Purview uses managed identity for storage access
- **RBAC**: Role-based access control with least privilege principles
- **Network Security**: Firewall rules restrict access to authorized IPs

### Additional Security Recommendations

1. **Private Endpoints**: Consider private endpoints for production environments
2. **Azure AD Integration**: Use Azure AD for user authentication
3. **Network Isolation**: Implement VNet integration for enhanced security
4. **Monitoring**: Enable Azure Monitor and Security Center
