---
title: Gegevens verlopen in Azure Cosmos DB met Time to Live
description: Met TTL biedt Microsoft Azure Cosmos DB de mogelijkheid om documenten na een bepaalde periode automatisch uit het systeem te laten verwijderen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.reviewer: sngun
ms.openlocfilehash: 5407c38f33d167ff5114cd55878e3470e7248d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77188722"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live (TTL) configureren in Azure Cosmos DB 

Met **Time to Live** of TTL biedt Azure Cosmos DB de mogelijkheid om items na een bepaalde periode automatisch uit een container te verwijderen. Standaard u de tijd instellen om op containerniveau te leven en de waarde per item te overschrijven. Nadat u de TTL hebt ingesteld op een container of op itemniveau, verwijdert Azure Cosmos DB deze items automatisch na de periode, sinds de tijd dat ze voor het laatst zijn gewijzigd. Time to live waarde is geconfigureerd in seconden. Wanneer u TTL configureert, verwijdert het systeem automatisch de verlopen items op basis van de TTL-waarde, zonder dat een verwijderbewerking nodig is die expliciet is uitgegeven door de clienttoepassing.

Verwijdering van verlopen items is een achtergrondtaak die overgebleven [aanvraageenheden](request-units.md)verbruikt, dat wil zeggen Aanvraageenheden die niet zijn verbruikt door gebruikersverzoeken. Zelfs nadat de TTL is verlopen, als de container is overladen met aanvragen en als er niet genoeg RU's beschikbaar zijn, wordt de verwijdering van gegevens vertraagd. Gegevens worden verwijderd zodra er voldoende R's beschikbaar zijn om de verwijderingsbewerking uit te voeren. Hoewel het verwijderen van gegevens vertraagd is, worden gegevens niet geretourneerd door query's (door een API) nadat de TTL is verlopen.

## <a name="time-to-live-for-containers-and-items"></a>Tijd om te leven voor containers en items

De tijd tot live waarde wordt in seconden ingesteld en wordt geïnterpreteerd als een delta vanaf het moment dat een item voor het laatst is gewijzigd. U de tijd instellen om te leven op een container of een item in de container:

1. **Time to Live on** a `DefaultTimeToLive`container (set using):

   - Als items ontbreken (of ingesteld op null) verlopen niet automatisch.

   - Als de huidige waarde is ingesteld op '-1', is deze gelijk aan oneindig en verlopen items niet standaard.

   - Als aanwezig en de waarde is ingesteld op een getal *"n"* - items vervallen *"n"* seconden na hun laatste gewijzigde tijd.

2. **Tijd om te leven** op `ttl`een item (ingesteld met behulp van:

   - Deze eigenschap is alleen `DefaultTimeToLive` van toepassing als deze aanwezig is en is niet ingesteld op null voor de bovenliggende container.

   - Als dit aanwezig is, wordt de `DefaultTimeToLive` waarde van de bovenliggende container overschreven.

## <a name="time-to-live-configurations"></a>Time to Live-configuraties

* Als TTL is ingesteld op *'n'* op een container, vervallen de artikelen in die container na *n* seconden.  Als er items in dezelfde container zijn die hun eigen tijd hebben om te leven, ingesteld op -1 (wat aangeeft dat ze niet verlopen) of als sommige items de tijd tot live-instelling met een ander getal hebben overschreven, verlopen deze items op basis van hun eigen geconfigureerde TTL-waarde. 

* Als TTL niet is ingesteld op een container, heeft de tijd om te leven op een item in deze container geen effect. 

* Als TTL op een container is ingesteld op -1, vervalt een item in deze container dat de tijd heeft om te leven ingesteld op n, na n seconden en verlopen de resterende items niet.

## <a name="examples"></a>Voorbeelden

In deze sectie worden enkele voorbeelden weergegeven met verschillende tijds- en leefwaarden die zijn toegewezen aan container en items:

### <a name="example-1"></a>Voorbeeld 1

TTL op container is ingesteld op null (DefaultTimeToLive = null)

|TTL op object| Resultaat|
|---|---|
|ttl = null|    TTL is uitgeschakeld. Het item vervalt nooit (standaard).|
|ttl = -1   |TTL is uitgeschakeld. Het item vervalt nooit.|
|ttl = 2000 |TTL is uitgeschakeld. Het item vervalt nooit.|


### <a name="example-2"></a>Voorbeeld 2

TTL op container is ingesteld op -1 (DefaultTimeToLive = -1)

|TTL op object| Resultaat|
|---|---|
|ttl = null |TTL is ingeschakeld. Het item vervalt nooit (standaard).|
|ttl = -1   |TTL is ingeschakeld. Het item vervalt nooit.|
|ttl = 2000 |TTL is ingeschakeld. Het artikel vervalt na 2000 seconden.|


### <a name="example-3"></a>Voorbeeld 3

TTL op container is ingesteld op 1000 (DefaultTimeToLive = 1000)

|TTL op object| Resultaat|
|---|---|
|ttl = null|    TTL is ingeschakeld. Het item vervalt na 1000 seconden (standaard).|
|ttl = -1   |TTL is ingeschakeld. Het item vervalt nooit.|
|ttl = 2000 |TTL is ingeschakeld. Het artikel vervalt na 2000 seconden.|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van Time to Live in de volgende artikelen:

* [Tijd configureren om te leven](how-to-time-to-live.md)
