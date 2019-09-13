---
title: Een failback uitvoeren tijdens de nood herstel van virtuele Hyper-v-machines van Azure naar on-premises | Microsoft Docs
description: Meer informatie over het failback van virtuele Hyper-V-machines naar een on-premises site tijdens herstel na nood gevallen naar Azure met de Azure Site Recovery-service.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: 07ecc8547ab155600bccfd1ad8f1ecbb58a18fa3
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70931846"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Een failback uitvoeren voor virtuele Hyper-V-machines

In dit artikel wordt beschreven hoe u een failback kunt uitvoeren voor virtuele Hyper-V-machines die worden beveiligd door Site Recovery.

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de details van de [verschillende typen failback](concepts-types-of-failback.md) en bijbehorende voor behoud hebt gelezen.
- Zorg ervoor dat de VMM-server van de primaire site of de Hyper-V-hostserver is verbonden met Azure.
- U moet de virtuele machine **door voeren** .
- Zorg ervoor dat u een opslag account gebruikt voor replicatie en niet voor beheerde schijven. Failback van virtuele Hyper-V-machines die zijn gerepliceerd met behulp van schijven beheren, wordt niet ondersteund.

## <a name="perform-failback"></a>Failback uitvoeren
Na een failover van de primaire naar de secundaire locatie worden gerepliceerde virtuele machines niet beveiligd door Site Recovery en fungeert de secundaire locatie nu als de actieve locatie. Als u een failback wilt uitvoeren voor Vm's in een herstel plan, voert u de volgende geplande failover uit van de secundaire site naar de primaire. 
1. Selecteer **herstel plannen** > *recoveryplan_name*. Klik op geplande **failover** > -**failover**.
2. Kies op de pagina **geplande failover bevestigen** de bron-en doel locaties. Let op de richting van de failover. Als de failover van de primaire naar verwachting werkt en alle virtuele machines zich op de secundaire locatie bevinden, is dit alleen ter informatie.
3. Als u geen back-up meer hebt van Azure Select-instellingen in **gegevens synchronisatie**:
    - **Gegevens synchroniseren vóór failover (alleen Delta wijzigingen synchroniseren)** : met deze optie wordt de uitval tijd voor virtuele machines geminimaliseerd wanneer deze worden gesynchroniseerd zonder dat ze worden afgesloten. De volgende stappen worden uitgevoerd:
        - Fase 1: Hiermee maakt u een moment opname van de virtuele machine in Azure en kopieert u deze naar de on-premises Hyper-V-host. De machine blijft actief in Azure.
        - Fase 2: Hiermee wordt de virtuele machine in azure afgesloten, zodat er geen nieuwe wijzigingen worden uitgevoerd. De laatste set Delta wijzigingen wordt overgedragen naar de on-premises server en de on-premises virtuele machine wordt gestart.

    - **Gegevens alleen synchroniseren tijdens failover (volledige down load)** : deze optie is sneller.
        - Deze optie is sneller omdat we verwachten dat het grootste deel van de schijf is gewijzigd en dat er geen tijd in de berekening van de controlesom wordt gespendeerd. Hiermee wordt de schijf gedownload. Het is ook handig als de on-premises virtuele machine is verwijderd.
        - U wordt aangeraden deze optie te gebruiken als u Azure al een tijdje hebt (een maand of meer) of als de on-premises virtuele machine is verwijderd. Met deze optie worden geen controlesom berekeningen uitgevoerd.


4. Als gegevens versleuteling is ingeschakeld voor de Cloud, selecteert u in **versleutelings sleutel** het certificaat dat is uitgegeven tijdens het inschakelen van gegevens versleuteling tijdens de installatie van de provider op de VMM-server.
5. Start de failover. U kunt de voortgang van de failover volgen op het tabblad **Taken**.
6. Als u de optie hebt geselecteerd voor het synchroniseren van de gegevens vóór de failover, wanneer de initiële gegevens synchronisatie is voltooid en u klaar bent om de virtuele machines in azure uit te scha kelen, klikt u op **taken** > taak naam > **failover volt ooien**. Hiermee wordt de Azure-machine afgesloten, worden de meest recente wijzigingen overgedragen naar de on-premises virtuele machine en wordt de VM op locatie gestart.
7. U kunt zich nu aanmelden bij de virtuele machine om te valideren dat deze beschikbaar is zoals verwacht.
8. De virtuele machine heeft de status in behandeling door voeren. Klik op **door voeren** om de failover door te voeren.
9. Klik op **achterwaartse replicatie** om te beginnen met het beveiligen van de virtuele machine op de primaire site om de failback te volt ooien.


Volg deze procedures om een failback uit te voeren naar de oorspronkelijke primaire site. In deze procedure wordt beschreven hoe u een geplande failover uitvoert voor een herstel plan. U kunt ook de failover uitvoeren voor één virtuele machine op het tabblad **virtual machines** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Failback naar een alternatieve locatie in de Hyper-V-omgeving
Als u de beveiliging tussen een [Hyper-V-site en Azure](site-recovery-hyper-v-site-to-azure.md) hebt geïmplementeerd, moet u de mogelijkheid tot failback van Azure naar een andere on-premises locatie. Dit is handig als u nieuwe on-premises hardware wilt instellen. U doet dit als volgt.

