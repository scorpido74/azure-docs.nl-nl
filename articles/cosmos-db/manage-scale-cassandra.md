---
title: Elastischschalen met Cassandra API in Azure Cosmos DB
description: Meer informatie over de opties die beschikbaar zijn om een Azure Cosmos DB Cassandra API-account te schalen en hun voordelen/nadelen
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: thvankra
ms.openlocfilehash: 10d81de48c0d8f56c7c3fd26e3fd82a8c3df84c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474676"
---
# <a name="elastically-scale-an-azure-cosmos-db-cassandra-api-account"></a>Een Azure Cosmos DB Cassandra API-account elastisch schalen

Er zijn verschillende opties om het elastische karakter van de Azure Cosmos DB API voor Cassandra te verkennen. Om te begrijpen hoe u effectief schalen in Azure Cosmos DB, is het belangrijk om te begrijpen hoe u de juiste hoeveelheid aanvraageenheden (RU/s) inrichten om rekening te houden met de prestatievereisten in uw systeem. Zie het artikel [over aanvraageenheden](request-units.md) voor meer informatie over aanvraageenheden. 

Voor de Cassandra API u de kosten voor aanvraageenheid voor afzonderlijke query's ophalen met de [.NET- en Java-SDK's.](https://docs.microsoft.com/azure/cosmos-db/find-request-unit-charge#cassandra-api) Dit is handig bij het bepalen van de hoeveelheid RU/s die u in de service moet inrichten.

![Databasebewerkingen verbruiken aanvraageenheden](./media/request-units/request-units.png)

## <a name="handling-rate-limiting-429-errors"></a>Beperking van de verwerkingssnelheid (429 fouten)

Azure Cosmos DB retourneert fouten met tariefbeperkte (429) als clients meer resources (RU/s) verbruiken dan het bedrag dat u hebt ingericht. De Cassandra API in Azure Cosmos DB vertaalt deze uitzonderingen naar overbelaste fouten op het Cassandra native protocol. 

Als uw systeem niet gevoelig is voor latentie, kan het voldoende zijn om de doorvoersnelheid te beperken met behulp van nieuwe pogingen. Zie het voorbeeld van [java-code](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) voor het transparant hanteren van de snelheid door de [Azure Cosmos DB-extensie](https://github.com/Azure/azure-cosmos-cassandra-extensions) voor [het beleid voor opnieuw proberen van Cassandra](https://docs.datastax.com/en/developer/java-driver/4.4/manual/core/retries/) op Java te gebruiken. U de [Spark-extensie](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) ook gebruiken om tariefbeperkend te werken.

## <a name="manage-scaling"></a>Schalen beheren

Als u de latentie wilt minimaliseren, is er een scala aan opties voor het beheren van schaal- en provisioningdoorvoerdoorvoer (RU's) in de Cassandra-API:

* [Handmatig gebruik maken van de Azure-portal](#use-azure-portal)
* [Programmatisch met behulp van de functies van het besturingsvlak](#use-control-plane)
* [Programmatisch met behulp van CQL-opdrachten met een specifieke SDK](#use-cql-queries)
* [Dynamisch met behulp van Autopilot](#use-autopilot)

In de volgende secties worden de voor- en nadelen van elke aanpak toegelicht. U dan beslissen over de beste strategie om de schaalbehoeften van uw systeem, de totale kosten en efficiëntiebehoeften voor uw oplossing in evenwicht te brengen.

## <a name="use-the-azure-portal"></a><a id="use-azure-portal"></a>De Azure-portal gebruiken

U de resources in Azure Cosmos DB Cassandra API-account schalen met azure portal. Zie voor meer informatie het artikel over [de doorvoer van bestanden en databases voor het inrichten van containers en databases](set-throughput.md). In dit artikel worden de relatieve voordelen van het instellen van doorvoer op [database-](set-throughput.md#set-throughput-on-a-database) of [containerniveau](set-throughput.md#set-throughput-on-a-container) in de Azure-portal uitgelegd. De termen "database" en "container" genoemd in deze artikelen kaart naar "keyspace" en "tabel" respectievelijk voor de Cassandra API.

Het voordeel van deze methode is dat het een eenvoudige kant-en-klare manier is om de doorvoercapaciteit in de database te beheren. Het nadeel is echter dat in veel gevallen, uw benadering van schalen kan vereisen dat bepaalde niveaus van automatisering zowel kosteneffectief als goed presterend zijn. In de volgende secties worden de relevante scenario's en methoden toegelicht.

## <a name="use-the-control-plane"></a><a id="use-control-plane"></a>Het besturingsvlak gebruiken

De API van Azure Cosmos DB voor Cassandra biedt de mogelijkheid om de doorvoer programmatisch aan te passen met behulp van onze verschillende functies voor besturingsvlak. Zie de artikelen [Azure Resource Manager,](manage-cassandra-with-resource-manager.md) [Powershell](powershell-samples-cassandra.md)en [Azure CLI](cli-samples-cassandra.md) voor richtlijnen en voorbeelden.

Het voordeel van deze methode is dat u het opschalen of verkleinen van resources automatiseren op basis van een timer om rekening te houden met piekactiviteit of perioden van lage activiteit. Bekijk [hier](https://github.com/Azure-Samples/azure-cosmos-throughput-scheduler) ons voorbeeld voor hoe u dit bereiken met Azure-functies en Powershell.

Een nadeel van deze aanpak kan zijn dat je niet reageren op onvoorspelbare veranderende schaalbehoeften in real-time. In plaats daarvan moet u mogelijk gebruik maken van de toepassingscontext in uw systeem, op client/SDK-niveau of met [Autopilot.](provision-throughput-autopilot.md)

## <a name="use-cql-queries-with-a-specific-sdk"></a><a id="use-cql-queries"></a>CQL-query's gebruiken met een specifieke SDK

U het systeem dynamisch schalen met code door de [CQL ALTER-opdrachten](cassandra-support.md#keyspace-and-table-options) voor de opgegeven database of container uit te voeren.

Het voordeel van deze aanpak is dat u hiermee dynamisch en op een aangepaste manier inspelen op schaalbehoeften die bij uw toepassing passen. Met deze aanpak u nog steeds gebruikmaken van de standaard RU/s kosten en tarieven. Als de schaalbehoeften van uw systeem meestal voorspelbaar zijn (ongeveer 70% of meer), kan het gebruik van SDK met CQL een kosteneffectievere methode zijn om automatisch te schalen dan het gebruik van Autopilot. Het nadeel van deze aanpak is dat het vrij complex kan zijn om nieuwe pogingen te implementeren, terwijl tariefbeperking de latentie kan verhogen.

## <a name="use-autopilot"></a><a id="use-autopilot"></a>Automatische piloot gebruiken

Naast handmatige of programmatische manier van inrichten van doorvoer, u azure cosmos-containers ook configureren in de Automatische pilootmodus. De automatische pilootmodus schaalt automatisch en direct naar uw verbruiksbehoeften binnen bepaalde RU-bereiken zonder dat dit ten koste gaat van SLA's. Zie het artikel [Azure Cosmos-containers en databases maken in het automatische pilootmodusartikel](provision-throughput-autopilot.md) voor meer informatie.

Het voordeel van deze aanpak is dat het de eenvoudigste manier is om de schaalbehoeften in uw systeem te beheren. Het garandeert geen tariefbeperking toe te passen **binnen de geconfigureerde RU-bereiken**. Het nadeel is dat, als de schaalbehoeften in uw systeem voorspelbaar zijn, Autopilot een minder kosteneffectieve manier kan zijn om met uw schaalbehoeften om te gaan dan het gebruik van de hierboven genoemde op maat gemaakte besturingsvlak of SDK-niveaubenaderingen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java toepassing
