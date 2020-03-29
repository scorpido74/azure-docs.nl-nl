---
title: Herstelplannen maken/aanpassen in Azure Site Recovery
description: Meer informatie over het maken en aanpassen van herstelplannen voor noodherstel met de Azure Site Recovery-service.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 6540317324a9f0d9bccc046ecf95824d4128bd09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705833"
---
# <a name="create-and-customize-recovery-plans"></a>Herstelplannen maken en aanpassen

In dit artikel wordt beschreven hoe u een herstelplan voor failover maken en aanpassen in [Azure Site Recovery](site-recovery-overview.md). Voordat u begint, [leest u meer](recovery-plan-overview.md) over herstelplannen.

## <a name="create-a-recovery-plan"></a>Een herstelplan maken

1. Selecteer **herstelplannen (siteherstel)** > **+herstelplan**in de kluis Herstelservices .
2. Geef **in Herstelplan maken**een naam op voor het plan.
3. Kies een bron en doel op basis van de machines in het plan en selecteer **Resourcemanager** voor het implementatiemodel. De bronlocatie moet machines hebben die zijn ingeschakeld voor failover en herstel. 

    **Failover** | **Bron** | **Doel** 
   --- | --- | ---
   Azure naar Azure | Het Azure-gebied selecteren | Het Azure-gebied selecteren
   VMware naar Azure | De configuratieserver selecteren | Selecteer Azure
   Fysieke machines naar Azure | De configuratieserver selecteren | Selecteer Azure   
   Hyper-V naar Azure | De naam van de Hyper-V-site selecteren | Selecteer Azure
   Hyper-V (beheerd door VMM) naar Azure  | De VMM-server selecteren | Selecteer Azure
  
    Houd rekening met het volgende:
    -  U alleen een herstelplan gebruiken voor failover van de bronlocatie naar Azure. U geen herstelplan gebruiken voor failback van Azure.
    - De bronlocatie moet machines hebben die zijn ingeschakeld voor failover en herstel. 
    - Een herstelplan kan machines bevatten met dezelfde bron en doelstelling. 
    - U VMware VM's en Hyper-V VM's die door VMM worden beheerd, in hetzelfde plan opnemen.
    - VMware VM's en fysieke servers kunnen in hetzelfde plan.

4. Selecteer **in Virtuele machines items**selecteren de machines (of replicatiegroep) die u aan het abonnement wilt toevoegen. Klik vervolgens op **OK**.
    - Machines worden standaardgroep (groep 1) toegevoegd in het plan. Na failover beginnen alle machines in deze groep tegelijkertijd.
    - U alleen machines selecteren in de bron- en doellocaties die u hebt opgegeven. 
5. Klik op **OK** om het plan te maken.

## <a name="add-a-group-to-a-plan"></a>Een groep toevoegen aan een plan

U maakt extra groepen en voegt machines toe aan verschillende groepen, zodat u per groep ander gedrag opgeven. U bijvoorbeeld opgeven wanneer machines in een groep moeten beginnen na een failover, of aangepaste acties per groep opgeven.

1. Klik in **Herstelplannen**met de rechtermuisknop op het plan > **aanpassen**. Na het maken van een abonnement bevinden alle machines die u eraan hebt toegevoegd zich standaard groep 1.
2. Klik **op +Groeperen**. Standaard is een nieuwe groep genummerd in de volgorde waarin deze is toegevoegd. U maximaal zeven groepen hebben.
3. Selecteer de machine die u naar de nieuwe groep wilt verplaatsen, klik op **Groep wijzigen**en selecteer de nieuwe groep. U ook met de rechtermuisknop op de groepsnaam > **beveiligde item**klikken en machines aan de groep toevoegen. Een machine of replicatiegroep kan slechts tot één groep in een herstelplan behoren.


## <a name="add-a-script-or-manual-action"></a>Een script of handmatige actie toevoegen

U een herstelplan aanpassen door een script of handmatige actie toe te voegen. Opmerking:

- Als u recuperatie naar Azure, u Azure-automatiseringsprogramma's integreren in uw herstelplan. [Meer informatie](site-recovery-runbook-automation.md).
- Als u Hyper-V VM's die door System Center VMM worden beheerd, repliceren, u een script maken op de on-premises VMM-server en dit opnemen in het herstelplan.
- Wanneer u een script toevoegt, wordt een nieuwe set acties voor de groep toegevoegd. Een set voorstapjes voor groep 1 wordt bijvoorbeeld gemaakt met de naam *Groep 1: voorstappen*. Alle voorstappen worden in deze set weergegeven. U een script alleen op de primaire site toevoegen als u een VMM-server hebt geïmplementeerd.
- Als u een handmatige actie toevoegt, stopt het wanneer het herstelplan wordt uitgevoerd, op het punt waarop u de handmatige actie hebt ingevoegd. In een dialoogvenster wordt u gevraagd op te geven dat de handmatige actie is voltooid.
- Als u een script wilt maken op de VMM-server, volgt u de instructies in [dit artikel.](hyper-v-vmm-recovery-script.md)
- Scripts kunnen worden toegepast tijdens failover naar de secundaire site, en tijdens failback van de secundaire site naar de primaire. Ondersteuning is afhankelijk van uw replicatiescenario:
    
    **Scenario** | **Failover** | **Failback**
    --- | --- | --- 
    Azure naar Azure  | Runbook | Runbook
    VMware naar Azure | Runbook | N.v.t. 
    Hyper-V met VMM naar Azure | Runbook | Script
    Hyper-V-site naar Azure | Runbook | N.v.t.
    VMM naar secundaire VMM | Script | Script

1. Klik in het herstelplan op de stap waaraan de actie moet worden toegevoegd en geef aan wanneer de actie moet plaatsvinden:
    1. Als u wilt dat de actie plaatsvindt voordat de machines in de groep na een failover worden gestart, selecteert u **Vooractie toevoegen**.
    1. Als u wilt dat de actie plaatsvindt nadat de machines in de groep zijn gestart na het mislukken, selecteert u **Actie na het bericht toevoegen**. Als u de positie van de actie wilt verplaatsen, selecteert u de knoppen **Omhoog** of **Omlaag verplaatsen.**
2. Selecteer **script** of **handmatige actie**in **invoegen actie**.
3. Als u een handmatige actie wilt toevoegen, gaat u als volgt te werk:
    1. Typ een naam voor de actie en typ actie-instructies. De persoon die de failover uitvoert, ziet deze instructies.
    1. Geef op of u de handmatige actie voor alle typen failover wilt toevoegen (Test, Failover, Planned failover (indien relevant)). Klik vervolgens op **OK**.
4. Als u een script wilt toevoegen, gaat u als volgt te werk:
    1. Als u een VMM-script toevoegt, selecteert u **Failover naar VMM-script**en typt u in **Scriptpad** het relatieve pad naar het aandeel. Als het aandeel zich bijvoorbeeld \\ \<bevindt op VMMServerName>\MSSCVMMLibrary\RPScripts, geeft u het pad op: \RPScripts\RPScript.PS1.
    1. Als u een Azure-automatiseringsrunboek toevoegt, geeft u het **Azure Automation-account** op waarin de runbook zich bevindt en selecteert u het juiste **Azure Runbook Script**.
5. Voer een testfailover van het herstelplan uit om ervoor te zorgen dat het script werkt zoals verwacht.

## <a name="watch-a-video"></a>Bekijk een video

Bekijk een video die laat zien hoe je een herstelplan maken.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het uitvoeren van failovers](site-recovery-failover.md).  

    
