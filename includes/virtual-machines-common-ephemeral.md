---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/08/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: d848b92da5d4181832adff8499b3531d020c30c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78155463"
---
Tijdelijke OS-schijven worden gemaakt op de vm-opslag (local virtual machine) en worden niet opgeslagen in de externe Azure-opslag. Kortstondige OS-schijven werken goed voor stateless workloads, waar toepassingen tolerant zijn ten opzichte van afzonderlijke VM-fouten, maar meer worden beïnvloed door vm-implementatietijd of het opnieuw weergeven van de afzonderlijke VM-exemplaren. Met de epfeosschijf krijgt u een lagere lees-/schrijflatentie op de OS-schijf en een snellere VM-reimage. 
 
De belangrijkste kenmerken van kortstondige schijven zijn: 
- Ideaal voor stateloze toepassingen.
- Ze kunnen worden gebruikt met zowel Marketplace als aangepaste afbeeldingen.
- Mogelijkheid om VM's snel te resetten of opnieuw te afbeelding en instellen van instanties op de oorspronkelijke opstartstatus.  
- Lagere latentie, vergelijkbaar met een tijdelijke schijf. 
- Kortstondige OS-schijven zijn gratis, u hoeft geen opslagkosten te maken voor de schijf van het besturingssysteem.
- Ze zijn beschikbaar in alle Azure-regio's. 
- Efemere OS Disk wordt ondersteund door [Shared Image Gallery](/azure/virtual-machines/linux/shared-image-galleries). 
 

 
Belangrijkste verschillen tussen permanente en kortstondige OS-schijven:

