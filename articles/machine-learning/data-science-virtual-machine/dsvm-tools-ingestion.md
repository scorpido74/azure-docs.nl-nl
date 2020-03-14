---
title: Gegevensopnamehulpprogramma's
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogram ma's voor gegevens opname en hulpprogram ma's die vooraf zijn geïnstalleerd op de Data Science Virtual Machine.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79270054"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine gegevensopnamehulpprogramma 's

Als een van de eerste technische stappen in een Data Science of AI-project, moet u de gegevens sets identificeren die moeten worden gebruikt en deze in uw analyse omgeving opnemen. De Data Science Virtual Machine (DSVM) bevat hulpprogram ma's en bibliotheken waarmee u gegevens uit verschillende bronnen lokaal op de DSVM kunt plaatsen, of in een gegevens platform in de Cloud of on-premises.

Hier volgen enkele hulp middelen voor het verplaatsen van gegevens die beschikbaar zijn in de DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een hulp programma voor het kopiëren van gegevens uit Azure Blob-opslag naar Azure Data Lake Store. Het kan ook gegevens tussen twee Azure Data Lake Store-accounts kopiëren.      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Meerdere blobs uit Azure Blob-opslag importeren in Azure Data Lake Store.      |
|  Hoe kan ik gebruik / uitvoeren?    |   Open een opdracht prompt en typ `adlcopy` om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [AdlCopy gebruiken](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Gerelateerde hulpprogram ma's op de DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een hulpprogramma voor Azure. Het bevat ook opdracht opdrachten voor het verplaatsen van gegevens van Azure-gegevens platforms zoals Azure Blob Storage en Azure Data Lake Store.     |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Wordt doorgaans gebruikt      | Importeren en exporteren van gegevens van en naar Azure Storage en Azure Data Lake Store.      |
|  Hoe kan ik gebruik / uitvoeren?    |   Open een opdracht prompt en typ `az` om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [Azure CLI gebruiken](https://docs.microsoft.com/cli/azure)     |
| Gerelateerde hulpprogram ma's op de DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een hulp programma voor het kopiëren van gegevens naar en van lokale bestanden, Azure Blob-opslag, bestanden en tabellen.      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Kopiëren van bestanden naar Azure Blob-opslag en het kopiëren van blobs tussen accounts.      |
|  Hoe kan ik gebruik / uitvoeren?    |   Open een opdracht prompt en typ `azcopy` om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Gerelateerde hulpprogram ma's op de DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB Data Migration tool

|    |           |
| ------------- | ------------- |
| Wat is het?   | Hulp programma voor het importeren van gegevens uit verschillende bronnen in Azure Cosmos DB, een NoSQL-data base in de Cloud. Deze bronnen zijn onder andere JSON-bestanden, CSV-bestanden, SQL, MongoDB, Azure-tabel opslag, Amazon DynamoDB en Azure Cosmos DB SQL API-verzamelingen.      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Importeren van bestanden van een virtuele machine naar CosmosDB, importeren van gegevens uit Azure-tabel opslag naar CosmosDB en het importeren van gegevens uit een Microsoft SQL Server-Data Base naar CosmosDB.     |
|  Hoe kan ik gebruik / uitvoeren?    |   Als u de opdracht regel versie wilt gebruiken, opent u een opdracht prompt en typt u `dt`. Als u het hulp programma GUI wilt gebruiken, opent u een opdracht prompt en typt u `dtui`.    |
| Koppelingen naar voorbeelden      | [Gegevens CosmosDB importeren](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Gerelateerde hulpprogram ma's op de DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Opslagverkenner

|    |           |
| ------------- | ------------- |
| Wat is het?   | Grafische gebruikers interface voor interactie met bestanden die zijn opgeslagen in de Azure-Cloud. |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Gegevens importeren en exporteren uit de DSVM.    |
|  Hoe kan ik gebruik / uitvoeren?    | Zoek naar ' Azure Storage Explorer ' in het menu Start. |
| Koppelingen naar voorbeelden      | [Azure-opslagverkenner](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| Wat is het?   | SQL Server-hulpprogramma om gegevens tussen SQL Server en een bestand te kopiëren.      |
| Ondersteunde DSVM-versies      | Windows      |
| Wordt doorgaans gebruikt      | Het importeren van een CSV-bestand in een SQL Server tabel en het exporteren van een SQL Server-tabel naar een bestand.      |
|  Hoe kan ik gebruik / uitvoeren?    |   Open een opdracht prompt en typ `bcp` om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [BCP-hulp programma](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Gerelateerde hulpprogram ma's op de DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>Blobfuse

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een hulp programma om een Azure Blob Storage-container te koppelen aan het Linux-bestands systeem.      |
| Ondersteunde DSVM-versies      | Linux      |
| Wordt doorgaans gebruikt      | Lezen van en schrijven naar blobs in een container.      |
|  Hoe gebruikt en voert u deze uit?    |   Voer _blobfuse_ uit op een Terminal.    |
| Koppelingen naar voorbeelden      | [blobfuse op GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Gerelateerde hulpprogram ma's op de DSVM      | Azure CLI      |
