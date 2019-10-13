---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: f634ebb4d8c3f443b5d843b0093c955b384a9f5f
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285608"
---
| Domein namen                  | Uitgaande poorten | Beschrijving                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Vereist door de zelf-hostende Integration runtime om verbinding te maken met Services voor gegevens verplaatsing in Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Vereist door de zelf-hostende Integration runtime om verbinding te maken met de Data Factory-service. |
| `download.microsoft.com`    | 443            | Vereist door de zelf-hostende Integration runtime voor het downloaden van de updates. Als u automatisch bijwerken hebt uitgeschakeld, kunt u dit overs Laan. |
| `*.core.windows.net`          | 443            | Wordt gebruikt door de zelf-hostende Integration runtime om verbinding te maken met het Azure Storage-account wanneer u de functie voor [gefaseerd kopiëren](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) gebruikt. |
| `*.database.windows.net`      | 1433           | Beschrijving Vereist wanneer u kopieert van of naar Azure SQL Database of Azure SQL Data Warehouse. Gebruik de functie voor gefaseerd kopiëren om gegevens te kopiëren naar Azure SQL Database of Azure SQL Data Warehouse zonder poort 1433 te openen. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Beschrijving Vereist wanneer u kopieert van of naar Azure Data Lake Store. |
