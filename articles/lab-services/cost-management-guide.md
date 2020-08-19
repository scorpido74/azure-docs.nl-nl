---
title: Gids voor kosten beheer voor Azure Lab Services
description: Meer informatie over de verschillende manieren om kosten voor Lab-services weer te geven.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 98ce4d5e82d65d911984dc45615253ddcae33ae1
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589852"
---
# <a name="cost-management-for-azure-lab-services"></a>Kosten beheer voor Azure Lab Services

Kosten beheer kan worden opgesplitst in twee verschillende gebieden: kosten raming en kosten analyse.  Kosten raming treedt op bij het instellen van de test omgeving om ervoor te zorgen dat de oorspronkelijke structuur van het lab binnen het verwachte budget past.  Kosten analyse vindt meestal plaats aan het einde van de maand om de kosten te analyseren en de vereiste acties voor de volgende maand te bepalen.

## <a name="estimate-the-lab-costs"></a>De kosten van het lab schatten

Elk lab-dash board bevat een **kosten & facturerings** sectie waarin een ruwe schatting wordt opgenomen van wat het lab voor de maand kost.  De schatting van de kosten geeft een overzicht van het uurverbruik met het maximum aantal gebruikers op basis van de geschatte kosten per uur.  Voor de meest nauw keurige schatting is het instellen van het lab, met inbegrip van het [schema](how-to-create-schedules.md), en de geschatte kosten in het dash board.  

Deze schatting mag niet alle mogelijke kosten zijn, maar er zijn enkele resources die niet zijn opgenomen.  De kosten voor de sjabloon voorbereiding zijn niet in de kosten raming opgenomen.  Dit kan aanzienlijk variëren in de tijd die nodig is om de sjabloon te maken. De kosten voor het uitvoeren van de sjabloon zijn hetzelfde als de totale Lab-kosten per uur. Alle kosten voor de [Galerie met gedeelde afbeeldingen](how-to-use-shared-image-gallery.md) zijn niet opgenomen in het lab-dash board omdat een galerie kan worden gedeeld tussen meerdere lessen.  Tot slot worden uren die worden gemaakt wanneer de Lab-Maker een computer start, uitgesloten van deze schatting.

> [!div class="mx-imgBorder"]
> ![Schatting van de dashboard kosten](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Gebruik van vorige maanden analyseren

De kosten analyse is voor het controleren van het gebruik van de vorige maanden om eventuele aanpassingen voor het lab te bepalen.  De uitsplitsing van de kosten in het verleden vindt u in de [kosten analyse](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis)van het abonnement.  In de Azure Portal kunt u ' Abonnementen ' typen in het bovenste zoek veld en vervolgens de optie abonnementen selecteren.  

> [!div class="mx-imgBorder"]
> ![Zoeken in abonnementen](./media/cost-management-guide/subscription-search.png)

Selecteer het specifieke abonnement dat moet worden beoordeeld.

> [!div class="mx-imgBorder"]
> ![Selectie van abonnementen](./media/cost-management-guide/subscription-select.png)

 Selecteer kosten analyse in het linkerdeel venster onder **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Kosten analyse van abonnement](./media/cost-management-guide/subscription-cost-analysis.png)

