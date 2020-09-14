---
title: Azure-workloads optimaliseren met Advisor-Score
description: Advisor-Score gebruiken om optimaal te profiteren van Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 720a2b358e35d776a7233452eee2bd69b521654f
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056231"
---
# <a name="optimize-azure-workloads-using-advisor-score"></a>Azure-workloads optimaliseren met Advisor-Score

## <a name="introduction-to-advisor-score"></a>Inleiding tot Advisor-Score

Azure Advisor biedt best practice aanbevelingen voor uw workloads. Deze aanbevelingen zijn persoonlijk en kunnen actie ondernemen om u te helpen:
* Verbeter de postuur van uw workloads en Optimaliseer uw Azure-implementaties
* Proactief voor komen van belangrijkste problemen door aanbevolen procedures te volgen
* Evalueer uw Azure-workloads met de vijf pijlers van het [Microsoft Azure goed ontworpen Framework](https://docs.microsoft.com/azure/architecture/framework/)

Als kern functie van Advisor wordt de **Advisor-Score** ontwikkeld om u te helpen deze doel stellingen effectief en efficiënt te bereiken. 

Om optimaal te profiteren van Azure, is het belang rijk om te begrijpen waar u zich bevindt in de optimalisatie van uw workload, welke services/resources goed worden verbruikt en wat niet. Daarnaast wilt u weten hoe u uw acties op basis van aanbevelingen kunt priori teren om het resultaat te maximaliseren. Het is ook belang rijk om de voortgang bij te houden en te rapporteren die u in deze optimalisatie reis gaat maken. Met **Advisor Score**kunt u eenvoudig al deze dingen doen met onze nieuwe gamificatietoepassing-ervaring. Als uw persoonlijke Cloud consultant Azure Advisor uw telemetrie van het gebruik en de resource configuratie doorlopend beoordelen om te controleren op best practices voor de branche. Advisor integreert de bevindingen vervolgens in één enkele score, zodat u in één oogopslag kunt zien of u de benodigde stappen neemt om betrouw bare, veilige en kostenbesparende oplossingen te bouwen. De Advisor-Score bestaat uit een algemene score die verder kan worden opgesplitst in vijf categorie scores, één voor elke categorie van Azure Advisor die de vijf pijlers van het goed ontworpen kader vertegenwoordigt. U kunt de voortgang volgen die u in de loop van de tijd maakt door uw totale score en categorie score te bekijken met dagelijkse, wekelijkse en maandelijkse trend, en u kunt benchmarks instellen om u te helpen bij het bereiken van uw doel stellingen. 

 ![Advisor-Score ervaring](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Advisor-Score gebruiken
Advisor geeft uw totale Advisor-Score en uitsplitsing voor Advisor-categorieën weer, in procenten. Een Score van 100% in een categorie betekent dat al uw resources die worden beoordeeld door Advisor, voldoen aan de aanbevolen procedures die door Advisor worden aanbevolen. Aan het andere uiteinde van het spectrum betekent een Score van 0% dat geen van uw resources die zijn beoordeeld door Advisor de aanbevelingen van Advisor volgen. Met behulp van deze score korrels kunt u de onderstaande stroom eenvoudig bereiken:
* **Advisor-Score** waarmee u de basis lijn van uw workload/abonnementen kunt bepalen op basis van de Advisor-Score. U kunt ook de historische trends zien om inzicht te krijgen in wat uw trend is.
* De **scores van Advisor categories** om u te helpen begrijpen welke categorieën meer aandacht nodig hebben en u de prioriteit kunt geven
* **Mogelijke toename** van de Score van elke aanbeveling om u te helpen prioriteiten te geven voor uw herstel acties voor elke categorie

De bijdrage van elke aanbeveling aan uw score wordt duidelijk weer gegeven op de pagina overzicht in de Azure Portal. U kunt uw Score verhogen door de aanbevolen procedures te gebruiken en u kunt een prioriteit geven aan de aanbevelingen die de grootste **potentiële toename** van de score hebben om de snelste voortgang te maken met de tijd die u hebt.  

![Impact van Advisor-Score](./media/advisor-score-2.png)

Omdat de beoordelings methodologie van Advisor een extra gewicht toepast op meer dure resources met lange permanente aanbevelingen, kunt u de meeste voortgang doen door de resources eerst te herstellen met de hoogste kosten voor de verkoop. Als een van de Advisor-aanbevelingen niet relevant is voor een afzonderlijke resource, kunt u deze aanbevelingen sluiten om ze uit te sluiten van de score berekening en feedback sturen naar Advisor om de aanbevelingen te verbeteren. 

## <a name="how-is-advisor-score-calculated"></a>Hoe wordt Advisor score berekend?
In Advisor worden de scores van uw categorie en uw totale Advisor-score als percentages weer gegeven. Een Score van 100% in een categorie betekent dat al uw resources, *beoordeeld door Advisor*, voldoen aan de aanbevolen procedures die door Advisor worden aanbevolen. Aan het andere uiteinde van het spectrum betekent een Score van 0% dat geen van uw resources, beoordeeld door Advisor, de aanbevelingen van Advisor volgen. 
**Elk van de vijf categorieën heeft een hoogst mogelijke Score van 100.** Uw totale Advisor-score wordt berekend als een som van elke toepasselijke categorie Score, gedeeld door de som van de hoogst mogelijke Score van alle toepasselijke categorieën. Voor de meeste abonnementen betekent Advisor dat de Score van elke categorie wordt toegevoegd en gedeeld door 500. **Elke categorie score wordt echter alleen berekend als u resources gebruikt die door Advisor zijn beoordeeld.**

### <a name="scoring-methodology"></a>Score methodologie: 
De berekening van de Advisor-Score kan in vier stappen worden samenvatten:
1. Advisor berekent de **dagelijkse verkoop kosten van betrokken resources**, die de resources zijn van uw abonnementen die ten minste één aanbeveling hebben in Advisor.
2. Advisor berekent de **dagelijkse verkoop kosten van de geraamde resources**, die de resources zijn die door Advisor worden bewaakt, of ze al dan niet een aanbeveling hebben. 
3. Voor elk type aanbeveling berekent Advisor de **goede resource verhouding**. Dit zijn de kosten van betrokken resources gedeeld door de kosten van de geraamde resources.
4. Advisor past drie extra gewichten toe op de goede resource verhouding van elke categorie:
* Aanbevelingen met meer impact worden gewogen zwaarder dan die met een lagere impact.
* Resources met lange, specifieke aanbevelingen tellen meer bij aan uw score.
* Resources die u in Advisor negeert, worden volledig verwijderd uit uw score berekening. 
    
Advisor past dit model op een niveau van de Advisor toe (beveiliging maakt gebruik van een [beveiligd Score](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) model), waarbij de Score van een adviseur Advisor voor elke categorie wordt toegepast en een eenvoudig gemiddelde het resultaat van de laatste Advisor.


## <a name="advisor-score-faq"></a>Veelgestelde vragen over Advisor-Score
* **Hoe vaak is mijn score vernieuwd?**
Uw score wordt minstens eenmaal per dag vernieuwd. 
* **Moet ik de aanbevelingen in Advisor bekijken om punten voor mijn score op te halen?**
Nee. Uw score geeft aan of u aanbevolen procedures voor Advisor aanbeveelt, zelfs als u deze aanbevelingen nooit in Advisor bekijkt en aanbevolen procedures proactief doorneemt.  
* **Hoe Bereken Advisor de dagelijkse verkoop kosten van resources voor een abonnement?**
Advisor gebruikt de tarieven voor *betalen naar* gebruik die worden gepubliceerd op de pagina met prijzen voor Azure.com, die geen toepasselijke kortingen weer spie gelen, vermenigvuldigd met de hoeveelheid gebruik op de laatste dag dat de resource is toegewezen. Als u de kortingen weglaat van de berekening van de resource kosten, wordt Advisor-Score vergeleken in abonnementen, tenants en inschrijvingen waarbij kortingen kunnen variëren. 
* **Wat moet ik doen als een aanbeveling niet relevant is?**
Als u een aanbeveling van Advisor negeert, wordt deze wegge laten uit de berekening van uw score. Het negeren van aanbevelingen kan ook helpen Advisor de kwaliteit van de aanbevelingen te verbeteren.
* **Waarom is mijn score gewijzigd?** U kunt de score wijzigen als u betrokken resources herstelt door de aanbevolen procedures voor Advisor te nemen. Als u of iemand met machtigingen voor uw abonnement nieuwe resources heeft gewijzigd of gemaakt, ziet u mogelijk ook schommelingen in uw score omdat uw score is gebaseerd op een verhouding van de kosten die invloed hebben op de resources ten opzichte van de totale kosten van alle resources.
* **Is mijn score afhankelijk van wat ik besteed aan Azure?**
Nee. De score is ontworpen voor het beheren van de grootte van een combi natie van een abonnement en service. 
* **Maakt de Score methodologie onderscheid tussen werk belastingen voor productie en ontwikkelings testen?**
Nee, maar u kunt aanbevelingen voor afzonderlijke resources negeren als deze resources worden gebruikt voor ontwikkeling en testen en de aanbeveling niet van toepassing is.
* **Kan ik de scores van een abonnement met 100 resources en een abonnement met 100.000 resources vergelijken?**
De Score methodologie is ontworpen voor het beheren van het aantal resources voor een combi natie van abonnementen en services, zodat abonnementen met minder resources een hogere of lagere score kunnen hebben dan abonnementen met meer bronnen. 

## <a name="how-to-access-advisor"></a>Toegang tot Advisor
Advisor-Score bevindt zich in de open bare preview-versie van Azure Portal. U moet naar de sectie Advisor gaan en de Advisor-Score vinden als het 2e menu-item in het linkernavigatievenster. 

![Ingang voor Advisor-Score-punt](./media/advisor-score-3.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen van Advisor met betrekking tot kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
