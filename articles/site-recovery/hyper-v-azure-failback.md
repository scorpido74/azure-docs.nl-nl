---
title: Hyper-V VM's uit Azure teruglaten mislukken met Azure-siteherstel
description: Hyper-V VM's weer geven aan een on-premises site vanuit Azure met Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 4b005ae308576db6fd26fcf079161430b266ec3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281780"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Een failback uitvoeren voor Hyper-V-VM's

In dit artikel wordt beschreven hoe u Azure VM's teruglaten die zijn gemaakt na het mislukken van Hyper V VM's van een on-premises site naar Azure, met [Azure Site Recovery](site-recovery-overview.md).

- U mislukt hypervvm's van Azure door een geplande failover van Azure naar de on-premises site uit te voeren. Als de failoverrichting van Azure naar on-premises gaat, wordt deze beschouwd als een failback.
- Aangezien Azure een zeer beschikbare omgeving is en VM's altijd beschikbaar zijn, is failback van Azure een geplande activiteit. U een kleine downtime plannen, zodat workloads opnieuw on-premises kunnen worden uitgevoerd. 
- Geplande failback schakelt de VM's in Azure uit en downloadt de nieuwste wijzigingen. Er wordt geen gegevensverlies verwacht.

## <a name="before-you-start"></a>Voordat u begint

