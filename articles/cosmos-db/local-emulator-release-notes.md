---
title: Azure Cosmos-emulator downloaden en release-opmerkingen
description: Down load de release opmerkingen voor de Azure Cosmos-emulator voor verschillende versies en down load gegevens.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: 3878f6d01a2adfff2adc6a22aad20a5f83992b8d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719684"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos-emulator-opmerkingen bij de release en informatie over downloaden

Dit artikel bevat de release opmerkingen voor de Azure Cosmos-emulator met een lijst met onderdeel updates die zijn gemaakt in elke release. Ook wordt de meest recente versie van de emulator weer gegeven die u kunt downloaden en gebruiken.

## <a name="download"></a>Downloaden

| | |
|---------|---------|
|**MSI downloaden**|[Microsoft Downloadcentrum](https://aka.ms/cosmosdb-emulator)|
|**Aan de slag**|[Lokaal ontwikkelen met Azure Cosmos-emulator](local-emulator.md)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="290"></a>2.9.0

- Deze release voegt de optie toe om de consistentie in te stellen op consistent voor voegsel en verg root de maximum limieten voor gebruikers en machtigingen.

### <a name="272"></a>2.7.2

- Deze release voegt MongoDB versie 3,6-server ondersteuning toe aan de Cosmos-emulator. Als u een MongoDB-eind punt met de doel versie 3,6 van de service wilt starten, start u de emulator vanaf een opdracht regel van de beheerder met de optie '/EnableMongoDBEndpoint = 3.6 '.

### <a name="270"></a>2.7.0

- Deze release corrigeert een regressie waarmee wordt voor komen dat gebruikers query's uitvoeren op het SQL-API-account van de emulator bij gebruik van .NET core-of x86 .NET-clients.

### <a name="246"></a>2.4.6

- Deze release biedt pariteit met de functies van de Azure Cosmos-service vanaf juli 2019, met de uitzonde ringen die worden vermeld in [lokaal ontwikkelen met de Azure Cosmos-emulator](local-emulator.md). Daarnaast worden verschillende fouten met betrekking tot het afsluiten van de emulator opgelost wanneer deze via de opdracht regel en het interne IP-adres worden overschreven voor SDK-clients die gebruikmaken van de directe modus connectiviteit.

### <a name="243"></a>2.4.3

- Standaard het starten van de MongoDB-service is uitgeschakeld. Alleen het SQL-eind punt is standaard ingeschakeld. De gebruiker moet het eind punt hand matig starten met de opdracht regel optie '/EnableMongoDbEndpoint ' van de emulator. Nu ziet u alle andere service-eind punten, zoals Gremlin, Cassandra en Table.
- Er is een fout opgelost in de emulator tijdens het starten met '/AllowNetworkAccess ', waarbij de Gremlin, Cassandra en Table-eind punten geen aanvragen van externe clients goed kunnen verwerken.
- Voeg directe verbindings poorten toe aan de instellingen van de firewall regels.

### <a name="240"></a>2.4.0

- Er is een probleem opgelost waarbij een emulator niet kan worden gestart wanneer netwerk bewaking-apps, zoals pulse-client, aanwezig zijn op de hostcomputer.
