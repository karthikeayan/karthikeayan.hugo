---
title: azure-ad-user-list
description: azure-ad-user-list
tags: ["azure", "ad", "cli", "cli", "microsoft", "list", "users"]
date: 2021-12-24
publishdate: 2021-12-24
---

# Get user details from Azure AD

Recently I came across a challenge in my work. We are using a CI tool and using an inbuilt authorization mechanism(roles) to control access to the users. CI tool is integrated with Azure AD.  We had 300+ entries in the user > roles assignment list and reached the maximum limit.

We also know that there are multiple people who left the organization whose entries are still present.

So, I was looking for a quick solution that will help me the list of people who left the organization, whose entry is still exist in the CI tool authorization list.

I was Googling at couple of solutions like ApacheDirectoryStudio, ldapsearch and couple of others.

I knew, I can list the users from Azure AD Web UI.

<https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview>

I then started thinking, if it is possible with Web UI, there should be a CLI for this. After Googling for Azure AD CLI, found this.

<https://docs.microsoft.com/en-us/cli/azure/ad/user?view=azure-cli-latest#az_ad_user_list>

So, I have to install Azure CLI,

```
brew install azure-cli
```

Getting authentication and authorization configuration was faily simple, below command will redirect to your browser and use SSO to login into Azure.

```
az login --allow-no-subscriptions
```

Once I logged in with the CLI, I used below script to check if the user is still present in the Azure AD or not. This will print the users whose details are not present in Azure AD.

```
#!/usr/local/bin/bash

while IFS= read -r line; do
    length=$(az ad user list --upn $line | jq length)
    if [[ $length == "0" ]]; then
       echo $line
    fi
done < jenkins-users.txt
```

jenkins-users.txt. In this file I had the list of user list whoes entries were present in the CI tool's authorization list.
