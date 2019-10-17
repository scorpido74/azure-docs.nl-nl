---
title: Aangepaste beleids regels voor schalen gebruiken met virtuele-machine schaal sets van Azure | Microsoft Docs
description: Meer informatie over het gebruik van aangepaste schaal-in-beleids regels met virtuele-machine schaal sets van Azure die gebruikmaken van configuratie voor automatisch schalen om het aantal exemplaren te beheren
services: virtual-machine-scale-sets
author: avverma
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: article
ms.date: 10/11/2019
ms.author: avverma
ms.openlocfilehash: c1618c398c0f7c4f0f54647e5232fdacc17de186
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453158"
---
# <a name="preview-use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Voor beeld: aangepaste beleids regels voor schalen gebruiken met schaal sets voor virtuele Azure-machines

Een implementatie van een schaalset voor virtuele machines kan worden uitgebreid of geschaald op basis van een matrix met metrische gegevens, waaronder platform en door de gebruiker gedefinieerde aangepaste metrische gegevens. Terwijl bij een uitschalen nieuwe Virtual Machines worden gemaakt op basis van het model met de schaalset, is een schaal van invloed op actieve virtuele machines met verschillende configuraties en/of functies als de werk belasting van de schaalset. 

De functie voor het schalen van beleid biedt gebruikers een manier om de volg orde te configureren waarin de virtuele machines worden geschaald. In het voor beeld worden drie configuraties voor inschalen geïntroduceerd: 

1. Standaard
2. NewestVM
3. OldestVM

***Deze preview-functie is beschikbaar zonder service level agreement en wordt niet aanbevolen voor productie werkbelastingen.***

### <a name="default-scale-in-policy"></a>Standaard beleid voor inschalen

Virtuele-machine schaal sets past dit beleid standaard toe om te bepalen welke exemplaren worden geschaald. Met het *standaard* beleid zijn vm's geselecteerd voor schalen in de volgende volg orde:

1. De verdeling van virtuele machines over beschikbaarheids zones (als de schaalset is geïmplementeerd in de zonegebonden-configuratie)
2. Virtuele machines verdelen over fout domeinen (beste poging)
3. Virtuele machine met de hoogste exemplaar-ID verwijderen

Gebruikers hoeven geen niveau van een inschaal beleid op te geven als ze alleen de standaard volgorde moeten volgen.

Houd er rekening mee dat bij het verdelen over beschikbaarheids zones of fout domeinen geen instanties over beschikbaarheids zones of fout domeinen worden verplaatst. De verdeling wordt bereikt door het verwijderen van virtuele machines uit de niet-sluitende beschikbaarheids zones of de fout domeinen. de verdeling van virtuele machines wordt evenwichtig.

### <a name="newestvm-scale-in-policy"></a>NewestVM-beleid voor inschalen

Met dit beleid wordt de nieuwste gemaakte virtuele machine in de schaalset verwijderd, na het verdelen van Vm's over beschikbaarheids zones (voor zonegebonden-implementaties). Als u dit beleid inschakelt, is een configuratie wijziging vereist voor het model van de virtuele-machine schaalset.

### <a name="oldestvm-scale-in-policy"></a>OldestVM-beleid voor inschalen

Met dit beleid wordt de oudste gemaakte virtuele machine in de schaalset verwijderd, na het verdelen van Vm's over beschikbaarheids zones (voor zonegebonden-implementaties). Als u dit beleid inschakelt, is een configuratie wijziging vereist voor het model van de virtuele-machine schaalset.

## <a name="enabling-scale-in-policy"></a>Inschaal beleid inschakelen

Er wordt een beleid voor inschalen gedefinieerd in het model voor virtuele-machine schaal sets. Zoals vermeld in de bovenstaande secties, is er een beleids definitie voor de schaal in het beleid nodig bij het gebruik van het beleid ' NewestVM ' en ' OldestVM '. In de schaalset voor virtuele machines wordt automatisch het ' standaard ' scale-in-beleid gebruikt als er geen definitie van een inschaal beleid is gevonden in het model voor de schaalset. 

