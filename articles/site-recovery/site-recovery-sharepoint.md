---
title: Herstel na noodgevallen voor een SharePoint-app met meerdere lagen met Azure Site Recovery
description: In dit artikel wordt beschreven hoe u noodherstel instelt voor een SharePoint-toepassing met meerdere lagen met azure siteherstelmogelijkheden.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 6/27/2019
ms.author: sutalasi
ms.openlocfilehash: d74e28ce470c23bbc8ee2081532a198c260ccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74706365"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sharepoint-application-for-disaster-recovery-using-azure-site-recovery"></a>Noodherstel instellen voor een SharePoint-toepassing met meerdere lagen voor noodherstel met Azure Site Recovery

In dit artikel wordt in detail beschreven hoe u een SharePoint-toepassing beveiligen met [Azure Site Recovery](site-recovery-overview.md).


## <a name="overview"></a>Overzicht

Microsoft SharePoint is een krachtige toepassing waarmee een groep of afdeling informatie kan organiseren, samenwerken en delen. SharePoint kan intranetportals, document- en bestandsbeheer, samenwerking, sociale netwerken, extranetten, websites, enterprise search en business intelligence bieden. Het heeft ook mogelijkheden voor systeemintegratie, procesintegratie en workflowautomatisering. Doorgaans beschouwen organisaties het als een Tier-1-toepassing die gevoelig is voor downtime en gegevensverlies.

Vandaag de dag biedt Microsoft SharePoint geen out-of-the-box disaster recovery-mogelijkheden. Ongeacht het type en de omvang van een ramp, herstel omvat het gebruik van een stand-by datacenter dat u herstellen van de boerderij naar. Stand-by datacenters zijn vereist voor scenario's waarin lokale redundante systemen en back-ups niet kunnen herstellen van de storing in het primaire datacenter.

Een goede oplossing voor noodherstel moet het modelleren van herstelplannen rond de complexe toepassingsarchitecturen zoals SharePoint mogelijk maken. Het moet ook de mogelijkheid hebben om aangepaste stappen toe te voegen om toepassingstoewijzingen tussen verschillende lagen te verwerken en dus een failover met één klik te bieden met een lagere RTO in het geval van een ramp.

In dit artikel wordt in detail beschreven hoe u een SharePoint-toepassing beveiligen met [Azure Site Recovery](site-recovery-overview.md). In dit artikel vindt u best practices voor het repliceren van een SharePoint-toepassing met drie lagen naar Azure, hoe u een noodhersteloefening doen en hoe u de toepassing naar Azure mislukken.

U de onderstaande video over het herstellen van een multi-tier toepassing naar Azure bekijken.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/Disaster-Recovery-of-load-balanced-multi-tier-applications-using-Azure-Site-Recovery/player]


## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint het volgende:

1. [Een virtuele machine repliceren naar Azure](site-recovery-vmware-to-azure.md)
2. Een [herstelnetwerk ontwerpen](site-recovery-network-design.md)
3. [Een testfailover naar Azure](site-recovery-test-failover-to-azure.md)
4. [Een failover naar Azure](site-recovery-failover.md)
5. Een [domeincontroller repliceren](site-recovery-active-directory.md)
6. SQL [Server repliceren](site-recovery-sql.md)

## <a name="sharepoint-architecture"></a>SharePoint-architectuur

