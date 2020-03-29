---
title: Bovenliggende resourcefouten
description: Beschrijft hoe u fouten oplossen wanneer u met een bovenliggende bron werkt in een Azure Resource Manager-sjabloon.
ms.topic: troubleshooting
ms.date: 08/01/2018
ms.openlocfilehash: f1847389d60ddf3c6abc70bc3309940c2246084e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76154037"
---
# <a name="resolve-errors-for-parent-resources"></a>Fouten voor bovenliggende resources oplossen

In dit artikel worden de fouten beschreven die u krijgen bij het implementeren van een resource die afhankelijk is van een bovenliggende bron.

## <a name="symptom"></a>Symptoom

Wanneer u een resource implementeert die een onderliggend kind is voor een andere resource, ontvangt u mogelijk de volgende fout:

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

## <a name="cause"></a>Oorzaak

Wanneer een resource een onderliggende bron is, moet de bovenliggende resource bestaan voordat de onderliggende bron wordt gemaakt. De naam van de onderliggende bron definieert de verbinding met de bovenliggende resource. De naam van de onderliggende `<parent-resource-name>/<child-resource-name>`bron staat in de indeling . Een SQL-database kan bijvoorbeeld worden gedefinieerd als:

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

Als u zowel de server als de database in dezelfde sjabloon implementeert, maar geen afhankelijkheid van de server opgeeft, kan de database-implementatie beginnen voordat de server is geïmplementeerd.

Als de bovenliggende resource al bestaat en niet in dezelfde sjabloon wordt geïmplementeerd, krijgt u deze fout wanneer Resource Manager de onderliggende bron niet kan koppelen aan de bovenliggende bron. Deze fout kan optreden wanneer de onderliggende resource niet in de juiste indeling is of wanneer de onderliggende resource wordt geïmplementeerd in een resourcegroep die anders is dan de resourcegroep voor bovenliggende resources.

## <a name="solution"></a>Oplossing

Als u deze fout wilt oplossen wanneer bovenliggende en onderliggende bronnen in dezelfde sjabloon worden geïmplementeerd, moet u een afhankelijkheid opnemen.

```json
"dependsOn": [
  "[variables('databaseServerName')]"
]
```

Als u deze fout wilt oplossen toen de bovenliggende resource eerder in een andere sjabloon werd geïmplementeerd, stelt u geen afhankelijkheid in. Implementeer het onderliggende kind in dezelfde resourcegroep en geef de naam van de bovenliggende resource op.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "sqlServerName": {
      "type": "string"
    },
    "databaseName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers/databases",
      "apiVersion": "2014-04-01",
      "name": "[concat(parameters('sqlServerName'), '/', parameters('databaseName'))]",
      "location": "[resourceGroup().location]",
      "properties": {
        "collation": "SQL_Latin1_General_CP1_CI_AS",
        "edition": "Basic"
      }
    }
  ],
  "outputs": {}
}
```

Zie [De volgorde definiëren voor het implementeren van resources in Azure Resource Manager-sjablonen voor](define-resource-dependency.md)meer informatie.