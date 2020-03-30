---
title: Referenties versleutelen in Azure Data Factory
description: Lees hoe u referenties voor uw on-premises gegevensarchieven op een machine versleutelen en opslaan met zelfgehoste runtime voor integratie.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: abnarain
ms.openlocfilehash: 67ba2fadd5376997b528af4fcd2c5a666bb134a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443991"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Referenties versleutelen voor on-premises gegevensarchieven in Azure Data Factory
U referenties voor uw on-premises gegevensopslag (gekoppelde services met gevoelige informatie) versleutelen en opslaan op een machine met zelf gehoste runtime voor integratie. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U geeft een JSON-definitiebestand met referenties door aan de <br/>[**Nieuw-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet om een uitvoer JSON definitie bestand met de versleutelde referenties te produceren. Gebruik vervolgens de bijgewerkte JSON-definitie om de gekoppelde services te maken.

## <a name="author-sql-server-linked-service"></a>Sql Server-gekoppelde service maken
Maak een JSON-bestand met de naam **SqlServerLinkedService.json** in elke map met de volgende inhoud:  

Vervang `<servername>` `<databasename>`, `<username>`, `<password>` en met waarden voor uw SQL Server voordat u het bestand opslaat. En, `<integration runtime name>` vervangen door de naam van uw integratie runtime. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>Referenties versleutelen
Als u de gevoelige gegevens van de JSON-payload wilt versleutelen op een on-premises self-hosted integration runtime, voert u **New-AzDataFactoryV2LinkedServiceEncryptedCredential**uit en geeft u de JSON-payload door. Deze cmdlet zorgt ervoor dat de referenties worden versleuteld met Behulp van DPAPI en lokaal opgeslagen op het zelf gehoste runtime-knooppunt voor integratie. De uitvoerpayload met de versleutelde verwijzing naar de referentie kan worden doorgestuurd naar een ander JSON-bestand (in dit geval 'encryptedLinkedService.json').

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>De JSON gebruiken met versleutelde referenties
Gebruik nu het JSON-bestand uitvoer van de vorige opdracht met de versleutelde referenties om de **SqlServerLinkedService**in te stellen.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>Volgende stappen
Zie Overwegingen voor [gegevensverplaatsingen](data-movement-security-considerations.md)voor informatie over beveiligingsoverwegingen voor gegevensverkeer.

