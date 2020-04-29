---
title: Incidenten onderzoeken met Azure Sentinel | Microsoft Docs
description: Gebruik deze zelf studie om te leren hoe u incidenten met Azure Sentinel kunt onderzoeken.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77587189"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>Zelf studie: incidenten onderzoeken met Azure Sentinel

> [!IMPORTANT]
> De onderzoek grafiek is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen.
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Deze zelf studie helpt u bij het onderzoeken van incidenten met Azure Sentinel. Nadat u uw gegevens bronnen hebt verbonden met Azure Sentinel, wilt u een melding ontvangen wanneer er iets verdacht is. Hiervoor kunt u met Azure Sentinel geavanceerde waarschuwings regels maken waarmee incidenten worden gegenereerd die u kunt toewijzen en onderzoeken.

In dit artikel komen de volgende onderwerpen aan bod:
> [!div class="checklist"]
> * Incidenten onderzoeken
> * De onderzoek grafiek gebruiken
> * Op bedreigingen reageren

Een incident kan meerdere waarschuwingen bevatten. Het is een aggregatie van alle relevante bewijzen voor een specifiek onderzoek. Er wordt een incident gemaakt op basis van analytische regels die u hebt gemaakt op de **Analytics** -pagina. De eigenschappen die betrekking hebben op de waarschuwingen, zoals ernst en status, worden ingesteld op incident niveau. Nadat u Azure-Sentinel weet wat voor soort bedreigingen u zoekt en hoe u deze kunt vinden, kun u gedetecteerde bedreigingen bewaken door incidenten te onderzoeken.

## <a name="prerequisites"></a>Vereisten
U kunt het incident alleen onderzoeken als u de velden voor entiteits toewijzing hebt gebruikt bij het instellen van de analytische regel. Het onderzoek diagram vereist dat uw oorspronkelijke incident entiteiten bevat.

## <a name="how-to-investigate-incidents"></a>Incidenten onderzoeken

1. Selecteer **incidenten**. Op de pagina **incidenten** kunt u zien hoeveel incidenten u hebt, hoeveel er zijn geopend, hoeveel er openstaan en hoeveel u hebt ingesteld op **in uitvoering**en hoeveel er zijn gesloten. Voor elk incident ziet u de tijd waarop het probleem is opgetreden en de status van het incident. Bekijk de ernst om te bepalen welke incidenten het eerst moeten worden verwerkt.

    ![Ernst van incident weer geven](media/tutorial-investigate-cases/incident-severity.png)

1. U kunt de incidenten zo nodig filteren, bijvoorbeeld op basis van de status of de ernst.

1. Selecteer een specifiek incident om een onderzoek te starten. Aan de rechter kant kunt u gedetailleerde informatie voor het incident bekijken, inclusief de ernst, samen vatting van het aantal betrokken entiteiten, de onbewerkte gebeurtenissen die dit incident hebben geactiveerd en de unieke ID van het incident.

1. Als u meer informatie over de waarschuwingen en entiteiten in het incident wilt weer geven, selecteert u **volledige details weer geven** op de pagina incident en bekijkt u de relevante tabbladen met een overzicht van de informatie over het incident. Bekijk op het tabblad **waarschuwingen** de waarschuwing zelf. U kunt alle relevante informatie over de waarschuwing zien: de query die de waarschuwing heeft geactiveerd, het aantal resultaten dat per query wordt geretourneerd en de mogelijkheid om playbooks uit te voeren op de waarschuwingen. Als u nog meer wilt inzoomen op het incident, selecteert u het aantal **gebeurtenissen**. Hiermee opent u de query die de resultaten heeft gegenereerd en de gebeurtenissen die de waarschuwing hebben geactiveerd in Log Analytics. Op het tabblad **entiteiten** ziet u alle entiteiten die u hebt toegewezen als onderdeel van de definitie van de waarschuwings regel.

    ![Waarschuwings details weer geven](media/tutorial-investigate-cases/alert-details.png)

1. Als u een incident actief onderzoekt, is het een goed idee om de status van het incident in te stellen op **in behandeling** tot u het hebt gesloten.

