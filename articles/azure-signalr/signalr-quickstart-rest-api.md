---
title: 'Snelstart: Azure SignalR Service REST-API'
description: Meer informatie over het gebruik van REST API met de Azure signalerings service volgende voor beelden. Details van REST API specificatie zoeken.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 17371e3bd426ea81b5e7e07610aac0073ea972c9
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157677"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Snelstartgids: Realtimeberichten verzenden via de console-app

Azure SignalR-service biedt [REST-API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) ter ondersteuning van server-naar-client-communicatie, zoals broadcasting. U kunt elke programmeertaal kiezen die een REST-API kan aanroepen. U kunt berichten verzenden naar alle verbonden clients, een specifieke client op basis van naam of een groep clients.

In deze snelstartgids leert u hoe u in C# berichten via een opdrachtregel-app kunt verzenden naar verbonden client-apps.

## <a name="prerequisites"></a>Vereisten

Deze quickstart kan worden uitgevoerd op macOS, Windows of Linux.

* [.NET Core-SDK](https://www.microsoft.com/net/download/core)
* Een teksteditor of code-editor naar keuze.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u met uw Azure-account aan bij Azure Portal op <https://portal.azure.com/>.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>De voorbeeldtoepassing klonen

Laten wij de code klaarmaken terwijl de service wordt geïmplementeerd. Kloon de [voorbeeld-app vanuit GitHub](https://github.com/aspnet/AzureSignalR-samples.git), stel de SignalR Service-verbindingsreeks in en voer de toepassing lokaal uit.

1. Open een nieuw git-terminalvenster. Ga naar een map waarin u het voorbeeldproject wilt klonen.

1. Voer de volgende opdracht uit om de voorbeeldopslagplaats te klonen. Deze opdracht maakt een kopie van de voorbeeld-app op uw computer.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Het voorbeeldproject compileren en uitvoeren

Dit voorbeeld is een console-app die het gebruik van de Azure SignalR-service weergeeft. Deze biedt twee modi:

- Servermodus: gebruik eenvoudige opdrachten om de REST-API voor Azure SignalR-service aan te roepen.
- Clientmodus: maak verbinding met de Azure SignalR-service en ontvang berichten van de server.

U kunt ook zien hoe u een toegangstoken kunt genereren voor verificatie met Azure SignalR-service.

### <a name="build-the-executable-file"></a>Het uitvoerbare bestand maken

We gebruiken macOS osx.10.13-x64 als voorbeeld. U kunt [naslaginformatie](https://docs.microsoft.com/dotnet/core/rid-catalog) vinden over het maken op andere platforms.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Een client starten

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Een server starten

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Het voorbeeld uitvoeren zonder te publiceren

U kunt ook de onderstaande opdracht uitvoeren om een server of client te starten

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Gebruikersgeheimen gebruiken om een verbindingsreeks te specificeren

U kunt `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"` uitvoeren in de hoofdmap van het voorbeeld. Daarna hebt u de optie `-c "<ConnectionString>"` niet meer nodig.

## <a name="usage"></a>Gebruik

Gebruik de opdracht om het bericht te verzenden nadat de server is gestart:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

U kunt meerdere clients met verschillende clientnamen starten.

## <a name="usage"> </a> Integratie met services van derden

Met behulp van de Azure SignalR-service kunt services van derden integreren met het systeem.

### <a name="definition-of-technical-specifications"></a>Definitie van technische specificaties

In de volgende tabel worden alle versies van de ondersteunde REST-API's weergegeven op datum. U kunt er ook het definitiebestand voor elke specifieke versie vinden

Versie | API-status | Door | Specifiek
--- | --- | --- | ---
`1.0-preview` | Beschikbaar | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | Beschikbaar | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

De lijst met beschikbare API's voor elke specifieke versie is beschikbaar in de volgende lijst.

API | `1.0-preview` | `1.0`
--- | --- | ---
[Uitzenden naar alle](#broadcast) | **&#x2713;** | **&#x2713;**
[Uitzenden naar een groep](#broadcast-group) | **&#x2713;** | **&#x2713;**
Uitzenden naar bepaalde groepen | **&#x2713;** (afgeschaft) | `N / A`
[Verzenden naar specifieke gebruikers](#send-user) | **&#x2713;** | **&#x2713;**
Verzenden naar bepaalde gebruikers | **&#x2713;** (afgeschaft) | `N / A`
[Een gebruiker aan een groep toevoegen](#add-user-to-group) | `N / A` | **&#x2713;**
[Een gebruiker uit een groep verwijderen](#remove-user-from-group) | `N / A` | **&#x2713;**

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Uitzenden naar iedereen

Versie | API HTTP-methode | Aanvraag-URL | Aanvraagbody
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Hetzelfde als hierboven

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Uitzenden naar een groep

Versie | API HTTP-methode | Aanvraag-URL | Aanvraagbody
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Hetzelfde als hierboven

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Verzenden naar specifieke gebruikers

Versie | API HTTP-methode | Aanvraag-URL | Aanvraagbody
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Hetzelfde als hierboven

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Een gebruiker aan een groep toevoegen

Versie | API HTTP-methode | Aanvraag-URL
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Een gebruiker uit een groep verwijderen

Versie | API HTTP-methode | Aanvraag-URL
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u REST API kunt gebruiken om real-time berichten van de signaal service te verzenden naar clients. Vervolgens vindt u meer informatie over het ontwikkelen en implementeren van Azure Functions met de service binding van de signalering, die boven op REST API is gebouwd.

> [!div class="nextstepaction"]
> [Azure Functions ontwikkelen met behulp van Azure signalerings service bindingen](signalr-quickstart-azure-functions-csharp.md)