U kunt op de volgende manieren een scale-in-beleid definiëren in het model voor de virtuele-machine schaal:

### <a name="using-api"></a>API gebruiken

Voer een PUT uit op de virtuele-machine schaalset met API 2019-03-01:

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

### <a name="using-template"></a>Sjabloon gebruiken

In uw sjabloon, onder eigenschappen, voegt u het volgende toe:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

De bovenstaande blokken geven aan dat de VM-schaalset de oudste virtuele machine in een schaalset met gelijke taak verdeling verwijdert wanneer een inschalen wordt geactiveerd (door automatisch schalen of hand matig te verwijderen).

Wanneer een schaalset voor een virtuele machine niet wordt verdeeld over de zone, verwijdert de schaalset eerst Vm's uit de niet-sluitende zone (s). In de niet-sluitende zones maakt de schaalset gebruik van het hierboven opgegeven niveau voor de schaal aanpassing om te bepalen welke VM moet worden geschaald. In dit geval selecteert de schaalset in een niet-sluitende zone de oudste virtuele machine in die zone die moet worden verwijderd.

Voor een niet-zonegebonden virtuele-machine schaalset selecteert het beleid de oudste VM in de schaalset voor verwijdering.

Hetzelfde proces geldt voor het gebruik van ' NewestVM ' in het bovenstaande scale-in-beleid.

## <a name="modifying-scale-in-policies"></a>Inschaal beleid wijzigen

Het aanpassen van het scale-in-beleid volgt hetzelfde proces als het Toep assen van het beleid voor inschalen. Als u bijvoorbeeld in het bovenstaande voor beeld het beleid wilt wijzigen van ' OldestVM ' in ' NewestVM ', kunt u dit als volgt doen:

### <a name="using-api"></a>API gebruiken

Voer een PUT uit op de virtuele-machine schaalset met API 2019-03-01:

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

### <a name="using-template"></a>Sjabloon gebruiken

Wijzig in uw sjabloon onder ' Eigenschappen ' de sjabloon zoals hieronder en implementeer deze opnieuw: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Hetzelfde proces is van toepassing als u besluit om ' NewestVM ' te wijzigen in ' default ' of ' OldestVM '

## <a name="instance-protection-and-scale-in-policy"></a>Instantie beveiliging en inschaal beleid

Virtuele-machine schaal sets bieden twee soorten [instantie beveiliging](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection):

1. Beveiligen tegen schalen
2. Beveiligen tegen schaal acties

Een beveiligde virtuele machine wordt niet verwijderd door een inschaal actie, ongeacht het toegepaste beleid voor de schaal aanpassing. Als bijvoorbeeld VM_0 (oudste VM in de schaalset) wordt beveiligd tegen schalen, en als de schaalset ' OldestVM ' is ingeschakeld, wordt VM_0 niet in overweging genomen in de schaalset, ook al is het de oudste VM in de schaalset. 

Een beveiligde virtuele machine kan op elk gewenst moment hand matig worden verwijderd door de gebruiker, ongeacht het ingestelde beleid voor inschalen in de schaalset. 

## <a name="usage-examples"></a>Gebruiks voorbeelden 

In de onderstaande voor beelden ziet u hoe u met een schaalset voor virtuele machines Vm's selecteert die moeten worden verwijderd wanneer een ingrijpende gebeurtenis wordt geactiveerd. Voor virtuele machines met de hoogste exemplaar-Id's wordt aangenomen dat de nieuwste Vm's in de schaalset zijn en er wordt ervan uitgegaan dat de Vm's met de kleinste exemplaar-Id's de oudste Vm's in de schaalset zijn. 

### <a name="oldestvm-scale-in-policy"></a>OldestVM-beleid voor inschalen

