---
title: Implementatievolgorde instellen voor resources
description: Beschrijft hoe u de ene resource instellen als afhankelijk van een andere resource tijdens de implementatie om ervoor te zorgen dat resources in de juiste volgorde worden geïmplementeerd.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f11f79df875492a568a76f494dfffb4a163f64cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153281"
---
# <a name="define-the-order-for-deploying-resources-in-arm-templates"></a>De volgorde definiëren voor het implementeren van resources in ARM-sjablonen

Wanneer u een resource implementeert, moet u er mogelijk voor zorgen dat er andere resources bestaan voordat deze wordt geïmplementeerd. U hebt bijvoorbeeld een SQL-server nodig voordat u een SQL-database implementeert. U definieert deze relatie door één resource te markeren als afhankelijk van de andere resource. U definieert een afhankelijkheid met het element **dependsOn** of met behulp van de **referentiefunctie.**

Resource Manager evalueert de afhankelijkheden tussen resources en implementeert ze in de volgorde van afhankelijkheid. Als resources niet van elkaar afhankelijk zijn, worden deze door Resource Manager parallel geïmplementeerd. U hoeft alleen afhankelijkheden te definiëren voor resources die in dezelfde sjabloon zijn geïmplementeerd.

## <a name="dependson"></a>dependsOn

Met het element dependsOn u met het element dependsOn één resource definiëren als afhankelijk van een of meer resources. De waarde is een door komma's gescheiden lijst met resourcenamen. De lijst kan resources bevatten die voorwaardelijk zijn [geïmplementeerd.](conditional-resource-deployment.md) Wanneer een voorwaardelijke bron niet is geïmplementeerd, verwijdert Azure Resource Manager deze automatisch uit de vereiste afhankelijkheden.

In het volgende voorbeeld wordt een virtuele machineschaalset weergegeven die afhankelijk is van een load balancer, virtueel netwerk en een lus waarmee meerdere opslagaccounts worden gemaakt. Deze andere bronnen worden niet weergegeven in het volgende voorbeeld, maar ze moeten elders in de sjabloon bestaan.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "apiVersion": "2016-03-30",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

In het voorgaande voorbeeld wordt een afhankelijkheid opgenomen van de bronnen die worden gemaakt via een kopieerlus met de naam **storageLoop**. Zie bijvoorbeeld [Meerdere exemplaren van resources maken in Azure Resource Manager](copy-resources.md).

