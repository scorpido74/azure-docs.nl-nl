---
title: Zelfstudie voor het reageren op incidenten - Azure Security Center
description: In deze zelfstudie leert u hoe u beveiligingswaarschuwingen kunt sorteren, hoe u de hoofdoorzaak en het bereik van een incident kunt bepalen en hoe u beveiligingsgegevens zoekt.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: memildin
ms.openlocfilehash: 08e04749eae7158abb501f9a4d127cdd7a89a391
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91336272"
---
# <a name="tutorial-respond-to-security-incidents"></a>Zelfstudie: Reageren op beveiligingsincidenten
Security Center analyseert voortdurend de werkbelasting van uw hybride cloud met behulp van geavanceerde analysen en bedreigingsinformatie, om u te waarschuwen voor schadelijke activiteiten. Bovendien kunt u waarschuwingen van andere beveiligingsproducten en -services integreren in Security Center en aangepaste waarschuwingen maken op basis van uw eigen indicatoren of informatiebronnen. Wanneer een waarschuwing is gegenereerd, moet onmiddellijk actie worden ondernomen om het probleem te onderzoeken en te herstellen. In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Beveiligingswaarschuwingen onderzoeken
> * Verder onderzoek uitvoeren om de oorzaak en het bereik van een beveiligingsincident te bepalen
> * Zoek beveiligingsgegevens die bij het onderzoek kunnen helpen

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
Om de functies in deze zelfstudie te doorlopen, moet Azure Defender zijn ingeschakeld. U kunt Azure Defender gratis uitproberen. Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/security-center/) voor meer informatie. De quickstart [Aan de slag met Security Center](security-center-get-started.md) begeleidt u bij de upgrade.

## <a name="scenario"></a>Scenario
Contoso heeft enkele van de on-premises resources onlangs naar Azure gemigreerd, met inbegrip van een aantal line-of-business workloads en SQL-databases op virtuele machines. Het Contoso Core Computer Security Incident Response Team (CSIRT) heeft een probleem met het onderzoeken van mogelijke beveiligingskwesties. De oorzaak hiervan is dat het bedrijf geen beveiligingsintelligence heeft die is geïntegreerd in de aanwezige hulpmiddelen voor het reageren op incidenten. Doordat deze integratie niet is doorgevoerd, ontstaat er een probleem tijdens de detectiefase (teveel onjuist positieve resultaten), en tijdens de beoordelings- en diagnosefase. Als onderdeel van deze migratie besloten ze Security Center in te schakelen om hen te helpen dit probleem aan te pakken.

De eerste fase van deze migratie werd afgesloten nadat alle resources waren voorbereid en alle aanbevelingen voor beveiliging van Security Center waren opgevolgd. Contoso CSIRT is het centrale punt voor het afhandelen van computerbeveiligingsincidenten. Het team bestaat uit een groep mensen met voldoende bevoegdheden voor het afhandelen van elk type beveiligingsincident. De teamleden hebben duidelijk gedefinieerde taken om ervoor te zorgen dat er geen onderdeel van een fase van een reactie over het hoofd wordt gezien.

Voor dit scenario gaan we ons richten op de rollen van de volgende personen die deel uitmaken van Contoso CSIRT:

![Levenscyclus van reacties op incidenten](./media/tutorial-security-incident/security-center-incident-response.png)

Judy houdt zich bezig met beveiligingsbewerkingen. Ze is onder andere verantwoordelijk voor het volgende:

* Het ononderbroken bewaken van en reageren op bedreigingen.
* Indien nodig een probleem doorverwijzen naar de eigenaar van de workloads in de cloud of naar de beveiligingsanalist.

Sam is een beveiligingsanalist en zijn verantwoordelijkheden omvatten:

* Het onderzoeken van aanvallen.
* Het oplossen van problemen die worden vermeld in beveiligingswaarschuwingen.
* Samenwerken met eigenaren van workloads om te bepalen hoe een probleem kan worden opgelost en hoe de oplossing kan worden toegepast.

Zoals u ziet, hebben Judy en Sam verschillende verantwoordelijkheden, en ze moeten samenwerken om de informatie te delen die ze van Security Center krijgen.

## <a name="triage-security-alerts"></a>Beveiligingswaarschuwingen onderzoeken
Security Center biedt een overkoepelend overzicht van alle beveiligingswaarschuwingen. Beveiligingswaarschuwingen worden gerangschikt op basis van de ernst, en wanneer mogelijke worden gerelateerde waarschuwingen samengevoegd in één beveiligingsincident. Bij het uitsorteren van waarschuwingen en incidenten, gaat u als volgt te werk:

- Negeer waarschuwingen waarvoor geen verdere actie is vereist, bijvoorbeeld als de waarschuwing onterecht is
- Los bekende aanvallen op, bijvoorbeeld wanneer netwerkverkeer wordt geblokkeerd vanuit een schadelijke IP-adres
- Bepaal welke waarschuwingen verder moeten worden onderzocht