| Gebeurtenis                 | Exemplaar-Id's in zone 1  | Exemplaar-Id's in Zone2  | Exemplaar-Id's in Zone3  | Selectie inschalen                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Itiaal               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Inschalen              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Kies tussen Zone 1 en 2, zelfs als Zone 3 de oudste virtuele machine heeft. Verwijder VM2 uit Zone 2, omdat dit de oudste virtuele machine in die zone is.   |
| Inschalen              | ***3***, 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Kies Zone 1 zelfs als Zone 3 de oudste virtuele machine heeft. Verwijder VM3 uit Zone 1, omdat dit de oudste virtuele machine in die zone is.                  |
| Inschalen              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Zones zijn evenwichtig. Verwijder VM1 in Zone 3, omdat dit de oudste VM in de schaalset is.                                               |
| Inschalen              | ***4***, 5, 10         | 6, 9, 11               | 7, 8                   | Kies tussen Zone 1 en Zone 2. Verwijder VM4 in Zone 1, omdat dit de oudste VM in de twee zones is.                              |
| Inschalen              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Kies Zone 2 zelfs als Zone 1 de oudste virtuele machine heeft. Verwijder VM6 in Zone 1, omdat dit de oudste virtuele machine in die zone is.                    |
| Inschalen              | ***5***, 10            | 9, 11                  | 7, 8                   | Zones zijn evenwichtig. Verwijder VM5 in Zone 1, omdat dit de oudste VM in de schaalset is.                                                |

Voor niet-zonegebonden virtuele-machine schaal sets selecteert het beleid de oudste VM in de schaalset voor verwijdering. Een ' beveiligde ' VM wordt overgeslagen voor verwijdering.

### <a name="newestvm-scale-in-policy"></a>NewestVM-beleid voor inschalen

| Gebeurtenis                 | Exemplaar-Id's in zone 1  | Exemplaar-Id's in Zone2  | Exemplaar-Id's in Zone3  | Selectie inschalen                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Itiaal               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Inschalen              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Kies tussen Zone 1 en 2. Verwijder VM11 uit Zone 2, omdat het de nieuwste VM in de twee zones is.                                |
| Inschalen              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Kies Zone 1 omdat het meer Vm's heeft dan de andere twee zones. Verwijder VM10 uit Zone 1, omdat dit de nieuwste virtuele machine in die zone is.          |
| Inschalen              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Zones zijn evenwichtig. Verwijder VM9 in Zone 2, omdat dit de nieuwste VM in de schaalset is.                                                |
| Inschalen              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Kies tussen Zone 1 en Zone 3. Verwijder VM8 in Zone 3, omdat dit de nieuwste virtuele machine in die zone is.                                      |
| Inschalen              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Kies Zone 1 zelfs als Zone 3 de nieuwste virtuele machine heeft. Verwijder VM5 in Zone 1, omdat dit de nieuwste virtuele machine in die zone is.                    |
| Inschalen              | 3, 4                   | 2, 6                   | 1, ***7***             | Zones zijn evenwichtig. Verwijder VM7 in Zone 3, omdat dit de nieuwste VM in de schaalset is.                                                |

Voor niet-zonegebonden virtuele-machine schaal sets selecteert het beleid de nieuwste VM in de schaalset voor verwijdering. Een ' beveiligde ' VM wordt overgeslagen voor verwijdering. 

## <a name="troubleshoot"></a>Problemen oplossen

1. Kan scaleInPolicy niet inschakelen als er een fout bericht ' onjuiste aanvraag ' wordt weer gegeven met de melding ' kan geen lid ' scaleInPolicy ' vinden voor een object van het type ' Eigenschappen ' ' en controleer vervolgens de API-versie die wordt gebruikt voor de schaalset van de virtuele machine. API-versie 2019-03-01 of hoger is vereist voor deze preview.

2. Verkeerde selectie van Vm's voor schalen: Raadpleeg de bovenstaande voor beelden. Als uw schaalset voor virtuele machines een zonegebonden-implementatie is, wordt het scale-in-beleid eerst toegepast op de niet-sluitende zones en vervolgens op de schaalset zodra de zone is verdeeld. Als de volg orde van de schaal niet consistent is met de bovenstaande voor beelden, verhoogt u een query met het team van de virtuele-machine schaalset voor het oplossen van problemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [implementeren van uw toepassing](virtual-machine-scale-sets-deploy-app.md) op virtuele-machine schaal sets.