1. Incidenten kunnen worden toegewezen aan een specifieke gebruiker. U kunt voor elk incident een eigenaar toewijzen door het veld eigenaar van het **incident** in te stellen. Alle incidenten worden gestart als niet-toegewezen. U kunt ook opmerkingen toevoegen zodat andere analisten kunnen begrijpen wat u hebt onderzocht en wat uw bezorgdheid over het incident zijn.

    ![Incident aan gebruiker toewijzen](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. Selecteer **onderzoeken** om de kaart weer te geven.

## <a name="use-the-investigation-graph-to-deep-dive"></a>De onderzoek grafiek gebruiken om te dieper

In het onderzoek diagram kunnen analisten de juiste vragen stellen voor elk onderzoek. De onderzoek grafiek helpt u bij het begrijpen van het bereik en het identificeren van de hoofd oorzaak van een mogelijke beveiligings risico door relevante gegevens met een wille keurige entiteit te correleren. U kunt een diep gaande en onderzoek doen naar elke entiteit die in de grafiek wordt weer gegeven door deze te selecteren en te kiezen tussen verschillende uitbreidings opties.  
  
In het onderzoek diagram vindt u het volgende:

- **Visuele context van onbewerkte gegevens: in**de Live, visuele grafiek worden entiteits relaties weer gegeven die automatisch zijn geëxtraheerd uit de onbewerkte gegevens. Zo kunt u eenvoudig verbindingen tussen verschillende gegevens bronnen weer geven.

- **Volledige detectie van het onderzoek bereik**: Breid uw onderzoek uit met behulp van ingebouwde onderzoek query's om het volledige bereik van een schending te behalen.

- **Geïntegreerde onderzoek stappen**: gebruik vooraf gedefinieerde opties om ervoor te zorgen dat u de juiste vragen in het vlak van een bedreiging vraagt.

De onderzoek grafiek gebruiken:

1. Selecteer een incident en selecteer vervolgens **onderzoeken**. Hiermee gaat u naar het onderzoek diagram. De grafiek bevat een illustrerende kaart van de entiteiten die rechtstreeks zijn verbonden met de waarschuwing en elke resource die u hebt verbonden.

   > [!IMPORTANT] 
   > U kunt het incident alleen onderzoeken als u de velden voor entiteits toewijzing hebt gebruikt bij het instellen van de analytische regel. Het onderzoek diagram vereist dat uw oorspronkelijke incident entiteiten bevat.

   ![Kaart weergeven](media/tutorial-investigate-cases/map1.png)

1. Selecteer een entiteit om het deel venster **entiteiten** te openen, zodat u informatie over die entiteit kunt controleren.

    ![Entiteiten in kaart weer geven](media/tutorial-investigate-cases/map-entities.png)
  
1. Breid uw onderzoek uit door over te bewegen op elke entiteit om een lijst met vragen te tonen die zijn ontworpen door onze beveiligings experts en analisten per entiteits type om uw onderzoek te verdiepen. We noemen deze opties voor het **verkennen van query's**.

    ![Meer details bekijken](media/tutorial-investigate-cases/exploration-cases.png)

   Op een computer kunt u bijvoorbeeld gerelateerde waarschuwingen aanvragen. Als u een onderzoek query selecteert, worden de resulterende vergunningen toegevoegd aan de grafiek. In dit voor beeld wordt het selecteren van **gerelateerde waarschuwingen** met de volgende waarschuwingen in de grafiek weer gegeven:

    ![Gerelateerde waarschuwingen weer geven](media/tutorial-investigate-cases/related-alerts.png)

1. Voor elke onderzoek query kunt u de optie selecteren voor het openen van de onbewerkte gebeurtenis resultaten en de query die in Log Analytics wordt gebruikt door **gebeurtenissen\>** te selecteren.

1. Om het incident te begrijpen, biedt de grafiek een parallelle tijd lijn.

    ![Tijd lijn weer geven in kaart](media/tutorial-investigate-cases/map-timeline.png)

1. Beweeg de muis aanwijzer over de tijd lijn om te zien welke items in de grafiek zich voordoen op welk moment.

    ![Tijd lijn gebruiken in kaart voor het onderzoeken van waarschuwingen](media/tutorial-investigate-cases/use-timeline.png)



## <a name="next-steps"></a>Volgende stappen
In deze zelf studie hebt u geleerd hoe u aan de slag gaat met het onderzoeken van incidenten met behulp van Azure Sentinel. Ga verder met de zelf studie voor het [reageren op bedreigingen met behulp van automatische playbooks](tutorial-respond-threats-playbook.md).
> [!div class="nextstepaction"]
> [Reageer op bedreigingen](tutorial-respond-threats-playbook.md) om uw reacties op bedreigingen te automatiseren.

