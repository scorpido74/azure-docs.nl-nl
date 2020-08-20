---
title: Diagnostische gegevens instellen
titleSuffix: Azure Digital Twins
description: Zie logboek registratie met Diagnostische instellingen inschakelen.
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: 5091edbf9138cb8ff03df193dcbeed692aaf13e3
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612398"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Problemen oplossen met Azure Digital Apparaatdubbels: logboek registratie van diagnostische gegevens

Azure Digital Apparaatdubbels verzamelt [metrische](troubleshoot-metrics.md) gegevens voor uw service-exemplaar die informatie geven over de status van uw resources. U kunt deze metrische gegevens gebruiken om de algemene status van de Azure Digital Apparaatdubbels-service en de gekoppelde resources te beoordelen. Met deze gebruikers gerichte statistieken kunt u zien wat er gebeurt met uw Azure Digital Apparaatdubbels en kunt u de hoofd oorzaak van problemen analyseren zonder dat u contact hoeft op te nemen met de ondersteuning van Azure.

In dit artikel wordt beschreven hoe u **Diagnostische logboek registratie** inschakelt voor uw metrische gegevens uit uw Azure Digital apparaatdubbels-exemplaar. U kunt deze logboeken gebruiken om problemen met de service op te lossen en diagnostische instellingen configureren voor het verzenden van Azure Digital Apparaatdubbels-metrische gegevens naar verschillende bestemmingen. Meer informatie over deze instellingen vindt u in [*Diagnostische instellingen maken om platform logboeken en-metrische gegevens naar verschillende bestemmingen te verzenden*](../azure-monitor/platform/diagnostic-settings.md).

## <a name="turn-on-diagnostic-settings-with-the-azure-portal"></a>Diagnostische instellingen inschakelen met de Azure Portal

Hier vindt u informatie over het inschakelen van diagnostische instellingen voor uw Azure Digital Apparaatdubbels-exemplaar:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Digital apparaatdubbels-exemplaar. U kunt het vinden door de naam ervan in de zoek balk van de portal te typen. 

2. Selecteer **Diagnostische instellingen** in het menu en vervolgens **Diagnostische instelling toevoegen**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

