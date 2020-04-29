---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 6af6795fefb41f1d8f2b56e0aa1fb367fc18cee2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74559310"
---
| Domeinnamen                  | Uitgaande poorten | Beschrijving                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Vereist door de zelf-hostende Integration runtime om verbinding te maken met Services voor gegevens verplaatsing in Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Vereist door de zelf-hostende Integration runtime om verbinding te maken met de Data Factory-service. |
| `download.microsoft.com`    | 443            | Vereist door de zelf-hostende Integration runtime voor het downloaden van de updates. Als u automatisch bijwerken hebt uitgeschakeld, kunt u het configureren van dit domein overs Laan. |
| `*.core.windows.net`          | 443            | Wordt gebruikt door de zelf-hostende Integration runtime om verbinding te maken met het Azure Storage-account wanneer u de functie voor [gefaseerd kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) gebruikt. |
| `*.database.windows.net`      | 1433           | Alleen vereist bij het kopiëren van of naar Azure SQL Database of Azure SQL Data Warehouse en optioneel. Gebruik de functie voor gefaseerd kopiëren om gegevens te kopiëren naar SQL Database of SQL Data Warehouse zonder poort 1433 te openen. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Alleen vereist als u kopieert van of naar Azure Data Lake Store en optioneel. |
