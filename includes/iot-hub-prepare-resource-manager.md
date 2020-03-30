---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 4eb794fa35164e3f86a5e3d6f67d446321f91f0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67132958"
---
## <a name="prepare-to-authenticate-azure-resource-manager-requests"></a>Voorbereiden op verificatie van Azure Resource Manager-aanvragen
U moet alle bewerkingen verifiëren die u uitvoert op resources met behulp van [Azure Resource Manager][lnk-authenticate-arm] met Azure Active Directory (AD). De eenvoudigste manier om dit te configureren is het gebruik van PowerShell of Azure CLI.

Installeer de [Azure PowerShell-cmdlets][lnk-powershell-install] voordat u verdergaat.

In de volgende stappen wordt uitgelegd hoe u wachtwoordverificatie instelt voor een AD-toepassing met PowerShell. U deze opdrachten uitvoeren in een standaard PowerShell-sessie.

1. Meld u aan bij uw Azure-abonnement met de volgende opdracht:

    ```powershell
    Connect-AzAccount
    ```

1. Als u meerdere Azure-abonnementen hebt, geeft aanmelden bij Azure u toegang tot alle Azure-abonnementen die zijn gekoppeld aan uw referenties. Gebruik de volgende opdracht om de Azure-abonnementen weer te geven die beschikbaar zijn voor gebruik:

    ```powershell
    Get-AzSubscription
    ```

    Gebruik de volgende opdracht om een abonnement te selecteren dat u wilt gebruiken om de opdrachten uit te voeren om uw IoT-hub te beheren. U kunt de naam van het abonnement of de id van de uitvoer van de vorige opdracht gebruiken:

    ```powershell
    Select-AzSubscription `
        -SubscriptionName "{your subscription name}"
    ```

2. Noteer uw **TenantId** en **SubscriptionId.** U hebt deze later nog nodig.
3. Maak een nieuwe Azure Active Directory-toepassing met de volgende opdracht, ter vervanging van de plaatshouders:
   
   * **{Weergavenaam}:** een weergavenaam voor uw toepassing, zoals **MySampleApp**
   * **{URL van de startpagina}:** de URL van de startpagina van uw app, zoals **http:\//mysampleapp/home**. Deze URL hoeft niet te wijzen op een echte toepassing.
   * **{Application identifier}:** Een unieke id zoals **http:\//mysampleapp**. Deze URL hoeft niet te wijzen op een echte toepassing.
   * **{Wachtwoord}:** Een wachtwoord dat u gebruikt om te verifiëren met uw app.
     
     ```powershell
     $SecurePassword=ConvertTo-SecureString {password} –asplaintext –force
     New-AzADApplication -DisplayName {Display name} -HomePage {Home page URL} -IdentifierUris {Application identifier} -Password $SecurePassword
     ```
4. Noteer de **ApplicationId** van de toepassing die u hebt gemaakt. Je hebt dit later nodig.
5. Maak een nieuwe serviceprincipal met de volgende opdracht en vervang **{MyApplicationId}** door de **ApplicationId** uit de vorige stap:
   
    ```powershell
    New-AzADServicePrincipal -ApplicationId {MyApplicationId}
    ```
6. Stel een roltoewijzing in met de volgende opdracht en vervang **{MyApplicationId}** door uw **ApplicationId**.
   
    ```powershell
    New-AzRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName {MyApplicationId}
    ```

U bent nu klaar met het maken van de Azure AD-toepassing waarmee u verifiëren vanaf uw aangepaste C#-toepassing. U hebt later in deze zelfstudie de volgende waarden nodig:

* TenantId
* SubscriptionId
* ApplicationId
* Wachtwoord

[lnk-authenticate-arm]: https://msdn.microsoft.com/library/azure/dn790557.aspx
[lnk-powershell-install]: /powershell/azure/install-az-ps
