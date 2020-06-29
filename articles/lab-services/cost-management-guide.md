---
title: Gids voor kosten beheer voor Azure Lab Services
description: Meer informatie over de verschillende manieren om kosten voor Lab-services weer te geven.
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 74ba70139781ed854d3ad898a9c65ef70c0ba99b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85445301"
---
# <a name="cost-management-for-azure-lab-services"></a>Kosten beheer voor Azure Lab Services

Kosten beheer kan worden opgesplitst in twee verschillende gebieden: kosten raming en kosten analyse.  Kosten raming treedt op bij het instellen van de test omgeving om ervoor te zorgen dat de oorspronkelijke structuur van het lab binnen het verwachte budget past.  Kosten analyse vindt meestal plaats aan het einde van de maand om de kosten te analyseren en de vereiste acties voor de volgende maand te bepalen.

## <a name="estimating-the-lab-costs"></a>De kosten van het lab schatten

Elk lab-dash board bevat een **kosten & facturerings** sectie waarin een ruwe schatting wordt opgenomen van wat het lab voor de maand kost.  De schatting van de kosten geeft een overzicht van het uurverbruik met het maximum aantal gebruikers op basis van de geschatte kosten per uur.  Voor de meest nauw keurige schatting is het instellen van het lab, met inbegrip van het [schema](how-to-create-schedules.md), en de geschatte kosten in het dash board.  

Deze schatting mag niet alle mogelijke kosten zijn, maar er zijn enkele resources die niet zijn opgenomen.  De kosten voor de sjabloon voorbereiding zijn niet in de kosten raming opgenomen.  Dit kan aanzienlijk variëren in de tijd die nodig is om de sjabloon te maken. De kosten voor het uitvoeren van de sjabloon zijn hetzelfde als de totale Lab-kosten per uur. Alle kosten voor de [Galerie met gedeelde afbeeldingen](how-to-use-shared-image-gallery.md) zijn niet opgenomen in het lab-dash board omdat een galerie kan worden gedeeld tussen meerdere lessen.  Tot slot worden uren die worden gemaakt wanneer de Lab-Maker een computer start, uitgesloten van deze schatting.

> [!div class="mx-imgBorder"]
> ![Schatting van de dashboard kosten](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>Het gebruik van eerdere maanden analyseren

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

## <a name="understanding-the-usage"></a>Meer informatie over het gebruik

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

### <a name="auto-shutdown-on-disconnect"></a>Automatisch afsluiten bij het verbreken van verbinding

Bij het maken van het Lab kan de eigenaar van het lab de virtuele machines in het lab zo instellen dat deze worden [afgesloten wanneer de verbinding met de RDP-verbinding met de virtuele machine wordt verbroken](how-to-enable-shutdown-disconnect.md).  Deze instelling vermindert het scenario waarbij de student de verbinding verbreekt, maar vergeet niet om de virtuele machine te stoppen.

### <a name="quota-vs-scheduled-time"></a>Quotum versus geplande tijd

Met de [quota tijd](classroom-labs-concepts.md#quota) versus de [geplande tijd](classroom-labs-concepts.md#schedules) kan de eigenaar van het lab het lab zo configureren dat het beter aansluit op de behoeften van de professor en de studenten.  Geplande tijd is een periode waarin alle student-Vm's zijn gestart en beschikbaar zijn om verbinding te maken met.  Normaal gesp roken wordt gepland in de situatie waarbij alle studenten hun eigen virtuele machine hebben en de aanwijzingen van de professor op een bepaalde tijd worden uitgevoerd gedurende de dag zoals class uren.  Het nadeel is dat alle student-Vm's worden gestart en dat ze kosten in beslag nemen, zelfs als een student zich niet bij de virtuele machine aanmeldt.  Quota tijd is de tijd die aan elke student wordt toegewezen en die kan worden gebruikt voor een onafhankelijke studie. De Vm's worden niet gestart totdat de student de virtuele machine start.  

Een Lab kan gebruikmaken van de quota tijd, de geplande tijd of een combi natie van beide. Als een klasse niet de geplande tijd nodig heeft, gebruikt u alleen de quota tijd voor het meest effectief gebruik van de Vm's.

### <a name="scheduled-event---stop-only"></a>Geplande gebeurtenis: alleen stoppen

In de planning kunt u het gebeurtenis type alleen stoppen toevoegen, waardoor alle computers op een specifiek tijdstip worden gestopt.  Sommige Lab-eigen aren hebben voor elke dag om middernacht een gebeurtenis voor alleen stoppen ingesteld om de kosten en het quotum gebruik te reduceren wanneer een student de virtuele machine sluit die ze gebruiken.  Het nadeel van dit type gebeurtenis is dat alle Vm's worden afgesloten, zelfs als de student de virtuele machine gebruikt.

### <a name="other-costs-related-to-labs"></a>Andere kosten met betrekking tot Labs 

Er zijn kosten die niet worden meegenomen in de test services, maar die kunnen worden gekoppeld aan een Lab-service.  Een galerie met gedeelde installatie kopieën kan worden verbonden met Labs, maar wordt niet weer gegeven onder de kosten van de Lab-Services en heeft kosten.  Verwijder ongebruikte installatie kopieën uit de galerie als de installatie kopieën de opslag kosten overnemen om de totale kosten te verlagen.  Labs kan verbinding maken met andere Azure-bronnen door een virtueel netwerk (VNet) wanneer een lab wordt verwijderd. u moet het VNet en de andere bronnen verwijderen.

## <a name="conclusion"></a>Conclusie

De bovenstaande informatie biedt u een beter inzicht in de gebruiks kosten en hoe u de hulpprogram ma's kunt gebruiken om overtollige kosten te verminderen.
