---
title: Incidenten onderzoeken met Azure Sentinel| Microsoft Documenten
description: Gebruik deze zelfstudie om te leren hoe u incidenten met Azure Sentinel onderzoeken.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: ecd8c508d05bfeb541a6cb5efbcdf2fffd3c78d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587189"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Zelfstudie: Incidenten onderzoeken met Azure Sentinel

> [!IMPORTANT]
> De onderzoeksgrafiek is momenteel in openbare preview.
> Deze functie wordt geleverd zonder een serviceniveauovereenkomst en wordt niet aanbevolen voor productieworkloads.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.


Met deze zelfstudie u incidenten met Azure Sentinel onderzoeken. Nadat u uw gegevensbronnen hebt verbonden met Azure Sentinel, wilt u een melding ontvangen wanneer er iets verdachts gebeurt. Om u in staat te stellen dit te doen, u met Azure Sentinel geavanceerde waarschuwingsregels maken die incidenten genereren die u toewijzen en onderzoeken.

Dit artikel behandelt:
> [!div class="checklist"]
> * Incidenten onderzoeken
> * De onderzoeksgrafiek gebruiken
> * Op bedreigingen reageren

Een incident kan meerdere waarschuwingen bevatten. Het is een samenvoeging van al het relevante bewijs voor een specifiek onderzoek. Er wordt een incident gemaakt op basis van analytische regels die u hebt gemaakt op de **pagina Analytics.** De eigenschappen met betrekking tot de waarschuwingen, zoals ernst en status, worden ingesteld op het incidentniveau. Nadat u Azure Sentinel hebt laten weten welke soorten bedreigingen u zoekt en hoe u deze vinden, u gedetecteerde bedreigingen controleren door incidenten te onderzoeken.

## <a name="prerequisites"></a>Vereisten
U het incident alleen onderzoeken als u de toewijzingsvelden van de entiteit hebt gebruikt wanneer u uw analytische regel instelt. De onderzoeksgrafiek vereist dat uw oorspronkelijke incident entiteiten bevat.

## <a name="how-to-investigate-incidents"></a>Incidenten onderzoeken

1. Selecteer **Incidenten**. Op de pagina **Incidenten** u weten hoeveel incidenten u hebt, hoeveel er open zijn, hoeveel u hebt ingesteld op **In uitvoering**en hoeveel er zijn gesloten. Voor elk incident u zien hoe laat het is opgetreden en hoe laat het incident is. Kijk naar de ernst om te beslissen welke incidenten het eerst moeten worden verwerkt.

    ![Ernst van incidenten weergeven](media/tutorial-investigate-cases/incident-severity.png)

1. U de incidenten filteren als dat nodig is, bijvoorbeeld op status of ernst.

1. Als u een onderzoek wilt starten, selecteert u een specifiek incident. Aan de rechterkant ziet u gedetailleerde informatie over het incident, inclusief de ernst, een overzicht van het aantal betrokken entiteiten, de onbewerkte gebeurtenissen die dit incident hebben veroorzaakt en de unieke ID van het incident.

1. Als u meer details wilt bekijken over de waarschuwingen en entiteiten in het incident, selecteert u **Volledige details weergeven** op de incidentpagina en bekijkt u de relevante tabbladen die de incidentgegevens samenvatten. Controleer op het tabblad **Waarschuwingen** de waarschuwing zelf. U alle relevante informatie over de waarschuwing zien: de query die de waarschuwing heeft geactiveerd, het aantal geretourneerde resultaten per query en de mogelijkheid om playbooks op de waarschuwingen uit te voeren. Als u nog verder wilt inzoomen op het incident, selecteert u het aantal **gebeurtenissen**. Hiermee wordt de query geopend die de resultaten en de gebeurtenissen heeft gegenereerd die de waarschuwing hebben geactiveerd in Log Analytics. Op het tabblad **Entiteiten** ziet u alle entiteiten die u hebt toegewezen als onderdeel van de definitie van waarschuwingsregel.

    ![Waarschuwingsgegevens weergeven](media/tutorial-investigate-cases/alert-details.png)

1. Als u een incident actief onderzoekt, is het een goed idee om de status van het incident in te stellen **op in uitvoering** totdat u het sluit.

