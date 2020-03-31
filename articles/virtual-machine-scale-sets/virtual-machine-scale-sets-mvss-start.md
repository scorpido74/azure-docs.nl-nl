---
title: Meer informatie over sjablonen voor het schalen van virtuele machines
description: Meer informatie over het maken van een sjabloon voor basisschaalsets voor virtuele machines in Azure via verschillende eenvoudige stappen.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 24db9b2d39771c481a8c43e2b55f12cef381b4d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76271893"
---
# <a name="learn-about-virtual-machine-scale-set-templates"></a>Meer informatie over sjablonen voor het schalen van virtuele machines
[Azure Resource Manager-sjablonen](https://docs.microsoft.com/azure/azure-resource-manager/template-deployment-overview#template-deployment-process) zijn bijzonder handig om groepen gerelateerde resources te implementeren. In deze zelfstudiereeks ziet u hoe u een sjabloon voor basisschaalsets maakt en hoe u deze sjabloon aanpassen aan verschillende scenario's. Alle voorbeelden komen uit deze [GitHub repository.](https://github.com/gatneil/mvss)

Deze sjabloon is bedoeld om eenvoudig te zijn. Zie de [GitHub-opslagplaats Azure Quickstart Templates](https://github.com/Azure/azure-quickstart-templates) en zoek naar mappen die de tekenreeks `vmss`bevatten voor meer volledige voorbeelden van sjablonen voor schaalsets.

Als u al bekend bent met het maken van sjablonen, u naar de sectie Volgende stappen gaan om te zien hoe u deze sjabloon wijzigen.

## <a name="define-schema-and-contentversion"></a>$schema en contentVersie definiëren
Definieer en `$schema` `contentVersion` in de sjabloon. Het `$schema` element definieert de versie van de sjabloontaal en wordt gebruikt voor visual studio-syntaxismarkering en vergelijkbare validatiefuncties. Het `contentVersion` element wordt niet gebruikt door Azure. In plaats daarvan u de sjabloonversie bijhouden.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
```

## <a name="define-parameters"></a>Parameters definiëren
Definieer vervolgens twee `adminUsername` parameters `adminPassword`en . Parameters zijn waarden die u opgeeft op het moment van implementatie. De `adminUsername` parameter is `string` gewoon een `adminPassword` type, maar omdat `securestring`is een geheim, geef het type . Later worden deze parameters doorgegeven in de configuratie van de schaalset.

```json
  "parameters": {
    "adminUsername": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    }
  },
```
## <a name="define-variables"></a>Variabelen definiëren
Met resourcebeheersjablonen u ook variabelen definiëren die later in de sjabloon moeten worden gebruikt. In het voorbeeld worden geen variabelen gebruikt, dus het JSON-object is leeg.

```json
  "variables": {},
```

## <a name="define-resources"></a>Resources definiëren
Vervolgens is het gedeelte resources van de sjabloon. Hier bepaalt u wat u daadwerkelijk wilt implementeren. In `parameters` `variables` tegenstelling tot en (die JSON objecten), `resources` is een JSON lijst van JSON objecten.

```json
   "resources": [
```

Alle resources `type` `name`vereisen `apiVersion`, `location` , en eigenschappen. De eerste bron van dit voorbeeld heeft het `myVnet`type [Microsoft.Network/virtualNetwork,](/azure/templates/microsoft.network/virtualnetworks)name en apiVersion `2018-11-01`. (Zie de [sjabloonverwijzing Azure Resource Manager](/azure/templates/)als u de nieuwste API-versie voor een resourcetype wilt vinden .)

```json
     {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "apiVersion": "2018-11-01",
```

## <a name="specify-location"></a>Locatie opgeven
Als u de locatie voor het virtuele netwerk wilt opgeven, gebruikt u een [sjabloonfunctie Resourcebeheer](../azure-resource-manager/templates/template-functions.md). Deze functie moet worden ingesloten tussen aanhalingstekens en vierkante haakjes als deze: `"[<template-function>]"`. Gebruik in dit `resourceGroup` geval de functie. Er zijn geen argumenten nodig en retourneert een JSON-object met metagegevens over de brongroep waarin deze implementatie wordt geïmplementeerd. De resourcegroep wordt ingesteld door de gebruiker op het moment van implementatie. Deze waarde wordt vervolgens geïndexeerd in `.location` dit JSON-object om de locatie van het JSON-object te halen.

```json
       "location": "[resourceGroup().location]",
```

## <a name="specify-virtual-network-properties"></a>Virtuele netwerkeigenschappen opgeven
Elke Resource Manager-bron `properties` heeft een eigen sectie voor configuraties die specifiek zijn voor de resource. Geef in dit geval op dat het virtuele netwerk één `10.0.0.0/16`subnet moet hebben met behulp van het privé-IP-adresbereik . Een schaalset is altijd opgenomen in één subnet. Het kan geen subnetten omvatten.

```json
       "properties": {
         "addressSpace": {
           "addressPrefixes": [
             "10.0.0.0/16"
           ]
         },
         "subnets": [
           {
             "name": "mySubnet",
             "properties": {
               "addressPrefix": "10.0.0.0/16"
             }
           }
         ]
       }
     },
```

## <a name="add-dependson-list"></a>Afhankelijk toevoegenOp de lijst
Naast de vereiste `type` `name`, `apiVersion`, `location` , en eigenschappen, `dependsOn` kan elke resource een optionele lijst met tekenreeksen hebben. In deze lijst wordt aangegeven welke andere resources uit deze implementatie moeten worden voltooid voordat deze bron wordt geïmplementeerd.

In dit geval is er slechts één element in de lijst, het virtuele netwerk uit het vorige voorbeeld. U geeft deze afhankelijkheid op omdat de schaalset het netwerk nodig heeft voordat u VM's maakt. Op deze manier kan de schaalset deze VM's privé-IP-adressen geven uit het IP-adresbereik dat eerder in de netwerkeigenschappen is opgegeven. De indeling van elke tekenreeks in `<type>/<name>`de lijst dependsOn is . Gebruik `type` hetzelfde `name` en eerder gebruikt in de definitie van virtuele netwerkbronnen.

```json
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "apiVersion": "2019-03-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
         "Microsoft.Network/virtualNetworks/myVnet"
       ],
```
## <a name="specify-scale-set-properties"></a>Eigenschappen voor schaalset opgeven
Schaalsets hebben veel eigenschappen voor het aanpassen van de VM's in de schaalset. Zie de [sjabloonverwijzing](/azure/templates/microsoft.compute/virtualmachinescalesets)voor een volledige lijst met deze eigenschappen . Voor deze zelfstudie worden slechts een paar veelgebruikte eigenschappen ingesteld.
### <a name="supply-vm-size-and-capacity"></a>VM-grootte en -capaciteit leveren
De schaalset moet weten hoe groot VM moet zijn om te maken ('sku-naam') en hoeveel dergelijke VM's er moeten worden gemaakt ('sku-capaciteit'). Zie de [documentatie VM-formaten](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-sizes)om te zien welke VM-formaten beschikbaar zijn.

```json
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
       },
