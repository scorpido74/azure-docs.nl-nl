---
title: Azure Cosmos-emulator downloaden en release-opmerkingen
description: Lees de release opmerkingen voor de Azure Cosmos-emulator en down load deze.
ms.service: cosmos-db
ms.topic: tutorial
author: markjbrown
ms.author: mjbrown
ms.date: 06/20/2019
ms.openlocfilehash: 03163d75681bee7434e2b7576a678b1094d47b94
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973700"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos-emulator-opmerkingen bij de release en informatie over downloaden

Dit artikel bevat de release opmerkingen voor de Azure Cosmos-emulator met een lijst met onderdeel updates die zijn gemaakt in elke release. Ook wordt de meest recente versie van de emulator weer gegeven die u kunt downloaden en gebruiken.

## <a name="download"></a>Downloaden

| | |
|---------|---------|
|**MSI downloaden**|[Microsoft Downloadcentrum](https://aka.ms/cosmosdb-emulator)|
|**Aan de slag**|[Lokaal ontwikkelen met Azure Cosmos-emulator](local-emulator.md)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="246"></a>2.4.6

- Deze release biedt pariteit met de functies van de Azure Cosmos-service vanaf juli 2019, met de uitzonde ringen die worden vermeld in [lokaal ontwikkelen met de Azure Cosmos-emulator](local-emulator.md). Daarnaast worden verschillende fouten met betrekking tot het afsluiten van de emulator opgelost wanneer deze via de opdracht regel en het interne IP-adres worden overschreven voor SDK-clients die gebruikmaken van de directe modus connectiviteit.

### <a name="243"></a>2.4.3

- Standaard het starten van de MongoDB-service is uitgeschakeld. Alleen het SQL-eind punt is standaard ingeschakeld. De gebruiker moet het eind punt hand matig starten met de opdracht regel optie '/EnableMongoDbEndpoint ' van de emulator. Nu ziet u alle andere service-eind punten, zoals Gremlin, Cassandra en Table.
- Er is een fout opgelost in de emulator tijdens het starten met '/AllowNetworkAccess ', waarbij de Gremlin, Cassandra en Table-eind punten geen aanvragen van externe clients goed kunnen verwerken.
- Voeg directe verbindings poorten toe aan de instellingen van de firewall regels.

### <a name="240"></a>2.4.0

- Er is een probleem opgelost waarbij een emulator niet kan worden gestart wanneer netwerk bewaking-apps, zoals pulse-client, aanwezig zijn op de hostcomputer.
