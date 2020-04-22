---
title: Aangepaste gegevens en Azure Virtual Machines
description: Details over het gebruik van aangepaste gegevens en Cloud-Init op Azure Virtual Machines
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: article
ms.date: 03/06/2020
ms.author: mimckitt
ms.openlocfilehash: 9497e665d024b583c261ade3e6fb5393a9322ce0
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759134"
---
# <a name="custom-data-and-cloud-init-on-azure-virtual-machines"></a>Aangepaste gegevens en Cloud-Init op Azure Virtual Machines

## <a name="what-is-custom-data"></a>Wat zijn aangepaste gegevens?

Klanten vragen vaak hoe ze een script of andere metagegevens kunnen injecteren in een virtuele microsoft-machine van Microsoft Azure tijdens het inrichten.  In andere clouds wordt dit concept vaak aangeduid als gebruikersgegevens.  In Microsoft Azure hebben we een vergelijkbare functie genaamd aangepaste gegevens. 

Aangepaste gegevens worden alleen beschikbaar gesteld aan de VM tijdens de eerste boot/initiële installatie, we noemen dit 'provisioning'. Provisioning is het proces waarbij VM Create parameters (bijvoorbeeld hostname, gebruikersnaam, wachtwoord, certificaten, aangepaste gegevens, sleutels etc.) beschikbaar worden gesteld aan de VM en een inrichtingsagent deze verwerkt, zoals de [Linux Agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) en [cloud-init.](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init) 


## <a name="passing-custom-data-to-the-vm"></a>Aangepaste gegevens doorgeven aan de VM
Als u aangepaste gegevens wilt gebruiken, moet u de inhoud eerst coderen voordat u deze doorgeeft aan de API, tenzij u een CLI-tool gebruikt die de conversie voor u doet, zoals AZ CLI. De grootte mag niet groter zijn dan 64 KB.

In CLI u uw aangepaste gegevens doorgeven als een bestand en wordt deze omgezet naar base64.
```bash
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys
```

In Azure Resource Manager (ARM) is er een [base64-functie](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-functions-string#base64).

```json
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2019-07-01",
"location": "[parameters('location')]",
"dependsOn": [
..],
"variables": {
        "customDataBase64": "[base64(parameters('stringData'))]"
    },
"properties": {
..
    "osProfile": {
        "computerName": "[parameters('virtualMachineName')]",
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "customData": "[variables('customDataBase64')]"
        },
```

## <a name="processing-custom-data"></a>Aangepaste gegevens verwerken
De inprovisioning agenten geïnstalleerd op de VM's omgaan interfacing met het platform en het plaatsen van het op het bestandssysteem. 

### <a name="windows"></a>Windows
Aangepaste gegevens worden in *%SYSTEMDRIVE%\AzureData\CustomData.bin* als een binair bestand geplaatst, maar deze worden niet verwerkt. Als u dit bestand wilt verwerken, moet u een aangepaste afbeelding maken en code schrijven om de CustomData.bin te verwerken.

### <a name="linux"></a>Linux  
Op Linux OS's worden aangepaste gegevens doorgegeven aan de VM via het ovf-env.xml-bestand, dat tijdens de inrichting wordt gekopieerd naar de */var/lib/waagent* directory.  Nieuwere versies van de Microsoft Azure Linux Agent kopiëren ook de base64-gecodeerde gegevens naar */var/lib/waagent/CustomData* en voor het gemak.

Azure ondersteunt momenteel twee inrichtingsagents:
* Linux Agent - Standaard verwerkt de agent geen aangepaste gegevens, u moet een aangepaste afbeelding bouwen met ingeschakeld. De relevante instellingen, volgens de [documentatie](https://github.com/Azure/WALinuxAgent#configuration) zijn:
    * Provisioning.DecodeCustomData
    * Provisioning.ExecuteCustomData

Wanneer u aangepaste gegevens inschakelt en een script uitvoert, wordt de VM-rapportage die klaar is, uitgesteld of is de inrichting geslaagd totdat het script is voltooid. Als het script de totale VM-insteltijdvan 40 minuten overschrijdt, mislukt de VM Create. Als het script niet wordt uitgevoerd of fouten tijdens het uitvoeren, wordt het niet beschouwd als een fatale inrichtingsfout, moet u een meldingspad maken om u te waarschuwen voor de voltooiingsstatus van het script.

Als u de uitvoering van aangepaste gegevens wilt oplossen, controleert u */var/log/waagent.log*

* cloud-init - Standaard verwerkt u standaard aangepaste gegevens, cloud-init accepteert [meerdere indelingen](https://cloudinit.readthedocs.io/en/latest/topics/format.html) van aangepaste gegevens, zoals cloud-init-configuratie, scripts enz. Vergelijkbaar met de Linux Agent, wanneer cloud-init de aangepaste gegevens verwerkt. Als er fouten zijn tijdens de uitvoering van de configuratieverwerking of scripts, wordt dit niet beschouwd als een fatale inrichtingsfout en moet u een meldingspad maken om u te waarschuwen voor de voltooiingsstatus van het script. Echter, anders dan de Linux Agent, cloud-init wacht niet op de gebruiker aangepaste gegevens configuraties te voltooien voordat rapportage aan het platform dat de VM klaar is. Voor meer informatie over cloud-init op azure, bekijk de [documentatie](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init).


Als u de uitvoering van aangepaste gegevens wilt oplossen, controleert u de [documentatie](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init#troubleshooting-cloud-init)voor probleemoplossing .


## <a name="faq"></a>Veelgestelde vragen
### <a name="can-i-update-custom-data-after-the-vm-has-been-created"></a>Kan ik aangepaste gegevens bijwerken nadat de vm is gemaakt?
Voor afzonderlijke VM's kunnen aangepaste gegevens in het VM-model niet worden bijgewerkt, maar voor VMSS u vmss-aangepaste gegevens bijwerken via REST API (niet van toepassing op PS- of AZ CLI-clients). Wanneer u aangepaste gegevens in het VMSS-model bijwerkt:
* Bestaande exemplaren in het VMSS krijgen de bijgewerkte aangepaste gegevens pas als ze opnieuw worden weergegeven.
* Bestaande exemplaren in het VMSS die worden bijgewerkt, krijgen de bijgewerkte aangepaste gegevens niet.
* Nieuwe exemplaren ontvangen de nieuwe aangepaste gegevens.

### <a name="can-i-place-sensitive-values-in-custom-data"></a>Kan ik gevoelige waarden plaatsen in aangepaste gegevens?
Wij adviseren om gevoelige gegevens **niet** op te slaan in aangepaste gegevens. Zie aanbevolen [procedures voor Azure Security en versleuteling](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices)voor meer informatie.


### <a name="is-custom-data-made-available-in-imds"></a>Worden aangepaste gegevens beschikbaar gesteld in IMDS?
Nee, deze functie is momenteel niet beschikbaar.
