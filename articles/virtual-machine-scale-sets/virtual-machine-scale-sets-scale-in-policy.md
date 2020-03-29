---
title: Aangepaste schaal-in-beleidsregels gebruiken met Azure-schaalsets voor virtuele machines
description: Meer informatie over het gebruik van aangepaste schaalbeheerbeleidsregels met Azure-configureering voor virtuele machines die automatische schaalconfiguratie gebruiken om het aantal gevallen te beheren
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919835"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Aangepaste schaal-in-beleidsregels gebruiken met Azure-schaalsets voor virtuele machines

Een implementatie van een virtuele machineschaalset kan worden geschaald of geschaald op basis van een reeks statistieken, waaronder platform- en door de gebruiker gedefinieerde aangepaste statistieken. Terwijl een scale-out nieuwe virtuele machines maakt op basis van het schaalsetmodel, is een scale-in van invloed op het uitvoeren van virtuele machines die verschillende configuraties en/of functies kunnen hebben naarmate de schaalsetworkload evolueert. 

De beleidsfunctie scale-in biedt gebruikers een manier om de volgorde te configureren waarin virtuele machines worden ingeschaald, door middel van drie scale-in configuraties: 

1. Standaard
2. NieuwsteVM
3. OudsteVM

### <a name="default-scale-in-policy"></a>Standaardscale-in-beleid

Standaard past de virtuele machineschaalset dit beleid toe om te bepalen in welke instantie(s) wordt geschaald. Met het *standaardbeleid* worden VM's geselecteerd voor scale-in in de volgende volgorde:

1. Virtuele machines in evenwicht brengen tussen beschikbaarheidszones (als de schaalset in zonale configuratie wordt geïmplementeerd)
2. Virtuele machines in balans brengen tussen foutdomeinen (beste inspanning)
3. Virtuele machine verwijderen met de hoogste instantie-id

Gebruikers hoeven geen scale-in beleid op te geven als ze alleen willen dat de standaardbestelling wordt gevolgd.

Houd er rekening mee dat het balanceren tussen beschikbaarheidszones of foutdomeinen geen instanties over beschikbaarheidszones of foutdomeinen verplaatst. Het balanceren wordt bereikt door het verwijderen van virtuele machines uit de onevenwichtige beschikbaarheidszones of foutdomeinen totdat de distributie van virtuele machines in evenwicht wordt gebracht.

### <a name="newestvm-scale-in-policy"></a>Nieuwste schaal-in-beleid

Met dit beleid wordt de nieuwste virtuele machine in de schaalset verwijderd, nadat vm's zijn in evenwicht te zijn tussen beschikbaarheidszones (voor zonale implementaties). Als u dit beleid inschakelt, is een configuratiewijziging vereist voor het model van de virtuele machineschaal.

### <a name="oldestvm-scale-in-policy"></a>OldestVM scale-in beleid

Met dit beleid wordt de oudste virtuele machine in de schaalset verwijderd, nadat vm's zijn in evenwicht te zijn tussen beschikbaarheidszones (voor zonale implementaties). Als u dit beleid inschakelt, is een configuratiewijziging vereist voor het model van de virtuele machineschaal.

## <a name="enabling-scale-in-policy"></a>Schaal-inbeleid inschakelen

Een scale-in beleid wordt gedefinieerd in het model van de virtuele machineschaalset. Zoals in de bovenstaande secties wordt opgemerkt, is een scale-in beleidsdefinitie nodig bij het gebruik van het beleid 'NewestVM' en 'OldestVM'. Virtuele machineschaalset gebruikt automatisch het 'Standaard'-scale-in-beleid als er geen scale-in beleidsdefinitie is gevonden op het schaalsetmodel. 

Een scale-in beleid kan op de volgende manieren worden gedefinieerd op het model van de virtuele machineschaalset:

### <a name="azure-portal"></a>Azure Portal
 
In de volgende stappen wordt het scale-in-beleid gedefinieerd bij het maken van een nieuwe schaalset. 
 
1. Ga naar **virtuele machineschaalsets**.
1. Selecteer **+ Toevoegen** om een nieuwe schaalset te maken.
1. Ga naar het tabblad **Schalen.** 
1. Zoek de sectie **Beleidssectie Schalen.**
1. Selecteer een scale-in beleid in de vervolgkeuzelijst.
1. Wanneer u klaar bent met het maken van de nieuwe schaalset, selecteert u De knop **Controleren + maken.**

