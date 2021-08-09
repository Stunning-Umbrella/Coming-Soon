# Coming soon template

Template for new apps I think I'm going to 1 day build for my account [@Gordon-Beeming](https://github.com/Gordon-Beeming). A sample of what this looks like can be found at [coming-soon-demo.gordonbeeming.xyz](https://coming-soon-demo.gordonbeeming.xyz/).

## Getting started

Getting started is simple... use this repo as a base, it contains a workflow to publish to an azure web app. Follow these steps to get started

- Update app name in index.html
- Update expected launch date in index.html and site.js.
- Remove demo date code from site.js and only have your launch date
- Create Service Principal for your apps resource group
- Add repository secret for AZURE_CREDENTIALS from the create service principal output
- Optionally replace background.jpg which comes from [Gordon Beeming](https://unsplash.com/@gordon_beeming) ([this 1](https://unsplash.com/photos/q_XRMFLzWwY)) on [Unsplash](https://unsplash.com/)

## Create Service Principal

Set $ResourceGroupName value at the top of the script and then run in segments. This would give the service principal contributor access to the resource group specified

    az login

    $ResourceGroupName = "comingsoon-rg"

    az account list --output table
    az account set --subscription "MVP - Dev Tech"
    $CurrentAccount = az account show | Out-string | ConvertFrom-Json
    $SubscriptionId = $CurrentAccount.id
    $DeploymentServicePrincipalName = "http://$($ResourceGroupName)SP"

    # Create resource group if it doesn't exist yet
    # az account list-locations
    # az group create --location westeurope --name $ResourceGroupName

    # Create a service principal
    $DeploymentServicePrincipalInfo = az ad sp create-for-rbac --name $DeploymentServicePrincipalName --role contributor --scopes "/subscriptions/$($SubscriptionId)/resourceGroups/$ResourceGroupName" --sdk-auth | Out-string | ConvertFrom-Json

    # New github secrets
    @"

    # AZURE_CREDENTIALS
    $($DeploymentServicePrincipalInfo | ConvertTo-Json)

    # SP_CLIENTID
    $($DeploymentServicePrincipalInfo.clientId)

    # SP_CLIENTSECRET
    $($DeploymentServicePrincipalInfo.clientSecret)

    # RESOURCE_GROUP
    $($ResourceGroupName)

    "@
