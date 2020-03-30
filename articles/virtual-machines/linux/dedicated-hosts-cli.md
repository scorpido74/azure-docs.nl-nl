---
title: Linux VM's implementeren voor speciale hosts met behulp van de CLI
description: Implementeer VM's voor speciale hosts met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: ba40e610e31a1215ac90baf63a04b435b636d68a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127696"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-cli"></a>VM's implementeren voor speciale hosts met de Azure CLI
 

In dit artikel u een [azure-dedicated host](dedicated-hosts.md) maken om uw virtuele machines (VM's) te hosten. 

Controleer of u Azure CLI-versie 2.0.70 of hoger hebt geïnstalleerd `az login`en u hebt aangemeld bij een Azure-account met behulp van . 


## <a name="limitations"></a>Beperkingen

- Virtuele machineschaalsets worden momenteel niet ondersteund op speciale hosts.
- De grootte en hardwaretypen die beschikbaar zijn voor toegewijde hosts verschillen per regio. Raadpleeg de [pagina hostprijzen](https://aka.ms/ADHPricing) voor meer informatie.
 

## <a name="create-resource-group"></a>Een resourcegroep maken 
Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak de resourcegroep met de az-groep maken. In het volgende voorbeeld wordt een resourcegroep met de naam *myDHResourceGroup* in de *locatie Oost-VS.*

```bash
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="create-a-host-group"></a>Een hostgroep maken 

Een **hostgroep** is een bron die een verzameling toegewijde hosts vertegenwoordigt. U maakt een hostgroep in een regio en een beschikbaarheidszone en voegt er hosts aan toe. Bij het plannen voor hoge beschikbaarheid zijn er extra opties. U een of beide van de volgende opties gebruiken met uw speciale hosts: 
- Overspannen in meerdere beschikbaarheidszones. In dit geval moet u een hostgroep hebben in elk van de zones die u wilt gebruiken.
- Overspannen meerdere foutdomeinen die zijn toegewezen aan fysieke racks. 
 
In beide gevallen moet u het aantal foutdomeinen voor uw hostgroep verstrekken. Als u foutdomeinen in uw groep niet wilt overspannen, gebruikt u een aantal foutdomeinen van 1. 

U ook besluiten om zowel beschikbaarheidszones als foutdomeinen te gebruiken. 

In dit voorbeeld gebruiken we [de az vm-hostgroep om](/cli/azure/vm/host/group#az-vm-host-group-create) een hostgroep te maken met zowel beschikbaarheidszones als foutdomeinen. 

```bash
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

### <a name="other-examples"></a>Andere voorbeelden

U ook [de AZ VM-hostgroep maken](/cli/azure/vm/host/group#az-vm-host-group-create) gebruiken om een hostgroep te maken in beschikbaarheidszone 1 (en foutdomeinen).

```bash
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Het volgende maakt gebruik van [az vm host group maken](/cli/azure/vm/host/group#az-vm-host-group-create) om een hostgroep te maken met behulp van fout domeinen alleen (te gebruiken in regio's waar beschikbaarheid zones niet worden ondersteund). 

```bash
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Een host maken 

Laten we nu een speciale host maken in de hostgroep. Naast een naam voor de host, bent u verplicht om de SKU voor de host te verstrekken. Host SKU legt de ondersteunde VM-serie vast, evenals de hardwaregeneratie voor uw toegewijde host.  

Zie [Azure Dedicated Host-prijzen](https://aka.ms/ADHPricing)voor meer informatie over de host-SKU's en -prijzen.

Gebruik [az vm host create](/cli/azure/vm/host#az-vm-host-create) om een host te maken. Als u een aantal foutdomeinen instelt voor uw hostgroep, wordt u gevraagd het foutdomein voor uw host op te geven.  

```bash
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Een virtuele machine maken 
Maak een virtuele machine binnen een dedicated host met behulp van [az vm maken](/cli/azure/vm#az-vm-create). Als u een beschikbaarheidszone hebt opgegeven bij het maken van uw hostgroep, moet u dezelfde zone gebruiken bij het maken van de virtuele machine.

```bash
az vm create \
   -n myVM \
   --image debian \
   --generate-ssh-keys \
   --host-group myHostGroup \
   --host myHost \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```
 
> [!WARNING]
> Als u een virtuele machine maakt op een host die niet genoeg resources heeft, wordt de virtuele machine in een mislukte status gemaakt. 


## <a name="check-the-status-of-the-host"></a>Controleer de status van de host

U de status van de hoststatus controleren en hoeveel virtuele machines u nog steeds aan de host implementeren met behulp van [de AZ VM-host get-instance-view.](/cli/azure/vm/host#az-vm-host-get-instance-view)

```bash
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 De output zal er hetzelfde uitzien als dit:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exporteren als sjabloon 
U een sjabloon exporteren als u nu een extra ontwikkelomgeving wilt maken met dezelfde parameters of een productieomgeving die overeenkomt met deze. Resource Manager gebruikt JSON-sjablonen die alle parameters voor uw omgeving definiëren. U bouwt hele omgevingen uit door naar deze JSON-sjabloon te verwijzen. U JSON-sjablonen handmatig bouwen of een bestaande omgeving exporteren om de JSON-sjabloon voor u te maken. Gebruik [de export van az-groepen](/cli/azure/group#az-group-export) om uw resourcegroep te exporteren.

```bash
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Met deze `myDHResourceGroup.json` opdracht wordt het bestand gemaakt in uw huidige werkmap. Wanneer u een omgeving maakt op basis van deze sjabloon, wordt u gevraagd om alle resourcenamen. U deze namen in uw `--include-parameter-default-value` sjabloonbestand `az group export` invullen door de parameter aan de opdracht toe te voegen. Bewerk uw JSON-sjabloon om de resourcenamen op te geven of maak een parameters.json-bestand dat de bronnamen opgeeft.
 
Als u een omgeving wilt maken op basis van uw sjabloon, gebruikt u [de implementatie van AZ-groepen.](/cli/azure/group/deployment#az-group-deployment-create)

```bash
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Opruimen 

Er worden kosten in rekening gebracht voor uw speciale hosts, zelfs als er geen virtuele machines worden geïmplementeerd. U moet alle hosts verwijderen die u momenteel niet gebruikt om kosten te besparen.  

U een host alleen verwijderen als er geen virtuele machines meer worden gebruikt. Verwijder de VM's met [behulp van az vm delete](/cli/azure/vm#az-vm-delete).

```bash
az vm delete -n myVM -g myDHResourceGroup
```

Nadat u de VM's hebt verwijderd, u de host verwijderen met behulp van [het verwijderen van az vm host.](/cli/azure/vm/host#az-vm-host-delete)

```bash
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Zodra je al je hosts hebt verwijderd, kun je de hostgroep verwijderen met behulp van [de AZ VM Host Group delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```bash
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
U ook de hele brongroep in één opdracht verwijderen. Hiermee worden alle bronnen die in de groep zijn gemaakt, inclusief alle VM's, hosts en hostgroepen, verwijderd.
 
```bash
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Volgende stappen

- Zie het overzicht [van speciale hosts](dedicated-hosts.md) voor meer informatie.

- U ook speciale hosts maken met behulp van de [Azure-portal.](dedicated-hosts-portal.md)

- Er is voorbeeld sjabloon, [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)gevonden, dat zowel zones en fout domeinen gebruikt voor maximale tolerantie in een regio.