Dit dash board biedt gedetailleerde kosten analyse, waaronder de mogelijkheid om te exporteren naar verschillende bestands typen volgens een schema.  De Cost Management heeft talloze mogelijkheden voor meer informatie, Zie [Cost Management facturering-overzicht](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filteren op het resource type: `microsoft.labservices/labaccounts` geeft alleen de kosten weer die zijn gekoppeld aan de test services.

## <a name="understand-the-usage"></a>Inzicht in het gebruik

Hieronder ziet u een voor beeld van de kosten analyse.

> [!div class="mx-imgBorder"]
> ![Kosten analyse van abonnement](./media/cost-management-guide/cost-analysis.png)

Standaard zijn er zes kolommen: resource, resource type, locatie, naam van resource groep, tags, kosten.  De kolom resource bevat informatie over het lab-account, de naam van het lab en de virtuele machine.  De rijen met een Lab-account/Lab-naam/standaard waarde zijn de kosten voor het lab, die kunnen worden weer gegeven op de tweede en derde rij.  De gebruikte Vm's hebben kosten voor de naam van het lab-account/Lab-naam/standaard/VM.  In dit voor beeld geeft de eerste rij met de tweede rij, die begint met ' aaalab/dockerlab ', de totale kosten voor het Lab ' dockerlab ' in het lab-account ' aaalab '.

Als u informatie over de galerie met gedeelde afbeeldingen wilt ophalen, wijzigt u het resource type in `Microsoft.Compute/Galleries` . Dit geeft u de totale kosten voor de galerie met installatie kopieën.  De galerij afbeeldings galerieën worden mogelijk niet weer gegeven in de kosten, afhankelijk van waar de galerie is opgeslagen.

> [!NOTE]
> Galerie met gedeelde afbeeldingen is verbonden met het lab-account.  Dit betekent dat meerdere Labs dezelfde installatie kopie zou kunnen gebruiken.

## <a name="separating-costs"></a>Kosten scheiden

Sommige universiteiten hebben het lab-account en de resource groep gebruikt als manieren om de verschillende klassen van elkaar te scheiden.  Elke klasse heeft een eigen lab-account en resource groep. Voeg in het deel venster kosten analyse een filter toe op basis van de naam van de resource groep met de juiste naam voor de resource groep voor de klasse, en alleen de kosten voor die klasse zijn zichtbaar.  Hierdoor is het mogelijk om de verschillende klassen duidelijker te betrekken bij het bekijken van de kosten.  Met de functie voor [geplande export](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) van de kosten analyse kunnen de kosten van elke klasse in afzonderlijke bestanden worden gedownload.

## <a name="managing-costs"></a>Kosten beheren

Afhankelijk van het type klasse, zijn er manieren om de kosten te beheren om ervoor te zorgen dat de virtuele machines worden uitgevoerd zonder een student met behulp van de machine.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Kosten beheer maximaliseren met instellingen voor automatisch afsluiten

Met de functies voor kosten beheer met automatisch afsluiten proactief kunt u voor komen dat de gebruiks uren van virtuele machines in de Labs worden verspild. De combi natie van de volgende drie functies voor automatisch afsluiten en verbreken van de meeste gevallen, waarbij gebruikers per ongeluk hun virtuele machines verlaten:

> [!div class="mx-imgBorder"]
> ![Kosten analyse van abonnement](./media/cost-management-guide/auto-shutdown-disconnect.png)

Deze instellingen kunnen worden geconfigureerd op het niveau van het lab-account en het lab-niveau. Als de instellingen zijn ingeschakeld op het niveau van het lab-account, worden deze toegepast op alle Labs binnen het lab-account. Deze instellingen zijn standaard ingeschakeld voor alle nieuwe lab-accounts. 

#### <a name="details-about-auto-shutdown-settings"></a>Details over instellingen voor automatisch afsluiten

* Automatisch de verbinding van gebruikers met virtuele machines die het besturings systeem inactief acht (alleen Windows) verbreken.

    > [!NOTE]
    > Deze instelling is alleen beschikbaar voor virtuele Windows-machines.

    Wanneer de instelling is ingeschakeld, wordt de verbinding van de gebruiker met alle computers in het lab verbroken wanneer het Windows-besturings systeem de sessie wordt gebruikt om niet-actief te zijn (inclusief de virtuele machine van de sjabloon). [Windows-besturingssysteem definitie niet actief](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) gebruikt twee criteria: 

    * Verzuim van gebruiker – geen toetsen bord-of muis invoer.
    * Gebrek aan Resource verbruik: alle processors en alle schijven zijn gedurende een bepaald percentage tijd niet actief

    Gebruikers zien een bericht zoals dit in de virtuele machine voordat ze worden losgekoppeld: 

    > [!div class="mx-imgBorder"]
    > ![Kosten analyse van abonnement](./media/cost-management-guide/idle-timer-expired.png)
    
    De virtuele machine wordt nog steeds uitgevoerd wanneer de verbinding van de gebruiker wordt verbroken. Als de gebruiker opnieuw verbinding maakt met de virtuele machine door zich aan te melden, worden er nog steeds Windows-of bestanden geopend of niet-opgeslagen werk dat eerder aan de verbinding is gemaakt. Omdat de virtuele machine wordt uitgevoerd in deze status, telt deze nog steeds als actief en worden kosten in rekening gebracht. 
    
    Als u de niet-actieve virtuele Windows-machines automatisch wilt afsluiten, gebruikt u de combi natie van **gebruikers loskoppelen wanneer virtuele machines inactief zijn** en **virtuele machines uitschakelen wanneer gebruikers** instellingen van de verbinding verbreken.

    Als u de instellingen bijvoorbeeld als volgt configureert:
    
    * Gebruikers de verbinding verbreken als de virtuele machines niet actief zijn: 15 minuten nadat de status niet-actief is gedetecteerd.
    * Virtuele machines uitschakelen wanneer gebruikers de verbinding verbreken – 5 minuten nadat de gebruiker de verbindingen heeft verbroken.
    
    De virtuele Windows-machines worden 20 minuten na het stoppen van de gebruiker automatisch afgesloten. 
    
    > [!div class="mx-imgBorder"]
    > ![Kosten analyse van abonnement](./media/cost-management-guide/vm-idle-diagram.png)
* Virtuele machines automatisch uitschakelen wanneer gebruikers de verbinding verbreken (Windows & Linux).
    
    Deze instelling ondersteunt virtuele Windows-en Linux-machines. Als deze instelling is ingeschakeld, wordt automatisch afsluiten uitgevoerd wanneer:
    
    * Voor de Windows-, Extern bureaublad-verbinding (RDP) wordt de verbinding verbroken.
    * Voor Linux is de SSH-verbinding verbroken.
    
    > [!NOTE]
    > Alleen [specifieke distributies en versies van Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) worden ondersteund.
    
    U kunt opgeven hoe lang de virtuele machines moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten. 
* Virtuele machines die zijn gestart automatisch afsluiten, maar gebruikers maken geen verbinding.
     
    Binnen een Lab kan een gebruiker een virtuele machine starten, maar er geen verbinding mee maken. Bijvoorbeeld:
    
    * Met een planning in het lab worden alle virtuele machines voor een klasse-sessie gestart, maar sommige studenten worden niet weer gegeven en er wordt geen verbinding gemaakt met hun machines.  
    * Een gebruiker start een virtuele machine, maar vergeet niet om verbinding te maken. 
    
    Met de instelling virtuele machines uitschakelen wanneer gebruikers geen verbinding maken, worden deze gevallen onderschept en automatisch de virtuele machines afgesloten.  
    
Raadpleeg de volgende artikelen voor meer informatie over het configureren en inschakelen van automatisch afsluiten van Vm's bij het verbreken van de verbinding:

* [Automatisch afsluiten van Vm's configureren bij het verbreken van de instelling voor een Lab-account](how-to-configure-lab-accounts.md)
* [Automatisch afsluiten van Vm's inschakelen bij het verbreken van de verbinding](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Quotum versus geplande tijd

Met de [quota tijd](classroom-labs-concepts.md#quota) versus de [geplande tijd](classroom-labs-concepts.md#schedules) kan de eigenaar van het lab het lab zo configureren dat het beter aansluit op de behoeften van de professor en de studenten.  Geplande tijd is een periode waarin alle student-Vm's zijn gestart en beschikbaar zijn om verbinding te maken met.  Normaal gesp roken wordt gepland in de situatie waarbij alle studenten hun eigen virtuele machine hebben en de aanwijzingen van de professor op een bepaalde tijd worden uitgevoerd gedurende de dag zoals class uren.  Het nadeel is dat alle student-Vm's worden gestart en dat ze kosten in beslag nemen, zelfs als een student zich niet bij de virtuele machine aanmeldt.  Quota tijd is de tijd die aan elke student wordt toegewezen en die kan worden gebruikt voor een onafhankelijke studie. De Vm's worden niet gestart totdat de student de virtuele machine start.  

Een Lab kan gebruikmaken van de quota tijd, de geplande tijd of een combi natie van beide. Als een klasse niet de geplande tijd nodig heeft, gebruikt u alleen de quota tijd voor het meest effectief gebruik van de Vm's.

### <a name="scheduled-event---stop-only"></a>Geplande gebeurtenis: alleen stoppen

In de planning kunt u het gebeurtenis type alleen stoppen toevoegen, waardoor alle computers op een specifiek tijdstip worden gestopt.  Sommige Lab-eigen aren hebben voor elke dag om middernacht een gebeurtenis voor alleen stoppen ingesteld om de kosten en het quotum gebruik te reduceren wanneer een student de virtuele machine sluit die ze gebruiken.  Het nadeel van dit type gebeurtenis is dat alle Vm's worden afgesloten, zelfs als de student de virtuele machine gebruikt.

### <a name="other-costs-related-to-labs"></a>Andere kosten met betrekking tot Labs 

Er zijn kosten die niet worden meegenomen in de test services, maar die kunnen worden gekoppeld aan een Lab-service.  Een galerie met gedeelde installatie kopieën kan worden verbonden met Labs, maar wordt niet weer gegeven onder de kosten van de Lab-Services en heeft kosten.  Verwijder ongebruikte installatie kopieën uit de galerie als de installatie kopieën de opslag kosten overnemen om de totale kosten te verlagen.  Labs kan verbinding maken met andere Azure-bronnen door een virtueel netwerk (VNet) wanneer een lab wordt verwijderd. u moet het VNet en de andere bronnen verwijderen.

## <a name="conclusion"></a>Conclusie

De bovenstaande informatie biedt u een beter inzicht in de gebruiks kosten en hoe u de hulpprogram ma's kunt gebruiken om overtollige kosten te verminderen.