|                             | Permanente schijf van besturingssysteem                          | Kortstondige besturingssysteemschijf                              |    |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| Groottelimiet voor osschijf      | 2 TiB                                                                                        | Cachegrootte voor de VM-grootte of 2TiB, welke kleiner is. Voor de **cachegrootte in GiB,** zie [DS,](../articles/virtual-machines/linux/sizes-general.md) [ES,](../articles/virtual-machines/linux/sizes-memory.md) [M,](../articles/virtual-machines/linux/sizes-memory.md) [FS](../articles/virtual-machines/linux/sizes-compute.md)en [GS](/azure/virtual-machines/linux/sizes-previous-gen#gs-series)              |
| Ondersteunde VM-formaten          | Alle                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M                                               |
| Ondersteuning voor schijftype           | Beheerde en onbeheerde osschijf                                                                | Alleen beheerde osschijf                                                               |
| Ondersteuning voor regio's              | Alle regio's                                                                                  | Alle regio's                              |
| Gegevenspersistentie            | OS-schijfgegevens die naar de OS-schijf zijn geschreven, worden opgeslagen in Azure Storage                                  | Gegevens die naar de OS-schijf zijn geschreven, worden opgeslagen in de lokale VM-opslag en worden niet gehandhaafd in Azure Storage. |
| Stop-dealtoegewezen status      | VM's en schaalset-exemplaren kunnen worden gestopt en opnieuw worden gestart vanuit de stop-deallocatie | VM's en schaalset-exemplaren kunnen niet worden gestopt                                  |
| Ondersteuning voor gespecialiseerde osschijf | Ja                                                                                          | Nee                                                                                 |
| Het formaat van de OS-schijf wijzigen              | Ondersteund tijdens het maken van vm's en nadat VM is gestopt                                | Alleen ondersteund tijdens het maken van vm's                                                  |
| Het formaat wijzigen van een nieuwe virtuele machine   | OS-schijfgegevens worden bewaard                                                                    | Gegevens op de OS-schijf worden verwijderd, os wordt opnieuw ingericht                                      |

## <a name="size-requirements"></a>Maateisen

U VM- en instantieafbeeldingen implementeren tot de grootte van de VM-cache. Standaard Windows Server-afbeeldingen van de marktplaats zijn bijvoorbeeld ongeveer 127 GiB, wat betekent dat u een VM-grootte nodig hebt met een cache groter dan 127 GiB. In dit geval heeft de [Standard_DS2_v2](~/articles/virtual-machines/dv2-dsv2-series.md) een cachegrootte van 86 GiB, wat niet groot genoeg is. De Standard_DS3_v2 heeft een cachegrootte van 172 GiB, wat groot genoeg is. In dit geval is de Standard_DS3_v2 de kleinste maat in de DSv2-serie die u met deze afbeelding gebruiken. Basis-Linux-afbeeldingen in de Marketplace- en `[smallsize]` Windows Server-afbeeldingen die worden aangeduid met de neiging om rond de 30 GiB te zijn en kunnen de meeste beschikbare VM-formaten gebruiken.

Kortstondige schijven vereisen ook dat de VM-grootte Premium-opslag ondersteunt. De maten hebben meestal (maar `s` niet altijd) een in de naam, zoals DSv2 en EsV3. Zie [Azure VM-formaten](../articles/virtual-machines/linux/sizes.md) voor meer informatie over welke formaten Premium-opslag ondersteunen voor meer informatie.

## <a name="powershell"></a>PowerShell

Als u een tijdelijke schijf wilt gebruiken voor een PowerShell VM-implementatie, gebruikt u [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk) in uw VM-configuratie. Stel `-DiffDiskSetting` de `Local` `-Caching` aan `ReadOnly`en aan .     

```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```

Voor schaalsetimplementaties gebruikt u de cmdlet [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) in uw configuratie. Stel `-DiffDiskSetting` de `Local` `-Caching` aan `ReadOnly`en aan .


```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

Als u een tijdelijke schijf wilt gebruiken voor `--ephemeral-os-disk` een CLI VM-implementatie, `--os-disk-caching` stelt `ReadOnly`u de parameter in het [az vm-create](/cli/azure/vm#az-vm-create) in op `true` en de parameter in op .

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

Voor schaalsets gebruikt u `--ephemeral-os-disk true` dezelfde parameter voor [az-vmss-create](/cli/azure/vmss#az-vmss-create) en stelt u de `--os-disk-caching` parameter in op `ReadOnly`.

## <a name="portal"></a>Portal   

In de Azure-portal u ervoor kiezen om tijdelijke schijven te gebruiken bij het implementeren van een vm door het gedeelte **Geavanceerd** van het tabblad **Schijven te** openen. Selecteer **Ja**voor **het gebruik van tijdelijke OS-schijf** .

![Schermafbeelding van de keuzerondje voor het gebruik van een tijdelijke OS-schijf](./media/virtual-machines-common-ephemeral/ephemeral-portal.png)

Als de optie voor het gebruik van een tijdelijke schijf grijs is geworden, hebt u mogelijk een VM-grootte geselecteerd die geen cachegrootte heeft die groter is dan de OS-afbeelding of die geen Premium-opslag ondersteunt. Ga terug naar de pagina **Basisbeginselen** en probeer een andere virtuele machinegrootte te kiezen.

U ook schaalsets maken met tijdelijke OS-schijven via de portal. Zorg ervoor dat u een VM-grootte selecteert met een groot genoeg cachegrootte en vervolgens in **Tijdelijke OS-schijf gebruiken** selecteert u **Ja**.

![Schermafbeelding van de keuzerondje voor het gebruik van een tijdelijke OS-schijf voor uw schaalset](./media/virtual-machines-common-ephemeral/scale-set.png)

## <a name="scale-set-template-deployment"></a>Implementatie van sjabloon voor schalen  
Het proces om een schaalset te maken die een tijdelijke `diffDiskSettings` OS-schijf gebruikt, is het toevoegen van de eigenschap aan het `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` resourcetype in de sjabloon. Ook moet het cachingbeleid `ReadOnly` worden ingesteld voor de kortstondige OS-schijf. 


```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
                "option": "Local" 
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM-sjabloonimplementatie 
U een VM met een tijdelijke OS-schijf implementeren met behulp van een sjabloon. Het proces om een VM te maken die kortstondige `diffDiskSettings` OS-schijven gebruikt, is om de eigenschap toe te voegen aan het resourcetype Microsoft.Compute/virtualMachines in de sjabloon. Ook moet het cachingbeleid `ReadOnly` worden ingesteld voor de kortstondige OS-schijf. 

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2018-06-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" 
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```


## <a name="reimage-a-vm-using-rest"></a>Een virtuele machine opnieuw afbeelding geven met REST
U een virtual machine-exemplaar opnieuw afbeelding geven met een tijdelijke OS-schijf met restapi zoals hieronder beschreven en via Azure Portal door naar het deelvenster Overzicht van de VM te gaan. Voor schaalsets is reimaging al beschikbaar via Powershell, CLI en het portaal.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2018-06-01" 
```
 
## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Wat is de grootte van de lokale OS-schijven?**

A: We ondersteunen platform- en aangepaste afbeeldingen, tot de grootte van de VM-cache, waarbij alle lees-/schrijfbewerkingen naar de OS-schijf lokaal zijn op hetzelfde knooppunt als de virtuele machine. 

**V: Kan de kortstondige OS-schijf worden aangepast?**

A: Nee, zodra de kortstondige OS-schijf is ingericht, kan de os-schijf niet worden aangepast. 

**V: Kan ik een beheerde schijven aan een ephemerale VM koppelen?**

A: Ja, u een beheerde gegevensschijf koppelen aan een VM die een tijdelijke OS-schijf gebruikt. 

**V: Worden alle VM-formaten ondersteund voor kortstondige OS-schijven?**

A: Nee, alle VM-formaten voor Premium Storage worden ondersteund (DS, ES, FS, GS en M), behalve de maten B-serie, N-serie en H-serie.  
 
**V: Kan de kortstondige OS-schijf worden toegepast op bestaande VM's en schaalsets?**

A: Nee, kortstondige OS-schijf kan alleen worden gebruikt tijdens het maken van VM- en schaalsets. 

**V: Kun je kortstondige en normale OS-schijven mengen in een schaalset?**

A: Nee, u geen mix van kortstondige en permanente OS-schijfexemplaren binnen dezelfde schaalset hebben. 

**V: Kan de kortstondige OS-schijf worden gemaakt met Powershell of CLI?**

A: Ja, u VM's maken met ephemerale OS-schijf met REST, Sjablonen, PowerShell en CLI.

**V: Welke functies worden niet ondersteund met kortstondige OS-schijf?**

A: Kortstondige schijven ondersteunen niet:
- VM-afbeeldingen vastleggen
- Momentopnamen van schijven 
- Azure Disk Encryption 
- Azure Backup
- Azure Site Recovery  
- Os-schijfswap 
