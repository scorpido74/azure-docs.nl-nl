---
title: 'Bekende problemen: Migreren van MongoDB naar Azure CosmosDB'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen en migratiebeperkingen met migraties van MongoDB naar Azure Cosmos DB met behulp van de Azure Database Migration Service.
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878048"
---
# <a name="known-issuesmigration-limitations-with-migrations-from-mongodb-to-azure-cosmos-dbs-api-for-mongodb"></a>Bekende problemen/migratiebeperkingen met migraties van MongoDB naar Azure Cosmos DB's API voor MongoDB

Bekende problemen en beperkingen in verband met migraties van MongoDB naar Cosmos DB's API voor MongoDB worden beschreven in de volgende secties.

## <a name="migration-fails-as-a-result-of-using-the-incorrect-ssl-cert"></a>Migratie mislukt als gevolg van het gebruik van het onjuiste SSL-certificaat

* **Symptoom:** Dit probleem is duidelijk wanneer een gebruiker geen verbinding kan maken met de MongoDB-bronserver. Ondanks het feit dat alle firewallpoorten zijn geopend, kan de gebruiker nog steeds geen verbinding maken.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| Het gebruik van een zelfondertekend certificaat in Azure Database Migration Service kan ertoe leiden dat de migratie mislukt vanwege het onjuiste SSL-certificaat. Het foutbericht kan 'Het externe certificaat is ongeldig volgens de validatieprocedure' bevatten. | Gebruik een echt certificaat van CA.  Zelfondertekende certs worden over het algemeen alleen gebruikt in interne tests. Wanneer u een echt cert installeert van een CA-autoriteit, u SSL zonder problemen gebruiken in Azure Database Migration Service (verbindingen met Cosmos DB gebruiken SSL via Mongo API).<br><br> |

## <a name="unable-to-get-the-list-of-databases-to-map-in-dms"></a>Kan de lijst met databases niet toewijzen in DMS

* **Symptoom:** Kan de DB-lijst niet krijgen op het **instellingsblad database** wanneer u **gegevens uit de Azure-opslagmodus** gebruikt op het **bronblad Selecteren.**

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| De tekenreeks voor de opslagaccountverbinding mist de SAS-gegevens en kan dus niet worden geverifieerd. | Maak de SAS op de blobcontainer in Storage Explorer en gebruik de URL met containerSAS-info als de verbindingstekenreeks voor brondetails.<br><br> |

## <a name="using-an-unsupported-version-of-the-database"></a>Een niet-ondersteunde versie van de database gebruiken

* **Symptoom**: De migratie mislukt.

| Oorzaak         | Oplossing |
| ------------- | ------------- |
| U probeert te migreren naar Azure Cosmos DB vanuit een niet-ondersteunde versie van MongoDB. | Wanneer nieuwe versies van MongoDB worden uitgebracht, worden ze getest om compatibiliteit met azure databasemigratieservice te garanderen en wordt de service periodiek bijgewerkt om de nieuwste versie(s) te accepteren. Als er onmiddellijk moet worden gemigreerd, u als tijdelijke oplossing de databases/verzamelingen exporteren naar Azure Storage en de bron vervolgens naar de resulterende dump leiden. Maak de SAS op de blobcontainer in Storage Explorer en gebruik de URL met containerSAS-info als verbindingstekenreeks voor brondetails.<br><br> |

## <a name="next-steps"></a>Volgende stappen

* Bekijk de zelfstudie [Migrate MongoDB naar Azure Cosmos DB's API voor MongoDB online met Behulp van DMS](tutorial-mongodb-cosmos-db-online.md).
* Bekijk de zelfstudie [Migreer MongoDB naar azure cosmos DB's API voor MongoDB offline met DMS](tutorial-mongodb-cosmos-db.md).