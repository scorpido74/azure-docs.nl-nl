---
title: Verificatie strategieën voor Marketplace-meet service | Azure Marketplace
description: Verificatie strategieën voor meet service worden ondersteund in azure Marketplace.
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/03/2020
ms.openlocfilehash: 31b9d4d57e38adcd079082a4f32770c4cbc8fbb3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82736199"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Verificatie strategieën voor Marketplace-meet service

Marketplace meter service ondersteunt twee verificatie strategieën:

* [Beveiligings token van Azure AD](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
* [beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) 

Er wordt uitgelegd wanneer en hoe u de verschillende verificatie strategieën gebruikt voor het veilig verzenden van aangepaste meters met behulp van Marketplace-meet service.

## <a name="using-the-azure-ad-security-token"></a>Het Azure AD-beveiligings token gebruiken

Toepasselijke aanbiedings typen zijn SaaS en Azure-toepassingen met een beheerd type toepassings plan.  

Verzend aangepaste meters met behulp van een vooraf gedefinieerde ID voor de vaste toepassing die moet worden geverifieerd.

Voor SaaS-aanbiedingen is Azure AD de enige beschik bare optie.

Voor Azure-toepassingen met een beheerd toepassings abonnement kunt u overwegen deze strategie in de volgende gevallen te gebruiken:

* U hebt al een mechanisme om te communiceren met uw back-end-services en u wilt deze methode uitbreiden om aangepaste meters van een centrale service te verzenden.
* U hebt complexe aangepaste meters Logic.  Voer deze logica uit op een centrale locatie in plaats van de resources van de beheerde toepassing.

Zodra u uw toepassing hebt geregistreerd, kunt u via een programma een Azure AD-beveiligings token aanvragen. De uitgever wordt verwacht dit token te gebruiken en een aanvraag te doen om deze op te lossen.

Zie [Azure Active Directory toegangs tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)voor meer informatie over deze tokens.

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Een Token ophalen op basis van de Azure AD-app

#### <a name="http-method"></a>HTTP-methode

**POST**

#### <a name="request-url"></a>*Aanvraag-URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI-para meter*

|  **Parameternaam** |  **Vereist**  |  **Beschrijving**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   True         | De Tenant-ID van de geregistreerde Azure AD-toepassing.   |
| | | |

#### <a name="request-header"></a>*Aanvraagheader*

|  **Headernaam**    |  **Vereist**  |  **Beschrijving**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   True         | Het inhouds type dat is gekoppeld aan de aanvraag. De standaardwaarde is `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Aanvraag tekst*

|  **Eigenschaps naam**  |  **Vereist**  |  **Beschrijving**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   True         | Toekennings type. De standaardwaarde is `client_credentials`. |
|  `Client_id`        |   True         | Client/App-ID die is gekoppeld aan de Azure AD-app.|
|  `client_secret`    |   True         | Het wacht woord dat is gekoppeld aan de Azure AD-app.  |
|  `Resource`         |   True         | Doel resource waarvoor het token wordt aangevraagd. De standaardwaarde is `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`.  |
| | | |

#### <a name="response"></a>*Beantwoord*

|  **Naam**    |  **Type**  |  **Beschrijving**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | De aanvraag is voltooid.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Voorbeeld token voor antwoorden:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Het token Azure Managed Identities gebruiken

Toepasselijk aanbiedings type is Azure-toepassingen met een beheerd toepassings plan type.

Met deze methode kan de geïmplementeerde bronnen identiteit worden geverifieerd voor het verzenden van gebruiks gebeurtenissen voor aangepaste meters.  U kunt de code die gebruik uitmaakt, insluiten binnen de grenzen van uw implementatie.

>[!Note]
>Publisher moet ervoor zorgen dat de resources die gebruik maken, zijn vergrendeld, waardoor niet wordt geknoeid.

Uw beheerde toepassing kan een ander type resources bevatten, van Virtual Machines tot Azure Functions.  Zie [Managed Identities voor Azure resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview#how-can-i-use-managed-identities-for-azure-resources)voor meer informatie over het verifiëren met behulp van beheerde identiteiten voor verschillende services.

Volg de onderstaande stappen om te verifiëren met behulp van een Windows-VM,

1. Zorg ervoor dat beheerde identiteit is geconfigureerd met een van de volgende methoden:
    * [Azure Portal gebruikers interface](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)
    * [CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
    * [PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
    * [Azure Resource Manager-sjabloon](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
    * [REST](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-rest-vm#system-assigned-managed-identity)
    * [Azure-SDK's](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

1. Een toegangs token voor de service toepassings-ID (`20e940b3-4c77-4b0b-9a53-9e16a1b010a7`) voor Marketplace-licentie controle gebruiken met behulp van de systeem identiteit RDP van de VM, open de Power shell-console en voer de onderstaande opdracht uit

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. De beheerde App-ID ophalen uit de huidige ManagedBy-eigenschap van de resource groepen

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 
    
    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Marketplace-meet service vereist het gebruik van het rapport `resourceID`op een `resourceUsageId` en als een beheerde toepassing.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Gebruik de [Marketplace meter Service-API](https://review.docs.microsoft.com/azure/marketplace/partner-center-portal/marketplace-metering-service-apis?branch=pr-en-us-101847) om het gebruik te verzenden.

## <a name="next-steps"></a>Volgende stappen

* [Een Azure-toepassingsaanbieding maken](./create-new-azure-apps-offer.md)