### <a name="using-api"></a>API gebruiken

Voer een PUT uit op de virtuele machineschaalset met API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Maak een resourcegroep en maak vervolgens een nieuwe schaalset met scale-in-beleidsset als *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

In het volgende voorbeeld wordt een scale-in-beleid toegevoegd tijdens het maken van een nieuwe schaalset. Maak eerst een resourcegroep en maak vervolgens een nieuwe schaalset met scale-inbeleid als *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sjabloon gebruiken

Voeg in uw sjabloon onder 'eigenschappen' het volgende toe:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

De bovenstaande blokken geven aan dat de virtuele machineschaalset de oudste VM in een zone-gebalanceerde schaalset verwijdert wanneer een scale-in wordt geactiveerd (via Autoscale of handmatig verwijderen).

Wanneer een virtuele machineschaalset niet in evenwicht is met de zone, verwijdert de schaalset eerst VM's in de onevenwichtige zone(s). Binnen de onevenwichtige zones gebruikt de schaalset het hierboven opgegeven schaal-in-beleid om te bepalen in welke VM u moet schalen. In dit geval selecteert de schaalset binnen een onevenwichtige zone de oudste vm in die zone die moet worden verwijderd.

Voor niet-zonale virtuele machineschaalset selecteert het beleid de oudste vm voor de schaaldie is ingesteld voor verwijdering.

Hetzelfde proces is van toepassing bij het gebruik van 'NewestVM' in het bovenstaande scale-in beleid.

## <a name="modifying-scale-in-policies"></a>Schaal-inbeleid wijzigen

Het wijzigen van het scale-in-beleid volgt hetzelfde proces als het toepassen van het scale-in-beleid. Als u in het bovenstaande voorbeeld bijvoorbeeld het beleid wilt wijzigen van 'OldestVM' naar 'NewestVM', u dit doen door:

### <a name="azure-portal"></a>Azure Portal

U het inschandebeleid van een bestaande schaalset wijzigen via de Azure-portal. 
 
1. Selecteer **Schalen** in een bestaande virtuele machineschaalset in het menu aan de linkerkant.
1. Selecteer het tabblad **Inschalen.**
1. Selecteer een scale-in beleid in de vervolgkeuzelijst.
1. Selecteer **Opslaan** wanneer u klaar bent. 

### <a name="using-api"></a>API gebruiken

Voer een PUT uit op de virtuele machineschaalset met API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Werk het scale-in-beleid van een bestaande schaalset bij:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Het volgende is een voorbeeld voor het bijwerken van het schaal-in-beleid van een bestaande schaalset: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Sjabloon gebruiken

Wijzig in uw sjabloon onder 'eigenschappen' de onderstaande sjabloon en implementeer: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Hetzelfde proces is van toepassing als u besluit om 'NewestVM' te wijzigen in 'Standaard' of 'OldestVM'

## <a name="instance-protection-and-scale-in-policy"></a>Instantiebescherming en schaal-inbeleid

