---
title: 'Bekende problemen: migreren van MongoDB naar Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen en migratie beperkingen met migraties van MongoDB naar Azure Cosmos DB met behulp van de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/27/2020
ms.openlocfilehash: ae5d5f2d282c546f5172ca1c8cb0e420d3b6e96b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878048"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Bekende problemen/migratie beperkingen met migraties van MongoDB naar de API van Azure Cosmos DB voor MongoDB

Bekende problemen en beperkingen die zijn gekoppeld aan migraties van MongoDB naar Cosmos DB-API voor MongoDB worden beschreven in de volgende secties.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>De migratie mislukt als gevolg van het gebruik van het onjuiste SSL-certificaat

* **Symptoom**: dit probleem is duidelijk wanneer een gebruiker geen verbinding kan maken met de MongoDb-bron server. Ondanks dat alle firewall poorten zijn geopend, kan de gebruiker nog steeds geen verbinding maken.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Het gebruik van een zelfondertekend certificaat in Azure Database Migration Service kan leiden tot het mislukken van de migratie vanwege het onjuiste SSL-certificaat. Het fout bericht kan het volgende omvatten: het externe certificaat is ongeldig volgens de validatie procedure. | Gebruik een authentiek certificaat van CA.  Zelfondertekende certificaten worden doorgaans alleen gebruikt in interne tests. Wanneer u een legitiem certificaat van een CA-instantie installeert, kunt u SSL gebruiken in Azure Database Migration Service zonder probleem (verbindingen met Cosmos DB SSL via Mongo API gebruiken).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Kan de lijst met data bases die moeten worden toegewezen niet ophalen in DMS

* **Symptoom**: kan geen database lijst ophalen op de Blade **Data Base-instelling** wanneer **gegevens uit Azure Storage** modus op de Blade **bron selecteren** worden gebruikt.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| De SAS-gegevens ontbreken in het connection string van het opslag account en kunnen daarom niet worden geverifieerd. | Maak de SAS in de BLOB-container in Storage Explorer en gebruik de URL met container SAS-gegevens als bron detail connection string.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Er wordt een niet-ondersteunde versie van de data base gebruikt

* **Symptoom**: de migratie mislukt.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| U probeert te migreren naar Azure Cosmos DB vanuit een niet-ondersteunde versie van MongoDB. | Wanneer er nieuwe versies van MongoDB worden uitgebracht, worden ze getest om te zorgen voor compatibiliteit met Azure Database Migration Service. de service wordt regel matig bijgewerkt om de nieuwste versie (s) te accepteren. Als er een onmiddellijke moet worden gemigreerd, kunt u, als tijdelijke oplossing, de data bases/verzamelingen exporteren naar Azure Storage en vervolgens de bron naar de resulterende dump aanwijzen. Maak de SAS in de BLOB-container in Storage Explorer en gebruik vervolgens de URL met container SAS-gegevens als bron detail connection string.<br><br> |

## <a name="next-steps"></a>Volgende stappen

* Bekijk de zelf studie [migratie MongoDb naar de API van Azure Cosmos DB voor MongoDb online met behulp van DMS](tutorial-mongodb-cosmos-db-online.md).
* Bekijk de zelf studie [migratie MongoDb naar de API van Azure Cosmos DB voor MongoDb offline met behulp van DMS](tutorial-mongodb-cosmos-db.md).