1. Als u nieuwe hardware wilt instellen, installeert u Windows Server 2012 R2 en de Hyper-V-rol op de-server.
2. Maak een virtuele netwerk switch met dezelfde naam als die van de oorspronkelijke server.
3. Selecteer **beveiligde items** ->  \<**beveiligings groep** -> ProtectionGroupName\<>-> VirtualMachineName > u een failback wilt uitvoeren en selecteer **geplande failover**.
4. Selecteer bij **geplande failover bevestigen** de optie **on-premises virtuele machine maken als deze niet bestaat**.
5. In hostnaam, * * selecteer de nieuwe Hyper-V-hostserver waarop u de virtuele machine wilt plaatsen.
6. Bij het synchroniseren van gegevens wordt u aangeraden om de optie voor het synchroniseren van de gegevens voor de failover te selecteren. Hiermee wordt de uitval tijd voor virtuele machines geminimaliseerd wanneer deze worden gesynchroniseerd zonder dat ze worden afgesloten. Dit doet het volgende:

    - Fase 1: Hiermee maakt u een moment opname van de virtuele machine in Azure en kopieert u deze naar de on-premises Hyper-V-host. De machine blijft actief in Azure.
    - Fase 2: Hiermee wordt de virtuele machine in azure afgesloten, zodat er geen nieuwe wijzigingen worden uitgevoerd. De laatste set wijzigingen wordt overgedragen naar de on-premises server en de on-premises virtuele machine wordt gestart.
    
7. Klik op het vinkje om de failover (failback) te starten.
8. Nadat de initiële synchronisatie is voltooid en u klaar bent om de virtuele machine in azure af te sluiten, klikt u op **taken** > \<geplande failover-taak > > **failover volt ooien**. Hiermee wordt de Azure-machine afgesloten, worden de meest recente wijzigingen overgedragen naar de on-premises virtuele machine en gestart.
9. U kunt u aanmelden bij de on-premises virtuele machine om te controleren of alles werkt zoals verwacht. Klik vervolgens op **door voeren** om de failover te volt ooien. Door voeren worden de virtuele machine van Azure en de bijbehorende schijven verwijderd en wordt de VM voor bereid om opnieuw te worden beveiligd.
10. Klik op **achterwaartse replicatie** om te beginnen met de beveiliging van de on-premises virtuele machine.

    > [!NOTE]
    > Als u de failback-taak annuleert tijdens de stap voor gegevens synchronisatie, wordt de status van de on-premises VM beschadigd. Dit komt doordat gegevens synchronisatie de nieuwste gegevens kopieert van Azure VM-schijven naar de on-premises gegevens schijven en totdat de synchronisatie is voltooid, de schijf gegevens mogelijk niet consistent zijn. Als de on-premises VM wordt opgestart nadat de gegevens synchronisatie is geannuleerd, wordt deze mogelijk niet opgestart. Failover opnieuw activeren om de gegevens synchronisatie te volt ooien.


## <a name="why-is-there-no-button-called-failback"></a>Waarom is er geen knop met de naam failback?
Op de portal is er geen expliciete beweging genaamd failback. Failback is een stap waarin u terugkeert naar de primaire site. Failback is per definitie het uitvoeren van een failover van de virtuele machines van herstellen naar primair.

Wanneer u een failover initieert, wordt u door de Blade geïnformeerd over de richting waarin de virtuele machines moeten worden verplaatst, als de richting van Azure naar on-premises is, is dit een failback.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Waarom is er slechts een geplande failover-beweging naar failback?
Azure is een Maxi maal beschik bare omgeving en uw virtuele machines zijn altijd beschikbaar. Failback is een geplande activiteit waarbij u besluit een kleine downtime te nemen, zodat de workloads weer on-premises kunnen worden uitgevoerd. Dit verwacht geen verlies van gegevens. Daarom is er alleen een geplande failover-beweging beschikbaar waarmee de virtuele machines in Azure worden uitgeschakeld, de meest recente wijzigingen kunnen worden gedownload en er geen gegevens verloren gaan.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Heb ik een proces server in azure nodig om een failback naar Hyper-v uit te voeren?
Nee, een proces server is alleen vereist wanneer u virtuele VMware-machines beveiligt. Er zijn geen extra onderdelen vereist om te worden geïmplementeerd bij het beveiligen/failback van virtuele Hyper-v-machines.


## <a name="time-taken-to-failback"></a>Tijd die nodig is voor failback
De benodigde tijd voor het volt ooien van de gegevens synchronisatie en het opstarten van de virtuele machine is afhankelijk van verschillende factoren. Om inzicht te krijgen in de gebruikte tijd, wordt uitgelegd wat er gebeurt tijdens de gegevens synchronisatie.

Bij het synchroniseren van gegevens wordt een moment opname gemaakt van de schijven van de virtuele machine en wordt het blok keren per blok gecontroleerd en de bijbehorende controlesom berekend. Deze berekende controlesom wordt naar on-premises verzonden om te vergelijken met de on-premises controlesom van hetzelfde blok. Als de controle sommen overeenkomen, wordt het gegevens blok niet overgedragen. Als deze niet overeenkomt, wordt het gegevens blok overgedragen naar on-premises. Deze overdrachts tijd is afhankelijk van de beschik bare band breedte. De snelheid van de controlesom is een aantal GB per minuut. 

Als u het downloaden van gegevens wilt versnellen, kunt u de MARS-agent zo configureren dat er meer threads worden gebruikt om de down load te parallelliseren. Raadpleeg het [document hier](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) voor informatie over het wijzigen van de Download threads in de agent.


## <a name="next-steps"></a>Volgende stappen

Na het **door voeren**kunt u de *omgekeerde replicatie*starten. Hiermee start u de beveiliging van de virtuele machine van on-premises terug naar Azure. Hiermee worden alleen de wijzigingen gerepliceerd, omdat de VM is uitgeschakeld in Azure en daarom alleen differentiële wijzigingen verzendt.
