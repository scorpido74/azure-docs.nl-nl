---
title: Gebeurtenis azure batchpool maken
description: Referentie voor de gebeurtenis Batchpool maken, die wordt uitgestoten zodra een groep is gemaakt. De inhoud van het logboek zal algemene informatie over het zwembad onthullen.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: dea025b274278aa5fed2900c95b4a274541ffef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022186"
---
# <a name="pool-create-event"></a>Gebeurtenis pool maken

 Deze gebeurtenis wordt uitgezonden zodra een pool is gemaakt. De inhoud van het logboek zal algemene informatie over het zwembad onthullen. Houd er rekening mee dat als de doelgrootte van de groep groter is dan 0 rekenknooppunten, een begingebeurtenis voor het wijzigen van de grootte van een groep onmiddellijk na deze gebeurtenis volgt.

 In het volgende voorbeeld ziet u de hoofdtekst `CloudServiceConfiguration` van een poolgebeurtenis maken voor een pool die met de eigenschap is gemaakt.

```
{
    "id": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Standard_F1s",
    "imageType": "VirtualMachineConfiguration",
    "cloudServiceConfiguration": {
        "osFamily": "3",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "virtualMachineConfiguration": {
          "imageReference": {
            "publisher": " ",
            "offer": " ",
            "sku": " ",
            "version": " "
          },
          "nodeAgentId": " "
        },
    "resizeTimeout": "300000",
    "targetDedicatedNodes": 2,
    "targetLowPriorityNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoScale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

|Element|Type|Opmerkingen|
|-------------|----------|-----------|
|`id`|Tekenreeks|De ID van het zwembad.|
|`displayName`|Tekenreeks|De weergavenaam van het zwembad.|
|`vmSize`|Tekenreeks|De grootte van de virtuele machines in het zwembad. Alle virtuele machines in een zwembad zijn even groot. <br/><br/> Zie [Grootte voor Cloud Services voor](https://azure.microsoft.com/documentation/articles/cloud-services-sizes-specs/)informatie over de beschikbare grootte van virtuele machines voor groepen voor Cloud Services (pools die zijn gemaakt met cloudServiceConfiguration. Batch ondersteunt alle VM-formaten van Cloud Services, behalve `ExtraSmall`.<br/><br/> Zie [Groottes voor virtuele machines](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-sizes/) (Linux) of [Groottes voor virtuele machines](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) (Windows) voor informatie over beschikbare VM-formaten voor pools met afbeeldingen van de Marketplace voor virtuele machines (pools die zijn gemaakt met virtualMachineConfiguration). Batch ondersteunt alle Azure VM-groottes met uitzondering van `STANDARD_A0` en die met Premium Storage (de serie `STANDARD_GS`, `STANDARD_DS` en `STANDARD_DSV2`).|
|`imageType`|Tekenreeks|De implementatiemethode voor de afbeelding. Ondersteunde waarden `virtualMachineConfiguration` zijn of zijn of`cloudServiceConfiguration`|
|[`cloudServiceConfiguration`](#bk_csconf)|Complex Type|De configuratie van de cloudservice voor de groep.|
|[`virtualMachineConfiguration`](#bk_vmconf)|Complex Type|De virtuele machineconfiguratie voor het zwembad.|
|[`networkConfiguration`](#bk_netconf)|Complex Type|De netwerkconfiguratie voor de pool.|
|`resizeTimeout`|Time|De time-out voor de toewijzing van compute nodes aan de groep die is opgegeven voor de laatste groottebewerking op de groep.  (De eerste grootte wanneer de groep wordt gemaakt, telt als een formaat.)|
|`targetDedicatedNodes`|Int32|Het aantal speciale compute nodes dat wordt aangevraagd voor de groep.|
|`targetLowPriorityNodes`|Int32|Het aantal computenodes met lage prioriteit dat wordt aangevraagd voor de groep.|
|`enableAutoScale`|Booleaanse waarde|Hiermee geeft u op of de grootte van de groep zich na verloop van tijd automatisch aanpast.|
|`enableInterNodeCommunication`|Booleaanse waarde|Hiermee geeft u op of de groep is ingesteld voor directe communicatie tussen knooppunten.|
|`isAutoPool`|Booleaanse waarde|Hiermee geeft u op of de groep is gemaakt via het AutoPool-mechanisme van een taak.|
|`maxTasksPerNode`|Int32|Het maximum aantal taken dat gelijktijdig kan worden uitgevoerd op één compute-knooppunt in de groep.|
|`vmFillType`|Tekenreeks|Hiermee definieert u hoe de batchservice taken verdeelt tussen rekenknooppunten in de groep. Geldige waarden zijn Spread of Pack.|

###  <a name="cloudserviceconfiguration"></a><a name="bk_csconf"></a>cloudServiceConfiguratie

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`osFamily`|Tekenreeks|De Azure Guest OS-familie die moet worden geïnstalleerd op de virtuele machines in het zwembad.<br /><br /> Mogelijke waarden zijn:<br /><br /> **2** – OS Family 2, gelijk aan Windows Server 2008 R2 SP1.<br /><br /> **3** – OS Family 3, gelijk aan Windows Server 2012.<br /><br /> **4** – OS Family 4, gelijk aan Windows Server 2012 R2.<br /><br /> Zie [Azure Guest OS Releases](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)voor meer informatie.|
|`targetOSVersion`|Tekenreeks|De Azure Guest OS-versie die moet worden geïnstalleerd op de virtuele machines in de groep.<br /><br /> De standaardwaarde **\*** is die de nieuwste versie van het besturingssysteem voor de opgegeven familie opgeeft.<br /><br /> Zie [Azure Guest OS Releases](https://azure.microsoft.com/documentation/articles/cloud-services-guestos-update-matrix/#releases)voor andere toegestane waarden.|

###  <a name="virtualmachineconfiguration"></a><a name="bk_vmconf"></a>virtualMachineConfiguration virtualMachineConfiguration

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|[`imageReference`](#bk_imgref)|Complex Type|Hiermee geeft u informatie op over het platform of de Marketplace-afbeelding die u wilt gebruiken.|
|`nodeAgentId`|Tekenreeks|De SKU van de batchknooppuntagent die is ingericht op het compute-knooppunt.|
|[`windowsConfiguration`](#bk_winconf)|Complex Type|Hiermee geeft u de instellingen van het Windows-besturingssysteem op de virtuele machine op. Deze eigenschap mag niet worden opgegeven als de imageReference verwijst naar een Linux-osafbeelding.|

###  <a name="imagereference"></a><a name="bk_imgref"></a>imageReference

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`publisher`|Tekenreeks|De uitgever van de afbeelding.|
|`offer`|Tekenreeks|Het aanbod van de afbeelding.|
|`sku`|Tekenreeks|De SKU van de afbeelding.|
|`version`|Tekenreeks|De versie van de afbeelding.|

###  <a name="windowsconfiguration"></a><a name="bk_winconf"></a>windowsConfiguratie

|Elementnaam|Type|Opmerkingen|
|------------------|----------|-----------|
|`enableAutomaticUpdates`|Booleaans|Geeft aan of de virtuele machine is ingeschakeld voor automatische updates. Als deze eigenschap niet is opgegeven, is de standaardwaarde waar.|

###  <a name="networkconfiguration"></a><a name="bk_netconf"></a>netwerkConfiguratie

|Elementnaam|Type|Opmerkingen|
|------------------|--------------|----------|
|`subnetId`|Tekenreeks|Hiermee geeft u de resource-id op van het subnet waarin de compute nodes van de groep worden gemaakt.|
