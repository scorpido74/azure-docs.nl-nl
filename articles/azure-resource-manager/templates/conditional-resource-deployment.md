---
title: Voorwaardelijke implementatie met sjablonen
description: Beschrijft hoe u een resource voorwaardelijk implementeert in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153417"
---
# <a name="conditional-deployment-in-arm-templates"></a>Voorwaardelijke implementatie in ARM-sjablonen

Soms moet u optioneel een resource implementeren in een ARM-sjabloon (Azure Resource Manager). Gebruik `condition` het element om op te geven of de resource is geïmplementeerd. De waarde voor dit element wordt oplost waar of onwaar. Wanneer de waarde waar is, wordt de resource gemaakt. Wanneer de waarde onwaar is, wordt de resource niet gemaakt. De waarde kan alleen worden toegepast op de hele resource.

## <a name="new-or-existing-resource"></a>Nieuwe of bestaande resource

U voorwaardelijke implementatie gebruiken om een nieuwe bron te maken of een bestaande bron te gebruiken. In het volgende voorbeeld ziet u hoe u de voorwaarde gebruikt om een nieuw opslagaccount te implementeren of een bestaand opslagaccount te gebruiken.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

Wanneer de parameter **newOrExisting** is ingesteld op **nieuw**, wordt de voorwaarde geëvalueerd op true. Het opslagaccount wordt geïmplementeerd. Wanneer **newOrExisting** echter is ingesteld op **bestaand,** wordt de voorwaarde geëvalueerd op false en wordt het opslagaccount niet geïmplementeerd.

Zie [VM met een nieuw of bestaand virtueel netwerk, opslag en openbaar IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)voor een volledige voorbeeldsjabloon die het `condition` element gebruikt.

## <a name="allow-condition"></a>Voorwaarde toestaan

U een parameterwaarde doorgeven die aangeeft of een voorwaarde is toegestaan. In het volgende voorbeeld wordt een SQL-server geïmplementeerd en wordt optioneel Azure-IP's toegestaan.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Zie Azure SQL [logische server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server)voor de volledige sjabloon.

## <a name="runtime-functions"></a>Runtime-functies

Als u een [referentie-](template-functions-resource.md#reference) of [lijstfunctie](template-functions-resource.md#list) gebruikt met een resource die voorwaardelijk is geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een foutmelding weergegeven als de functie verwijst naar een resource die niet bestaat.

Gebruik de functie [als](template-functions-logical.md#if) om ervoor te zorgen dat de functie alleen wordt geëvalueerd op voorwaarden wanneer de resource wordt geïmplementeerd. Zie de [functie als](template-functions-logical.md#if) voor een voorbeeldsjabloon die wordt gebruikt als en verwijzen naar een voorwaardelijk geïmplementeerde bron.

U stelt een [resource in als afhankelijk van](define-resource-dependency.md) een voorwaardelijke resource, precies zoals u elke andere resource zou doen. Wanneer een voorwaardelijke bron niet is geïmplementeerd, verwijdert Azure Resource Manager deze automatisch uit de vereiste afhankelijkheden.

## <a name="condition-with-complete-mode"></a>Voorwaarde met volledige modus

Als u een sjabloon met [volledige modus](deployment-modes.md) implementeert en een resource niet wordt geïmplementeerd omdat de voorwaarde wordt geëvalueerd op false, is het resultaat afhankelijk van welke REST API-versie u gebruikt om de sjabloon te implementeren. Als u een versie gebruikt die eerder is dan 2019-05-10, wordt de bron **niet verwijderd.** Met 2019-05-10 of hoger wordt de bron **verwijderd.** De nieuwste versies van Azure PowerShell en Azure CLI verwijderen de bron wanneer de voorwaarde onwaar is.

## <a name="next-steps"></a>Volgende stappen

* Zie [AANBEVOLEN NARM-sjabloonaanbevolen voor aanbevelingen](template-best-practices.md)over het maken van sjablonen.
* Zie [Resourceiteratie in ARM-sjablonen](copy-resources.md)als u meerdere exemplaren van een resource wilt maken.