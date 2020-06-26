---
title: Een clienttoepassing verifiëren
titleSuffix: Azure Digital Twins
description: Zie een client toepassing verifiëren op basis van de Azure Digital Apparaatdubbels-service.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e52307c92d9371af6479f64841c6f269ed10e4b4
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85390819"
---
# <a name="authenticate-a-client-application-with-azure-digital-twins"></a>Een client toepassing verifiëren met Azure Digital Apparaatdubbels

Nadat u [een Azure Digital apparaatdubbels-exemplaar hebt gemaakt](how-to-set-up-instance.md), kunt u een client toepassing maken die u gaat gebruiken om te communiceren met het exemplaar. Wanneer u een eerste client project hebt ingesteld, wordt in dit artikel beschreven hoe u de client toepassing op de juiste wijze verifieert met het Azure Digital Apparaatdubbels-exemplaar.

Dit gebeurt in twee stappen:
1. Een app-registratie maken
2. Verificatie code schrijven in een client toepassing

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-an-app-registration"></a>Een app-registratie maken

Als u wilt verifiëren tegen Azure Digital Apparaatdubbels vanuit een client toepassing, moet u een **app-registratie** instellen in [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md).

Met deze app-registratie kunt u toegangs machtigingen voor de [Azure Digital apparaatdubbels-api's](how-to-use-apis-sdks.md)configureren. Uw client-app wordt geverifieerd aan de hand van de app-registratie en daarom worden de geconfigureerde toegangs machtigingen voor de Api's verleend.

Als u een app-registratie wilt maken, moet u de resource-Id's voor de Azure Digital Apparaatdubbels-Api's en de basislijn machtigingen voor de API opgeven. Open een nieuw bestand in uw werkmap en voer het volgende JSON-fragment in om deze details te configureren: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Sla dit bestand *op alsmanifest.jsop*.

> [!NOTE] 
> Er zijn enkele locaties waar een ' vriendelijke ', lees bare teken reeks `https://digitaltwins.azure.net` kan worden gebruikt voor de app-id van de Azure Digital apparaatdubbels-bron in plaats van de GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Zo kunnen bijvoorbeeld veel voor beelden in deze documentatieset authenticatie gebruiken met de MSAL-bibliotheek, en de beschrijvende teken reeks kan hiervoor worden gebruikt. Tijdens deze stap voor het maken van de app-registratie is de GUID-vorm van de ID echter vereist, zoals hierboven wordt weer gegeven. 

Klik in het venster Cloud Shell op het pictogram bestanden uploaden/downloaden en kies uploaden.

:::image type="content" source="media/how-to-authenticate-client/upload-extension.png" alt-text="Cloud Shell venster met de selectie van de Upload optie":::
Ga naar de *manifest.js* die u zojuist hebt gemaakt en klik op openen.

Voer vervolgens de volgende opdracht uit om een app-registratie te maken (waar nodig tijdelijke aanduidingen vervangen):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

De uitvoer van deze opdracht ziet er ongeveer als volgt uit.

:::image type="content" source="media/how-to-authenticate-client/new-app-registration.png" alt-text="Nieuwe AAD-app-registratie":::

Nadat u de app-registratie hebt gemaakt, klikt u op [deze koppeling](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) om naar de pagina Registratie overzicht van Aad-app in het Azure portal te gaan.

Selecteer in dit overzicht de app-registratie die u zojuist hebt gemaakt in de lijst. Hiermee opent u de details op een pagina zoals deze:

:::image type="content" source="media/how-to-authenticate-client/get-authentication-ids.png" alt-text="Azure Portal: verificatie-Id's":::

Noteer de ID van de *toepassings* -id en de *Directory (Tenant)* die op **de** pagina wordt weer gegeven. U gebruikt deze waarden later om een client-app te verifiëren tegen de Azure Digital Apparaatdubbels-Api's.

> [!NOTE]
> Afhankelijk van uw scenario moet u mogelijk aanvullende wijzigingen aanbrengen in de app-registratie. Hier volgen enkele algemene vereisten waaraan u mogelijk moet voldoen:
> * Open bare client toegang activeren
> * Specifieke antwoord-Url's instellen voor web-en bureaublad toegang
> * Impliciete OAuth2-verificatie stromen toestaan
> * Als uw Azure-abonnement is gemaakt met behulp van een Microsoft-account zoals Live, Xbox of Hotmail, moet u de *signInAudience* voor de app-registratie instellen ter ondersteuning van persoonlijke accounts.
> De eenvoudigste manier om deze instellingen in te stellen is door gebruik te maken van de [Azure Portal](https://portal.azure.com/). Zie [een toepassing registreren bij het micro soft Identity-platform](https://docs.microsoft.com/graph/auth-register-app-v2)voor meer informatie over dit proces.

## <a name="write-client-app-authentication-code-net-c-sdk"></a>Client-App-verificatie code schrijven: .NET (C#) SDK

In deze sectie wordt beschreven welke code u moet gebruiken in uw client toepassing om het verificatie proces te volt ooien met behulp van de .NET-SDK (C#).
De Azure Digital Apparaatdubbels C# SDK maakt deel uit van de Azure SDK voor .NET. Deze bevindt zich hier: [Azure IOT Digital-client bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).

### <a name="prerequisites"></a>Vereisten

Als u nog geen starter client-app-project hebt ingesteld, maakt u een Basic .NET-project dat u met deze zelf studie kunt gebruiken.

Als u de .NET SDK wilt gebruiken, moet u de volgende pakketten in uw project toevoegen:
* `Azure.DigitalTwins.Core`(versie `1.0.0-preview.2` )
* `Azure.Identity`

Afhankelijk van uw hulp middelen kunt u dit doen met behulp van Visual Studio package manager of het `dotnet` opdracht regel programma. 

### <a name="authentication-and-client-creation-net"></a>Verificatie en client maken: .NET

Als u wilt verifiëren met de .NET SDK, gebruikt u een van de methoden voor het verkrijgen van referenties die zijn gedefinieerd in de [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -bibliotheek.

Hier volgen twee die veel worden gebruikt: 
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet). Deze methode is bedoeld voor interactieve toepassingen en brengt een webbrowser voor verificatie.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet). Deze methode werkt goed in gevallen waarin u [beheerde identiteiten (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)nodig hebt, bijvoorbeeld bij het werken met Azure functions. 

U hebt ook het volgende nodig met-instructies:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Als u de interactieve browser referenties wilt gebruiken om een geverifieerde SDK-client te maken, voegt u de volgende code toe:

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Hoewel u de client-ID, Tenant-ID en instantie-URL rechtstreeks in de code kunt plaatsen zoals hierboven wordt weer gegeven, is het een goed idee om uw code deze waarden te laten ophalen uit een configuratie bestand of omgevings variabele.

In een Azure-functie kunt u de referenties van de beheerde identiteit als volgt gebruiken:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Zie [How-to: een Azure-functie instellen voor het verwerken van gegevens](how-to-create-azure-function.md) voor een meer uitgebreid voor beeld waarin enkele belang rijke configuratie opties in de context van functions worden uitgelegd.

Als u verificatie in een functie wilt gebruiken, moet u ook rekening houden met het volgende:
* [Beheerde identiteit inschakelen](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Omgevingsvariabelen](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp)
* Wijs machtigingen toe aan de functions-app waarmee deze toegang kan krijgen tot de Digital Apparaatdubbels-Api's. Zie [How-to: een Azure-functie instellen voor het verwerken van gegevens](how-to-create-azure-function.md) voor meer informatie.

## <a name="authentication-in-an-autorest-generated-sdk"></a>Verificatie in een autorest-gegenereerde SDK

Als u .NET niet gebruikt, kunt u ervoor kiezen om een SDK-bibliotheek te bouwen in een taal van uw keuze, zoals beschreven in [How to: aangepaste sdk's voor Azure Digital Apparaatdubbels maken met auto rest](how-to-create-custom-sdks.md).

In deze sectie wordt uitgelegd hoe u in dat geval verifieert.

### <a name="prerequisites"></a>Vereisten

In dit voor beeld wordt een type script-SDK gebruikt die wordt gegenereerd met auto rest. Als gevolg hiervan is ook het volgende vereist:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Mini maal voor beeld van een verificatie code

Als u een .NET-app wilt verifiëren met Azure-Services, kunt u de volgende minimale code gebruiken in uw client-app.

U hebt de toepassings-id van de *toepassing (client)* en de *map (Tenant)* van de vorige versie nodig, evenals de URL van uw Azure Digital apparaatdubbels-exemplaar.

> [!TIP]
> De URL van het Azure Digital Apparaatdubbels-exemplaar wordt gemaakt door *https://* toe te voegen aan het begin van de *hostnaam*van uw Azure Digital apparaatdubbels-exemplaar. Als u de *hostnaam*, samen met alle eigenschappen van uw exemplaar, wilt zien, kunt u uitvoeren `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . U kunt de `az account show --query tenantId` opdracht gebruiken om de *map-id (Tenant)* te bekijken. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

Houd er rekening mee dat de code hierboven de client-ID, Tenant-ID en instantie-URL rechtstreeks in de code plaatst voor eenvoud. het is een goed idee om uw code deze waarden te laten ophalen uit een configuratie bestand of omgevings variabele.

MSAL heeft veel meer opties die u kunt gebruiken voor het implementeren van dingen zoals caching en andere verificatie stromen. Zie [overzicht van micro soft Authentication Library (MSAL) (Engelstalig)](../active-directory/develop/msal-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de werking van beveiliging in azure Digital Apparaatdubbels:
* [Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions](concepts-security.md)

Of nu de verificatie is ingesteld, gaat u verder met het maken van modellen in uw exemplaar:
* [Instructies: aangepaste modellen beheren](how-to-manage-model.md)