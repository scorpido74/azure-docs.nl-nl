---
title: Overzicht van de Fault Analysis Service
description: In dit artikel wordt de Fault Analysis Service in Service Fabric beschreven voor het veroorzaken van fouten en het uitvoeren van testscenario's tegen uw services.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465569"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Inleiding tot de fault analysis service
De Fault Analysis Service is ontworpen voor het testen van services die zijn gebouwd op Microsoft Azure Service Fabric. Met de Fault Analysis Service u zinvolle fouten veroorzaken en volledige testscenario's uitvoeren tegen uw toepassingen. Deze fouten en scenario's oefenen en valideren van de talrijke toestanden en overgangen die een service gedurende zijn hele levensduur zal ervaren, allemaal op een gecontroleerde, veilige en consistente manier.

Acties zijn de afzonderlijke fouten die gericht zijn op een service om deze te testen. Een serviceontwikkelaar kan deze gebruiken als bouwstenen om ingewikkelde scenario's te schrijven. Bijvoorbeeld:

* Start een knooppunt opnieuw om een willekeurig aantal situaties te simuleren waarin een machine of VM opnieuw wordt opgestart.
* Een replica van uw stateful service verplaatsen om taakverdeling, failover of applicatie-upgrade te simuleren.
* Beroep doen op quorumverlies op een stateful service om een situatie te creëren waarin schrijfbewerkingen niet kunnen doorgaan omdat er niet genoeg 'back-up' of 'secundaire' replica's zijn om nieuwe gegevens te accepteren.
* Beroep op gegevensverlies op een stateful service om een situatie te creëren waarin alle in-memory status volledig is weggevaagd.

Scenario's zijn complexe bewerkingen die bestaan uit een of meer acties. De Fault Analysis Service biedt twee ingebouwde complete scenario's:

* Chaos Scenario
* Failoverscenario

## <a name="testing-as-a-service"></a>Testen als service
De Fault Analysis Service is een Service Fabric-systeemservice die automatisch wordt gestart met een Service Fabric-cluster. Deze service fungeert als de host voor foutinjectie, testscenario-uitvoering en gezondheidsanalyse. 

![Foutanalyseservice][0]

Wanneer een foutactie of testscenario wordt gestart, wordt een opdracht naar de foutanalyseservice verzonden om de foutactie of het testscenario uit te voeren. De Fault Analysis Service is stateful, zodat het op betrouwbare wijze fouten en scenario's kan uitvoeren en resultaten kan valideren. Een langlopend testscenario kan bijvoorbeeld betrouwbaar worden uitgevoerd door de Fault Analysis Service. En omdat tests binnen het cluster worden uitgevoerd, kan de service de status van het cluster en uw services onderzoeken om meer diepgaande informatie over fouten te bieden.

## <a name="testing-distributed-systems"></a>Testen van gedistribueerde systemen
Service Fabric maakt het schrijven en beheren van gedistribueerde schaalbare applicaties aanzienlijk eenvoudiger. De Fault Analysis Service maakt het testen van een gedistribueerde toepassing op dezelfde manier eenvoudiger. Er zijn drie belangrijke problemen die moeten worden opgelost tijdens het testen:

1. Fouten simuleren/genereren die zich kunnen voordoen in echte scenario's: Een van de belangrijke aspecten van Service Fabric is dat gedistribueerde toepassingen kunnen herstellen van verschillende fouten. Echter, om te testen dat de toepassing in staat is om te herstellen van deze fouten, hebben we een mechanisme nodig om deze real-world storingen te simuleren / genereren in een gecontroleerde testomgeving.
1. De mogelijkheid om gecorreleerde fouten te genereren: Basisfouten in het systeem, zoals netwerkstoringen en machinestoringen, zijn eenvoudig afzonderlijk te produceren. Het genereren van een aanzienlijk aantal scenario's die kunnen gebeuren in de echte wereld als gevolg van de interacties van deze individuele mislukkingen is niet-triviaal.
1. Uniforme ervaring op verschillende niveaus van ontwikkeling en implementatie: Er zijn veel foutinjectiesystemen die verschillende soorten storingen kunnen uitvoeren. Echter, de ervaring in al deze is slecht bij het verplaatsen van one-box developer scenario's, om dezelfde tests uit te voeren in grote testomgevingen, om ze te gebruiken voor tests in productie.

Hoewel er veel mechanismen zijn om deze problemen op te lossen, ontbreekt een systeem dat hetzelfde doet met vereiste garanties - helemaal uit een one-box developer-omgeving, om te testen in productieclusters - ontbreekt. De Fault Analysis Service helpt de ontwikkelaars van de toepassing zich te concentreren op het testen van hun bedrijfslogica. De Fault Analysis Service biedt alle mogelijkheden die nodig zijn om de interactie van de service met het onderliggende gedistribueerde systeem te testen.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Scenario's voor het simuleren/genereren van echte fouten
Om de robuustheid van een gedistribueerd systeem tegen storingen te testen, hebben we een mechanisme nodig om storingen te genereren. Terwijl in theorie, het genereren van een mislukking als een knooppunt naar beneden lijkt eenvoudig, het begint het raken van dezelfde set van consistentie problemen die Service Fabric probeert op te lossen. Als we bijvoorbeeld een knooppunt willen afsluiten, is de vereiste werkstroom de volgende:

