---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: 8754504655cdd08c9bf9f89311cb6c5d1057f0e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78262410"
---
## <a name="authenticate-with-azure-active-directory"></a>Verifiëren bij Azure Active Directory

> [!IMPORTANT]
> 1. Momenteel zijn **alleen** de Computer Vision API, Face API, Text Analytics API, Immersive Reader, Form Recognizer, Anomaly Detector en alle Bing Services behalve Bing Custom Search support authentication met Azure Active Directory (AAD).
> 2. AAD-verificatie moet altijd worden gebruikt in combinatie met de aangepaste subdomeinnaam van uw Azure-bron. [Regionale eindpunten](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) ondersteunen geen AAD-verificatie.

In de vorige secties lieten we u zien hoe u verifiëren tegen Azure Cognitive Services met behulp van een abonnementssleutel voor één service of meerdere services. Hoewel deze toetsen een snel en eenvoudig pad bieden om de ontwikkeling te starten, schieten ze tekort in complexere scenario's waarvoor op rollen gebaseerde toegangscontroles nodig zijn. Laten we eens kijken wat er nodig is om te verifiëren met Azure Active Directory (AAD).

In de volgende secties gebruikt u de Azure Cloud Shell-omgeving of de Azure CLI om een subdomein te maken, rollen toe te wijzen en een token aan toonder te verkrijgen om de Azure Cognitive Services aan te roepen. Als u vastkomt te zitten, worden koppelingen in elke sectie geleverd met alle beschikbare opties voor elke opdracht in Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Een resource maken met een aangepast subdomein

De eerste stap is het maken van een aangepast subdomein. Als u een bestaande resource voor Cognitive Services wilt gebruiken die geen aangepaste subdomeinnaam heeft, volgt u de instructies in [aangepaste subdomeinen voor cognitieve services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) om het aangepaste subdomein voor uw resource in te schakelen.

1. Begin met het openen van de Azure Cloud Shell. [Selecteer vervolgens een abonnement:](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0)

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Maak vervolgens [een cognitive services-bron](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) met een aangepast subdomein. De subdomeinnaam moet wereldwijd uniek zijn en mag geen speciale tekens bevatten, zoals: "",, "!", ","."

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Als dit is gelukt, moet het **eindpunt** de subdomeinnaam weergeven die uniek is voor uw resource.


### <a name="assign-a-role-to-a-service-principal"></a>Een rol toewijzen aan een serviceprincipal

Nu u een aangepast subdomein aan uw resource hebt gekoppeld, moet u een rol toewijzen aan een serviceprincipal.

> [!NOTE]
> Houd er rekening mee dat het tot vijf minuten kan duren voordat AAD-roltoewijzingen worden verspreid.

1. Laten we eerst een [AAD-aanvraag](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)registreren.

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Je hebt de **ApplicationId** nodig in de volgende stap.

2. Vervolgens moet u [een serviceprincipal maken](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) voor de AAD-toepassing.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Als u een toepassing registreert in de Azure-portal, wordt deze stap voor u voltooid.

3. De laatste stap is het [toewijzen van de rol 'Gebruiker van cognitieve services'](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) aan de serviceprincipal (scoped naar de resource). Door een rol toe te kennen, verleent u serviceprincipal toegang tot deze bron. U dezelfde serviceprincipal toegang verlenen tot meerdere bronnen in uw abonnement.
   >[!NOTE]
   > De ObjectId van de serviceprincipal wordt gebruikt, niet de ObjectId voor de toepassing.
   > De ACCOUNT_ID is de Azure-bron-id van het Cognitive Services-account dat u hebt gemaakt. U Azure-bron-id vinden van 'eigenschappen' van de bron in Azure-portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Voorbeeldaanvraag

In dit voorbeeld wordt een wachtwoord gebruikt om de serviceprincipal te verifiëren. Het opgegeven token wordt vervolgens gebruikt om de Computer Vision API aan te roepen.

1. Haal uw **TenantId:**
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Ontvang een token:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Roep de Computer Vision API:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Als alternatief kan de serviceprincipal worden geverifieerd met een certificaat. Naast serviceprincipal wordt user principal ook ondersteund door het laten delegeren van machtigingen via een andere AAD-toepassing. In dit geval, in plaats van wachtwoorden of certificaten, zouden gebruikers worden gevraagd om twee-factor authenticatie bij het verwerven van token.
