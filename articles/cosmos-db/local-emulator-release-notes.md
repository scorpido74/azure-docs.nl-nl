---
title: Azure Cosmos Emulator download en release notes Azure Cosmos Emulator download en release notes Azure Cosmos Emulator download en release notes Azure Cosmos
description: Download de Azure Cosmos emulator release notes voor verschillende versies en download informatie.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 4dffe169908d0dd3effa4e46140b5f6696805a3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77168654"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos Emulator - Notities vrijgeven en informatie downloaden

In dit artikel ziet u de azure cosmos-releasenotities met een lijst met functie-updates die in elke release zijn gemaakt. Het bevat ook de nieuwste versie van emulator te downloaden en te gebruiken.

## <a name="download"></a>Download

| | |
|---------|---------|
|**MSI downloaden**|[Microsoft Downloadcentrum](https://aka.ms/cosmosdb-emulator)|
|**Aan de slag**|[Lokaal ontwikkelen met Azure Cosmos-emulator](local-emulator.md)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="291"></a>2.9.1

- Deze release lost enkele problemen in de query-API-ondersteuning op en herstelt de compatibiliteit met oudere bes.

### <a name="290"></a>2.9.0

- In deze versie wordt de optie toegevoegd om de consistentie in te stellen op consistent voorvoegsel en de maximale limieten voor gebruikers en machtigingen te verhogen.

### <a name="272"></a>2.7.2

- Deze release voegt MongoDB versie 3.6 server ondersteuning toe aan de Cosmos Emulator. Als u een MongoDB-eindpunt wilt starten dat is gericht op versie 3.6 van de service, start u de emulator vanaf een opdrachtregel administrator met de optie "/EnableMongoDBEndpoint=3.6".

### <a name="270"></a>2.7.0

- Deze release lost een regressie op waardoor gebruikers geen query's konden uitvoeren op het SQL API-account van de emulator wanneer ze .NET-kern- of x86.NET-gebaseerde clients gebruiken.

### <a name="246"></a>2.4.6

- Deze release biedt pariteit met de functies in de Azure Cosmos-service vanaf juli 2019, met de uitzonderingen die zijn vermeld in [Lokaal ontwikkelen met Azure Cosmos-emulator](local-emulator.md). Het lost ook verschillende bugs met betrekking tot emulator shutdown wanneer aangeroepen via command line en interne IP-adres overschrijvingen voor SDK-clients met behulp van directe modus connectiviteit.

### <a name="243"></a>2.4.3

- Standaard uitgeschakeld bij het starten van de MongoDB-service. Alleen het SQL-eindpunt is standaard ingeschakeld. De gebruiker moet het eindpunt handmatig starten met de opdrachtregeloptie "/EnableMongoDbEndpoint". Nu, het is net als alle andere service eindpunten, zoals Gremlin, Cassandra, en Table.
- Fixed a bug in the emulator when starting with "/AllowNetworkAccess" where the Gremlin, Cassandra, and Table endpoints waren not properly handling requests from external clients.
- Voeg directe verbindingspoorten toe aan de instellingen voor firewallregels.

### <a name="240"></a>2.4.0

- Fixed an issue with emulator not to start when network monitoring apps, such as Pulse Client, are present on the host computer.