3. Vul op de onderstaande pagina de volgende waarden in:
     * **Naam van de diagnostische instelling**: Geef de diagnostische instellingen een naam.
     * **Categorie Details**: Kies welke bewerkingen u wilt bewaken en schakel de selectie vakjes in om diagnostische gegevens voor deze bewerkingen in te scha kelen. De bewerkingen waarvan de diagnostische instellingen kunnen rapporteren, zijn:
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        Meer informatie over deze opties kunt u vinden in de sectie [*categorie gegevens*](#category-details) hieronder.
     * **Doel Details**: Kies waar u de logboeken wilt verzenden. U kunt een combi natie van de drie opties selecteren:
        - Verzenden naar Log Analytics
        - Archiveren naar een opslagaccount
        - Streamen naar een Event Hub

        U wordt mogelijk gevraagd aanvullende gegevens in te vullen als deze nodig zijn voor uw doel selectie.  
    
4. Sla de nieuwe instellingen op. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="Scherm opname van de pagina met Diagnostische instellingen en de knop die u wilt toevoegen":::

Nieuwe instellingen worden in ongeveer 10 minuten van kracht. Daarna worden logboeken weer gegeven in de geconfigureerde doel weer op de pagina **Diagnostische instellingen** voor uw exemplaar. 

## <a name="category-details"></a>Categorie Details

Hier vindt u meer informatie over de logboek categorieën die kunnen worden geselecteerd onder **categorie Details** bij het instellen van diagnostische instellingen.

| Logboekcategorie | Beschrijving |
| --- | --- |
| ADTModelsOperation | Alle API-aanroepen voor modellen vastleggen |
| ADTQueryOperation | Alle API-aanroepen met betrekking tot Query's vastleggen |
| ADTEventRoutesOperation | Registreer alle API-aanroepen die betrekking hebben op gebeurtenis routes en voor het uitvoeren van gebeurtenissen van Azure Digital Apparaatdubbels naar een eindpunt service, zoals Event Grid, Event Hubs en Service Bus |
| ADTDigitalTwinsOperation | Alle API-aanroepen vastleggen die betrekking hebben op Azure Digital Apparaatdubbels |

Elke logboek categorie bestaat uit bewerkingen van schrijven, lezen, verwijderen en actie.  Deze toewijzing REST API aanroepen als volgt:

| Gebeurtenistype | REST API bewerkingen |
| --- | --- |
| Schrijven | PUT and PATCH |
| Lezen | GET |
| Verwijderen | DELETE |
| Bewerking | POST |

Hier volgt een uitgebreide lijst met de bewerkingen en bijbehorende [Azure Digital apparaatdubbels rest API-aanroepen](https://docs.microsoft.com/rest/api/azure-digitaltwins/) die in elke categorie zijn geregistreerd. 

>[!NOTE]
> Elke logboek categorie bevat verschillende bewerkingen/REST API-aanroepen. In de onderstaande tabel wordt elke logboek categorie toegewezen aan alle bewerkingen/REST API-aanroepen, totdat de volgende logboek categorie wordt weer gegeven. 

| Logboekcategorie | Bewerking | REST API-aanroepen en andere gebeurtenissen |
| --- | --- | --- |
| ADTModelsOperation | Micro soft. DigitalTwins/modellen/schrijven | Update-API voor digitale dubbele modellen |
|  | Micro soft. DigitalTwins/modellen/lezen | Digitale twee modellen ontvangen op id en lijst-Api's |
|  | Micro soft. DigitalTwins/modellen/verwijderen | Digitale dubbele modellen API verwijderen |
|  | Micro soft. DigitalTwins/modellen/actie | Digitale dubbele modellen API toevoegen |
| ADTQueryOperation | Micro soft. DigitalTwins/query/actie | Query Apparaatdubbels-API |
| ADTEventRoutesOperation | Micro soft. DigitalTwins/eventroutes/schrijven | Gebeurtenis routes toevoegen API |
|  | Micro soft. DigitalTwins/eventroutes/lezen | Gebeurtenis routes ophalen op basis van id en lijst-Api's |
|  | Micro soft. DigitalTwins/eventroutes/verwijderen | Gebeurtenis routes verwijderen API |
|  | Micro soft. DigitalTwins/eventroutes/actie | Fout bij het publiceren van gebeurtenissen naar een eindpunt service (geen API-aanroep) |
| ADTDigitalTwinsOperation | Micro soft. DigitalTwins/DigitalTwins/schrijven | Digitale Apparaatdubbels toevoegen, relatie toevoegen, bijwerken, onderdeel bijwerken |
|  | Micro soft. DigitalTwins/DigitalTwins/lezen | Digital Apparaatdubbels get by id, component ophalen, relatie ophalen op id, binnenkomende relaties weer geven, lijst relaties |
|  | Micro soft. DigitalTwins/DigitalTwins/verwijderen | Digitale Apparaatdubbels verwijderen, relatie verwijderen |
|  | Micro soft. DigitalTwins/DigitalTwins/actie | Digital Apparaatdubbels, telemetrie van het onderdeel verzenden, telemetrie verzenden |

## <a name="next-steps"></a>Volgende stappen

* Zie [*logboek gegevens verzamelen en gebruiken van uw Azure-resources*](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over het configureren van diagnostiek.
* Zie voor meer informatie over de metrische gegevens van de Azure Digital Apparaatdubbels [*problemen oplossen: metrische gegevens weer geven met Azure monitor*](troubleshoot-metrics.md).
* Zie [*probleem oplossing: waarschuwingen instellen*](troubleshoot-alerts.md)voor meer informatie over het inschakelen van waarschuwingen voor uw metrische gegevens.
