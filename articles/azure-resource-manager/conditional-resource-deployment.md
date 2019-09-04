---
title: Voorwaardelijke implementatie met Azure Resource Manager sjablonen
description: Hierin wordt beschreven hoe u een resource voorwaardelijk kunt implementeren in een Azure Resource Manager sjabloon.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259507"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Voorwaardelijke implementatie in Resource Manager-sjablonen

Soms moet u optioneel een resource in een sjabloon implementeren. Gebruik het `condition` -element om op te geven of de resource is geïmplementeerd. De waarde voor dit element wordt omgezet in True of false. Als de waarde True is, wordt de resource gemaakt. Als de waarde False is, wordt de resource niet gemaakt. De waarde kan alleen worden toegepast op de hele resource.

## <a name="new-or-existing-resource"></a>Nieuwe of bestaande resource

U kunt voorwaardelijke implementatie gebruiken om een nieuwe resource te maken of een bestaande te gebruiken. In het volgende voor beeld ziet u hoe u een voor waarde gebruikt om een nieuw opslag account te implementeren of een bestaand opslag account te gebruiken.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Wanneer de para meter **newOrExisting** is ingesteld op **Nieuw**, wordt de voor waarde geëvalueerd als waar. Het opslag account wordt geïmplementeerd. Als **newOrExisting** echter is ingesteld op **Exists**, wordt de voor waarde geëvalueerd als False en wordt het opslag account niet geïmplementeerd.

Zie `condition` [VM met een nieuwe of bestaande Virtual Network, opslag en openbaar IP-adres](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)voor een complete voorbeeld sjabloon die gebruikmaakt van het-element.

## <a name="allow-condition"></a>Voor waarde toestaan

U kunt een parameter waarde door geven die aangeeft of een voor waarde is toegestaan. In het volgende voor beeld wordt een SQL-Server geïmplementeerd en worden optioneel Azure Ip's toegestaan.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
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
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
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

Zie voor de volledige sjabloon de [logische Azure SQL-Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Runtime-functies

Als u een [verwijzing](resource-group-template-functions-resource.md#reference) of [lijst](resource-group-template-functions-resource.md#list) functie gebruikt met een resource die voorwaardelijk is geïmplementeerd, wordt de functie geëvalueerd, zelfs als de resource niet is geïmplementeerd. Er wordt een fout bericht weer geven als de functie verwijst naar een resource die niet bestaat.

Gebruik de functie [als](resource-group-template-functions-logical.md#if) om ervoor te zorgen dat de functie alleen wordt geëvalueerd voor omstandigheden wanneer de resource wordt geïmplementeerd. Zie de [functie als](resource-group-template-functions-logical.md#if) voor een voorbeeld sjabloon die gebruikmaakt van if en verwijst naar een voorwaardelijk geïmplementeerde resource.

## <a name="next-steps"></a>Volgende stappen

* Zie voor aanbevelingen voor het maken van sjablonen [Azure Resource Manager best practices](template-best-practices.md)voor sjablonen.
* Als u meerdere exemplaren van een resource wilt maken, raadpleegt u [resource, eigenschap of variabele herhaling in azure Resource Manager sjablonen](resource-group-create-multiple.md).