---
title: Over herstelplannen in Azure Site Recovery
description: Meer informatie over herstelplannen in Azure Site Recovery.
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: beb92bd62d011ef8aaf304dbb769e7694e6d7e60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257769"
---
# <a name="about-recovery-plans"></a>Over herstelplannen

In dit artikel vindt u een overzicht van herstelplannen in [Azure Site Recovery](site-recovery-overview.md).

Een herstelplan verzamelt machines in herstelgroepen met het oog op failover. Een herstelplan helpt u om een systematisch herstelproces te definiëren, door kleine onafhankelijke eenheden te maken die u mislukken. Een eenheid vertegenwoordigt doorgaans een app in uw omgeving.

- Een herstelplan definieert hoe machines mislukken en de volgorde waarin ze beginnen na failover.
- Herstelplannen worden gebruikt voor failover naar Azure, maar kunnen niet worden gebruikt voor failback vanuit Azure.
- Er kunnen maximaal 100 beveiligde exemplaren aan één herstelplan worden toegevoegd.
- U een plan aanpassen door er volgorde, instructies en taken aan toe te voegen.
- Nadat een plan is gedefinieerd, u er een failover uitvoeren.
- Machines kunnen worden verwezen in meerdere herstelplannen, waarin latere plannen de implementatie/het opstarten van een machine overslaan als deze eerder is geïmplementeerd met een ander herstelplan.



### <a name="why-use-a-recovery-plan"></a>Waarom een herstelplan gebruiken?

Herstelplannen gebruiken om:

* Modelleer een app rond de afhankelijkheden.
* Hersteltaken automatiseren om de doelstelling (RTO) te verminderen.
* Controleer of u voorbereid bent op migratie of herstel na noodgevallen door ervoor te zorgen dat uw apps deel uitmaken van een herstelplan.
* Testfailovers uitvoeren op herstelplannen om te zorgen dat herstel of migratie na noodgevallen werkt zoals verwacht.


## <a name="model-apps"></a>Apps modelleren 
U een herstelgroep plannen en maken om app-specifieke eigenschappen vast te leggen. Laten we bijvoorbeeld eens kijken naar een typische drielaagse toepassing met een SQL-server backend, middleware en een webfrontend. Doorgaans past u het herstelplan aan zodat machines in elke laag in de juiste volgorde starten na een failover.

- De SQL backend moet eerst beginnen, de middleware volgende, en ten slotte de web frontend.
- Deze startvolgorde zorgt ervoor dat de app werkt tegen de tijd dat de laatste machine wordt gestart.
- Deze volgorde zorgt ervoor dat wanneer de middleware wordt gestart en probeert verbinding te maken met de SQL Server-laag, de SQL Server-laag al wordt uitgevoerd. 
- Deze volgorde helpt er ook voor te zorgen dat de front-endserver als laatste start, zodat eindgebruikers geen verbinding maken met de URL van de app voordat alle onderdelen actief zijn en de app klaar is om aanvragen te accepteren.

Als u deze volgorde wilt maken, voegt u groepen toe aan de herstelgroep en voegt u machines toe aan de groepen.
- Wanneer de volgorde is opgegeven, wordt sequencing gebruikt. Acties lopen parallel, naar gelang van het geval, om de rto voor toepassingsherstel te verbeteren.
- Machines in één groep mislukken parallel.
- Machines in verschillende groepen mislukken in groepsvolgorde, zodat groep 2-machines hun failover pas starten nadat alle machines in groep 1 zijn mislukt en zijn gestart.

    ![Voorbeeld herstelplan](./media/recovery-plan-overview/rp.png)

Met deze aanpassing op zijn plaats, hier is wat er gebeurt als je een failover op het herstelplan: 

1. Met een afsluitstap wordt geprobeerd de on-premises machines uit te schakelen. De uitzondering is als u een testfailover uitvoert, in welk geval de primaire site blijft worden uitgevoerd. 
2. De shutdown activeert een parallelle failover van alle machines in het herstelplan.
3. De failover bereidt virtuele machineschijven voor met behulp van gerepliceerde gegevens.
4. De opstartgroepen worden in volgorde uitgevoerd en starten de machines in elke groep. Eerst loopt groep 1, dan groep 2 en tenslotte groep 3. Als er meer dan één machine in een groep is, beginnen alle machines parallel.


## <a name="automate-tasks-in-recovery-plans"></a>Taken in herstelplannen automatiseren

Het herstellen van grote toepassingen kan een complexe taak zijn. Handmatige stappen maken het proces foutgevoelig en de persoon die de failover uitvoert, is mogelijk niet op de hoogte van alle complexiteiten van apps. U een herstelplan gebruiken om order op te leggen en de acties te automatiseren die nodig zijn bij elke stap, met Azure Automation-runbooks voor failover naar Azure of scripts. Voor taken die niet kunnen worden geautomatiseerd, u pauzes voor handmatige acties invoegen in herstelplannen. Er zijn een paar soorten taken die u configureren:

* **Taken op de Azure VM na failover:** Wanneer u niet overgaat naar Azure, moet u doorgaans acties uitvoeren zodat u na failover verbinding maken met de VM. Bijvoorbeeld: 
    * Maak een openbaar IP-adres op de Azure VM.
    * Wijs een netwerkbeveiligingsgroep toe aan de netwerkadapter van de Azure VM.
    * Voeg een load balancer toe aan een beschikbaarheidsset.
* **Taken in VM na failover**: Deze taken configureren de app die op de machine wordt uitgevoerd doorgaans opnieuw, zodat deze correct blijft werken in de nieuwe omgeving. Bijvoorbeeld:
    * Wijzig de tekenreeks voor de databaseverbinding in de machine.
    * Wijzig de configuratie of regels van de webserver.


### <a name="run-a-test-failover-on-recovery-plans"></a>Een testfailover uitvoeren op herstelplannen

U een herstelplan gebruiken om een testfailover te activeren. Gebruik de volgende aanbevolen procedures:

- Voltooi altijd een testfailover op een app, voordat u een volledige failover uitvoert. Testfailovers helpen u om te controleren of de app op de herstelsite verschijnt.
- Als je merkt dat je iets hebt gemist, activeer dan een clean-up en voer de testfailover opnieuw uit. 
- Voer een testfailover meerdere keren uit, totdat u zeker weet dat de app soepel herstelt.
- Omdat elke app uniek is, moet u herstelplannen maken die voor elke toepassing zijn aangepast en een testfailover uitvoeren op elk.
- Apps en hun afhankelijkheden veranderen regelmatig. Voer elk kwartaal een testfailover uit voor elke app om ervoor te zorgen dat herstelplannen up-to-date zijn.

    ![Schermafbeelding van een voorbeeldtestherstelplan in Siteherstel](./media/recovery-plan-overview/rptest.png)

## <a name="watch-a-recovery-plan-video"></a>Bekijk een video van een herstelplan

Bekijk een snelle voorbeeldvideo met een failover op de muis voor een herstelplan voor een WordPress-app met twee lagen.
    
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]



## <a name="next-steps"></a>Volgende stappen

- [Maak](site-recovery-create-recovery-plans.md) een herstelplan.
- [Run](site-recovery-failover.md) failovers. 
