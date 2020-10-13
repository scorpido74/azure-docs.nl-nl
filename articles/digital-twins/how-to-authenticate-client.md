---
title: Verificatiecode voor app schrijven
titleSuffix: Azure Digital Twins
description: Zie verificatie code schrijven in een client toepassing
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f2cef34413f46608e8bc35a009a29212af5ddf20
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893591"
---
# <a name="write-client-app-authentication-code"></a>Verificatie code voor client-app schrijven

Nadat u [een Azure Digital apparaatdubbels-exemplaar en-verificatie hebt ingesteld](how-to-set-up-instance-portal.md), kunt u een client toepassing maken die u gaat gebruiken om te communiceren met het exemplaar. Zodra u een eerste client project hebt ingesteld, moet u **code in die client-app schrijven om deze te verifiëren** tegen het Azure Digital apparaatdubbels-exemplaar.

Azure Digital Apparaatdubbels voert verificatie uit met behulp van [Azure AD-beveiligings tokens op basis van OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). Als u uw SDK wilt verifiëren, moet u een Bearer-token met de juiste machtigingen voor Azure Digital Apparaatdubbels verkrijgen en dit samen met uw API-aanroepen door geven. 

In dit artikel wordt beschreven hoe u referenties kunt verkrijgen met behulp van de `Azure.Identity` client bibliotheek. Hoewel in dit artikel code voorbeelden in C# worden weer gegeven, zoals wat u schrijft voor [.net (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core), kunt u een versie van gebruiken, `Azure.Identity` ongeacht de SDK die u gebruikt (voor meer informatie over de sdk's die beschikbaar zijn voor Azure Digital apparaatdubbels raadpleegt u [*Hoe: de Azure Digital Apparaatdubbels-api's en sdk's gebruiken*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Vereisten

Voltooi eerst de installatie stappen in [*instructies: een exemplaar en verificatie instellen*](how-to-set-up-instance-portal.md). Dit zorgt ervoor dat u een Azure Digital Apparaatdubbels-exemplaar hebt, uw gebruiker heeft toegangs machtigingen en u hebt machtigingen ingesteld voor client toepassingen. Nadat u deze instellingen hebt ingesteld, bent u klaar voor het schrijven van client-app-code.

Als u wilt door gaan, hebt u een client-app-project nodig waarin u uw code schrijft. Als u nog geen client-app-project hebt ingesteld, maakt u een eenvoudig project in de taal van uw keuze voor gebruik in deze zelf studie.

## <a name="common-authentication-methods-with-azureidentity"></a>Algemene verificatie methoden met Azure. Identity

