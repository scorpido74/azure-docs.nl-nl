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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74559310"
---
| Domeinnamen                  | Uitgaande poorten | Beschrijving                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Vereist door de self-hosted integratieruntime om verbinding te maken met gegevensverplaatsingsservices in Azure Data Factory. |
| `*.frontend.clouddatahub.net` | 443            | Vereist door de self-hosted integratie runtime om verbinding te maken met de Data Factory service. |
| `download.microsoft.com`    | 443            | Vereist door de zelf gehoste integratie runtime voor het downloaden van de updates. Als u de automatische update hebt uitgeschakeld, u het configureren van dit domein overslaan. |
| `*.core.windows.net`          | 443            | Wordt gebruikt door de zelf gehoste runtime voor integratie om verbinding te maken met het Azure-opslagaccount wanneer u de [gefaseerde kopieerfunctie](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#staged-copy) gebruikt. |
| `*.database.windows.net`      | 1433           | Alleen vereist wanneer u van of naar Azure SQL Database of Azure SQL Data Warehouse kopieert en optioneel anders. Gebruik de functie gefaseerdkopiëren om gegevens naar SQL Database of SQL Data Warehouse te kopiëren zonder poort 1433 te openen. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Alleen vereist wanneer u van of naar Azure Data Lake Store kopieert en anders optioneel. |
