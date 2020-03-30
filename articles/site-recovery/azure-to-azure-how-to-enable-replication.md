---
title: Replicatie configureren voor Azure VM's in Azure Site Recovery
description: Meer informatie over het configureren van replicatie naar een ander gebied voor Azure VM's met Siteherstel.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: 1c6b7cfbf193f02598052b6922efec17fb16ec83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973693"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Azure VM's repliceren naar een andere Azure-regio


In dit artikel wordt beschreven hoe u replicatie van Azure VM's inschakelen, van de ene Azure-regio naar de andere.

## <a name="before-you-start"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u hebt voorbereid op de implementatie van siteherstel, zoals beschreven in de [zelfstudie Azure naar Azure voor noodherstel.](azure-to-azure-tutorial-enable-replication.md)

Er moeten voorwaarden zijn en u moet een kluis van Recovery Services hebben gemaakt.


## <a name="enable-replication"></a>Replicatie inschakelen

Schakel replicatie in. Deze procedure gaat ervan uit dat de primaire Azure-regio Oost-Azië is en de secundaire regio Zuidoost-Azië.

1. Klik in de kluis op **+Repliceren**.
2. Let op de volgende velden:
   - **Bron:** Het punt van oorsprong van de VM's, dat in dit geval **Azure**is.
   - **Bronlocatie:** het Azure-gebied van waaruit u uw VM's wilt beschermen. Ter illustratie is de bronlocatie 'Oost-Azië'
   - **Implementatiemodel:** Azure-implementatiemodel van de bronmachines.
   - **Bronabonnement**: Het abonnement waartoe uw bron-VM's behoren. Dit kan elk abonnement zijn binnen dezelfde Azure Active Directory-tenant waar uw Recovery Services-kluis zich bevindt.
   - **Resourcegroep:** de resourcegroep waartoe uw bronvirtuele machines behoren. Alle VM's onder de geselecteerde resourcegroep worden in de volgende stap voor bescherming weergegeven.

     ![Replicatie inschakelen](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Klik in **Virtuele machines > Virtuele machines**selecteren, klik en selecteer elke vm die u wilt repliceren. U kunt alleen machines selecteren waarvoor replicatie kan worden ingeschakeld. Klik vervolgens op **OK**.
    ![Replicatie inschakelen](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. In **Instellingen**u optioneel doelsite-instellingen configureren:

   - **Doellocatie:** de locatie waar uw brongegevens van virtuele machine worden gerepliceerd. Afhankelijk van de locatie van uw geselecteerde machines biedt Site Recovery u de lijst met geschikte doelregio's. We raden u aan de doellocatie hetzelfde te houden als de kluislocatie van Recovery Services.
   - **Doelabonnement**: het doelabonnement dat wordt gebruikt voor herstel na noodgevallen. Standaard is het doelabonnement niet hetzelfde als het bronabonnement.
   - **Doelgroepgroep**: de resourcegroep waartoe al uw gerepliceerde virtuele machines behoren.
       - Siteherstel maakt standaard een nieuwe resourcegroep in het doelgebied met een 'asr'-achtervoegsel in de naam.
       - Als de resourcegroep die door Siteherstel is gemaakt, al bestaat, wordt deze opnieuw gebruikt.
       - U de instellingen van de brongroep aanpassen.
       - De locatie van de doelgroepkan elke Azure-regio zijn, behalve het gebied waarin de bron-VM's worden gehost.
   - **Virtueel netwerk target:** Siterecovery maakt standaard een nieuw virtueel netwerk in het doelgebied met een 'asr'-achtervoegsel in de naam. Dit wordt toegewezen aan uw bronnetwerk en wordt gebruikt voor toekomstige bescherming. [Meer informatie](site-recovery-network-mapping-azure-to-azure.md) over netwerktoewijzing.
   - **Doelopslagaccounts (bron-VM maakt geen beheerde schijven)**: Site recovery maakt standaard een nieuw doelopslagaccount dat uw bron-VM-opslagconfiguratie nabootst. In het geval dat het opslagaccount al bestaat, wordt het opnieuw gebruikt.
   - **Replicabeheerde schijven (bron-VM maakt gebruik van beheerde schijven)**: Site Recovery maakt nieuwe replicabeheerde schijven in het doelgebied om de beheerde schijven van de bron-VM te spiegelen met hetzelfde opslagtype (Standaard of premium) als de beheerde schijf van de bron-VM.
   - **Cacheopslagaccounts:** Siteherstel vereist extra opslagaccount genaamd cacheopslag in het brongebied. Alle wijzigingen die op de bron-VM's plaatsvinden, worden bijgehouden en naar het opslagaccount in de cache verzonden voordat deze worden gerepliceerd naar de doellocatie. Dit opslagaccount moet standaard zijn.
   - **Doelbeschikbaarheidssets:** Siteherstel maakt standaard een nieuwe beschikbaarheiddie is ingesteld in het doelgebied met het 'asr'-achtervoegsel in de naam, voor VM's die deel uitmaken van een beschikbaarheidsset in het brongebied. Als de beschikbaarheidsset die door Site Recovery is gemaakt, al bestaat, wordt deze opnieuw gebruikt.
   - **Doelbeschikbaarheidszones**: in Site Recovery wordt standaard hetzelfde zonenummer toegewezen als de bronregio in de doelregio, als de doelregio ondersteuning biedt voor beschikbaarheidszones.

     Als de doelregio geen ondersteuning biedt voor beschikbaarheidszones, worden de doel-VM's standaard geconfigureerd als enkele exemplaren. Indien vereist, kunt u dergelijke VM's configureren als onderdeel van beschikbaarheidssets in de doelregio door op Aanpassen te klikken.

     >[!NOTE]
     >Nadat u replicatie hebt ingeschakeld, kunt u het type beschikbaarheid - enkel exemplaar, beschikbaarheidsset of beschikbaarheidszone - niet meer wijzigen. Als u het type beschikbaarheid wilt wijzigen, moet u replicatie uitschakelen en opnieuw inschakelen.
     >

   - **Replicatiebeleid:** hiermee worden de instellingen gedefinieerd voor de bewaargeschiedenis van herstelpunten en de consistente momentopnamefrequentie voor apps. Azure Site Recovery maakt standaard een nieuw replicatiebeleid met standaardinstellingen van '24 uur' voor herstelpuntbehoud en '4 uur' voor de consistente momentopnamefrequentie van apps.

     ![Replicatie inschakelen](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Replicatie inschakelen voor toegevoegde schijven

Als u schijven toevoegt aan een Azure VM waarvoor replicatie is ingeschakeld, gebeurt het volgende:
-   Replicatiestatus voor de VM toont een waarschuwing en een notitie geeft aan dat een of meer schijven beschikbaar zijn voor bescherming.
-   Als u de beveiliging van de toegevoegde schijven inschakelt, verdwijnt de waarschuwing na de eerste replicatie van de schijf.
-   Als u ervoor kiest om replicatie voor de schijf niet in te schakelen, u ervoor kiezen de waarschuwing te verwijderen.


    ![Nieuwe schijf toegevoegd](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Ga als volgt te werk om replicatie voor een toegevoegde schijf in te schakelen:

1.  Klik in de kluis > **gerepliceerde items**op de vm waaraan u de schijf hebt toegevoegd.
2.  Klik op **Schijven**en selecteer vervolgens de gegevensschijf waarvoor u replicatie wilt inschakelen (deze schijven hebben een **niet-beveiligde** status).
3.  Klik **in Schijfgegevens**op **Replicatie inschakelen**.

    ![Replicatie inschakelen voor toegevoegde schijf](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Nadat de replicatietaak inschakelen is uitgevoerd en de eerste replicatie is voltooid, wordt de waarschuwing voor de replicatiestatus voor het schijfprobleem verwijderd.



## <a name="customize-target-resources"></a>Doelbronnen aanpassen

U de standaarddoelinstellingen wijzigen die door Site Recovery worden gebruikt.

1. Klik **op Aanpassen:** naast 'Doelabonnement' om het standaarddoelabonnement te wijzigen. Selecteer het abonnement in de lijst met alle abonnementen die beschikbaar zijn in dezelfde Azure Active Directory -tenant (AAD).

2. Klik **op Aanpassen:** om de standaardinstellingen te wijzigen:
    - Selecteer **in doelgroepgroep Target**de resourcegroep in de lijst met alle resourcegroepen op de doellocatie van het abonnement.
    - Selecteer **in het virtuele netwerk Target**het netwerk uit een lijst met alle virtuele netwerken op de doellocatie.
    - In **beschikbaarheidsset**u beschikbaarheidssetinstellingen toevoegen aan de vm als deze deel uitmaken van een beschikbaarheidsset in het brongebied.
    - Selecteer **in Doelopslagaccounts**het account dat u wilt gebruiken.

        ![Replicatie inschakelen](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klik **op Aanpassen:** om replicatie-instellingen te wijzigen.
4. Selecteer in **multi-VM-consistentie**de VM's die u samen wilt repliceren.
    - Alle machines in een replicatiegroep hebben gedeelde crash-consistente en app-consistente herstelpunten bij een failover.
    - Het inschakelen van multi-VM-consistentie kan de prestaties van de werkbelasting beïnvloeden (omdat het CPU-intensief is). Het moet alleen worden ingeschakeld als machines dezelfde werkbelasting uitvoeren en u consistentie nodig hebt voor meerdere machines.
    - Als een toepassing bijvoorbeeld twee virtuele SQL Server-machines en twee webservers heeft, moet u alleen de SQL Server-VM's aan een replicatiegroep toevoegen.
    - U ervoor kiezen om maximaal 16 VM's in een replicatiegroep te hebben.
    - Als u multi-VM-consistentie inschakelt, communiceren machines in de replicatiegroep met elkaar via poort 20004.
    - Zorg ervoor dat er geen firewalltoestel is dat de interne communicatie tussen de VM's over poort 20004 blokkeert.
    - Als u wilt dat Linux VM's deel uitmaken van een replicatiegroep, moet u ervoor zorgen dat het uitgaande verkeer op poort 20004 handmatig wordt geopend volgens richtlijnen voor de specifieke Linux-versie.
![Replicatie inschakelen](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klik **op Doelbron** > **maken Replicatie inschakelen**.
6. Nadat de VM's zijn ingeschakeld voor replicatie, u de status van de VM-status controleren onder **Gerepliceerde items**

>[!NOTE]
>Tijdens de eerste replicatie kan het enige tijd duren voordat de status is vernieuwd, zonder dat er vooruitgang is. Klik **op** de knop Vernieuwen om de laatste status te krijgen.
>

## <a name="next-steps"></a>Volgende stappen

[Meer informatie](site-recovery-test-failover-to-azure.md) over het uitvoeren van een testfailover.
