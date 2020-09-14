---
title: Meer informatie over het verplaatsen van een Azure Cosmos DB-account naar een andere regio
description: Meer informatie over het verplaatsen van een Azure Cosmos DB-account naar een andere regio
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 09/12/2020
ms.author: mjbrown
ms.openlocfilehash: 60c28a96008355491c058cd08dbbb3a1cbffad98
ms.sourcegitcommit: 94c750edd4d755d6ecee50ac977328098a277479
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90059365"
---
# <a name="how-to-move-an-azure-cosmos-db-account-to-another-region"></a>Een Azure Cosmos DB-account verplaatsen naar een andere regio

In dit artikel wordt beschreven hoe u een regio kunt verplaatsen waar gegevens worden gerepliceerd in Azure Cosmos DB of hoe u de meta gegevens van de account (Azure Resource Manager) en de gegevens van de ene naar de andere regio kunt migreren.

## <a name="move-data-from-one-region-to-another"></a>Gegevens verplaatsen van de ene regio naar een andere

Azure Cosmos DB biedt standaard ondersteuning voor gegevens replicatie, zodat het verplaatsen van gegevens van de ene regio naar een andere eenvoudig is en kan worden uitgevoerd met behulp van de Azure Portal, Azure PowerShell of Azure CLI en omvat de volgende stappen:

1. Een nieuwe regio toevoegen aan het account

    Als u een nieuwe regio aan een Azure Cosmos DB account wilt toevoegen, raadpleegt [u regio's toevoegen aan of verwijderen uit een Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

1. Een hand matige failover uitvoeren naar de nieuwe regio

    In situaties waarin de regio die wordt verwijderd, momenteel de schrijf regio voor het account is, moet u een failover initiëren naar de nieuwe regio die hierboven is toegevoegd. Dit is een downtime-bewerking van nul. Als u een lees regio in een account met meerdere regio's wilt verplaatsen, kunt u deze stap overs Laan. Ga als volgt te werk om een failover te initiëren: [hand matige failover uitvoeren voor een Azure Cosmos-account](how-to-manage-database-account.md#manual-failover)

1. De oorspronkelijke regio verwijderen

    Als u een regio uit een Azure Cosmos DB account wilt verwijderen, raadpleegt [u regio's toevoegen aan of verwijderen uit een Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)

## <a name="migrate-azure-cosmos-db-account-meta-data"></a>Meta gegevens van Azure Cosmos DB-account migreren

Azure Cosmos DB biedt geen systeem eigen ondersteuning voor het migreren van meta gegevens van accounts vanuit de ene regio naar een andere. Als u de meta gegevens van het account en de klant gegevens van de ene naar de andere regio wilt migreren, moet u een nieuw account in de gewenste regio maken en moeten de gegevens hand matig worden gekopieerd. Bij een bijna geen downtime-migratie voor SQL API moet u gebruikmaken van [ChangeFeed](change-feed.md) of een hulp programma waarmee het kan worden gebruikt. Zie [Opties voor het migreren van uw on-premises of Cloud gegevens naar Azure Cosmos DB](cosmosdb-migrationchoices.md)als u de MONGODB-API, Cassandra of een andere API wilt migreren of meer wilt weten over de opties voor het migreren van gegevens tussen accounts. De volgende stappen laten zien hoe u een Azure Cosmos DB-account voor SQL API en de bijbehorende gegevens van de ene regio naar een andere kunt migreren:

1. Een nieuw Azure Cosmos DB-account maken in de gewenste regio

    Als u een nieuw account via Azure Portal, Power shell of CLI wilt maken, gaat u naar [een Azure Cosmos DB account maken](how-to-manage-database-account.md#create-an-account).

1. Een nieuwe data base en container maken

    Als u een nieuwe data base en container wilt maken, gaat u naar [een Azure Cosmos-container maken](how-to-create-container.md)

1. Gegevens migreren met het Azure Cosmos DB live data Migrator-hulp programma

    Als u gegevens met bijna nul downtime wilt migreren, raadpleegt u [Azure Cosmos DB hulp programma live data Migrator](https://github.com/Azure-Samples/azure-cosmosdb-live-data-migrator)

1. connection string van toepassing bijwerken

    Wanneer het hulp programma voor Live migratie nog steeds wordt uitgevoerd, werkt u de verbindings gegevens bij in de nieuwe implementatie van uw toepassingen. De eind punten en sleutels voor uw toepassing kunnen worden opgehaald uit de Azure Portal.

    :::image type="content" source="./media/secure-access-to-data/nosql-database-security-master-key-portal.png" alt-text="Toegangs beheer (IAM) in de Azure Portal-demonstring NoSQL data base Security":::

1. Aanvragen omleiden naar een nieuwe toepassing

    Zodra de nieuwe toepassing is verbonden met Azure Cosmos DB kunt u client aanvragen omleiden naar uw nieuwe implementatie.

1. Verwijder alle resources die niet meer nodig zijn

    Met aanvragen die nu volledig worden omgeleid naar het nieuwe exemplaar, kunt u de oude Azure Cosmos DB-account en het hulp programma live data Migrator verwijderen.

## <a name="next-steps"></a>Volgende stappen

Lees de volgende artikelen voor meer informatie en voor beelden over het beheren van het Azure Cosmos-account, de data base en containers:

* [Een Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Feed wijzigen in Azure Cosmos DB](change-feed.md)
