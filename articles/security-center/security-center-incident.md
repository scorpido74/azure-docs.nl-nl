---
title: Beveiligings incidenten beheren in Azure Security Center | Microsoft Docs
description: Dit document helpt u bij het gebruik van Azure Security Center voor het beheren van beveiligings incidenten.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 28a6ea4ed40df909b4d74ff52703babb8e8cd949
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84791729"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Beveiligings incidenten beheren in Azure Security Center

Uitsorteren en onderzoek van beveiligings waarschuwingen kan tijdrovend zijn voor zelfs de meest ervaren beveiligings analisten. Voor veel is het moeilijk te weten waar u moet beginnen. 

Security Center gebruikt [Analytics](security-center-detection-capabilities.md) om de informatie te verbinden tussen afzonderlijke [beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md). Met deze verbindingen kunt Security Center een enkele weer gave van een aanvals campagne en de bijbehorende waarschuwingen bieden die u helpen de acties van de aanvaller en de betrokken resources te begrijpen.

Op deze pagina vindt u een overzicht van incidenten in Security Center.

## <a name="what-is-a-security-incident"></a>Wat is een beveiligingsincident?

In Security Center is een beveiligingsincident een samenloop van alle waarschuwingen voor een resource die overeenstemt met [kill chain](alerts-reference.md#intentions)-patronen. Incidenten worden weer gegeven op de pagina [beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) . Selecteer een incident om de gerelateerde waarschuwingen weer te geven en meer informatie te krijgen.

## <a name="managing-security-incidents"></a>Beveiligingsincidenten beheren

1. Selecteer op de pagina overzicht van Security Center de tegel **beveiligings waarschuwingen** . De incidenten en waarschuwingen worden weer gegeven. U ziet dat beveiligings incidenten een ander pictogram voor beveiligings waarschuwingen hebben.

    ![Beveiligings incidenten weer geven](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Als u details wilt weer geven, selecteert u een incident. Op de pagina **beveiligings incident** vindt u meer informatie. 

    [![Reageren op beveiligings incidenten in Azure Security Center](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    In het linkerdeel venster van de pagina beveiligings incident ziet u informatie op hoog niveau over het beveiligings incident: titel, Ernst, status, activiteit tijd, beschrijving en de betreffende resource. Naast de betreffende resource ziet u de relevante Azure-Tags. Gebruik deze tags om de organisatie context van de resource af te leiden wanneer de waarschuwing wordt onderzocht.

    Het rechterdeel venster bevat het tabblad **waarschuwingen** met de beveiligings waarschuwingen die zijn gecorreleerd als onderdeel van dit incident. 

    >[!TIP]
    > Voor meer informatie over een specifieke waarschuwing, selecteert u deze. 

    [![Tabblad actie ondernemen van incident](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Als u wilt overschakelen naar het tabblad **actie ondernemen** , selecteert u het tabblad of de knop aan de onderkant van het rechterdeel venster. Gebruik dit tabblad om verdere acties uit te voeren, zoals:
    - *De dreiging beperken* : biedt hand matige herstel stappen voor dit beveiligings incident
    - *Toekomstige aanvallen voor komen* : bevat aanbevelingen voor beveiliging om de kwets baarheid te verminderen, de beveiliging postuur te verbeteren en toekomstige aanvallen te voor komen
    - *Automatische reactie activeren* : biedt de mogelijkheid om een logische app als reactie op dit beveiligings incident te activeren
    - *Vergelijk bare waarschuwingen onderdrukken* : biedt de mogelijkheid om toekomstige waarschuwingen met vergelijk bare kenmerken te onderdrukken als de waarschuwing niet relevant is voor uw organisatie 

   > [!NOTE]
   > Dezelfde waarschuwing kan bestaan als onderdeel van een incident, en als een zelfstandige waarschuwing worden weer gegeven.

1. Volg de herstels tappen bij elke waarschuwing om de bedreigingen in het incident op te lossen.


## <a name="next-steps"></a>Volgende stappen

Op deze pagina zijn de mogelijkheden van het beveiligings incident van Security Center uitgelegd. Zie de volgende pagina's voor verwante informatie:

* [Beveiliging tegen bedreigingen in Security Center](threat-protection.md)
* [Beveiligings waarschuwingen in Security Center](security-center-alerts-overview.md)
* [Beveiligingswaarschuwingen beheren en erop reageren](security-center-managing-and-responding-alerts.md)