Virtuele machineschaalsets bieden twee soorten [instantiebescherming:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Beschermen tegen scale-in
2. Beschermen tegen schaalacties

Een beveiligde virtuele machine wordt niet verwijderd door middel van een scale-in actie, ongeacht het toegepaste scale-in beleid. Als VM_0 (oudste VM in de schaalset) bijvoorbeeld is beveiligd tegen scale-in en de schaalset 'OldestVM'-inschaedbeleid is ingeschakeld, wordt VM_0 niet in aanmerking genomen als ingeschaald, ook al is het de oudste VM in de schaalset. 

Een beveiligde virtuele machine kan op elk moment handmatig door de gebruiker worden verwijderd, ongeacht het inschandebeleid dat is ingeschakeld op de schaalset. 

## <a name="usage-examples"></a>Gebruiksvoorbeelden 

De onderstaande voorbeelden laten zien hoe een virtuele machineschaalset VM's selecteert die moeten worden verwijderd wanneer een scale-in-gebeurtenis wordt geactiveerd. Virtuele machines met de hoogste instantie-id's worden verondersteld de nieuwste VM's in de schaalset te zijn en de VM's met de kleinste instantie-id's worden verondersteld de oudste VM's in de schaalset te zijn. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM scale-in beleid

| Gebeurtenis                 | Instantie-iD's in zone1  | Instantie-iD's in zone2  | Instantie-iD's in zone3  | Selectie schalen                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Eerste               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Schaal-in              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Kies tussen zone 1 en 2, ook al heeft zone 3 de oudste VM. Verwijder VM2 uit zone 2 omdat het de oudste VM in die zone is.   |
| Schaal-in              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Kies Zone 1, ook al heeft zone 3 de oudste VM. Verwijder VM3 uit zone 1 omdat het de oudste VM in die zone is.                  |
| Schaal-in              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Zones zijn in evenwicht. Verwijder VM1 in zone 3 omdat het de oudste vm in de schaalset is.                                               |
| Schaal-in              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Kies tussen zone 1 en zone 2. Verwijder VM4 in zone 1 omdat het de oudste VM in de twee zones is.                              |
| Schaal-in              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Kies Zone 2, ook al heeft zone 1 de oudste VM. Verwijder VM6 in zone 1 omdat het de oudste vm in die zone is.                    |
| Schaal-in              | ***5***, 10            | 9, 11                  | 7, 8                   | Zones zijn in evenwicht. Verwijder VM5 in zone 1 omdat het de oudste vm in de schaalset is.                                                |

Voor niet-zonale virtuele machineschaalsets selecteert het beleid de oudste vm voor de schaaldie is ingesteld voor verwijdering. Elke "beveiligde" VM wordt overgeslagen voor verwijdering.

### <a name="newestvm-scale-in-policy"></a>Nieuwste schaal-in-beleid

| Gebeurtenis                 | Instantie-iD's in zone1  | Instantie-iD's in zone2  | Instantie-iD's in zone3  | Selectie schalen                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Eerste               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Schaal-in              | 3, 4, 5, 10            | 2, 6, 9, ***11.***      | 1, 7, 8                | Kies tussen zone 1 en 2. Verwijder VM11 uit zone 2 omdat het de nieuwste VM is voor de twee zones.                                |
| Schaal-in              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Kies Zone 1 omdat deze meer VM's heeft dan de andere twee zones. Verwijder VM10 uit zone 1 omdat het de nieuwste VM in die zone is.          |
| Schaal-in              | 3, 4, 5                | 2, 6, ***9.***          | 1, 7, 8                | Zones zijn in evenwicht. Verwijder VM9 in zone 2 omdat het de nieuwste VM in de schaalset is.                                                |
| Schaal-in              | 3, 4, 5                | 2, 6                   | 1, 7, ***8.***          | Kies tussen zone 1 en zone 3. Verwijder VM8 in zone 3 omdat het de nieuwste VM in die zone is.                                      |
| Schaal-in              | 3, 4, ***5.***          | 2, 6                   | 1, 7                   | Kies Zone 1, ook al heeft zone 3 de nieuwste VM. Verwijder VM5 in zone 1 omdat het de nieuwste VM in die zone is.                    |
| Schaal-in              | 3, 4                   | 2, 6                   | 1. ***7***             | Zones zijn in evenwicht. Verwijder VM7 in zone 3 omdat het de nieuwste VM in de schaalset is.                                                |

Voor niet-zonale virtuele machineschaalsets selecteert het beleid de nieuwste VM voor de schaaldie is ingesteld voor verwijdering. Elke "beveiligde" VM wordt overgeslagen voor verwijdering. 

## <a name="troubleshoot"></a>Problemen oplossen

1. ScaleInPolicy niet inschakelen Als u een fout in 'BadRequest' krijgt met een foutbericht met de vermelding 'Kon lid 'scaleInPolicy' niet vinden op object van type 'eigenschappen'", controleer dan de API-versie die wordt gebruikt voor de virtuele machineschaalset. API versie 2019-03-01 of hoger is vereist voor deze functie.

2. Verkeerde selectie van VM's voor schaalvergroting Verwijzen naar de bovenstaande voorbeelden. Als uw virtuele machineschaalset een zonale implementatie is, wordt het scale-in-beleid eerst toegepast op de onevenwichtige zones en vervolgens op de schaaldie is ingesteld zodra deze zone in evenwicht is. Als de volgorde van de scale-in niet overeenkomt met de bovenstaande voorbeelden, stelt u een query op met het team met de virtuele machineschaalschaal voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele machineschaalsets.