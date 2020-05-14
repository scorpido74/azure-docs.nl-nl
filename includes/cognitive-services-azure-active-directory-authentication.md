---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: ddc61a0d0cb5a630282a9ba0589cef6fda29c4b5
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83343460"
---
## <a name="authenticate-with-azure-active-directory"></a>Verifiëren bij Azure Active Directory

> [!IMPORTANT]
> 1. Momenteel worden **alleen** de Computer Vision-API, Face-API, Text Analytics-API, insluitende lezer, formulier herkenning, afwijkings detectie en alle Bing-services ondersteund, met uitzonde ring van Bing aangepaste zoekopdrachten ondersteuning voor verificatie met behulp van Azure Active Directory (Aad).
> 2. AAD-verificatie moet altijd worden gebruikt in combi natie met aangepaste subdomeinnaam van uw Azure-resource. [Regionale eind punten](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#is-there-a-list-of-regional-endpoints) bieden geen ondersteuning voor Aad-verificatie.

In de vorige secties laten we u zien hoe u met Azure Cognitive Services kunt verifiëren met behulp van een single-service of een sleutel van een abonnement op meerdere services. Hoewel deze sleutels een snel en eenvoudig pad bieden om de ontwikkeling te starten, vallen ze op korter complexe scenario's waarvoor op rollen gebaseerd toegangs beheer is vereist. Laten we eens kijken wat u nodig hebt om te verifiëren met behulp van Azure Active Directory (AAD).

In de volgende secties gebruikt u de Azure Cloud Shell-omgeving of de Azure CLI voor het maken van een subdomein, het toewijzen van rollen en het verkrijgen van een Bearer-token om de Azure Cognitive Services aan te roepen. Als u vastloopt, worden er links in elke sectie weer gegeven met alle beschik bare opties voor elke opdracht in Azure Cloud Shell/Azure CLI.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Een resource maken met een aangepast subdomein

De eerste stap is het maken van een aangepast subdomein. Als u een bestaande Cognitive Services resource zonder aangepaste subdomeinnaam wilt gebruiken, volgt u de instructies in [Cognitive Services aangepaste subdomeinen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains#how-does-this-impact-existing-resources) om het aangepaste subdomein voor uw resource in te scha kelen.

1. Begin met het openen van de Azure Cloud Shell. [Selecteer vervolgens een abonnement](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext?view=azps-3.3.0):

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. Maak vervolgens [een Cognitive Services resource](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) met een aangepast subdomein. De naam van het subdomein moet globaal uniek zijn en mag geen speciale tekens bevatten, zoals: ".", "!", ",".

   ```powershell-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Als dit lukt, moet het **eind punt** de subdomeinnaam weer geven die uniek is voor uw resource.


### <a name="assign-a-role-to-a-service-principal"></a>Een rol toewijzen aan een Service-Principal

Nu u een aangepast subdomein hebt dat aan uw resource is gekoppeld, moet u een rol aan een Service-Principal toewijzen.

> [!NOTE]
> Houd er rekening mee dat AAD-roltoewijzingen tot vijf minuten kunnen duren.

1. Eerst gaan we een Aad- [toepassing](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0)registreren.

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   U hebt de **ApplicationId** nodig in de volgende stap.

2. Vervolgens moet u [een service-principal maken](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) voor de Aad-toepassing.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Als u een toepassing registreert in de Azure Portal, wordt deze stap voor u voltooid.

3. De laatste stap is het [toewijzen van de rol ' Cognitive Services gebruiker '](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) aan de Service-Principal (bereik van de resource). Door een rol toe te wijzen, verleent u Service-Principal-toegang tot deze resource. U kunt dezelfde service-principal toegang verlenen tot meerdere resources in uw abonnement.
   >[!NOTE]
   > De ObjectId van de service-principal wordt gebruikt, niet de ObjectId voor de toepassing.
   > De ACCOUNT_ID is de Azure-resource-id van het Cognitive Services account dat u hebt gemaakt. U vindt de Azure-resource-id van ' Eigenschappen ' van de resource in Azure Portal.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Voorbeeld aanvraag

In dit voor beeld wordt een wacht woord gebruikt voor het verifiëren van de Service-Principal. Het gegeven token wordt vervolgens gebruikt om de Computer Vision-API aan te roepen.

1. Down load uw **TenantId**:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Een Token ophalen:
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Roep de Computer Vision-API aan:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

U kunt de Service-Principal ook verifiëren met een certificaat. Naast de service-principal wordt ook User Principal ondersteund door de machtigingen te delegeren via een andere AAD-toepassing. In dit geval, in plaats van wacht woorden of certificaten, wordt gebruikers gevraagd om twee ledige verificatie bij het ophalen van token.

## <a name="authorize-access-to-managed-identities"></a>Toegang tot beheerde identiteiten toestaan
 
Cognitive Services ondersteuning voor Azure Active Directory (Azure AD)-verificatie met [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Beheerde identiteiten voor Azure-resources kunnen toegang tot Cognitive Services resources toestaan via Azure AD-referenties van toepassingen die worden uitgevoerd in azure virtual machines (Vm's), functie-apps, schaal sets voor virtuele machines en andere services. Door beheerde identiteiten voor Azure-resources te gebruiken in combi natie met Azure AD-verificatie kunt u voor komen dat referenties worden opgeslagen in uw toepassingen die in de cloud worden uitgevoerd.  

### <a name="enable-managed-identities-on-a-vm"></a>Beheerde identiteiten op een virtuele machine inschakelen

Voordat u beheerde identiteiten voor Azure-resources kunt gebruiken om toegang te verlenen tot Cognitive Services resources van uw VM, moet u beheerde identiteiten voor Azure-resources inschakelen op de VM. Zie voor meer informatie over het inschakelen van beheerde identiteiten voor Azure-resources:

- [Azure Portal](https://docs.microsoft.com/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm)
- [Azure PowerShell](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm)
- [Azure-CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm)
- [Azure Resource Manager sjabloon](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm)
- [Client bibliotheken Azure Resource Manager](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm)

Zie [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)voor meer informatie over beheerde identiteiten.
