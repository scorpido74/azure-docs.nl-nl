---
title: Diagnostische gegevens instellen
titleSuffix: Azure Digital Twins
description: Zie logboek registratie met Diagnostische instellingen inschakelen.
author: baanders
ms.author: baanders
ms.date: 7/27/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 60ee2102a2b725c68e29afec9a44de03e52e9467
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374534"
---
# <a name="enable-logging-with-diagnostics-settings"></a>Logboek registratie met Diagnostische instellingen inschakelen

U kunt ervoor kiezen om uw metrische gegevens te verzenden naar [log Analytics](../azure-monitor/log-query/get-started-portal.md), een [Event hubs](../event-hubs/event-hubs-about.md) -eind punt of [Azure Storage](../storage/blobs/storage-blobs-overview.md) door logboek registratie in te scha kelen met Diagnostische instellingen voor uw exemplaar.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnostische instellingen inschakelen met de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **Diagnostische instellingen** in het menu en vervolgens **Diagnostische instelling toevoegen**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

3. Vul op de onderstaande pagina de volgende waarden in:
 * **Naam van de diagnostische instelling**: Geef de diagnostische instellingen een naam.
 * **Categorie Details**: Kies welke bewerkingen u wilt bewaken en schakel de selectie vakjes in om diagnostische gegevens voor deze bewerkingen in te scha kelen. De bewerkingen waarvan de diagnostische instellingen kunnen rapporteren, zijn:
    - DigitalTwinsOperation
    - EventRoutesOperation
    - ModelsOperation
    - QueryOperation
    - AllMetrics
 * **Doel Details**: Kies waar u de logboeken wilt verzenden. U kunt een combi natie van de drie opties selecteren:
    - Verzenden naar Log Analytics
    - Archiveren naar een opslagaccount
    - Streamen naar een Event Hub

    U wordt mogelijk gevraagd aanvullende gegevens in te vullen als deze nodig zijn voor uw doel selectie.  
    
4. Sla de nieuwe instellingen op. 

    :::image type="content" source="media/how-to-set-up-diagnostics/diagnostic-settings-details.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in de geconfigureerde doel weer op de pagina **Diagnostische instellingen** voor uw exemplaar. 

## <a name="next-steps"></a>Volgende stappen

* Zie [*logboek gegevens verzamelen en gebruiken van uw Azure-resources*](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.
* Zie [ *How-to: metrisch weer geven met Azure monitor* voor meer informatie over de metrische gegevens van de Azure Digital apparaatdubbels](how-to-view-metrics.md)