`Azure.Identity` is een client bibliotheek met verschillende methoden voor het verkrijgen van referenties die u kunt gebruiken om een Bearer-token op te halen en te verifiëren met uw SDK. Hoewel in dit artikel voor beelden in C# worden weer gegeven, kunt u deze bekijken `Azure.Identity` voor verschillende talen, waaronder...
* [.NET (C#)](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)
* [Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme?view=azure-java-stable&preserve-view=true)
* [JavaScript](https://docs.microsoft.com/javascript/api/overview/azure/identity-readme?view=azure-node-latest&preserve-view=true)
* [Python](https://docs.microsoft.com/python/api/overview/azure/identity-readme?view=azure-python&preserve-view=true)

Drie veelvoorkomende methoden voor het verkrijgen van referenties in `Azure.Identity` zijn:
* [DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) biedt een standaard `TokenCredential` verificatie stroom voor toepassingen die worden geïmplementeerd in azure, en is **de aanbevolen keuze voor lokale ontwikkeling**. Het kan ook worden ingeschakeld om de andere twee methoden te proberen die in dit artikel worden aanbevolen. Er wordt gewikkeld `ManagedIdentityCredential` en kan toegang krijgen tot `InteractiveBrowserCredential` een configuratie variabele.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) werkt prima in gevallen waarin u [beheerde identiteiten (MSI)](../active-directory/managed-identities-azure-resources/overview.md)nodig hebt, en is een goede kandidaat voor het werken met Azure functions en het implementeren van Azure-Services.
* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) is bedoeld voor interactieve toepassingen en kan worden gebruikt voor het maken van een geverifieerde SDK-client

In het volgende voor beeld ziet u hoe u deze met behulp van de .NET (C#) SDK gebruikt.

## <a name="authentication-examples-net-c-sdk"></a>Verificatie voorbeelden: .NET (C#) SDK

In deze sectie ziet u een voor beeld in C# voor het gebruik van de meegeleverde .NET SDK om verificatie code te schrijven.

Voeg eerst het SDK-pakket `Azure.DigitalTwins.Core` en het `Azure.Identity` pakket in uw project toe. Afhankelijk van uw hulp middelen kunt u de pakketten toevoegen met behulp van Visual Studio package manager of het `dotnet` opdracht regel programma. 

U moet ook de volgende using-instructies toevoegen aan de project code:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Voeg vervolgens code toe om referenties te verkrijgen met een van de methoden in `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>Methode DefaultAzureCredential

[DefaultAzureCredential](https://docs.microsoft.com/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true) biedt een standaard `TokenCredential` verificatie stroom voor toepassingen die worden geïmplementeerd in azure, en is **de aanbevolen keuze voor lokale ontwikkeling**.

Als u de standaard Azure-referenties wilt gebruiken, hebt u de URL van het Azure Digital Apparaatdubbels-exemplaar ([instructies om te vinden](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) nodig.

Hier volgt een code voorbeeld om een `DefaultAzureCredential` aan het project toe te voegen:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

### <a name="managedidentitycredential-method"></a>Methode ManagedIdentityCredential

De methode [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet&preserve-view=true) werkt prima in gevallen waar u [beheerde identiteiten (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)nodig hebt, bijvoorbeeld bij het werken met Azure functions.

Dit betekent dat u `ManagedIdentityCredential` in hetzelfde project kunt gebruiken als `DefaultAzureCredential` of `InteractiveBrowserCredential` , om een ander deel van het project te verifiëren.

Als u de standaard Azure-referenties wilt gebruiken, hebt u de URL van het Azure Digital Apparaatdubbels-exemplaar ([instructies om te vinden](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)) nodig.

In een Azure-functie kunt u de referenties van de beheerde identiteit als volgt gebruiken:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>Methode InteractiveBrowserCredential

De methode [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) is bedoeld voor interactieve toepassingen en brengt een webbrowser voor verificatie. U kunt dit in plaats van gebruiken `DefaultAzureCredential` in gevallen waarin u interactieve verificatie nodig hebt.

Als u de interactieve browser referenties wilt gebruiken, hebt u een **app-registratie** nodig die machtigingen heeft voor de Azure Digital Apparaatdubbels-api's. Voor stappen voor het instellen van deze app-registratie, zie de sectie [*toegangs machtigingen voor client toepassingen instellen*](how-to-set-up-instance-portal.md#set-up-access-permissions-for-client-applications) van *instructies: een exemplaar en verificatie*instellen. Zodra de app-registratie is ingesteld, hebt u nodig...
* de toepassings-ID van de app-registratie *(client)*
* de *Directory-id (Tenant)* van de app-registratie
* de URL van het Azure Digital Apparaatdubbels-exemplaar ([instructies om te vinden](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Hier volgt een voor beeld van de code voor het maken van een geverifieerde SDK-client met `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

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

#### <a name="other-notes-about-authenticating-azure-functions"></a>Andere opmerkingen over het verifiëren van Azure Functions

Zie [*How-to: een Azure-functie instellen voor het verwerken van gegevens*](how-to-create-azure-function.md) voor een meer uitgebreid voor beeld waarin enkele belang rijke configuratie opties in de context van functions worden uitgelegd.

Als u verificatie in een functie wilt gebruiken, moet u ook rekening houden met het volgende:
* [Beheerde identiteit inschakelen](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [Omgevings variabelen](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) gebruiken waar nodig
* Wijs machtigingen toe aan de functions-app waarmee deze toegang kan krijgen tot de Digital Apparaatdubbels-Api's. Zie [*How to: een Azure-functie instellen voor het verwerken van gegevens*](how-to-create-azure-function.md)voor meer informatie over Azure functions processen.

## <a name="other-credential-methods"></a>Andere referentie methoden

Als de gemarkeerde verificatie scenario's hierboven geen betrekking hebben op de behoeften van uw app, kunt u andere typen verificatie bekijken die worden aangeboden in het [**micro soft Identity-platform**](../active-directory/develop/v2-overview.md#getting-started). De documentatie voor dit platform omvat aanvullende verificatie scenario's, geordend op toepassings type.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de werking van beveiliging in azure Digital Apparaatdubbels:
* [*Concepten: beveiliging voor Azure Digital Apparaatdubbels Solutions*](concepts-security.md)

Of nu de verificatie is ingesteld, gaat u verder met het maken van modellen in uw exemplaar:
* [*Instructies: Aangepaste modellen beheren*](how-to-manage-model.md)
