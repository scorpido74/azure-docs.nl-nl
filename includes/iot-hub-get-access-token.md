---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 7f7dc1483002c2bdfe3227a8aade8dbf2a8da417
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70803003"
---
## <a name="obtain-an-azure-resource-manager-token"></a>Een Azure Resource Manager-token verkrijgen
Azure Active Directory moet alle taken verifiëren die u uitvoert op resources met Azure Resource Manager. In het voorbeeld dat hier wordt weergegeven, wordt wachtwoordverificatie gebruikt, zie [Verificatie van Azure Resource Manager-aanvragen][lnk-authenticate-arm].

1. Voeg de volgende code toe aan de **hoofdmethode** in Program.cs om een token uit Azure AD op te halen met behulp van de toepassings-id en het wachtwoord.
   
    ```csharp
    var authContext = new AuthenticationContext(string.Format  
      ("https://login.microsoftonline.com/{0}", tenantId));
    var credential = new ClientCredential(applicationId, password);
    AuthenticationResult token = authContext.AcquireTokenAsync
      ("https://management.core.windows.net/", credential).Result;
   
    if (token == null)
    {
      Console.WriteLine("Failed to obtain the token");
      return;
    }
    ```
2. Maak een **ResourceManagementClient-object** dat het token gebruikt door de volgende code toe te voegen aan het einde van de **hoofdmethode:**
   
    ```csharp
    var creds = new TokenCredentials(token.AccessToken);
    var client = new ResourceManagementClient(creds);
    client.SubscriptionId = subscriptionId;
    ```
3. Maak of verkrijg een verwijzing naar de resourcegroep die u gebruikt:
   
    ```csharp
    var rgResponse = client.ResourceGroups.CreateOrUpdate(rgName,
        new ResourceGroup("East US"));
    if (rgResponse.Properties.ProvisioningState != "Succeeded")
    {
      Console.WriteLine("Problem creating resource group");
      return;
    }
    ```

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx