---
title: Beheerde identiteiten
description: Meer informatie over hoe beheerde identiteiten werken in Azure App Service en Azure Functions, hoe u een beheerde identiteit kunt configureren en een token voor een back-end-bron kunt genereren.
author: mattchenderson
ms.topic: article
ms.date: 10/30/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: f341f5bbf7221664301ca53eea1edd6af7544950
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422007"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Beheerde identiteiten gebruiken voor App Service en Azure Functions

> [!Important] 
> Beheerde identiteiten voor App Service en Azure Functions werken niet zoals verwacht als uw app wordt gemigreerd tussen abonnementen/tenants. De app moet een nieuwe identiteit verkrijgen. Dit kan worden gedaan door de functie uit te scha kelen en opnieuw in te scha kelen. Zie hieronder [een identiteit verwijderen](#remove) . Downstream-resources moeten ook toegangs beleid hebben bijgewerkt voor het gebruik van de nieuwe identiteit.

In dit onderwerp wordt beschreven hoe u een beheerde identiteit kunt maken voor App Service en Azure Functions toepassingen en hoe u deze kunt gebruiken om toegang te krijgen tot andere resources. Met een beheerde identiteit van Azure Active Directory kunt u via uw app eenvoudig toegang krijgen tot andere door AAD beveiligde resources, zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform en u hoeft geen geheimen in te richten of te draaien. Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in Aad.

Aan uw toepassing kunnen twee typen identiteiten worden verleend: 
- Een door het **systeem toegewezen identiteit** is gekoppeld aan uw toepassing en wordt verwijderd als uw app wordt verwijderd. Een app kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de **gebruiker toegewezen identiteit** is een zelfstandige Azure-resource die aan uw app kan worden toegewezen. Een app kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="adding-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Voor het maken van een app met een door het systeem toegewezen identiteit moet er een extra eigenschap worden ingesteld voor de toepassing.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal instelt, moet u eerst een toepassing als normaal aanmaken en vervolgens de functie inschakelen.

1. Maak een app in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.

2. Als u een functie-app gebruikt, navigeert u naar **platform functies**. Voor andere typen apps schuift u omlaag naar de **instellingen** groep in het linkernavigatievenster.

3. Selecteer **identiteit**.

4. Schakel op het tabblad **systeem toegewezen** de optie **status** in **op aan**. Klik op **Opslaan**.

    ![Beheerde identiteit in App Service](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, moet u de `az webapp identity assign`-opdracht gebruiken voor een bestaande toepassing. Er zijn drie opties voor het uitvoeren van de voor beelden in deze sectie:

- Gebruik [Azure Cloud shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de Inge sloten Azure Cloud Shell via de knop ' try-button ', in de rechter bovenhoek van elk hieronder opgenomen code blok.
- [Installeer de nieuwste versie van Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale cli-console gebruikt. 

De volgende stappen helpen u bij het maken van een web-app en het toewijzen van een-id met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de toepassing wilt implementeren:

    ```azurecli-interactive
    az login
    ```
2. Maak een webtoepassing met behulp van de CLI. Voor meer voor beelden van het gebruik van de CLI met App Service raadpleegt u [app service cli](../app-service/samples-cli.md)-voor beelden:

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Voer de `identity assign` opdracht uit om de identiteit voor deze toepassing te maken:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

De volgende stappen helpen u bij het maken van een web-app en het toewijzen van een identiteit met behulp van Azure PowerShell:

1. Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/overview) en voer vervolgens `Login-AzAccount` uit om verbinding te maken met Azure.

2. Maak een webtoepassing met behulp van Azure PowerShell. Zie voor meer voor beelden van het gebruik van Azure PowerShell met App Service [app Service Power shell](../app-service/samples-powershell.md)-voor beelden:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Voer de `Set-AzWebApp -AssignIdentity` opdracht uit om de identiteit voor deze toepassing te maken:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Een Azure Resource Manager sjabloon kan worden gebruikt voor het automatiseren van de implementatie van uw Azure-resources. Zie voor meer informatie over de implementatie van App Service en functions de [implementatie van resources automatiseren in app service](../app-service/deploy-complex-application-predictably.md) en de [implementatie van resources in azure functions automatiseren](../azure-functions/functions-infrastructure-as-code.md).

Een resource van het type `Microsoft.Web/sites` kan worden gemaakt met een identiteit door de volgende eigenschap op te nemen in de resource definitie:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Een toepassing kan gelijktijdig een door het systeem toegewezen en door de gebruiker toegewezen identiteiten hebben. In dit geval wordt de eigenschap `type` `SystemAssigned,UserAssigned`

Door het door het systeem toegewezen type toe te voegen, vertelt Azure om de identiteit voor uw toepassing te maken en te beheren.

Een web-app kan er bijvoorbeeld als volgt uitzien:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Wanneer de site wordt gemaakt, heeft deze de volgende aanvullende eigenschappen:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Waar `<TENANTID>` en `<PRINCIPALID>` worden vervangen door GUID'S. De eigenschap tenantId identificeert de AAD-Tenant waarvan de identiteit deel uitmaakt. De principalId is een unieke id voor de nieuwe identiteit van de toepassing. Binnen AAD heeft de service-principal dezelfde naam die u hebt gegeven aan uw App Service-of Azure Functions-exemplaar.


## <a name="adding-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Voor het maken van een app met een door de gebruiker toegewezen identiteit moet u de identiteit maken en vervolgens de resource-id toevoegen aan uw app-configuratie.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Eerst moet u een door de gebruiker toegewezen id-resource maken.

1. Maak een door de gebruiker toegewezen beheerde identiteits bron op basis van [deze instructies](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Maak een app in de portal zoals u dat gewend bent. Navigeer ernaar in de portal.

3. Als u een functie-app gebruikt, navigeert u naar **platform functies**. Voor andere typen apps schuift u omlaag naar de **instellingen** groep in het linkernavigatievenster.

4. Selecteer **identiteit**.

5. Klik op het tabblad **toegewezen door gebruiker** op **toevoegen**.

6. Zoek de identiteit die u eerder hebt gemaakt en selecteer deze. Klik op **Add**.

    ![Beheerde identiteit in App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Een Azure Resource Manager sjabloon kan worden gebruikt voor het automatiseren van de implementatie van uw Azure-resources. Zie voor meer informatie over de implementatie van App Service en functions de [implementatie van resources automatiseren in app service](../app-service/deploy-complex-application-predictably.md) en de [implementatie van resources in azure functions automatiseren](../azure-functions/functions-infrastructure-as-code.md).

Een resource van het type `Microsoft.Web/sites` kan worden gemaakt met een identiteit door het volgende blok in de resource definitie op te nemen, waarbij u `<RESOURCEID>` vervangt door de resource-ID van de gewenste identiteit:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Een toepassing kan gelijktijdig een door het systeem toegewezen en door de gebruiker toegewezen identiteiten hebben. In dit geval wordt de eigenschap `type` `SystemAssigned,UserAssigned`

Als u het door de gebruiker toegewezen type toevoegt, krijgt Azure de door de gebruiker toegewezen identiteit te gebruiken die voor uw toepassing is opgegeven.

Een web-app kan er bijvoorbeeld als volgt uitzien:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Wanneer de site wordt gemaakt, heeft deze de volgende aanvullende eigenschappen:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Waar `<PRINCIPALID>` en `<CLIENTID>` worden vervangen door GUID'S. De principalId is een unieke id voor de identiteit die wordt gebruikt voor AAD-beheer. De clientId is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt om op te geven welke identiteit tijdens runtime-aanroepen moet worden gebruikt.


## <a name="obtaining-tokens-for-azure-resources"></a>Tokens verkrijgen voor Azure-resources

Een app kan de identiteit ervan gebruiken om tokens te verkrijgen voor andere bronnen die worden beveiligd door AAD, zoals Azure Key Vault. Deze tokens vertegenwoordigen de toepassing die toegang heeft tot de resource en geen specifieke gebruiker van de toepassing. 

> [!IMPORTANT]
> Mogelijk moet u de doel bron configureren om toegang toe te staan vanuit uw toepassing. Als u bijvoorbeeld een token aan Key Vault vraagt, moet u ervoor zorgen dat u een toegangs beleid hebt toegevoegd dat de identiteit van uw toepassing bevat. Anders worden uw aanroepen naar Key Vault geweigerd, zelfs als ze het token bevatten. Zie [Azure-Services die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor meer informatie over welke bronnen Azure Active Directory-tokens ondersteunen.

Er is een eenvoudig REST-protocol voor het verkrijgen van een token in App Service en Azure Functions. Dit kan worden gebruikt voor alle toepassingen en talen. Voor sommige .NET en Java biedt de Azure SDK een samen vatting van dit protocol en wordt een lokale ontwikkel ervaring vergemakkelijkt.

### <a name="using-the-rest-protocol"></a>Het REST-protocol gebruiken

Er zijn twee omgevings variabelen gedefinieerd voor een app met een beheerde identiteit:

- MSI_ENDPOINT: de URL naar de lokale token service.
- MSI_SECRET: een kop die wordt gebruikt om SSRF-aanvallen (server-side Request vervalsing) te voor komen. De waarde wordt geroteerd door het platform.

De **MSI_ENDPOINT** is een lokale URL van waaruit uw app tokens kan aanvragen. Als u een token voor een resource wilt ophalen, maakt u een HTTP GET-aanvraag naar dit eind punt, met inbegrip van de volgende para meters:

> |Parameternaam|In|Beschrijving|
> |-----|-----|-----|
> |resource|Query|De AAD-resource-URI van de resource waarvoor een token moet worden verkregen. Dit kan een van de [Azure-Services zijn die ondersteuning bieden voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) of een andere resource-URI.|
> |api-version|Query|De versie van de token-API die moet worden gebruikt. "2017-09-01" is momenteel de enige versie die wordt ondersteund.|
> |geheim|Header|De waarde van de omgevings variabele MSI_SECRET. Deze header wordt gebruikt om SSRF-aanvallen (server-side Request vervalsing) te voor komen.|
> |clientid|Query|(Optioneel tenzij gebruikers toegewezen) De ID van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Als u dit weglaat, wordt de door het systeem toegewezen identiteit gebruikt.|

> [!IMPORTANT]
> Als u probeert tokens te verkrijgen voor door de gebruiker toegewezen identiteiten, moet u de eigenschap `clientid` toevoegen. Anders probeert de token service een token te verkrijgen voor een door het systeem toegewezen identiteit, die al dan niet bestaat.

Een geslaagd 200 OK-antwoord bevat een JSON-hoofd tekst met de volgende eigenschappen:

> |Naam van eigenschap|Beschrijving|
> |-------------|----------|
> |access_token|Het aangevraagde toegangs token. De aanroepende webservice kan dit token gebruiken om te verifiëren bij de ontvangende webservice.|
> |expires_on|Het tijdstip waarop het toegangs token verloopt. De datum wordt weer gegeven als het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de verloop tijd. Deze waarde wordt gebruikt om de levens duur van tokens in de cache te bepalen.|
> |resource|De App-ID-URI van de ontvangende webservice.|
> |token_type|Geeft de waarde van het token type aan. Het enige type dat door Azure AD wordt ondersteund, is Bearer. Zie voor meer informatie over Bearer-tokens [het OAuth 2,0 Authorization Framework: Bearer-token gebruik (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt).|

Dit antwoord is hetzelfde als het [antwoord voor de aanvraag van de Aad service-to-service-toegangs token](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> Omgevings variabelen worden ingesteld wanneer het proces voor het eerst wordt gestart, dus nadat u een beheerde identiteit voor uw toepassing hebt ingeschakeld, moet u de toepassing mogelijk opnieuw opstarten of de code opnieuw implementeren voordat `MSI_ENDPOINT` en `MSI_SECRET` beschikbaar zijn voor uw code.

### <a name="rest-protocol-examples"></a>Voor beelden van REST-protocollen

Een voorbeeld aanvraag kan er als volgt uitzien:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

En een voor beeld van een antwoord kan er als volgt uitzien:

```
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Codevoorbeelden

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

> [!TIP]
> Voor .NET-talen kunt u ook gebruikmaken van [micro soft. Azure. Services. AppAuthentication](#asal) in plaats van deze aanvraag zelf te vervaardigen.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2017-09-01", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource));
    request.Headers.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await _client.SendAsync(request);
}
```

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=2017-09-01`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version=2017-09-01"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=2017-09-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="asal"></a>De bibliotheek micro soft. Azure. Services. AppAuthentication voor .NET gebruiken

Voor .NET-toepassingen en-functies is de eenvoudigste manier om te werken met een beheerde identiteit via het pakket micro soft. Azure. Services. AppAuthentication. Met deze bibliotheek kunt u uw code ook lokaal op uw ontwikkel computer testen met behulp van uw gebruikers account uit Visual Studio, de [Azure cli](/cli/azure)of Active Directory geïntegreerde verificatie. Zie de [Naslag informatie over micro soft. Azure. Services. AppAuthentication]voor meer informatie over de lokale ontwikkelings opties voor deze bibliotheek. In deze sectie wordt beschreven hoe u aan de slag kunt met de bibliotheek in uw code.

1. Voeg verwijzingen naar [micro soft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en alle andere benodigde NuGet-pakketten toe aan uw toepassing. In het onderstaande voor beeld wordt ook gebruikgemaakt van [micro soft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)de sleutel kluis.

2. Voeg de volgende code toe aan uw toepassing, waarbij u de juiste resource wijzigt. In dit voor beeld ziet u twee manieren om te werken met Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Zie voor meer informatie over micro soft. Azure. Services. AppAuthentication en de bewerkingen die worden weer gegeven, de naslag informatie over [Naslag informatie over micro soft. Azure. Services. AppAuthentication] en het [app service en de sleutel kluis met MSI .net-voor beeld](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>De Azure SDK voor Java gebruiken

Voor Java-toepassingen en-functies is de eenvoudigste manier om te werken met een beheerde identiteit via de [Azure SDK voor Java](https://github.com/Azure/azure-sdk-for-java). In deze sectie wordt beschreven hoe u aan de slag kunt met de bibliotheek in uw code.

1. Voeg een verwijzing naar de [Azure SDK-bibliotheek](https://mvnrepository.com/artifact/com.microsoft.azure/azure)toe. Voor maven-projecten kunt u dit fragment toevoegen aan de sectie `dependencies` van het POM-bestand van het project:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Het `AppServiceMSICredentials`-object gebruiken voor verificatie. Dit voor beeld laat zien hoe dit mechanisme kan worden gebruikt voor het werken met Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te scha kelen met behulp van de portal, Power shell of CLI op dezelfde manier als waarop deze is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, moet u in het REST/ARM-sjabloon protocol dit doen door het type in te stellen op ' geen ':

```json
"identity": {
    "type": "None"
}
```

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit AAD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit AAD wanneer de app-resource wordt verwijderd.

> [!NOTE]
> Er is ook een toepassings instelling die kan worden ingesteld, WEBSITE_DISABLE_MSI, waarmee de lokale token service wordt uitgeschakeld. De identiteit blijft echter aanwezig en er wordt nog steeds de beheerde identiteit weer gegeven als ' aan ' of ' ingeschakeld '. Als gevolg hiervan wordt het gebruik van deze instelling niet aanbevolen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Veilig toegang SQL Database met behulp van een beheerde identiteit](app-service-web-tutorial-connect-msi.md)

[Naslag informatie over micro soft. Azure. Services. AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
