---
title: Azure Cosmos-emulator downloaden en opmerkingen bij de release
description: Download de opmerkingen bij de release voor de Azure Cosmos-emulator voor verschillende versies en downloadgegevens.
ms.service: cosmos-db
ms.topic: tutorial
author: milismsft
ms.author: adrianmi
ms.date: 06/20/2019
ms.openlocfilehash: ab31daafe496f1aaad2bbc8075b2514d9e57e997
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84687171"
---
# <a name="azure-cosmos-emulator---release-notes-and-download-information"></a>Azure Cosmos-emulator - opmerkingen bij de release en downloadgegevens

Dit artikel bevat de opmerkingen bij de release voor Azure Cosmos-emulator met een lijst met functie-updates die in elke release zijn uitgevoerd. Ook wordt de meest recente versie van de emulator weergegeven die u kunt downloaden en gebruiken.

## <a name="download"></a>Downloaden

| | |
|---------|---------|
|**MSI-download**|[Microsoft Downloadcentrum](https://aka.ms/cosmosdb-emulator)|
|**Aan de slag**|[Lokaal ontwikkelen met Azure Cosmos DB-emulator](local-emulator.md)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="2111"></a>2.11.1

- Deze versie corrigeert een aantal bugs die zijn gerelateerd aan de emulator Data Explorer. In bepaalde gevallen bij het gebruik van de emulator Data Explorer via een webbrowser, kan het voorkomen dat deze geen verbinding kan maken met het Cosmos-emulatoreindpunt en alle gerelateerde acties, zoals het maken van een database of een container, veroorzaken dan een fout. Het tweede probleem dat is opgelost heeft betrekking op het maken van een item vanuit een JSON-bestand met behulp van de uploadactie van Data Explorer.

### <a name="2110"></a>2.11.0

- In deze release wordt ondersteuning geïntroduceerd voor automatische schaalaanpassing van ingerichte doorvoer. Deze nieuwe functies omvatten de mogelijkheid om een aangepast maximaal doorvoerniveau in te stellen in aanvraageenheden (RU/s), automatische schaalaanpassing in te schakelen voor bestaande databases en containers en programmatische ondersteuning via Azure Cosmos DB SDK's.
- Oplossing voor een probleem tijdens het uitvoeren van een query op een grote hoeveelheid documenten (meer dan 1 GB), waarbij de emulator uitvalt met interne foutstatuscode 500.

### <a name="292"></a>2.9.2

- In deze release wordt een bug gecorrigeerd en tegelijk ondersteuning ingeschakeld voor MongoDB-eindpunt versie 3.2. Er wordt ook ondersteuning toegevoegd voor het genereren van ETL-traceringen voor het oplossen van problemen met behulp van WPR in plaats van LOGMAN.

### <a name="291"></a>2.9.1

- In deze release worden problemen in de query-API gecorrigeerd en de compatibiliteit met oudere besturingssystemen hersteld, zoals Windows Server 2012.

### <a name="290"></a>2.9.0

- In deze release wordt de optie toegevoegd om de consistentie in te stellen op consistent voorvoegsel en worden de maximum limieten verhoogd voor gebruikers en machtigingen.

### <a name="272"></a>2.7.2

- In deze release wordt MongoDB-serverondersteuning versie 3.6 toegevoegd aan de Cosmos-emulator. Als u een MongoDB-eindpunt met doelversie 3.6 van de service wilt starten, start u de emulator vanaf een beheerdersopdrachtregel met de optie '/EnableMongoDBEndpoint = 3.6 '.

### <a name="270"></a>2.7.0

- In deze release wordt een regressie gecorrigeerd waarmee wordt voorkomen dat gebruikers query's uitvoeren op het SQL-API-account van de emulator bij gebruik van .NET core- of x86 .NET-clients.

### <a name="246"></a>2.4.6

- Deze release biedt pariteit met de functies van de Azure Cosmos-service vanaf juli 2019, met de uitzonderingen die worden vermeld in [Lokaal ontwikkelen met Azure Cosmos-emulator](local-emulator.md). Daarnaast worden verschillende fouten met betrekking tot het afsluiten van de emulator opgelost wanneer deze via de opdrachtregel wordt aangeroepen, en bij overschrijvingen van het interne IP-adres voor SDK-clients die gebruikmaken van een verbinding in directe modus.

### <a name="243"></a>2.4.3

- Het starten van de MongoDB-service is standaard uitgeschakeld. Alleen het SQL-eindpunt is standaard ingeschakeld. De gebruiker moet het eindpunt handmatig starten met de opdrachtregeloptie '/EnableMongoDbEndpoint' van de emulator. Nu is deze gelijk aan alle andere service-eindpunten, zoals Gremlin, Cassandra en Table.
- Er is een fout opgelost in de emulator tijdens het starten met '/AllowNetworkAccess', waarbij de Gremlin-, Cassandra- en Table-eindpunten aanvragen van externe clients niet goed konden verwerken.
- Directe verbindingspoorten toegevoegd aan de instellingen van de firewallregels.

### <a name="240"></a>2.4.0

- Er is een probleem opgelost waarbij de emulator niet kon worden gestart wanneer netwerkbewaking-apps, zoals Pulse Client, aanwezig waren op de hostcomputer.