Wanneer u afhankelijkheden definieert, u de naamruimte en het resourcetype van de resourceprovider opnemen om dubbelzinnigheid te voorkomen. Gebruik bijvoorbeeld de volgende indeling om een load balancer en virtueel netwerk met dezelfde namen als andere bronnen te verduidelijken:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
```

Hoewel u misschien geneigd bent om afhankelijk te zijn van het in kaart brengen van relaties tussen uw resources, is het belangrijk om te begrijpen waarom u het doet. Bijvoorbeeld, om te documenteren hoe resources met elkaar verbonden zijn, dependsOn is niet de juiste aanpak. U niet navraag doen in welke resources zijn gedefinieerd in het element dependsOn na implementatie. Door dependsTe te gebruiken, hebt u mogelijk invloed op de implementatietijd omdat Resource Manager niet parallel twee resources implementeert die afhankelijk zijn.

## <a name="child-resources"></a>Onderliggende bronnen

Met de eigenschap Resources u onderliggende resources opgeven die gerelateerd zijn aan de resource die wordt gedefinieerd. Onderliggende bronnen kunnen slechts vijf niveaus diep worden gedefinieerd. Het is belangrijk op te merken dat er geen impliciete implementatieafhankelijkheid wordt gemaakt tussen een onderliggende bron en de bovenliggende bron. Als u de onderliggende resource moet implementeren na de bovenliggende resource, moet u expliciet aangeven dat afhankelijkheid met de eigenschap dependsOn.

Elke bovenliggende resource accepteert alleen bepaalde resourcetypen als onderliggende bronnen. De geaccepteerde resourcetypen worden opgegeven in het [sjabloonschema](https://github.com/Azure/azure-resource-manager-schemas) van de bovenliggende resource. De naam van het onderliggende brontype bevat de naam van het bovenliggende brontype, zoals **Microsoft.Web/sites/config** en **Microsoft.Web/sites/extensies** zijn beide onderliggende bronnen van de **Microsoft.Web/sites**.

In het volgende voorbeeld ziet u een SQL-server en SQL-database. Merk op dat een expliciete afhankelijkheid is gedefinieerd tussen de SQL-database en SQL-server, ook al is de database een onderliggend kind van de server.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "apiVersion": "2014-04-01-preview",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "apiVersion": "2014-04-01-preview",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "tags": {
          "displayName": "Database"
        },
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>referentie- en lijstfuncties

Met [de referentiefunctie](template-functions-resource.md#reference) kan een expressie de waarde ervan ontlenen aan andere JSON-naam- en waardeparen of runtime-resources. De [lijst* functies](template-functions-resource.md#list) retourwaarden voor een resource uit een lijstbewerking.  Referentie- en lijstexpressies verklaren impliciet dat de ene resource afhankelijk is van een andere bron, wanneer de bron waarnaar wordt verwezen in dezelfde sjabloon wordt geïmplementeerd en wordt aangeduid met de naam (niet resource-ID). Als u de resource-id doorgeeft aan de referentie- of lijstfuncties, wordt er geen impliciete verwijzing gemaakt.

Het algemene formaat van de referentiefunctie is:

```json
reference('resourceName').propertyPath
```

De algemene indeling van de functie listKeys is:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

In het volgende voorbeeld is een CDN-eindpunt expliciet afhankelijk van het CDN-profiel en is het impliciet afhankelijk van een web-app.

```json
{
    "name": "[variables('endpointName')]",
    "apiVersion": "2016-04-02",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "dependsOn": [
      "[variables('profileName')]"
    ],
    "properties": {
      "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
      ...
    }
```

U dit element of het element dependsOn gebruiken om afhankelijkheden op te geven, maar u hoeft beide niet voor dezelfde afhankelijke bron te gebruiken. Gebruik waar mogelijk een impliciete verwijzing om te voorkomen dat er een onnodige afhankelijkheid wordt toegevoegd.

Zie [referentiefunctie voor](template-functions-resource.md#reference)meer informatie.

## <a name="circular-dependencies"></a>Kringafhankelijkheden

Resource Manager identificeert cirkelvormige afhankelijkheden tijdens sjabloonvalidatie. Als u een fout ontvangt waarin staat dat er een kringafhankelijkheid bestaat, evalueert u uw sjabloon om te zien of afhankelijkheden niet nodig zijn en kunnen worden verwijderd. Als het verwijderen van afhankelijkheden niet werkt, u kringafhankelijkheden voorkomen door bepaalde implementatiebewerkingen te verplaatsen naar onderliggende resources die worden geïmplementeerd na de resources met de kringafhankelijkheid. Stel dat u twee virtuele machines implementeert, maar dat u eigenschappen moet instellen op elk machines die naar de andere verwijzen. U ze in de volgende volgorde implementeren:

1. vm1
2. vm2
3. Uitbreiding op vm1 is afhankelijk van vm1 en vm2. De extensie stelt waarden in op vm1 die het krijgt van vm2.
4. Uitbreiding op vm2 is afhankelijk van vm1 en vm2. De extensie stelt waarden in op vm2 die het krijgt van vm1.

Zie [Veelvoorkomende Azure-implementatiefouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor informatie over het beoordelen van de implementatieorder en het oplossen van afhankelijkheidsfouten.

## <a name="next-steps"></a>Volgende stappen

* Zie [Zelfstudie: Azure Resource Manager-sjablonen maken met afhankelijke resources](template-tutorial-create-templates-with-dependent-resources.md)voor het doorlopen van een zelfstudie.
* Zie Aanbevolen procedures voor [Azure Resource Manager-sjabloon voor](template-best-practices.md)aanbevelingen bij het instellen van afhankelijkheden.
* Zie [Veelvoorkomende Azure-implementatiefouten oplossen met Azure Resource Manager](common-deployment-errors.md)voor meer informatie over het oplossen van problemen met afhankelijkheden tijdens de implementatie.
* Zie [Sjablonen voor ontwerpen](template-syntax.md)voor meer informatie over het maken van Azure Resource Manager-sjablonen.
* Zie [Sjabloonfuncties](template-functions.md)voor een lijst met beschikbare functies in een sjabloon .