```

### <a name="choose-type-of-updates"></a>Type updates kiezen
De schaalset moet ook weten hoe u updates op de schaalset moet verwerken. Momenteel zijn er drie `Manual` `Rolling` opties, en `Automatic`. Zie de documentatie over het upgraden van [een schaalset](./virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)voor meer informatie over de verschillen tussen de twee.

```json
       "properties": {
         "upgradePolicy": {
           "mode": "Manual"
         },
```

### <a name="choose-vm-operating-system"></a>VM-besturingssysteem kiezen
De schaalset moet weten welk besturingssysteem u op de VM's moet zetten. Maak hier de VM's met een volledig gepatchte Ubuntu 16.04-LTS-afbeelding.

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
               "publisher": "Canonical",
               "offer": "UbuntuServer",
               "sku": "16.04-LTS",
               "version": "latest"
             }
           },
```

### <a name="specify-computernameprefix"></a>ComputerNamePrefix opgeven
De schaalset implementeert meerdere VM's. Geef in plaats van elke `computerNamePrefix`VM-naam op te geven . De schaalset voegt een index toe aan het voorvoegsel `<computerNamePrefix>_<auto-generated-index>`voor elke virtuele machine, zodat VM-namen het formulier hebben.

Gebruik in het volgende fragment de parameters van vroeger om de gebruikersnaam en het wachtwoord van de beheerder in te stellen voor alle VM's in de schaalset. Dit proces `parameters` maakt gebruik van de sjabloonfunctie. Deze functie neemt een tekenreeks op die aangeeft naar welke parameter u moet verwijzen en de waarde voor die parameter moet worden uitgevoerd.

```json
           "osProfile": {
             "computerNamePrefix": "vm",
             "adminUsername": "[parameters('adminUsername')]",
             "adminPassword": "[parameters('adminPassword')]"
           },
```

### <a name="specify-vm-network-configuration"></a>Vm-netwerkconfiguratie opgeven
Geef ten slotte de netwerkconfiguratie voor de VM's op in de schaalset. In dit geval hoeft u alleen de id van het eerder gemaakte subnet op te geven. Dit vertelt de schaal ingesteld op de netwerkinterfaces in dit subnet te zetten.

U de id van het virtuele netwerk met `resourceId` het subnet krijgen met behulp van de sjabloonfunctie. Deze functie neemt het type en de naam van een resource in beslag en retourneert de volledig gekwalificeerde id van die resource. Deze id heeft het formulier:`/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/<resourceProviderNamespace>/<resourceType>/<resourceName>`

De id van het virtuele netwerk is echter niet voldoende. Geef het specifieke subnet op waarin de vm's van de schaalset moeten zijn. Om dit te doen, `/subnets/mySubnet` te concatenate aan de ID van het virtuele netwerk. Het resultaat is de volledig gekwalificeerde ID van het subnet. Doe dit samenvoeging `concat` met de functie, die neemt in een reeks van snaren en keert hun samenvoeging.

```json
           "networkProfile": {
             "networkInterfaceConfigurations": [
               {
                 "name": "myNic",
                 "properties": {
                   "primary": "true",
                   "ipConfigurations": [
                     {
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
                           "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
                         }
                       }
                     }
                   ]
                 }
               }
             ]
           }
         }
       }
     }
   ]
}

```

## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
