---
title: 'Quickstart: Inleiding tot de Piggy Metrics-voorbeeld-app - Azure Spring Cloud'
description: Hierin wordt de voorbeeld-app Piggy Metrics beschreven die wordt gebruikt bij implementeren in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: d8eee715a3ac2256838b37c1c6722aa1307f6dd0
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89046779"
---
# <a name="introduction-to-piggy-metrics-sample-app"></a>Inleiding tot de voorbeeld-app Piggy Metrics

In deze quickstart gebruiken we een voorbeeld van persoonlijk financiën, genaamd Piggy Metrics, om te laten zien hoe u een app kunt implementeren in de Azure Spring Cloud-service. Piggy Metrics laat het architectuurpatroon van de microservice zien en markeert de uitsplitsing van de services. U krijgt te zien hoe dit wordt geïmplementeerd in Azure met krachtige Azure Spring Cloud-mogelijkheden, waaronder servicedetectie, configuratieserver, logboeken, metrische gegevens, en gedistribueerde tracering.

Als u de voorbeelden van Azure Spring Cloud-implementatie wilt volgen, hebt u alleen de locatie van de broncode nodig. Indien nodig wordt deze locatie opgegeven.

## <a name="functional-services"></a>Functionele services
Piggy Metrics bestaat uit drie kernmicroservices. Dit zijn allemaal toepassingen die onafhankelijk kunnen worden geïmplementeerd, georganiseerd op bedrijfsdomein.

* **Rekeningservice (te implementeren)** : Bevat algemene logica en validatie voor gebruikersinvoer: inkomsten/uitgaven, spaargeld, en instellingen voor rekeningen.
* **Statistiekservice (niet gebruikt in deze quickstart)** : Hiermee worden berekeningen uitgevoerd op belangrijke statistische parameters en tijdreeksen vastgelegd voor elke rekening. Gegevenspunt bevat waarden, genormaliseerd naar basisvaluta en tijdsperiode. Deze gegevens worden gebruikt om de geldstroomdynamiek voor de levensduur van een rekening bij te houden.
* **Meldingsservice (niet gebruikt in deze quickstart)** : Hiermee worden contactgegevens en meldingsinstellingen voor gebruikers opgeslagen, zoals de frequentie van herinneringen en back-ups. Geplande werknemer verzamelt de vereiste gegevens van andere services en verzendt e-mailberichten naar geabonneerde klanten.

## <a name="infrastructure-services"></a>Infrastructuurservices
Er zijn verschillende algemene patronen in gedistribueerde systemen die helpen bij de werking van kernservices. Azure Spring Cloud biedt krachtige hulpmiddelen die het gedrag van Spring Boot-toepassingen verbeteren om deze patronen te implementeren: 

* **Configuratieservice (gehost met Azure Spring Cloud)** : Azure Spring Cloud-configuratie is een gecentraliseerde configuratieservice voor gedistribueerde systemen die horizontaal kan worden geschaald. De service maakt gebruik van een pluggable opslagplaats die momenteel ondersteuning biedt voor lokale opslag, Git en Subversion.
* **Servicedetectie (gehost met Azure Spring Cloud)** : Staat automatische detectie van netwerklocaties toe voor service-exemplaren die dynamisch toegewezen adressen kunnen hebben vanwege automatisch schalen, fouten en upgrades.
* **Verificatieservice (te implementeren)** Autorisatieverantwoordelijkheden worden volledig uitgepakt op een afzonderlijke server, die OAuth2-tokens verleent voor de back-endresourceservices. Verificatieserver zorgt voor gebruikersautorisatie en beveiligde communicatie tussen machines binnen een perimeter.
* **API-gateway (te implementeren)** : De drie kernservices maken een externe API beschikbaar voor de client. In de praktijk kan het aantal functies in een systeem zeer snel toenemen naarmate een systeem complexer wordt. Er kunnen honderden services betrokken zijn bij het weergeven van één complexe webpagina. De API-gateway is een enkel ingangspunt in het systeem, dat wordt gebruikt om aanvragen af te handelen en te routeren naar de juiste back-endservice, of om meerdere back-endservices aan te roepen, waarbij de resultaten worden samengevoegd. 

## <a name="sample-usage-of-piggy-metrics"></a>Voorbeeld van het gebruik van Piggy Metrics
Zie [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) voor de volledige implementatiegegevens. De voorbeelden verwijzen naar de broncode, indien nodig.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Azure Spring Cloud-exemplaar inrichten](spring-cloud-quickstart-provision-service-instance.md)
