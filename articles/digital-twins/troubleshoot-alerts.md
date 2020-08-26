---
title: Waarschuwingen instellen
titleSuffix: Azure Digital Twins
description: Zie waarschuwingen inschakelen op de metrische gegevens van Azure Digital Apparaatdubbels.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ded2f54379e60e8e3fc26d9c2166930a6f953078
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854863"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Problemen met Azure Digital Apparaatdubbels oplossen: waarschuwingen

Azure Digital Apparaatdubbels verzamelt [metrische](troubleshoot-metrics.md) gegevens voor uw service-exemplaar die informatie geven over de status van uw resources. U kunt deze metrische gegevens gebruiken om de algemene status van de Azure Digital Apparaatdubbels-service en de gekoppelde resources te beoordelen.

**Waarschuwingen** geven u proactief op de hoogte wanneer er belang rijke voor waarden worden gevonden in uw metrische gegevens. Hiermee kunt u problemen identificeren en verhelpen voordat de gebruikers van uw systeem ze merken. Meer informatie over waarschuwingen vindt u in [*overzicht van waarschuwingen in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md).

## <a name="turn-on-alerts"></a>Waarschuwingen inschakelen

Hier vindt u informatie over het inschakelen van waarschuwingen voor uw Azure Digital Apparaatdubbels-exemplaar:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **waarschuwingen** in het menu en vervolgens **+ nieuwe waarschuwings regel**.

3. Op de volgende pagina *waarschuwings regel maken* kunt u de prompts voor het definiëren van voor waarden, te activeren acties en waarschuwings Details volgen.     
    * De **Scope** gegevens moeten automatisch worden gevuld met de details van uw exemplaar
    * U definieert **voor waarde** en **actie groeps** gegevens voor het aanpassen van waarschuwings triggers en antwoorden
    * Voer in de sectie **Details van waarschuwings regel** de _naam van de waarschuwings regel_in _(optioneel)_. U kunt het selectie vakje _waarschuwings regel inschakelen bij maken_ selecteren als u wilt dat de waarschuwing actief wordt zodra deze wordt gemaakt.
    * Op basis van bepaalde voor waarden en acties die u toevoegt, moet u _waarschuwings regel opslaan in resource groep_ en _Ernst_ van de respectieve vervolg keuzelijsten selecteren.

4. Selecteer de knop _waarschuwings regel maken_ om uw waarschuwings regel te maken.

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="Scherm opname van de pagina waarschuwings regel maken met secties voor het bereik, de voor waarde en de actie groep" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

:::image type="content" source="media/troubleshoot-alerts/alert-rule-details.png" alt-text="Scherm afbeelding van het gedeelte waarschuwings regel Details van het maken van een waarschuwings regel" lightbox="media/troubleshoot-alerts/alert-rule-details.png":::

Zie [*overzicht van waarschuwingen in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md)voor een rond overzicht van het invullen van deze velden. Hieronder ziet u enkele voor beelden van hoe de stappen eruitzien voor Azure Digital Apparaatdubbels.

Hier volgt een fragment van het proces *voor voor waarden selecteren* waarin wordt uitgelegd welke typen waarschuwings signalen er beschikbaar zijn voor Azure Digital apparaatdubbels. Op deze pagina kunt u het type signaal filteren en het gewenste signaal in een lijst selecteren.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="Scherm opname met de eerste pagina signaal logica configureren. Er is een hooglicht op het vak signaal type voor het selecteren van metrische gegevens of activiteiten logboeken en een lijst met metrische gegevens waaronder kan worden geselecteerd.":::

Nadat u een signaal hebt geselecteerd, wordt u gevraagd om de logica van de waarschuwing te configureren. U kunt filteren op een dimensie, een drempel waarde voor uw waarschuwing instellen en de frequentie van controles voor de voor waarde instellen. Hier volgt een voor beeld van het instellen van een waarschuwing voor wanneer de gemiddelde waarde voor het aantal mislukte routerings fouten boven 5% komt.

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="Scherm afbeelding met de tweede pagina signaal logica configureren. Op de pagina wordt de geschiedenis van metrische gegevens weer gegeven, heeft een gebied om te filteren op een dimensie zoals Event Grid bewerkingen, en een sectie voor het definiëren van de logica van een waarschuwing zoals ' gemiddelde is groter dan 5 '":::
 
Nadat u de waarschuwingen hebt ingesteld, worden deze weer gegeven op de pagina *waarschuwingen* voor uw exemplaar.
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="Scherm afbeelding van de pagina met waarschuwingen en de knop om toe te voegen. Er is één waarschuwing geconfigureerd" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>Volgende stappen

* Zie [*overzicht van waarschuwingen in Microsoft Azure*](../azure-monitor/platform/alerts-overview.md)voor meer informatie over waarschuwingen met Azure monitor.
* Zie voor meer informatie over de metrische gegevens van de Azure Digital Apparaatdubbels [*problemen oplossen: metrische gegevens weer geven met Azure monitor*](troubleshoot-metrics.md).
* Zie [*probleem oplossing: diagnostische gegevens instellen*](troubleshoot-diagnostics.md)voor meer informatie over het inschakelen van diagnostische logboek registratie voor uw metrische gegevens.
