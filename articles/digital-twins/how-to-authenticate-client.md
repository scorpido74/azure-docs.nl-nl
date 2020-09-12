---
title: Verificatiecode voor app schrijven
titleSuffix: Azure Digital Twins
description: Zie verificatie code schrijven in een client toepassing
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: 88f74bcc93d640ec8d4d9014c6f25a6d0d0df680
ms.sourcegitcommit: f845ca2f4b626ef9db73b88ca71279ac80538559
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614001"
---
# <a name="write-client-app-authentication-code"></a>Verificatie code voor client-app schrijven

Nadat u [een Azure Digital apparaatdubbels-exemplaar en-verificatie hebt ingesteld](how-to-set-up-instance-scripted.md), kunt u een client toepassing maken die u gaat gebruiken om te communiceren met het exemplaar. Zodra u een eerste client project hebt ingesteld, wordt in dit artikel **beschreven hoe u code in die client-app schrijft om deze te verifiëren voor** het Azure Digital apparaatdubbels-exemplaar.

Er zijn twee benaderingen van voorbeeld code in dit artikel. U kunt de optie gebruiken die geschikt is voor u, afhankelijk van de taal van uw keuze:
* De eerste sectie van de voorbeeld code maakt gebruik van de Azure Digital Apparaatdubbels .NET (C#) SDK. De SDK maakt deel uit van de Azure-SDK voor .NET en bevindt zich hier: [*Azure IOT Digital-client bibliotheek voor .net*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core).
* Het tweede gedeelte van de voorbeeld code is voor gebruikers die niet gebruikmaken van de .NET-SDK en in plaats daarvan gebruikmaken van autorest-gegenereerde Sdk's in andere talen. Zie [*How to: aangepaste sdk's voor Azure Digital Apparaatdubbels maken met auto rest*](how-to-create-custom-sdks.md)voor meer informatie over deze strategie.

Meer informatie over de Api's en Sdk's voor Azure Digital Apparaatdubbels vindt u in [*procedures: gebruik de Azure Digital apparaatdubbels-api's en sdk's*](how-to-use-apis-sdks.md).

## <a name="prerequisites"></a>Vereisten

Voltooi eerst de installatie stappen in [*instructies: een exemplaar en verificatie instellen*](how-to-set-up-instance-scripted.md). Dit zorgt ervoor dat u een Azure Digital Apparaatdubbels-exemplaar hebt, uw gebruiker heeft toegangs machtigingen en u hebt machtigingen ingesteld voor client toepassingen. Nadat u deze instellingen hebt ingesteld, bent u klaar voor het schrijven van client-app-code.

Als u wilt door gaan, hebt u een client-app-project nodig waarin u uw code schrijft. Als u nog geen client-app-project hebt ingesteld, maakt u een eenvoudig project in de taal van uw keuze voor gebruik in deze zelf studie.

## <a name="authentication-and-client-creation-net-c-sdk"></a>Verificatie en client maken: .NET (C#) SDK

Neem eerst de volgende pakketten op in uw project om de .NET SDK en verificatie hulpprogramma's te gebruiken voor deze procedure:
* `Azure.DigitalTwins.Core`
* `Azure.Identity`

Afhankelijk van uw hulp middelen kunt u de pakketten toevoegen met behulp van Visual Studio package manager of het `dotnet` opdracht regel programma. 

U hebt ook het volgende nodig met-instructies:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
Als u wilt verifiëren met de .NET SDK, gebruikt u een van de methoden voor het verkrijgen van referenties die zijn gedefinieerd in de [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) -bibliotheek. Hier volgen twee die vaak worden gebruikt (zelfs samen in dezelfde toepassing):

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) is bedoeld voor interactieve toepassingen en kan worden gebruikt voor het maken van een geverifieerde SDK-client
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) werkt prima in gevallen waarin u beheerde identiteiten (MSI) nodig hebt, en is een goede kandidaat voor het werken met Azure functions

### <a name="interactivebrowsercredential-method"></a>Methode InteractiveBrowserCredential
De methode [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) is bedoeld voor interactieve toepassingen en brengt een webbrowser voor verificatie.

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

### <a name="managedidentitycredential-method"></a>Methode ManagedIdentityCredential
 De methode [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) werkt prima in gevallen waar u [beheerde identiteiten (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)nodig hebt, bijvoorbeeld bij het werken met Azure functions.
In een Azure-functie kunt u de referenties van de beheerde identiteit als volgt gebruiken:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

Zie [*How-to: een Azure-functie instellen voor het verwerken van gegevens*](how-to-create-azure-function.md) voor een meer uitgebreid voor beeld waarin enkele belang rijke configuratie opties in de context van functions worden uitgelegd.

Als u verificatie in een functie wilt gebruiken, moet u ook rekening houden met het volgende:
* [Beheerde identiteit inschakelen](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Omgevings variabelen](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) gebruiken waar nodig
* Wijs machtigingen toe aan de functions-app waarmee deze toegang kan krijgen tot de Digital Apparaatdubbels-Api's. Zie [*How to: een Azure-functie instellen voor het verwerken van gegevens*](how-to-create-azure-function.md)voor meer informatie over Azure functions processen.

## <a name="authentication-with-an-autorest-generated-sdk"></a>Verificatie met een autorest-gegenereerde SDK

Als u .NET niet gebruikt, kunt u ervoor kiezen om een SDK-bibliotheek te bouwen in een taal van uw keuze, zoals beschreven in [*How to: aangepaste sdk's voor Azure Digital Apparaatdubbels maken met auto rest*](how-to-create-custom-sdks.md).

In deze sectie wordt uitgelegd hoe u in dat geval verifieert.

### <a name="prerequisites"></a>Vereisten

Eerst moet u de stappen voor het maken van een aangepaste SDK met auto rest volt ooien met behulp van de stappen in [*How to: aangepaste sdk's voor Azure Digital Apparaatdubbels maken met auto rest*](how-to-create-custom-sdks.md).

In dit voor beeld wordt een type script-SDK gebruikt die wordt gegenereerd met auto rest. Als gevolg hiervan is ook het volgende vereist:
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [MS-rest-js](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>Mini maal voor beeld van een verificatie code

Als u een app wilt verifiëren met Azure-Services, kunt u de volgende minimale code gebruiken in uw client-app.

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

MSAL heeft veel meer opties die u kunt gebruiken voor het implementeren van dingen zoals caching en andere verificatie stromen. Zie [*overzicht van micro soft Authentication Library (MSAL) (Engelstalig)*](../active-directory/develop/msal-overview.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de werking van beveiliging in azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)

Of nu de verificatie is ingesteld, gaat u verder met het maken van modellen in uw exemplaar:
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)