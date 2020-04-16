---
title: Gekoppelde services parameteriseren in Azure Data Factory
description: Meer informatie over het parameteriseren van gekoppelde services in Azure Data Factory en het doorgeven van dynamische waarden tijdens het uitvoeren van de tijd.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/18/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 8fa8603f2bee7e42db0f085d78117d61bd14ce5c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419388"
---
# <a name="parameterize-linked-services-in-azure-data-factory"></a>Gekoppelde services parameteriseren in Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

U nu een gekoppelde service parameteriseren en dynamische waarden doorgeven tijdens de looptijd. Als u bijvoorbeeld verbinding wilt maken met verschillende databases op dezelfde Azure SQL Database-server, u nu de databasenaam parameteriseren in de definitie van de gekoppelde service. Dit voorkomt dat u voor elke database op de Azure SQL-databaseserver een gekoppelde service hoeft te maken. U ook andere eigenschappen in de gekoppelde servicedefinitie parameteriseren, bijvoorbeeld *gebruikersnaam.*

U de gebruikersinterface van Data Factory gebruiken in de Azure-portal of een programmeerinterface om gekoppelde services te parameteriseren.

> [!TIP]
> We raden aan om geen wachtwoorden of geheimen te parameteriseren. Sla in plaats daarvan alle verbindingstekenreeksen op in Azure Key Vault en parameterer de *geheime naam*.

Voor een zeven minuten durende introductie en demonstratie van deze functie, bekijk de volgende video:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Parameterize-connections-to-your-data-stores-in-Azure-Data-Factory/player]

## <a name="supported-data-stores"></a>Ondersteunde gegevensarchieven

Op dit moment wordt gekoppelde serviceparameterisatie ondersteund in de gebruikersinterface van datafabriek in de Azure-portal voor de volgende gegevensopslag. Voor alle andere gegevensopslag u de gekoppelde service parameteriseren door het pictogram **Code** op het tabblad **Verbindingen** te selecteren en de JSON-editor te gebruiken.
- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server
- Oracle
- Cosmos DB
- Amazon Redshift
- MySQL
- Azure Database for MySQL

## <a name="data-factory-ui"></a>Gebruikersinterface van Data Factory

![Dynamische inhoud toevoegen aan de definitie van gekoppelde service](media/parameterize-linked-services/parameterize-linked-services-image1.png)

![Een nieuwe parameter maken](media/parameterize-linked-services/parameterize-linked-services-image2.png)

## <a name="json"></a>JSON

```json
{
    "name": "AzureSqlDatabase",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:myserver.database.windows.net,1433;Database=@{linkedService().DBName};User ID=user;Password=fake;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": null,
        "parameters": {
            "DBName": {
                "type": "String"
            }
        }
    }
}
```
