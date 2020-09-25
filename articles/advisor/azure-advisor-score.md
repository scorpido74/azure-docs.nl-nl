---
title: Azure-workloads optimaliseren met Advisor-Score
description: Advisor-Score gebruiken om optimaal te profiteren van Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 29d8480f501a78c1668b52034f439f998419f9d9
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335616"
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
* Met de **scores** van de Advisor-categorie van elke aanbeveling kunt u zien welke uitmuntende aanbevelingen uw score het meest zullen verbeteren. Deze waarden zijn zowel het gewicht van de aanbeveling als het voor speld gemak van de implementatie om ervoor te zorgen dat u uw tijd optimaal kunt benutten en u helpt met prioriteiten te stellen
* De **categorie Score-impact** van elke aanbeveling om u te helpen prioriteiten te geven voor uw herstel acties voor elke categorie

De bijdrage van elke aanbeveling aan uw categorie score wordt duidelijk weer gegeven op de pagina Advisor Score van de Azure Portal. U kunt elke categorie Score verhogen op basis van het percentage punt dat wordt vermeld in de kolom Categorie Score impact. Deze waarde weerspiegelt zowel het gewicht van de aanbeveling binnen de categorie als het voor speld gemak van de implementatie om het potentieel lage hangende fruit te verhelpen. Als u zich richt op de aanbevelingen met de grootste invloed op de score, kunt u de meest voor uitgang van tijd maken.  

![Impact van Advisor-Score](./media/advisor-score-2.png)

Als een van de Advisor-aanbevelingen niet relevant is voor een afzonderlijke resource, kunt u deze aanbevelingen uitstellen of negeren en ze worden uitgesloten van de score berekening bij de volgende vernieuwing. Advisor gebruikt deze invoer ook als aanvullende feedback om het model te verbeteren.

## <a name="how-is-advisor-score-calculated"></a>Hoe wordt Advisor score berekend?
In Advisor worden de scores van uw categorie en uw totale Advisor-score als percentages weer gegeven. Een Score van 100% in een categorie betekent dat al uw resources, *beoordeeld door Advisor*, voldoen aan de aanbevolen procedures die door Advisor worden aanbevolen. Aan het andere uiteinde van het spectrum betekent een Score van 0% dat geen van uw resources, beoordeeld door Advisor, de aanbevelingen van Advisor volgen. 
**Elk van de vijf categorieën heeft een hoogst mogelijke Score van 100.** Uw totale Advisor-score wordt berekend als een som van elke toepasselijke categorie Score, gedeeld door de som van de hoogst mogelijke Score van alle toepasselijke categorieën. Voor de meeste abonnementen betekent Advisor dat de Score van elke categorie wordt toegevoegd en gedeeld door 500. **Elke categorie score wordt echter alleen berekend als u resources gebruikt die door Advisor zijn beoordeeld.**

**Voor beeld van Advisor score berekening**
* Score voor één abonnement: dit is het eenvoudige gemiddelde van alle Advisor categorie scores voor uw abonnement. Als de Advisor categorie scores als kosten = 73, Reliabilit = 85, operationele uitmuntendheid = 77, prestaties = 100; de **Advisor-Score** is (73 + 85 + 77 + 100)/(4x100) = 0,84 of 84%.
* Score voor meerdere abonnementen: wanneer meerdere abonnementen zijn geselecteerd, worden de totale Advisor-scores die we genereren, gewogen geaggregeerde categorie scores. Hier wordt elke categorie Score van Advisor geaggregeerd op basis van bronnen die worden verbruikt door abonnementen. Zodra we de gewogen geaggregeerde categorie scores hebben, hebben we een eenvoudig gemiddelde om u een algemene score voor abonnementen te geven. 


### <a name="scoring-methodology"></a>Score methodologie: 
De berekening van de Advisor-Score kan in vier stappen worden samenvatten:
1. Advisor berekent de **verkoop kosten van betrokken resources**, die de resources zijn van uw abonnementen die ten minste één aanbeveling hebben in Advisor.
2. Advisor berekent de **kost prijs van de geraamde resources**, die de resources zijn die door Advisor worden bewaakt, of ze al dan niet een aanbeveling hebben. 
3. Voor elk type aanbeveling berekent Advisor de **goede resource verhouding**. Dit zijn de kosten voor de verkoop van betrokken resources gedeeld door de kosten van de geraamde resources.
4. Advisor past drie extra gewichten toe op de goede resource verhouding van elke categorie:
  * Aanbevelingen met meer impact worden gewogen zwaarder dan die met een lagere impact.
  * Resources met lange, specifieke aanbevelingen tellen meer bij aan uw score.
  * Resources die u uitstelt of negeert in Advisor, worden volledig verwijderd uit uw score berekening. 
    