1. Geef bij de client een knooppuntaanvraag voor afsluiten uit.
1. Stuur het verzoek naar het juiste knooppunt.
   
    a. Als het knooppunt niet wordt gevonden, moet het mislukken.
   
    b. Als het knooppunt wordt gevonden, mag het alleen terugkeren als het knooppunt wordt afgesloten.

Om de fout vanuit een testperspectief te verifiëren, moet de test weten dat wanneer deze fout wordt veroorzaakt, de fout daadwerkelijk gebeurt. De garantie die Service Fabric biedt is dat ofwel het knooppunt naar beneden zal gaan of al naar beneden is gegaan toen het commando het knooppunt bereikte. In beide gevallen moet de test in staat zijn om correct reden over de status en slagen of correct mislukken in de validatie. Een systeem dat buiten Service Fabric is geïmplementeerd om dezelfde reeks fouten uit te voeren, kan veel netwerk-, hardware- en softwareproblemen veroorzaken, waardoor het de voorgaande garanties niet kan bieden. In aanwezigheid van de eerder genoemde problemen zal Service Fabric de clusterstatus opnieuw configureren om de problemen te omzeilen, en daarom kan de Fault Analysis Service nog steeds de juiste set garanties geven.

### <a name="generating-required-events-and-scenarios"></a>Vereiste gebeurtenissen en scenario's genereren
Terwijl het simuleren van een real-world mislukking consequent is moeilijk om mee te beginnen, de mogelijkheid om gecorreleerde mislukkingen te genereren is nog moeilijker. Een gegevensverlies vindt bijvoorbeeld plaats in een stateful persisted-service wanneer de volgende dingen gebeuren:

1. Alleen een schrijfquorum van de replica's wordt ingehaald op replicatie. Alle secundaire replica's blijven achter bij de primaire.
1. Het schrijfquorum gaat omlaag vanwege de replica's die naar beneden gaan (als gevolg van een codepakket of knooppunt dat naar beneden gaat).
1. Het schrijfquorum kan niet worden teruggedraaid omdat de gegevens voor de replica's verloren zijn gegaan (als gevolg van schijfbeschadiging of machinereimaging).

Deze gecorreleerde mislukkingen gebeuren in de echte wereld, maar niet zo vaak als individuele mislukkingen. De mogelijkheid om te testen voor deze scenario's voordat ze gebeuren in de productie is van cruciaal belang. Nog belangrijker is de mogelijkheid om deze scenario's te simuleren met productieworkloads in gecontroleerde omstandigheden (in het midden van de dag met alle ingenieurs aan dek). Dat is veel beter dan dat het voor het eerst in productie is om 02:00 uur.

### <a name="unified-experience-across-different-environments"></a>Uniforme ervaring in verschillende omgevingen
De praktijk is van oudsher het creëren van drie verschillende sets van ervaringen, een voor de ontwikkeling omgeving, een voor tests, en een voor de productie. Het model was:

1. In de ontwikkelingsomgeving, produceren staat overgangen die eenheid tests van individuele methoden mogelijk te maken.
1. In de testomgeving, produceren mislukkingen om end-to-end tests die verschillende mislukking scenario's uit te oefenen.
1. Houd de productieomgeving ongerept om niet-natuurlijke storingen te voorkomen en om ervoor te zorgen dat er een extreem snelle menselijke reactie op falen is.

In Service Fabric stellen we via de Fault Analysis Service voor om dit om te draaien en dezelfde methodologie te gebruiken van ontwikkelaarsomgeving tot productie. Er zijn twee manieren om dit te bereiken:

1. Als u gecontroleerde fouten wilt veroorzaken, gebruikt u de API's van de Foutanalyseservice vanuit een one-box-omgeving tot productieclusters.
1. Gebruik de Fault Analysis Service om het cluster koorts te geven die automatische inductie van storingen veroorzaakt, om automatische storingen te genereren. Door de snelheid van storingen via configuratie te regelen, kan dezelfde service in verschillende omgevingen anders worden getest.

Met Service Fabric, hoewel de schaal van storingen zou verschillen in de verschillende omgevingen, de werkelijke mechanismen identiek zou zijn. Dit zorgt voor een veel snellere code-to-deployment pijplijn en de mogelijkheid om de services te testen onder real-world ladingen.

## <a name="using-the-fault-analysis-service"></a>De foutanalyseservice gebruiken
**C #**

De functies van de Foutanalyseservice bevinden zich in de naamruimte van System.Fabric in het Microsoft.ServiceFabric NuGet-pakket. Als u de functies van de Fault Analysis Service wilt gebruiken, neemt u het nuget-pakket op als referentie in uw project.

**Powershell**

Als u PowerShell wilt gebruiken, moet u de Service Fabric SDK installeren. Nadat de SDK is geïnstalleerd, wordt de ServiceFabric PowerShell-module automatisch geladen voor u om te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Om echt cloud-scale services te creëren, is het van cruciaal belang om ervoor te zorgen, zowel voor als na implementatie, dat services bestand zijn tegen echte fouten in de wereld. In de dienstenwereld van vandaag is de mogelijkheid om snel te innoveren en code snel naar productie te verplaatsen erg belangrijk. De Fault Analysis Service helpt serviceontwikkelaars om precies dat te doen.

Begin met het testen van uw toepassingen en services met behulp van de ingebouwde [testscenario's](service-fabric-testability-scenarios.md)of schrijf uw eigen testscenario's met behulp van de [foutacties](service-fabric-testability-actions.md) van de Fault Analysis Service.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