1. [Bekijk de typen failback die](failover-failback-overview.md#hyper-v-reprotectionfailback) u gebruiken - herstel van de oorspronkelijke locatie en het herstel van alternatieve locaties.
2. Controleer of de Azure VM's een opslagaccount gebruiken en niet beheerde schijven. Failback van Hyper-V VM's gerepliceerd met beheerde schijven wordt niet ondersteund.
3. Controleer of de on-premises Hyper-V-host (of System Center VMM-server als u met Site Recovery gebruikt) wordt uitgevoerd en verbonden met Azure. 
4. Zorg ervoor dat failover en commit compleet zijn voor de VM's. U hoeft geen specifieke siteherstelcomponenten in te stellen voor het mislukken van Hyper-V VM's vanuit Azure.
5. De tijd die nodig is om de gegevenssynchronisatie te voltooien en de on-premises VM te starten, is afhankelijk van een aantal factoren. Als u het downloaden van gegevens wilt versnellen, u de Microsoft Recovery Services-agent configureren om meer threads te gebruiken om de download te parallellopen. [Meer informatie](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="fail-back-to-the-original-location"></a>Terug naar de oorspronkelijke locatie

Als u hypervm's in Azure niet terugwilt naar de oorspronkelijke on-premises VM, voert u een geplande failover van Azure naar de on-premises site als volgt uit:

1. Selecteer de VM in de kluis > **gerepliceerde items.** Klik met de rechtermuisknop op de VM-> **Geplande failover**. Als u een herstelplan niet meer achterwerkt, selecteert u de naam van het plan en klikt u op **Failover** > **Planned Failover**.
2. Kies **in Geplande failover bevestigen**de bron- en doellocaties. Let op de failoverrichting. Als de failover van primaire werkte zoals verwacht en alle virtuele machines zijn in de secundaire locatie is dit alleen voor informatie.
3. Selecteer **in Gegevenssynchronisatie**een optie:
    - **Gegevens synchroniseren voordat de failover mislukt (alleen deltawijzigingen synchroniseren)**— Deze optie minimaliseert downtime voor VM's omdat deze worden gesynchroniseerd zonder ze af te sluiten.
        - **Fase 1**: Maakt een momentopname van Azure VM en kopieert deze naar de on-premises Hyper-V-host. De machine blijft draaien in Azure.
        - **Fase 2**: Hiermee wordt de Azure VM uitgeschakeld, zodat daar geen nieuwe wijzigingen optreden. De uiteindelijke set deltawijzigingen wordt overgebracht naar de on-premises server en de on-premises VM wordt gestart.
    - **Gegevens synchroniseren tijdens failover alleen (volledige download)**—Deze optie is sneller omdat we aannemen dat het grootste deel van de schijf is gewijzigd en geen tijd willen besteden aan het berekenen van checksums. Met deze optie worden geen checksumberekeningen uitgevoerd.
        - Het voert een download van de schijf. 
        - We raden u aan deze optie te gebruiken als u Azure al een tijdje (een maand of langer) uitvoert of als de on-premises VM wordt verwijderd.

4. Als vMM alleen is ingeschakeld voor de cloud, selecteert u in **Encryptiesleutel**het certificaat dat is uitgegeven toen u gegevensversleuteling inschakelt tijdens de installatie van de provider op de VMM-server.
5. Start de failover. U kunt de voortgang van de failover volgen op het tabblad **Taken**.
6. Als u de optie hebt geselecteerd om de gegevens te synchroniseren vóór de failover, klikt u op **Taken** > taaknaam > **Voltooien failover**. Er gebeurt nu het volgende:
    - Schakelt de Azure-machine uit.
    - Hiermee worden de laatste wijzigingen doorgevoerd naar de on-premises VM.
    - Hiermee start u de on-premises VM.
7. U zich nu aanmelden bij de on-premises VM-machine om te controleren of deze beschikbaar is zoals verwacht.
8. De virtuele machine is in een commit in afwachting van staat. Klik **op Commit** om de failover te plegen.
9. Als u de failback wilt voltooien, klikt u op **Repliceren om** de on-premises VM opnieuw te repliceren naar Azure.



## <a name="fail-back-to-an-alternate-location"></a>Terug naar een alternatieve locatie mislukt 

Ga als volgt terug naar een alternatieve locatie:

1. Als u nieuwe hardware instelt, installeert u een [ondersteunde versie van Windows](hyper-v-azure-support-matrix.md#replicated-vms)en de Hyper-V-rol op de machine.
2. Maak een virtuele netwerkswitch met dezelfde naam als die op de oorspronkelijke server.
3. Selecteer in **ProtectionGroupName** > voor beveiligde \<items**protectiongroupname** > \<> -> VirtualMachineName> de VM die u wilt mislukken en selecteer Vervolgens Geplande **failover**.
4. Kies in **Geplande failovers bevestigen**de kies **on-premises virtuele machine maken als deze niet bestaat.**
5. Selecteer **in Host Name**de nieuwe Hyper-V-hostserver waarop u de VM wilt plaatsen.
6. In **Gegevenssynchronisatie**raden we u aan de optie te selecteren om de gegevens te synchroniseren voordat de failover wordt gemaakt. Dit minimaliseert downtime voor VM's omdat deze synchroniseert zonder ze af te sluiten. Het doet het volgende:
    - **Fase 1**: Maakt momentopname van de Azure VM en kopieert deze naar de on-premises Hyper-V-host. De machine blijft draaien in Azure.
    - **Fase 2**: Hiermee wordt de Azure VM uitgeschakeld, zodat daar geen nieuwe wijzigingen optreden. De uiteindelijke set wijzigingen wordt overgebracht naar de on-premises server en de on-premises virtuele machine wordt opgestart.
    
7. Klik op het vinkje om de failover te starten (failback).
8. Nadat de eerste synchronisatie is voltooid en u klaar bent om de Azure VM af te sluiten, klikt u op Geplande failovertaak **voltooien** > \<> > **Failover voltooien**. Hiermee wordt de Azure-machine uitgeschakeld, worden de laatste wijzigingen naar de on-premises VM overgebracht en gestart.
9. U zich aanmelden bij de on-premises VM om te controleren of alles werkt zoals verwacht.
10. Klik **op Vastleggen** om de failover te voltooien. Commit verwijdert de Azure VM en zijn schijven en bereidt de on-premises VM voor om opnieuw te worden beveiligd.
10. Klik **op Repliceren om** te beginnen met het repliceren van de on-premises VM naar Azure. Alleen de deltawijzigingen sinds de VM is uitgeschakeld in Azure, worden gerepliceerd.

    > [!NOTE]
    > Als u de failback-taak annuleert tijdens het synchroniseren van gegevens, bevindt de on-premises VM zich in een beschadigde status. Dit komt omdat gegevenssynchronisatie de meest recente gegevens van Azure VM-schijven kopieert naar de on-premises gegevensschijven en totdat de synchronisatie is voltooid, de schijfgegevens mogelijk niet consistent zijn. Als de on-premises VM wordt gestart nadat de synchronisatie van gegevens is geannuleerd, wordt deze mogelijk niet opgestart. Voer in dit geval de failover opnieuw uit om gegevenssynchronisatie te voltooien.


## <a name="next-steps"></a>Volgende stappen
Nadat de on-premises VM is gerepliceerd naar Azure, u naar behoefte [een andere failover](site-recovery-failover.md) naar Azure uitvoeren.
