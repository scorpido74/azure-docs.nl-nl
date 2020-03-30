---
title: Gegevensopnamehulpprogramma's
titleSuffix: Azure Data Science Virtual Machine
description: Meer informatie over de hulpprogramma's en hulpprogramma's voor gegevensopname die vooraf zijn geïnstalleerd op de Virtuele Machine van Data Science.
keywords: hulpprogramma's voor datatechnologie, virtuele machine voor datatechnologie, hulpprogramma voor datatechnologie, linux-datatechnologie
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: d86858f8d7f09628457b718ca3c481934d720081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270054"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Data Science Virtual Machine data ingestion tools

Als een van de eerste technische stappen in een data science- of AI-project moet u de te gebruiken gegevenssets identificeren en deze in uw analyseomgeving brengen. De Data Science Virtual Machine (DSVM) biedt tools en bibliotheken om gegevens uit verschillende bronnen lokaal op de DSVM of in een dataplatform in de cloud of on-premises te brengen.

Hier volgen enkele tools voor gegevensverplaatsing die beschikbaar zijn in de DSVM.

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een tool om gegevens uit Azure Blob-opslag te kopiëren naar Azure Data Lake Store. Het kan ook gegevens kopiëren tussen twee Azure Data Lake Store-accounts.      |
| Ondersteunde DSVM-versies      | Windows      |
| Typische toepassingen      | Meerdere blobs importeren uit Azure Blob-opslag in Azure Data Lake Store.      |
|  Hoe te gebruiken / uitvoeren?    |   Open een opdrachtprompt `adlcopy` en typ om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [AdlCopy gebruiken](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Gerelateerde tools op de DSVM      | AzCopy, Azure CLI     |

## <a name="azure-cli"></a>Azure-CLI

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een beheertool voor Azure. Het bevat ook opdrachtwerkwoorden om gegevens van Azure-gegevensplatforms zoals Azure Blob-opslag en Azure Data Lake Store te verplaatsen.     |
| Ondersteunde DSVM-versies      | Windows, Linux     |
| Typische toepassingen      | Gegevens importeren en exporteren van en naar Azure Storage en Azure Data Lake Store.      |
|  Hoe te gebruiken / uitvoeren?    |   Open een opdrachtprompt `az` en typ om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [Azure CLI gebruiken](https://docs.microsoft.com/cli/azure)     |
| Gerelateerde tools op de DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een hulpmiddel om gegevens te kopiëren van en naar lokale bestanden, Azure Blob-opslag, bestanden en tabellen.      |
| Ondersteunde DSVM-versies      | Windows      |
| Typische toepassingen      | Bestanden kopiëren naar Azure Blob-opslag en blobs kopiëren tussen accounts.      |
|  Hoe te gebruiken / uitvoeren?    |   Open een opdrachtprompt `azcopy` en typ om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [AzCopy in Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Gerelateerde tools op de DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB-gegevensmigratie, hulpprogramma

|    |           |
| ------------- | ------------- |
| Wat is het?   | Hulpprogramma om gegevens uit verschillende bronnen te importeren in Azure Cosmos DB, een NoSQL-database in de cloud. Deze bronnen omvatten JSON-bestanden, CSV-bestanden, SQL, MongoDB, Azure Table-opslag, Amazon DynamoDB en Azure Cosmos DB SQL API-verzamelingen.      |
| Ondersteunde DSVM-versies      | Windows      |
| Typische toepassingen      | Bestanden importeren van een VM naar CosmosDB, gegevens importeren uit Azure-tabelopslag naar CosmosDB en gegevens importeren uit een Microsoft SQL Server-database naar CosmosDB.     |
|  Hoe te gebruiken / uitvoeren?    |   Als u de opdrachtregelversie wilt gebruiken, `dt`opent u een opdrachtprompt en typt u . Als u het gereedschap GUI wilt `dtui`gebruiken, opent u een opdrachtprompt en typt u .    |
| Koppelingen naar voorbeelden      | [CosmosDB-importgegevens](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Gerelateerde tools op de DSVM      | AzCopy, AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure Opslagverkenner

|    |           |
| ------------- | ------------- |
| Wat is het?   | Grafische gebruikersinterface voor interactie met bestanden die zijn opgeslagen in de Azure-cloud. |
| Ondersteunde DSVM-versies      | Windows      |
| Typische toepassingen      | Gegevens importeren en exporteren van de DSVM.    |
|  Hoe te gebruiken / uitvoeren?    | Zoeken naar 'Azure Storage Explorer' in het menu Start. |
| Koppelingen naar voorbeelden      | [Azure Storage Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>BCP

|    |           |
| ------------- | ------------- |
| Wat is het?   | SQL Server-hulpprogramma om gegevens tussen SQL Server en een gegevensbestand te kopiëren.      |
| Ondersteunde DSVM-versies      | Windows      |
| Typische toepassingen      | Een CSV-bestand importeren in een SQL Server-tabel en een SQL Server-tabel exporteren naar een bestand.      |
|  Hoe te gebruiken / uitvoeren?    |   Open een opdrachtprompt `bcp` en typ om hulp te krijgen.    |
| Koppelingen naar voorbeelden      | [bcp-hulpprogramma](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Gerelateerde tools op de DSVM      | SQL Server, sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| Wat is het?   | Een tool om een Azure Blob-opslagcontainer in het Linux-bestandssysteem te monteren.      |
| Ondersteunde DSVM-versies      | Linux      |
| Typische toepassingen      | Lezen en schrijven naar blobs in een container.      |
|  Hoe te gebruiken en uit te voeren?    |   Voer _blobfuse_ uit op een terminal.    |
| Koppelingen naar voorbeelden      | [blobfuse op GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Gerelateerde tools op de DSVM      | Azure-CLI      |
