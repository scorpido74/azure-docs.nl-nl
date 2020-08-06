---
title: Diagnostische gegevens instellen
titleSuffix: Azure Digital Twins
description: Zie logboek registratie met Diagnostische instellingen inschakelen.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 402eda92d5cc8e5c2566345d31c9ae625b82695c
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809392"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Problemen oplossen met Azure Digital Apparaatdubbels: logboek registratie van diagnostische gegevens

Azure Digital Apparaatdubbels verzamelt [metrische](troubleshoot-metrics.md) gegevens voor uw service-exemplaar die informatie geven over de status van uw resources. U kunt deze metrische gegevens gebruiken om de algemene status van de Azure Digital Apparaatdubbels-service en de gekoppelde resources te beoordelen. Met deze gebruikers gerichte statistieken kunt u zien wat er gebeurt met uw Azure Digital Apparaatdubbels en kunt u de hoofd oorzaak van problemen analyseren zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

In dit artikel wordt beschreven hoe u **Diagnostische logboek registratie** inschakelt voor uw metrische gegevens uit uw Azure Digital apparaatdubbels-exemplaar. U kunt deze logboeken gebruiken om problemen met de service op te lossen.

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnostische instellingen inschakelen met de Azure Portal

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **Diagnostische instellingen** in het menu en vervolgens **Diagnostische instelling toevoegen**.

    :::image type="content" source="media/troubleshoot-metrics/diagnostic-settings.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

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

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in de geconfigureerde doel weer op de pagina **Diagnostische instellingen** voor uw exemplaar. 

## <a name="next-steps"></a>Volgende stappen

* Zie [*logboek gegevens verzamelen en gebruiken van uw Azure-resources*](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.
* Zie voor meer informatie over de metrische gegevens van de Azure Digital Apparaatdubbels [*problemen oplossen: metrische gegevens weer geven met Azure monitor*](troubleshoot-metrics.md).
