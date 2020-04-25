---
title: Azure-oplossingen voor vertrouwelijke Computing op virtuele machines
description: Meer informatie over Azure-oplossingen voor vertrouwelijke Computing op virtuele machines.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8db477f87f344b28e8941e185c70c81d9b860a72
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149474"
---
# <a name="solutions-on-azure-virtual-machines"></a>Oplossingen op virtuele machines van Azure

Dit artikel bevat informatie over het implementeren van Azure vertrouwelijk computing virtual machines (Vm's) met Intel-processors die worden ondersteund door [Intel software extension Guard](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>VM-grootten van Azure vertrouwelijk computing

Azure vertrouwelijk computing virtual machines zijn ontworpen om het vertrouwelijk en de integriteit van uw gegevens en code te beschermen tijdens de verwerking in de Cloud 

[DCsv2-serie](../virtual-machines/dcv2-series.md) Vm's zijn de nieuwste en meest recente familie voor vertrouwelijke computing-grootte. Deze Vm's ondersteunen een groter aantal implementatie mogelijkheden, hebben twee maal de enclave-pagina cache (EPC) en een grotere selectie van grootten vergeleken met onze Vm's van de DC-serie. De vm's van de [DC-serie](../virtual-machines/sizes-previous-gen.md#preview-dc-series) zijn momenteel beschikbaar als preview-versie en worden afgeschaft en zijn niet opgenomen in de algemene Beschik baarheid.

Begin met het implementeren van een virtuele machine uit de DCsv2-serie via de micro soft Commercial Marketplace door de [Snelstartgids zelf](quick-create-marketplace.md)te volgen.

### <a name="current-available-sizes-and-regions"></a>Huidige beschik bare grootten en regio's

Voer de volgende opdracht uit in de [Azure-cli](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest)om een lijst op te halen met alle algemeen beschik bare generatie-VM-grootten in de beschik bare regio's en beschikbaarheids zones:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

Vanaf april 2020 zijn deze Sku's beschikbaar in de volgende regio's en beschikbaarheids zones:

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

Voer de volgende opdracht uit voor een gedetailleerde weer gave van de bovenstaande grootten:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Volg een zelf studie over het uitvoeren van een Snelstartgids voor het implementeren van een virtuele machine uit de DCsv2-serie van minder dan 10 minuten. 

- **Azure-abonnement** : als u een exemplaar van een vertrouwelijk computing-VM wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of een andere aanschaf optie gebruiken. Als u een [gratis Azure-account](https://azure.microsoft.com/free/)gebruikt, hebt u geen quota voor de juiste hoeveelheid Azure-reken kernen.

- **Prijzen en regionale Beschik baarheid** : Zoek de prijzen voor vm's uit de DCsv2-serie op de [pagina met prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Controleer of de beschik [bare producten per regio beschikbaar zijn](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) in azure-regio's.


- **Quotum voor kernen** : u moet mogelijk het quotum voor kernen in uw Azure-abonnement verhogen van de standaard waarde. Uw abonnement kan ook het aantal kernen beperken dat u kunt implementeren in bepaalde VM-grootte families, met inbegrip van de DCsv2-serie. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) . Opmerking: de standaard limieten kunnen variëren, afhankelijk van de categorie van uw abonnement.

  > [!NOTE]
  > Neem contact op met de ondersteuning van Azure als er grootschalige capaciteits behoeften zijn. Azure-quota zijn krediet limieten, geen capaciteits garanties. Ongeacht uw quotum worden er alleen kosten in rekening gebracht voor kernen die u gebruikt.
  
- **Grootte wijzigen** : vanwege hun gespecialiseerde hardware kunt u alleen de grootte van vertrouwelijke computing-instanties binnen dezelfde omvang aanpassen. U kunt bijvoorbeeld alleen het formaat van een virtuele machine uit de DCsv2-serie wijzigen van de grootte van een DCsv2-serie naar een andere. Het wijzigen van het formaat van een niet-vertrouwelijke computer grootte naar een vertrouwelijk computer formaat wordt niet ondersteund.  

- **Afbeelding** : om Intel-software Guard-extensie (Intel SGX) ondersteuning te bieden voor vertrouwelijke Compute-instanties, moeten alle implementaties worden uitgevoerd op installatie kopieën van de tweede generatie. Azure vertrouwelijk computing ondersteunt werk belastingen die worden uitgevoerd op Ubuntu 18,04 gen 2, Ubuntu 16,04 gen 2 en Windows Server 2016 gen 2. Meer informatie over [ondersteuning voor virtuele machines van de tweede generatie op Azure](../virtual-machines/linux/generation-2.md) voor meer info over ondersteunde en niet-ondersteunde scenario's. 

- **Opslag** : Azure vertrouwelijk computing gegevens schijven van virtuele machines en onze tijdelijke OS-schijven bevinden zich op NVMe-schijven. Instanties ondersteunen alleen Premium-SSD en Standard-SSD schijven, niet Ultra-SSD of Standard-HDD. De grootte van de virtuele machine **DC8_v2** biedt geen ondersteuning voor Premium Storage. 

- **Schijf versleuteling** : vertrouwelijke reken instanties ondersteunen op dit moment geen schijf versleuteling. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Overwegingen voor hoge Beschik baarheid en herstel na nood gevallen

Wanneer u virtuele machines in azure gebruikt, bent u verantwoordelijk voor het implementeren van een oplossing voor hoge Beschik baarheid en herstel na nood gevallen om uitval tijd te voor komen. 

Azure vertrouwelijk computing ondersteunt momenteel geen zone-redundantie via Beschikbaarheidszones. Gebruik [beschikbaarheids sets](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)voor de hoogst mogelijke Beschik baarheid en redundantie voor vertrouwelijke computing. Vanwege de beperkingen van de hardware kunnen beschikbaarheids sets voor vertrouwelijke computing instanties slechts een maximum van 10 update domeinen hebben. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Implementeren via een Azure Resource Manager sjabloon 

Azure Resource Manager is de implementatie- en beheersservice voor Azure. Er wordt een Management-laag geboden waarmee u resources in uw Azure-abonnement kunt maken, bijwerken en verwijderen. U kunt beheer functies, zoals toegangs beheer, vergren delingen en tags, gebruiken om uw resources na de implementatie te beveiligen en te organiseren.

Zie [Sjabloonimlementatie Overview](../azure-resource-manager/templates/overview.md)voor meer informatie over Azure Resource Manager sjablonen.

Als u een DCsv2-serie wilt implementeren in een ARM-sjabloon, gebruikt u de resource van de [virtuele machine](../virtual-machines/windows/template-description.md). U moet ervoor zorgen dat u de juiste eigenschappen opgeeft voor **vmSize** en voor uw **imageReference**.

### <a name="vm-size"></a>VM-grootte

Geef een van de volgende grootten op in uw ARM-sjabloon in de virtuele-machine bron. Deze teken reeks wordt in **Eigenschappen**als **vmSize** geplaatst.

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Installatie kopie van Gen2-besturings systeem

Onder **Eigenschappen**moet u ook verwijzen naar een installatie kopie onder **storageProfile**. Gebruik *slechts één* van de volgende installatie kopieën voor uw **imageReference**.

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Volgende stappen 

In dit artikel hebt u geleerd over de kwalificaties en configuraties die nodig zijn bij het maken van een virtuele machine met vertrouwelijke computing. U kunt nu de Azure Marketplace gebruiken om een virtuele machine uit de DCsv2-serie te implementeren.

[!div class=”nextstepaction”]
[Een virtuele machine uit de DCsv2-serie implementeren in azure Marketplace](quick-create-marketplace.md)