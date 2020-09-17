---
title: De beschik baarheid van Azure Cosmos-Sdk's in multiregionale omgevingen vaststellen en oplossen
description: Meer informatie over de beschik baarheid van Azure Cosmos SDK als u in meerdere regionale omgevingen werkt.
author: ealsur
ms.service: cosmos-db
ms.date: 09/16/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0c717aca88095df05fc7927f3c3d6e2d481925d2
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708704"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>De beschik baarheid van Azure Cosmos-Sdk's in multiregionale omgevingen vaststellen en oplossen

In dit artikel wordt het gedrag van de nieuwste versie van Azure Cosmos Sdk's beschreven wanneer u een connectiviteits probleem met een bepaalde regio ziet of wanneer er een failover optreedt voor een regio.

Alle Azure Cosmos Sdk's bieden u een optie om de regionale voor keur aan te passen. De volgende eigenschappen worden gebruikt in verschillende Sdk's:

* De eigenschap [Connection Policy. PreferredLocations](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) in de .NET v2-SDK.
* De eigenschappen [CosmosClientOptions. ApplicationRegion](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) of [CosmosClientOptions. ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) in de .net v3-SDK.
* De methode [CosmosClientBuilder. preferredRegions](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions) in Java v4 SDK.
* De para meter [CosmosClient. preferred_locations](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) in python SDK.

Voor accounts met één schrijf regio worden alle schrijf bewerkingen altijd naar de schrijf regio uitgevoerd, zodat de lijst met voorkeurs regio's alleen van toepassing is op Lees bewerkingen. Voor meerdere accounts voor het schrijven van regio's is de lijst met voor keuren van invloed op de lees-en schrijf bewerkingen.

Als u geen voorkeurs regio instelt, wordt de regionale voorkeurs volgorde gedefinieerd in de [volg orde van de Azure Cosmos DB regio lijst](distribute-data-globally.md).

Wanneer een van de volgende scenario's optreedt, worden logboeken door de client met behulp van de Azure Cosmos SDK beschikbaar gemaakt en worden de gegevens voor nieuwe pogingen opgenomen als onderdeel van de **Diagnostische gegevens**van de bewerking.

## <a name="removing-a-region-from-the-account"></a><a id="remove region"></a>Een regio uit het account verwijderen

Wanneer u een regio uit een Azure Cosmos-account verwijdert, detecteert elke SDK-client die actief gebruikmaakt van het account, het verwijderen van de regio via een back-end-respons code. De client markeert vervolgens het regionale eind punt als niet-beschikbaar. De client probeert de huidige bewerking opnieuw en alle toekomstige bewerkingen worden permanent doorgestuurd naar de volgende regio in de volg orde van voor keur.

## <a name="adding-a-region-to-an-account"></a>Een regio toevoegen aan een account

De Azure Cosmos SDK-client leest elke vijf minuten de account configuratie en vernieuwt de regio's waarvan het op de hoogte is.

Als u een regio verwijdert en later weer toevoegt aan het account en de toegevoegde regio een hogere voor keur heeft, zal de SDK deze regio permanent gaan gebruiken. Nadat de toegevoegde regio is gedetecteerd, worden alle toekomstige aanvragen ernaar doorgeleid.

Als u de client zo configureert dat bij voor keur verbinding wordt gemaakt met een regio die het Azure Cosmos-account niet heeft, wordt de voorkeurs regio genegeerd. Als u die regio later toevoegt, detecteert de client deze en gaat deze over naar die regio.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>Een failover voor de schrijf regio in één schrijf regio-account

Als u een failover van de huidige schrijf regio initieert, mislukt de volgende schrijf aanvraag met een bekend back-end-antwoord. Wanneer dit antwoord wordt gedetecteerd, voert de client een query uit op het account om te zien wat de nieuwe schrijf regio is en gaat het om de huidige bewerking opnieuw uit te voeren en alle toekomstige schrijf bewerkingen permanent naar de nieuwe regio te routeren.

## <a name="regional-outage"></a>Regionale storing

Als het account een enkele schrijf regio is en de regionale storing optreedt tijdens een schrijf bewerking, is het gedrag vergelijkbaar met een [hand matige failover](#manual-failover-single-region). Voor lees aanvragen of meerdere accounts voor het schrijven van regio's is het gedrag vergelijkbaar met het [verwijderen van een regio](#remove region).

## <a name="session-consistency-guarantees"></a>Garantie voor sessie consistentie

Bij het gebruik van [sessie consistentie](consistency-levels.md#guarantees-associated-with-consistency-levels)moet de client garanderen dat het eigen schrijf bewerkingen kan lezen. In accounts met een enkele schrijf regio waarbij de voor keur voor de Lees regio afwijkt van de schrijf regio, kunnen er gevallen zijn waarin de gebruiker een schrijf actie uitvoert en wanneer een lees bewerking wordt uitgevoerd vanuit een lokale regio, de lokale regio de gegevens replicatie nog niet heeft ontvangen (snelheid van de Light beperking). In dergelijke gevallen detecteert de SDK de specifieke fout voor de Lees bewerking en wordt de Lees actie in de regio hub opnieuw geprobeerd om de consistentie van de sessie te garanderen.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>Problemen met de tijdelijke verbinding met het TCP-protocol

In scenario's waarin de Azure Cosmos SDK-client is geconfigureerd voor het gebruik van het TCP-protocol voor een bepaalde aanvraag, kunnen er situaties zijn waarin de netwerk omstandigheden de communicatie met een bepaald eind punt tijdelijk beïnvloeden. Deze tijdelijke netwerk omstandigheden kunnen worden geoppereerd als TCP-time-outs. De client probeert de aanvraag gedurende enkele seconden lokaal uit te voeren op hetzelfde eind punt.

Als de gebruiker een lijst met voorkeurs regio's heeft geconfigureerd met meer dan één regio en het Azure Cosmos-account meerdere schrijf regio's of één schrijf regio is en de bewerking een lees aanvraag is, probeert de client de ene bewerking in de volgende regio uit de voorkeurs lijst uit te voeren.

## <a name="next-steps"></a>Volgende stappen

* De nieuwste [.NET SDK](sql-api-sdk-dotnet-standard.md) gebruiken
* De meest recente [Java-SDK](sql-api-sdk-java-v4.md) gebruiken
* De meest recente [python-SDK](sql-api-sdk-python.md) gebruiken
* De nieuwste [knoop punt-SDK](sql-api-sdk-node.md) gebruiken