SharePoint kan worden geïmplementeerd op een of meer servers met gelaagde topologieën en serverrollen om een farmontwerp te implementeren dat voldoet aan specifieke doelen en doelstellingen. Een typische grote SharePoint-serverfarm met hoge vraag die een groot aantal gelijktijdige gebruikers ondersteunt en een groot aantal inhoudsitems servicegroepering gebruiken als onderdeel van hun schaalbaarheidsstrategie. Deze aanpak omvat het uitvoeren van services op dedicated servers, het groeperen van deze services en vervolgens het schalen van de servers als een groep. De volgende topologie illustreert de service- en servergroepering voor een SharePoint-serverfarm met drie lagen. Raadpleeg SharePoint-documentatie en productlijnarchitecturen voor gedetailleerde richtlijnen over verschillende SharePoint-topologieën. Meer informatie over de implementatie van SharePoint 2013 vindt u in [dit document.](https://technet.microsoft.com/library/cc303422.aspx)



![Implementatiepatroon 1](./media/site-recovery-sharepoint/sharepointarch.png)


## <a name="site-recovery-support"></a>Ondersteuning voor Site Recovery

Site recovery is applicatie-agnostisch en moet werken met elke versie van SharePoint die op een ondersteunde machine wordt uitgevoerd. Voor het maken van dit artikel zijn virtuele VMware-machines met Windows Server 2012 R2 Enterprise gebruikt. Er zijn SharePoint 2013 Enterprise edition en SQL server 2014 Enterprise edition gebruikt.

### <a name="source-and-target"></a>Bron en doel

**Scenario** | **Op een secundaire site** | **Naar Azure**
--- | --- | ---
**Hyper-V** | Ja | Ja
**Vmware** | Ja | Ja
**Fysieke server** | Ja | Ja
**Azure** | N.v.t. | Ja


### <a name="things-to-keep-in-mind"></a>Zaken om rekening mee te houden

Als u een gedeeld schijfgebaseerd cluster gebruikt als elke laag in uw toepassing, u de replicatie van siteherstel niet gebruiken om deze virtuele machines te repliceren. U native replicatie gebruiken die door de toepassing wordt geleverd en vervolgens een [herstelplan](site-recovery-create-recovery-plans.md) gebruiken om alle lagen te mislukken.

## <a name="replicating-virtual-machines"></a>Virtuele machines repliceren

Volg [deze richtlijnen](site-recovery-vmware-to-azure.md) om te beginnen met het repliceren van de virtuele machine naar Azure.

* Zodra de replicatie is voltooid, moet u naar elke virtuele machine van elke laag gaan en dezelfde beschikbaarheidselecteren in 'Gerepliceerd item > Instellingen > Eigenschappen > Compute en Netwerk'. Als uw weblaag bijvoorbeeld 3 VM's heeft, moet u ervoor zorgen dat alle drie de VM's zijn geconfigureerd om deel uit te maken van dezelfde beschikbaarheiddie is ingesteld in Azure.

    ![Set beschikbaarheid](./media/site-recovery-sharepoint/select-av-set.png)

* Raadpleeg Active Directory en DNS beveiligen voor richtlijnen voor het beveiligen van Active Directory [en DNS.](site-recovery-active-directory.md)

* Raadpleeg [SQL Server-document beveiligen](site-recovery-sql.md) voor richtlijnen voor het beveiligen van databaselagen die op SQL-server worden uitgevoerd.

## <a name="networking-configuration"></a>Netwerkconfiguratie

### <a name="network-properties"></a>Netwerkeigenschappen

* Configureer voor de VM's van app- en weblagen netwerkinstellingen in Azure-portal, zodat de VM's na een failover aan het juiste DR-netwerk worden gekoppeld.

    ![Netwerk selecteren](./media/site-recovery-sharepoint/select-network.png)


* Als u een statisch IP-adres gebruikt, geeft u het IP op dat de virtuele machine moet gebruiken in het **doel-IP-veld**

    ![Statisch IP instellen](./media/site-recovery-sharepoint/set-static-ip.png)

### <a name="dns-and-traffic-routing"></a>DNS- en trafficrouting

Maak voor sites die op internet staan [een Traffic Manager-profiel van het type 'Prioriteit'](../traffic-manager/traffic-manager-create-profile.md) in het Azure-abonnement. Configureer vervolgens uw DNS- en Traffic Manager-profiel op de volgende manier.


| **Waar** | **Bron** | **Doel**|
| --- | --- | --- |
| Openbare DNS | Openbare DNS voor SharePoint-sites <br/><br/> Bijvoorbeeld sharepoint.contoso.com | Traffic Manager <br/><br/> contososharepoint.trafficmanager.net |
| On-premises DNS | sharepointonprem.contoso.com | Openbaar IP op de on-premises boerderij |


Maak in het profiel Traffic Manager [de primaire en hersteleindpunten](../traffic-manager/traffic-manager-configure-priority-routing-method.md). Gebruik het externe eindpunt voor on-premises eindpunt en openbaar IP-adres voor Azure-eindpunt. Zorg ervoor dat de prioriteit hoger is ingesteld op on-premises eindpunt.

Host een testpagina op een specifieke poort (bijvoorbeeld 800) in de SharePoint-weblaag, zodat Traffic Manager automatisch de failover van beschikbaarheid na failover kan detecteren. Dit is een tijdelijke oplossing voor het geval u geen anonieme verificatie op een van uw SharePoint-sites inschakelen.

[Configureer het profiel Traffic Manager](../traffic-manager/traffic-manager-configure-priority-routing-method.md) met de onderstaande instellingen.

* Routeringsmethode - 'Prioriteit'
* DNS time to live (TTL) - '30 seconden'
* Instellingen voor endpointmonitor - Als u anonieme verificatie inschakelen, u een specifiek eindpunt van de website opgeven. U ook een testpagina op een specifieke poort gebruiken (bijvoorbeeld 800).

## <a name="creating-a-recovery-plan"></a>Een herstelplan maken

Een herstelplan maakt het mogelijk om de failover van verschillende lagen in een multi-tier toepassing te rangschikken, vandaar, waardoor de consistentie van de toepassing behouden blijft. Volg de onderstaande stappen terwijl u een herstelplan maakt voor een multi-tier webtoepassing. [Meer informatie over het maken van een herstelplan](site-recovery-runbook-automation.md#customize-the-recovery-plan).

### <a name="adding-virtual-machines-to-failover-groups"></a>Virtuele machines toevoegen aan failovergroepen

1. Maak een herstelplan door de VM's van app en weblaag toe te voegen.
2. Klik op 'Aanpassen' om de VM's te groeperen. Standaard maken alle VM's deel uit van 'Groep 1'.

    ![RP aanpassen](./media/site-recovery-sharepoint/rp-groups.png)

3. Maak een andere groep (groep 2) en verplaats de vm's van het webniveau naar de nieuwe groep. Uw VM's voor de app-laag moeten deel uitmaken van 'Groep 1' en vm's van weblagen moeten deel uitmaken van 'Groep 2'. Dit is om ervoor te zorgen dat de VM's van de app-laag eerst worden opgestart, gevolgd door VM's met weblagen.


### <a name="adding-scripts-to-the-recovery-plan"></a>Scripts toevoegen aan het herstelplan

U de meest gebruikte Azure Site Recovery-scripts implementeren in uw Automatiseringsaccount en klik hieronder op de knop 'Implementeren naar Azure'. Wanneer u een gepubliceerd script gebruikt, moet u ervoor zorgen dat u de richtlijnen in het script volgt.

[![Implementeren naar Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Voeg een pre-action script toe aan 'Groep 1' aan failover SQL Availability group. Gebruik het script 'ASR-SQL-FailoverAG' dat is gepubliceerd in de voorbeeldscripts. Zorg ervoor dat u de richtlijnen in het script volgt en breng de vereiste wijzigingen in het script op de juiste manier aan.

    ![Add-AG-Script-Stap-1](./media/site-recovery-sharepoint/add-ag-script-step1.png)

    ![Add-AG-Script-Stap-2](./media/site-recovery-sharepoint/add-ag-script-step2.png)

2. Voeg een post-actiescript toe om een load balancer toe te voegen aan de mislukte virtuele machines van weblaag (groep 2). Gebruik het script 'ASR-AddSingleLoadBalancer' dat is gepubliceerd in de voorbeeldscripts. Zorg ervoor dat u de richtlijnen in het script volgt en breng de vereiste wijzigingen in het script op de juiste manier aan.

    ![Invoeg-LB-Script-Stap-1](./media/site-recovery-sharepoint/add-lb-script-step1.png)

    ![Add-LB-Script-Stap-2](./media/site-recovery-sharepoint/add-lb-script-step2.png)

3. Voeg een handmatige stap toe om de DNS-records bij te werken om naar de nieuwe farm in Azure te wijzen.

    * Voor sites die op internet worden geconfronteerd, zijn er geen DNS-updates vereist na failover. Volg de stappen die zijn beschreven in de sectie 'Netwerkrichtlijnen' om Traffic Manager te configureren. Als het Traffic Manager-profiel is ingesteld zoals beschreven in de vorige sectie, voegt u een script toe om dummypoort (800 in het voorbeeld) te openen op de Azure VM.

    * Voor sites met interne aan-ogen voegt u een handmatige stap toe om de DNS-record bij te werken om het IP-IP van de nieuwe weblaag-VM aan te wijzen.

4. Voeg een handmatige stap toe om de zoektoepassing te herstellen vanuit een back-up of een nieuwe zoekservice te starten.

5. Voer onderstaande stappen uit voor het herstellen van de zoekservicetoepassing vanuit een back-up.

    * Deze methode gaat ervan uit dat een back-up van de zoekservicetoepassing is uitgevoerd vóór de catastrofale gebeurtenis en dat de back-up beschikbaar is op de DR-site.
    * Dit kan eenvoudig worden bereikt door het plannen van de back-up (bijvoorbeeld eenmaal per dag) en het gebruik van een kopieerprocedure om de back-up op de DR-site te plaatsen. Kopieerprocedures kunnen gescripte programma's zoals AzCopy (Azure Copy) of het instellen van DFSR (Distributed File Services Replication) omvatten.
    * Nu de SharePoint-farm wordt uitgevoerd, navigeert u door het centrale beheer, 'Back-up en herstel' en selecteert u Herstellen. Het herstel ondervraagt de opgegeven back-uplocatie (mogelijk moet u de waarde bijwerken). Selecteer de back-up van de zoekservicetoepassing die u wilt herstellen.
    * Zoeken wordt hersteld. Houd er rekening mee dat het herstel verwacht dezelfde topologie (hetzelfde aantal servers) en dezelfde harde schijf letters toegewezen aan die servers te vinden. Zie ['Zoekservicetoepassing herstellen in SharePoint 2013'](https://technet.microsoft.com/library/ee748654.aspx) voor meer informatie.


6. Volg hieronder stappen om te beginnen met een nieuwe zoekservicetoepassing.

    * Deze methode gaat ervan uit dat een back-up van de database 'Zoekbeheer' beschikbaar is op de DR-site.
    * Aangezien de andere databases van de zoekservicetoepassing niet worden gerepliceerd, moeten ze opnieuw worden gemaakt. Ga hiervoor naar Centraal beheer en verwijdert de toepassing Zoekservice. Verwijder de indexbestanden op servers die de zoekindex hosten.
    * Maak de zoekservicetoepassing opnieuw en hiermee worden de databases opnieuw gemaakt. Het wordt aanbevolen om een voorbereid script te hebben dat deze servicetoepassing opnieuw maakt, omdat het niet mogelijk is om alle acties via de GUI uit te voeren. Het instellen van de locatie van het indexstation en het configureren van de zoektopologie is bijvoorbeeld alleen mogelijk met SharePoint PowerShell-cmdlets. Gebruik de Windows PowerShell-cmdlet Restore-SPEnterpriseSearchServiceApplication en geef de database voor het beheer van logboeken en gerepliceerde Search_Service__DB op. Deze cmdlet geeft de zoekconfiguratie, het schema, de beheerde eigenschappen, regels en bronnen en maakt een standaardset van de andere componenten.
    * Zodra de zoekservicetoepassing opnieuw is gemaakt, moet u een volledige verkenning starten voor elke inhoudsbron om de zoekservice te herstellen. U verliest bepaalde analyse-informatie van de on-premises farm, zoals zoekaanbevelingen.

7. Zodra alle stappen zijn voltooid, slaat u het herstelplan op en ziet het uiteindelijke herstelplan er als volgt uit.

    ![Opgeslagen RP](./media/site-recovery-sharepoint/saved-rp.png)

## <a name="doing-a-test-failover"></a>Het doen van een test failover
Volg [deze richtlijnen](site-recovery-test-failover-to-azure.md) om een testfailover te doen.

1.  Ga naar Azure portal en selecteer uw Vault Recovery Service.
2.  Klik op het herstelplan dat is gemaakt voor SharePoint-toepassing.
3.  Klik op 'Test Failover'.
4.  Selecteer herstelpunt en het virtuele Azure-netwerk om het failoverproces te starten.
5.  Zodra de secundaire omgeving is up, u uw validaties uitvoeren.
6.  Zodra de validaties zijn voltooid, u op 'Cleanup test failover' op het herstelplan klikken en wordt de failover-omgeving van de test gereinigd.

Raadpleeg [failoveroverwegingen voor AD- en DNS-indelingen voor](site-recovery-active-directory.md#test-failover-considerations) richtlijnen voor het doen van testfailover.

Raadpleeg voor richtlijnen voor het uitvoeren van testfailover voor SQL Always ON-beschikbaarheidsgroepen [naar Het uitvoeren van application DR met Azure Site Recovery en het uitvoeren van testfailoverdocument.](site-recovery-sql.md#disaster-recovery-of-an-application)

## <a name="doing-a-failover"></a>Het doen van een failover
Volg [deze richtlijnen](site-recovery-failover.md) voor het doen van een failover.

1.  Ga naar Azure-portal en selecteer uw vault voor Herstelservices.
2.  Klik op het herstelplan dat is gemaakt voor SharePoint-toepassing.
3.  Klik op 'Failover'.
4.  Selecteer herstelpunt om het failoverproces te starten.

## <a name="next-steps"></a>Volgende stappen
U meer informatie vinden over [het repliceren van andere toepassingen](site-recovery-workload.md) met siteherstel.