Advisor past dit model op een niveau van de Advisor toe (beveiliging maakt gebruik van een [beveiligd Score](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) model), waarbij de Score van een adviseur Advisor voor elke categorie wordt toegepast en een eenvoudig gemiddelde het resultaat van de laatste Advisor.


## <a name="advisor-score-faq"></a>Veelgestelde vragen over Advisor-Score
* **Hoe vaak is mijn score vernieuwd?**
Uw score wordt minstens eenmaal per dag vernieuwd. 
* **Waarom hebben sommige aanbevelingen een lege waarde voor '-' in de kolom Categorie Score impact?** Advisor bevat niet onmiddellijk nieuwe aanbevelingen of gebruikers met recente wijzigingen in het score model. Na een korte evaluatie periode worden ze meestal enkele weken opgenomen in de score. 
* **Waarom is de kosten score hoger voor sommige aanbevelingen, zelfs als ze een lagere mogelijke besparing hebben?**
Uw kosten Score weerspiegelt uw mogelijke besparingen van geweinig gebruikte resources en het voor speld gemak voor het implementeren van deze aanbevelingen. Extra gewicht wordt bijvoorbeeld toegepast op resources die invloed hebben op een langere periode, zelfs als de mogelijke besparingen lager zijn. 
* **Waarom heb ik geen score voor een of meer categorieën of abonnementen?**
Advisor genereert alleen een score voor de categorieën en abonnementen die resources hebben die door Advisor zijn beoordeeld.
* **Wat moet ik doen als een aanbeveling niet relevant is?**
Als u een aanbeveling van Advisor negeert, wordt deze wegge laten uit de berekening van uw score. Het negeren van aanbevelingen kan ook helpen Advisor de kwaliteit van de aanbevelingen te verbeteren.
* **Waarom is mijn score gewijzigd?** U kunt de score wijzigen als u betrokken resources herstelt door de aanbevolen procedures voor Advisor te nemen. Als u of iemand met machtigingen voor uw abonnement nieuwe resources heeft gewijzigd of gemaakt, ziet u mogelijk ook schommelingen in uw score omdat uw score is gebaseerd op een verhouding van de kosten die invloed hebben op de resources ten opzichte van de totale kosten van alle resources.
* **Hoe berekent Advisor de kosten van resources voor een abonnement?**
Advisor gebruikt de tarieven voor betalen naar gebruik die worden gepubliceerd op de pagina met prijzen voor Azure.com, die geen toepasselijke kortingen weer spie gelen, vermenigvuldigd met de hoeveelheid gebruik op de laatste dag dat de resource is toegewezen. Als u de kortingen weglaat van de berekening van de resource kosten, wordt Advisor-Score vergeleken in abonnementen, tenants en inschrijvingen waarbij kortingen kunnen variëren. 
* **Moet ik de aanbevelingen in Advisor bekijken om punten voor mijn score op te halen?** Nee. Uw score geeft aan of u aanbevolen procedures voor Advisor aanbeveelt, zelfs als u deze best practices proactief doorneemt en nooit uw aanbevelingen in Advisor bekijkt.
* **Maakt de Score methodologie onderscheid tussen werk belastingen voor productie en ontwikkelings testen?**
Nee, maar u kunt aanbevelingen voor afzonderlijke resources negeren als deze resources worden gebruikt voor ontwikkeling en testen en de aanbeveling niet van toepassing is.
* **Kan ik de scores van een abonnement met 100 resources en een abonnement met 100.000 resources vergelijken?**
De Score methodologie is ontworpen voor het beheren van het aantal resources voor een combi natie van abonnementen en services, zodat abonnementen met minder resources een hogere of lagere score kunnen hebben dan abonnementen met meer bronnen. 
* **Wat betekent dat wanneer ik ' binnenkort ' wordt weer geven in de kolom Score impact?**
Dit betekent dat dit een nieuwe aanbeveling is en er nog steeds aan de slag gaat in ons Advisor-score model. Zodra deze nieuwe aanbeveling in aanmerking komt voor score berekening, ziet u de waarde voor de score voor uw aanbeveling.  
* **Is mijn score afhankelijk van wat ik besteed aan Azure?**
Nee, uw score is niet noodzakelijkerwijs een weer spiegeling van de hoeveelheid die u besteedt en er wordt een lagere kosten score berekend.

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
