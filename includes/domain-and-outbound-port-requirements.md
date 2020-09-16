---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 0b2831321c9f4d855a19605c1ce5ace9fa11c88b
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596108"
---
| Domeinnamen                  | Uitgaande poorten | Beschrijving                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Vereist door de zelf-hostende Integration Runtime om verbinding te maken met services voor gegevensverplaatsing in Azure Data Factory. |
| `{datafactory}.{region}.datafactory.azure.net`<br> of `*.frontend.clouddatahub.net` | 443            | Vereist door de zelf-hostende Integration Runtime om verbinding te maken met de Data Factory-service. <br>Voor nieuwe Data Factory's kunt u de FQDN vinden in uw zelf-hostende Integration Runtime-sleutel, in de indeling {datafactory}.{regio}.datafactory.azure.net. Gebruik voor een oude Data factory, als u de FQDN niet ziet in uw zelf-hostende Integration-sleutel, in plaats daarvan *.frontend.clouddatahub.net. |
| `download.microsoft.com`    | 443            | Vereist door de zelf-hostende Integration Runtime voor het downloaden van de updates. Als u automatische updates heb uitgeschakeld, kunt u het configureren van dit domein overslaan. |
| `*.core.windows.net`          | 443            | Wordt gebruikt door de zelf-hostende Integration Runtime om verbinding te maken met het Azure-opslagaccount wanneer u de functie voor [gefaseerd kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) gebruikt. |
| `*.database.windows.net`      | 1433           | Alleen vereist bij het kopiëren van of naar Azure SQL Database of Azure Synapse Analytics, en anders optioneel. Gebruik de functie voor gefaseerd kopiëren om gegevens te kopiëren naar SQL Database of Synapse Analytics zonder poort 1433 te openen. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Alleen vereist als u kopieert van of naar Azure Data Lake Store, en anders optioneel. |