1. Selecteer in het hoofdmenu van Security Center onder **DETECTIE** de optie **Beveiligingswaarschuwingen**:

   ![Beveiligingswaarschuwingen](./media/tutorial-security-incident/tutorial-security-incident-fig1.png)

2. Selecteer een beveiligingsincident (een verzameling waarschuwingen) in de lijst met waarschuwingen voor meer informatie over dit incident. **Veiligheidsincident gedetecteerd** wordt geopend.

   ![Veiligheidsincident gedetecteerd](./media/tutorial-security-incident/tutorial-security-incident-fig2.png)

3. Bovenin dit scherm staat de beschrijving van het incident, met de lijst van waarschuwingen die deel uitmaken van dit incident. Klik op de waarschuwing die u verder wilt onderzoeken, voor meer informatie.

   ![Waarschuwingsdetails van incident](./media/tutorial-security-incident/tutorial-security-incident-fig3.png)

   Het type waarschuwing kan verschillen. Lees [Beveiligingswaarschuwingen in Azure Security Center](security-center-alerts-type.md) voor meer informatie over het type waarschuwing en mogelijke herstelstappen. Voor waarschuwingen die veilig kunnen worden genegeerd, klikt u met de rechtermuisknop op de waarschuwing en selecteert u de optie **Sluiten**:

   ![Waarschuwing](./media/tutorial-security-incident/tutorial-security-incident-fig4.png)

4. Als de hoofdoorzaak en het bereik van de schadelijke activiteit onbekend is, gaat u verder met de volgende stap om verder onderzoek te doen.

## <a name="investigate-an-alert-or-incident"></a>Een waarschuwing of incident onderzoeken
1. Klik op de pagina **Beveiligingswaarschuwing** op de knop **Onderzoek starten** (als u al bent begonnen, verandert de naam in **Onderzoek voortzetten**).

   ![Onderzoek](./media/tutorial-security-incident/tutorial-security-incident-fig5.png)

   De onderzoekskaart is een grafische weergave van de entiteiten die zijn verbonden met deze beveiligingswaarschuwing of dit incident. Door op een entiteit in de kaart te klikken, toont de informatie over die entiteit nieuwe entiteiten en wordt de kaart uitgebreid. De eigenschappen van de entiteit die in de kaart is geselecteerd, zijn gemarkeerd in het deelvenster aan de rechterkant van de pagina. Welke informatie op elk tabblad beschikbaar is, is afhankelijk van de geselecteerde entiteit. Bekijk tijdens het onderzoek alle relevante informatie om meer inzicht te krijgen in de tactiek van de aanvaller.

2. Als u meer bewijs nodig hebt of meer onderzoek moet verrichten voor entiteiten die tijdens het onderzoek zijn gevonden, gaat u verder met de volgende stap.

## <a name="search-data-for-investigation"></a>Gegevens zoeken voor onderzoek

Met de zoekmogelijkheden van Security Center kunt u meer bewijs van gecompromitteerde systemen zoeken en meer informatie over de entiteiten die deel uitmaken van het onderzoek.

Als u een zoekopdracht wilt uitvoeren, opent u het dashboard van **Security Center**, klikt u in het linkernavigatievenster op **Zoeken**, selecteert u de werkruimte die de entiteiten bevat die u wilt zoeken, typt u de zoekopdracht en klikt u op de zoekknop.

## <a name="clean-up-resources"></a>Resources opschonen

Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u de volgende quickstarts en zelfstudies ook wilt doornemen, houdt u automatische inrichting en Azure Defender ingeschakeld. Als u niet wilt doorgaan of Azure Defender wilt uitschakelen:

1. Ga terug naar het hoofdmenu van Security Center en selecteer **Prijzen en instellingen**.
1. Selecteer het abonnement dat u wilt downgraden.
1. Stel **Azure Defender** in op Uit.
1. Selecteer **Opslaan**.

Als u automatisch inrichten wilt uitschakelen:

1. Ga terug naar het hoofdmenu van Security Center en selecteer **Beveiligingsbeleid**.
2. Selecteer het abonnement waarvoor u automatisch inrichten wilt uitschakelen.
3. Ga naar **Beveiligingsbeleid – Gegevensverzameling** en selecteer onder **Onboarding** de optie **Uit** om automatisch inrichten uit te schakelen.
4. Selecteer **Opslaan**.

>[!NOTE]
> Wanneer u automatische inrichting uitschakelt, wordt de Log Analytics-agent niet verwijderd van Azure-VM's waarop de agent is ingericht. Door automatische inrichting uit te schakelen, wordt de beveiligingsbewaking voor uw resources beperkt.
>

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd over Security Center-functies waarmee u kunt reageren op beveiligingsincidenten, zoals:

> [!div class="checklist"]
> * Beveiligingsincident dat een aggregatie is van gerelateerde waarschuwingen voor een resource
> * Onderzoekskaart die een grafische weergave is van de entiteiten die zijn verbonden met een beveiligingswaarschuwing of incident
> * Zoekmogelijkheden om meer bewijs te vinden van gecompromitteerde systemen