1. Incidenten kunnen worden toegewezen aan een specifieke gebruiker. Voor elk incident u een eigenaar toewijzen door het veld **Incident-eigenaar** in te stellen. Alle incidenten beginnen als niet toegewezen. U ook opmerkingen toevoegen, zodat andere analisten in staat zullen zijn om te begrijpen wat u onderzocht en wat uw zorgen zijn rond het incident.

    ![Incident toewijzen aan gebruiker](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selecteer **Onderzoeken** om de onderzoekskaart te bekijken.

## <a name="use-the-investigation-graph-to-deep-dive"></a>Gebruik de onderzoeksgrafiek om diep te duiken

De onderzoeksgrafiek stelt analisten in staat om voor elk onderzoek de juiste vragen te stellen. De onderzoeksgrafiek helpt u de omvang van een potentiële beveiligingsbedreiging te begrijpen en te identificeren door relevante gegevens te correleren met een betrokken entiteit. U dieper duiken en elke entiteit in de grafiek onderzoeken door deze te selecteren en te kiezen tussen verschillende uitbreidingsopties.  
  
De onderzoeksgrafiek geeft u de tekst:

- **Visuele context uit ruwe gegevens:** in de live, visuele grafiek worden entiteitsrelaties weergegeven die automatisch uit de ruwe gegevens worden geëxtraheerd. Hierdoor u eenvoudig verbindingen zien tussen verschillende gegevensbronnen.

- **Volledige detectie van het onderzoeksbereik:** breid uw onderzoeksbereik uit met ingebouwde verkenningsquery's om het volledige bereik van een inbreuk te laten zien.

- **Ingebouwde onderzoeksstappen**: Gebruik vooraf gedefinieerde verkenningsopties om ervoor te zorgen dat u de juiste vragen stelt in het licht van een bedreiging.

Ga als het gaat om het gebruik van de onderzoeksgrafiek:

1. Selecteer een incident en selecteer **Vervolgens Onderzoeken**. Dit brengt je naar de onderzoeksgrafiek. De grafiek biedt een illustratieve kaart van de entiteiten die rechtstreeks zijn verbonden met de waarschuwing en elke bron die verder is verbonden.

   > [!IMPORTANT] 
   > U het incident alleen onderzoeken als u de toewijzingsvelden van de entiteit hebt gebruikt wanneer u uw analytische regel instelt. De onderzoeksgrafiek vereist dat uw oorspronkelijke incident entiteiten bevat.

   ![Kaart weergeven](media/tutorial-investigate-cases/map1.png)

1. Selecteer een entiteit die het deelvenster Entiteiten wilt **openen,** zodat u informatie over die entiteit bekijken.

    ![Entiteiten weergeven in kaart](media/tutorial-investigate-cases/map-entities.png)
  
1. Breid uw onderzoek uit door over elke entiteit te zweven om een lijst met vragen te onthullen die zijn ontworpen door onze beveiligingsexperts en analisten per entiteitstype om uw onderzoek te verdiepen. We noemen deze opties **exploratie query's**.

    ![Meer informatie](media/tutorial-investigate-cases/exploration-cases.png)

   Op een computer u bijvoorbeeld gerelateerde waarschuwingen aanvragen. Als u een verkenningsquery selecteert, worden de resulterende titels weer aan de grafiek toegevoegd. In dit voorbeeld heeft het selecteren **van Gerelateerde waarschuwingen** de volgende waarschuwingen in de grafiek geretourneerd:

    ![Gerelateerde waarschuwingen weergeven](media/tutorial-investigate-cases/related-alerts.png)

1. Voor elke verkenningsquery u de optie selecteren om de resultaten van de ruwe gebeurtenis en de query die wordt gebruikt in Logboekanalyse te openen, door **Gebeurtenissen\>** te selecteren.

1. Om het incident te begrijpen, geeft de grafiek u een parallelle tijdlijn.

    ![Tijdlijn weergeven in kaart](media/tutorial-investigate-cases/map-timeline.png)

1. Zweef over de tijdlijn om te zien welke dingen op de grafiek op welk moment in de tijd hebben plaatsgevonden.

    ![Tijdlijn in kaart gebruiken om waarschuwingen te onderzoeken](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u geleerd hoe u aan de slag met het onderzoeken van incidenten met Azure Sentinel. Ga verder naar de zelfstudie voor [het reageren op bedreigingen met behulp van geautomatiseerde playbooks.](tutorial-respond-threats-playbook.md)
> [!div class="nextstepaction"]
> [Reageer op bedreigingen om](tutorial-respond-threats-playbook.md) uw reacties op bedreigingen te automatiseren.

