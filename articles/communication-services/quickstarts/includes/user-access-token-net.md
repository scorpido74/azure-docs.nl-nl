---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 5c9066f369183de3b4cfe19cc5635e8f1b4a94a2
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779775"
---
## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- De nieuwste versie van [.NET Core-clientbibliotheek](https://dotnet.microsoft.com/download/dotnet-core) voor uw besturingssysteem.
- Een actieve Communication Services-resource en verbindingsreeks. [Een Communication Services-resource maken](../create-communication-resource.md).

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `UserAccessTokensQuickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: **Program.cs**.

```console
dotnet new console -o UserAccessTokensQuickstart
```

Wijzig uw map in de zojuist gemaakte app-map en gebruik de opdracht `dotnet build` om uw toepassing te compileren.

```console
cd UserAccessTokensQuickstart
dotnet build
```

### <a name="install-the-package"></a>Het pakket installeren

Terwijl u nog in de toepassingsmap bent, installeer met de opdracht `dotnet add package` de Azure Communications Services-beheerbibliotheek voor het .NET-pakket.

```console
dotnet add package Azure.Communication.Administration --version 1.0.0-beta.2
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

Ga als volgt te werk vanuit de projectmap:

1. Open het bestand **Program.cs** in een teksteditor
1. Voeg een `using`-instructie toe voor het insluiten van de `Azure.Communication.Administration` naamruimte
1. De declaratie van de `Main`-methode bijwerken ter ondersteuning van async-code

Gebruik de volgende code om te beginnen:

```csharp
using System;
using Azure.Communication.Administration;

namespace UserAccessTokensQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - User Access Tokens Quickstart");

            // Quickstart code goes here
        }
    }
}
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>De client verifiëren

Initialiseer een `CommunicationIdentityClient` met uw verbindingsreeks. Met de onderstaande code wordt de verbindingsreeks voor de resource opgehaald uit een omgevingsvariabele met de naam `COMMUNICATION_SERVICES_CONNECTION_STRING`. Meer informatie over het [beheren van de verbindingsreeks van uw resource](../create-communication-resource.md#store-your-connection-string).

Voeg de volgende code aan de `Main` methode toe:

```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string ConnectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
var client = new CommunicationIdentityClient(ConnectionString);
```

## <a name="create-a-user"></a>Een gebruiker maken

Azure Communication Services onderhoudt een lichte identiteitsmap. Gebruik de methode `createUser` om een nieuwe vermelding in de map te maken met een unieke `Id`. U moet een toewijzing onderhouden tussen de gebruikers van uw toepassing en de door de Communication Services gegenereerde identiteiten (bijv. door ze op te slaan in de database van uw toepassingsserver).

```csharp
var userResponse = await client.CreateUserAsync();
var user = userResponse.Value;
Console.WriteLine($"\nCreated a user with ID: {user.Id}");
```

## <a name="issue-user-access-tokens"></a>Tokens voor gebruikerstoegang uitgeven

Gebruik de methode `issueToken` om een toegangstoken voor een Communication Services-gebruiker uit te geven. Als u de optionele `user` parameter niet opgeeft, wordt er een nieuwe gebruiker gemaakt en geretourneerd met het token.

```csharp
// Issue an access token with the "voip" scope for a new user
var tokenResponse = await client.IssueTokenAsync(user, scopes: new [] { CommunicationTokenScope.VoIP });
var token =  tokenResponse.Value.Token;
var expiresOn = tokenResponse.Value.ExpiresOn;
Console.WriteLine($"\nIssued a token with 'voip' scope that expires at {expiresOn}:");
Console.WriteLine(token);
```

Tokens voor gebruikerstoegang zijn kortdurige referenties die opnieuw moeten worden uitgegeven om te voor komen dat uw gebruikers problemen ondervinden met de service. De antwoordeigenschap `expiresOn` geeft de levensduur van het token aan.

## <a name="revoke-user-access-tokens"></a>Tokens voor gebruikerstoegang intrekken

In sommige gevallen moet u de tokens voor gebruikerstoegang wellicht intrekken, bijvoorbeeld wanneer een gebruiker het wachtwoord wijzigt dat wordt gebruikt voor verificatie bij uw service. Deze functionaliteit is beschikbaar via de clientbibliotheek voor beheer van Azure Communication Services.

```csharp  
await client.RevokeTokensAsync(user);
Console.WriteLine($"\nSuccessfully revoked all tokens for user with ID: {user.Id}");
```

## <a name="delete-a-user"></a>Een gebruiker verwijderen

Als u een identiteit verwijdert, worden alle actieve tokens ingetrokken en wordt voorkomen dat nieuwe tokens voor de identiteiten worden uitgegeven. Ook wordt alle persistente inhoud verwijderd die aan de gebruiker is gekoppeld.

```csharp
await client.DeleteUserAsync(user);
Console.WriteLine($"\nDeleted the user with ID: {user.Id}");
```

## <a name="run-the-code"></a>De code uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```console
dotnet run
```
