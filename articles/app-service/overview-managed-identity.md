---
title: Beheerde identiteiten
description: Meer informatie over hoe beheerde identiteiten werken in Azure App Service en Azure Functions, hoe u een beheerde identiteit configureert en een token genereert voor een back-endbron.
author: mattchenderson
ms.topic: article
ms.date: 04/14/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 875d2bbebdfa95c6d180979399d876eb2afc01b4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392528"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Beheerde identiteiten gebruiken voor App Service en Azure-functies

> [!Important] 
> Beheerde identiteiten voor App Service en Azure-functies gedragen zich niet zoals verwacht als uw app is gemigreerd tussen abonnementen/tenants. De app moet een nieuwe identiteit verkrijgen, wat kan worden gedaan door de functie uit te schakelen en opnieuw in te schakelen. Zie [Hieronder een identiteit verwijderen.](#remove) Downstreambronnen moeten ook het toegangsbeleid hebben bijgewerkt om de nieuwe identiteit te gebruiken.

In dit onderwerp ziet u hoe u een beheerde identiteit maakt voor toepassingen voor App-service en Azure-functies en hoe u deze gebruiken om toegang te krijgen tot andere bronnen. Met een beheerde identiteit van Azure Active Directory (Azure AD) heeft uw app eenvoudig toegang tot andere door Azure AD beveiligde bronnen, zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform en vereist niet dat u geheimen indient of roteert. Zie [Beheerde identiteiten voor Azure-resources voor](../active-directory/managed-identities-azure-resources/overview.md)meer informatie over beheerde identiteiten in Azure AD.

Uw aanvraag kan worden toegekend twee soorten identiteiten:

- Een **door het systeem toegewezen identiteit** is gekoppeld aan uw toepassing en wordt verwijderd als uw app wordt verwijderd. Een app kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de gebruiker toegewezen identiteit is een zelfstandige **Azure-bron** die aan uw app kan worden toegewezen. Een app kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Voor het maken van een app met een door het systeem toegewezen identiteit moet een extra eigenschap op de toepassing worden ingesteld.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Als u een beheerde identiteit in de portal instelt, moet u eerst een toepassing als normaal aanmaken en vervolgens de functie inschakelen.

1. Maak een app in de portal zoals u dat normaal zou doen. Navigeer ernaar in de portal.

2. Als u een functie-app gebruikt, navigeert u naar **platformfuncties.** Voor andere app-typen bladert u omlaag naar de groep **Instellingen** in de linkernavigatie.

3. Selecteer **Identiteit**.

4. Schakel op het tabblad **Systeem toegewezen** **over** naar **Aan**. Klik op **Opslaan**.

    ![Beheerde identiteit in App-service](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Azure CLI gebruiken

Als u een beheerde identiteit wilt instellen met `az webapp identity assign` de Azure CLI, moet u de opdracht tegen een bestaande toepassing gebruiken. U hebt drie opties voor het uitvoeren van de voorbeelden in deze sectie:

- Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) vanuit de Azure-portal.
- Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk codeblok hieronder.
- [Installeer de nieuwste versie van Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale CLI-console gebruikt. 

Met de volgende stappen u een web-app maken en een identiteit toewijzen met de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login](/cli/azure/reference-index#az-login). Gebruik een account dat is gekoppeld aan het Azure-abonnement waaronder u de toepassing wilt implementeren:

    ```azurecli-interactive
    az login
    ```

2. Een webtoepassing maken met de CLI. Zie [App Service CLI-voorbeelden](../app-service/samples-cli.md)voor meer voorbeelden van het gebruik van de CLI met App Service:

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Voer `identity assign` de opdracht uit om de identiteit voor deze toepassing te maken:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met de volgende stappen u een web-app maken en een identiteit toewijzen met Azure PowerShell:

1. Installeer indien nodig de Azure PowerShell met de instructies in de `Login-AzAccount` [Azure PowerShell-handleiding](/powershell/azure/overview)en voer deze uit om een verbinding met Azure te maken.

2. Maak een webtoepassing met Azure PowerShell. Zie Voorbeeld van [App Service PowerShell](../app-service/samples-powershell.md)voor meer voorbeelden van het gebruik van Azure PowerShell met App Service:

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Voer `Set-AzWebApp -AssignIdentity` de opdracht uit om de identiteit voor deze toepassing te maken:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Een Azure Resource Manager-sjabloon kan worden gebruikt om de implementatie van uw Azure-resources te automatiseren. Zie Het implementeren van [resources in App-service](../app-service/deploy-complex-application-predictably.md) en [het automatiseren van resourceimplementatie in Azure-functies](../azure-functions/functions-infrastructure-as-code.md)voor meer informatie over het implementeren naar app-service en -functies.

Elke resource `Microsoft.Web/sites` van het type kan worden gemaakt met een identiteit door de volgende eigenschap op te geven in de resourcedefinitie:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Een toepassing kan zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten tegelijkertijd hebben. In dit geval `type` zou het pand`SystemAssigned,UserAssigned`

Als u het systeemtoegewezen type toevoegt, moet Azure de identiteit van uw toepassing maken en beheren.

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

De eigenschap tenantId identificeert tot welke Azure AD-tenant de identiteit behoort. De principalId is een unieke id voor de nieuwe identiteit van de toepassing. Binnen Azure AD heeft de serviceprincipal dezelfde naam die u aan uw appservice of Azure-functieinstantie hebt gegeven.

## <a name="add-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Als u een app maakt met een door de gebruiker toegewezen identiteit, moet u de identiteit maken en vervolgens de bron-id toevoegen aan uw app-config.

### <a name="using-the-azure-portal"></a>Azure Portal gebruiken

Eerst moet u een door de gebruiker toegewezen identiteitsbron maken.

1. Maak een door de gebruiker toegewezen beheerde identiteitsbron volgens [deze instructies](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Maak een app in de portal zoals u dat normaal zou doen. Navigeer ernaar in de portal.

3. Als u een functie-app gebruikt, navigeert u naar **platformfuncties.** Voor andere app-typen bladert u omlaag naar de groep **Instellingen** in de linkernavigatie.

4. Selecteer **Identiteit**.

5. Klik op het **tabblad Toegewezen gebruiker** op **Toevoegen**.

6. Zoek naar de identiteit die u eerder hebt gemaakt en selecteer deze. Klik op **Add**.

    ![Beheerde identiteit in App-service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon gebruiken

Een Azure Resource Manager-sjabloon kan worden gebruikt om de implementatie van uw Azure-resources te automatiseren. Zie Het implementeren van [resources in App-service](../app-service/deploy-complex-application-predictably.md) en [het automatiseren van resourceimplementatie in Azure-functies](../azure-functions/functions-infrastructure-as-code.md)voor meer informatie over het implementeren naar app-service en -functies.

Elke bron `Microsoft.Web/sites` van het type kan worden gemaakt met een identiteit `<RESOURCEID>` door het volgende blok op te nemen in de resourcedefinitie, te vervangen door de resource-id van de gewenste identiteit:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Een toepassing kan zowel door het systeem toegewezen als door de gebruiker toegewezen identiteiten tegelijkertijd hebben. In dit geval `type` zou het pand`SystemAssigned,UserAssigned`

Als u het door de gebruiker toegewezen type toevoegt, moet Azure de door de gebruiker toegewezen identiteit gebruiken die voor uw toepassing is opgegeven.

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

De principalId is een unieke id voor de identiteit die wordt gebruikt voor Azure AD-beheer. De clientId is een unieke id voor de nieuwe identiteit van de toepassing die wordt gebruikt voor het opgeven van welke identiteit moet worden gebruikt tijdens runtime-oproepen.

## <a name="obtain-tokens-for-azure-resources"></a>Tokens voor Azure-bronnen verkrijgen

Een app kan de beheerde identiteit gebruiken om tokens toegang te geven tot andere bronnen die worden beschermd door Azure AD, zoals Azure Key Vault. Deze tokens vertegenwoordigen de toepassing die toegang heeft tot de bron en geen specifieke gebruiker van de toepassing. 

Mogelijk moet u de doelbron configureren om toegang te krijgen vanuit uw toepassing. Als u bijvoorbeeld een token aanvraagt om toegang te krijgen tot Key Vault, moet u ervoor zorgen dat u een toegangsbeleid hebt toegevoegd dat de identiteit van uw toepassing bevat. Anders worden uw oproepen naar Key Vault geweigerd, zelfs als ze het token bevatten. Zie [Azure-services die Azure AD-verificatie ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)voor meer informatie over welke resources Azure Active Directory-tokens ondersteunen.

> [!IMPORTANT]
> De back-endservices voor beheerde identiteiten behouden een cache per resource URI gedurende ongeveer 8 uur. Als u het toegangsbeleid van een bepaalde doelbron bijwerkt en onmiddellijk een token voor die bron ophaalt, u een token in de cache blijven ontvangen met verouderde machtigingen totdat dat token verloopt. Er is momenteel geen manier om een tokenvernieuwing te forceren.

Er is een eenvoudig REST-protocol voor het verkrijgen van een token in App Service en Azure-functies. Dit kan worden gebruikt voor alle toepassingen en talen. Voor .NET en Java biedt de Azure SDK een abstractie over dit protocol en vergemakkelijkt het een lokale ontwikkelingservaring.

### <a name="using-the-rest-protocol"></a>Het REST-protocol gebruiken

Een app met een beheerde identiteit heeft twee omgevingsvariabelen gedefinieerd:

- IDENTITY_ENDPOINT - de URL naar de lokale tokenservice.
- IDENTITY_HEADER - een header die wordt gebruikt om te helpen server-side request forgery (SSRF) aanvallen te beperken. De waarde wordt geroteerd door het platform.

De **IDENTITY_ENDPOINT** is een lokale URL waaruit uw app tokens kan aanvragen. Als u een token voor een resource wilt krijgen, dient u een HTTP GET-verzoek in voor dit eindpunt, inclusief de volgende parameters:

> | Parameternaam    | In     | Beschrijving                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Query’s uitvoeren  | De Azure AD-bron URI van de bron waarvoor een token moet worden verkregen. Dit kan een van de [Azure-services zijn die Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) of een andere bron-URI ondersteunen.    |
> | api-versie       | Query’s uitvoeren  | De versie van de token-API die moet worden gebruikt. Gebruik "2019-08-01" of hoger.                                                                                                                                                                                                                                                                 |
> | X-IDENTITEIT-HEADER | Header | De waarde van de IDENTITY_HEADER omgevingsvariabele. Deze header wordt gebruikt om te helpen server-side request forgery (SSRF) aanvallen te beperken.                                                                                                                                                                                                    |
> | client_id         | Query’s uitvoeren  | (Optioneel) De client-ID van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Kan niet worden gebruikt `principal_id`op `mi_res_id`een `object_id`verzoek dat omvat , , of . Als alle ID-parameters `object_id`( `mi_res_id``client_id`, `principal_id`, en ) worden weggelaten, wordt de door het systeem toegewezen identiteit gebruikt.                                             |
> | principal_id      | Query’s uitvoeren  | (Optioneel) De hoofd-ID van de door de gebruiker toegewezen identiteit die moet worden gebruikt. `object_id`is een alias die in plaats daarvan kan worden gebruikt. Kan niet worden gebruikt op een verzoek dat client_id, mi_res_id of object_id bevat. Als alle ID-parameters `object_id`( `mi_res_id``client_id`, `principal_id`, en ) worden weggelaten, wordt de door het systeem toegewezen identiteit gebruikt. |
> | mi_res_id         | Query’s uitvoeren  | (Optioneel) De Azure-bron-id van de door de gebruiker toegewezen identiteit die moet worden gebruikt. Kan niet worden gebruikt `principal_id`op `client_id`een `object_id`verzoek dat omvat , , of . Als alle ID-parameters `object_id`( `mi_res_id``client_id`, `principal_id`, en ) worden weggelaten, wordt de door het systeem toegewezen identiteit gebruikt.                                      |

> [!IMPORTANT]
> Als u tokens probeert te verkrijgen voor door gebruikers toegewezen identiteiten, moet u een van de optionele eigenschappen opnemen. Anders probeert de tokenservice een token te verkrijgen voor een door het systeem toegewezen identiteit, die al dan niet bestaat.

Een succesvolle 200 OK-respons bevat een JSON-body met de volgende eigenschappen:

> | Naam van eigenschap | Beschrijving                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Het gevraagde toegangstoken. De bellende webservice kan dit token gebruiken om te verifiëren bij de ontvangende webservice.                                                                                                                               |
> | client_id     | De client-ID van de identiteit die werd gebruikt.                                                                                                                                                                                                       |
> | expires_on    | De tijdspanne wanneer het toegangstoken verloopt. De datum wordt weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" `exp` (komt overeen met de claim van het token).                                                                                |
> | not_before    | De tijdspanne waarin het toegangstoken van kracht wordt en kan worden geaccepteerd. De datum wordt weergegeven als het aantal seconden van "1970-01-01T0:0:0Z UTC" `nbf` (komt overeen met de claim van het token).                                                      |
> | resource      | De bron waarvoor het toegangstoken is `resource` aangevraagd, komt overeen met de parameter querytekenreeks van de aanvraag.                                                                                                                               |
> | token_type    | Geeft de waarde van het tokentype aan. Het enige type dat Azure AD ondersteunt, is FBearer. Zie [Het OAuth 2.0 Authorization Framework: Toondertokengebruik (RFC 6750) voor](https://www.rfc-editor.org/rfc/rfc6750.txt)meer informatie over tokens aan toonder. |

Dit antwoord is hetzelfde als het [antwoord voor de Azure AD-service-to-service-tokenaanvraag](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)voor toegangstoegang .

> [!NOTE]
> Een oudere versie van dit protocol, met behulp van de API-versie "2017-09-01", gebruikte de `secret` header in plaats van `X-IDENTITY-HEADER` en accepteerde alleen de eigenschap voor door de `clientid` gebruiker toegewezen. Het keerde `expires_on` ook de in een timestamp formaat. MSI_ENDPOINT kan worden gebruikt als alias voor IDENTITY_ENDPOINT en MSI_SECRET kan worden gebruikt als alias voor IDENTITY_HEADER.

### <a name="rest-protocol-examples"></a>Voorbeelden van REST-protocol

Een voorbeeldaanvraag kan er als volgt uitzien:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

En een voorbeeldreactie kan er als volgt uitzien:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Codevoorbeelden

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> Voor .NET-talen u ook [Microsoft.Azure.Services.AppAuthentication](#asal) gebruiken in plaats van dit verzoek zelf te maken.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>De Microsoft.Azure.Services.AppAuthentication-bibliotheek voor .NET gebruiken

Voor .NET-toepassingen en -functies is de eenvoudigste manier om met een beheerde identiteit te werken het Microsoft.Azure.Services.AppAuthentication-pakket. Met deze bibliotheek u uw code ook lokaal testen op uw ontwikkelingsmachine met behulp van uw gebruikersaccount van Visual Studio, Azure [CLI](/cli/azure)of Active Directory Integrated Authentication. Zie de [verwijzing naar Microsoft.Azure.Services.AppAuthentication]voor meer informatie over lokale ontwikkelingsopties met deze bibliotheek. In deze sectie ziet u hoe u aan de slag met de bibliotheek in uw code.

1. Voeg verwijzingen naar de [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) en alle andere noodzakelijke NuGet-pakketten toe aan uw toepassing. In het onderstaande voorbeeld wordt ook gebruik gebruikt [voor Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Voeg de volgende code toe aan uw toepassing en wijzig om de juiste bron te targeten. In dit voorbeeld worden twee manieren weergegeven om met Azure Key Vault te werken:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Zie de [verwijzing Microsoft.Azure.Services.AppAuthentication] en de bewerkingen die worden blootgesteld voor meer informatie over Microsoft.Azure.Services.AppAuthentication en de [voorbeeldgroep Microsoft.Azure.Services.AppAuthentication](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)en de bewerkingen die worden blootgesteld.

### <a name="using-the-azure-sdk-for-java"></a>De Azure SDK voor Java gebruiken

Voor Java-toepassingen en -functies is de eenvoudigste manier om met een beheerde identiteit te werken via de [Azure SDK voor Java.](https://github.com/Azure/azure-sdk-for-java) In deze sectie ziet u hoe u aan de slag met de bibliotheek in uw code.

1. Voeg een verwijzing toe naar de [Azure SDK-bibliotheek](https://mvnrepository.com/artifact/com.microsoft.azure/azure). Voor Maven-projecten u dit `dependencies` fragment toevoegen aan het gedeelte van het POM-bestand van het project:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Gebruik `AppServiceMSICredentials` het object voor verificatie. In dit voorbeeld ziet u hoe dit mechanisme kan worden gebruikt voor het werken met Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie met behulp van de portal, PowerShell of CLI op dezelfde manier uit te schakelen als deze is gemaakt. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u alle identiteiten wilt verwijderen, stelt u het type in op 'Geen' in de [ARM-sjabloon:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook verwijderd uit Azure AD. Door het systeem toegewezen identiteiten worden ook automatisch uit Azure AD verwijderd wanneer de app-bron wordt verwijderd.

> [!NOTE]
> Er is ook een toepassingsinstelling die kan worden ingesteld, WEBSITE_DISABLE_MSI, die alleen de lokale tokenservice uitschakelt. Het laat de identiteit echter op zijn plaats en tooling toont nog steeds de beheerde identiteit als 'aan' of 'ingeschakeld'. Als gevolg hiervan wordt het gebruik van deze instelling niet aanbevolen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [SQL-database veilig openen met een beheerde identiteit](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication-verwijzing]: https://go.microsoft.com/fwlink/p/?linkid=862452
