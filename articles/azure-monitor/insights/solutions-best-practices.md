---
title: Beheeroplossing in Azure-aanbevolen procedures | Microsoft Documenten
description: ''
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 837fb87d73698961ec1550b122840563d1707f8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663195"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Aanbevolen procedures voor het maken van beheeroplossingen in Azure (Voorbeeld)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheeroplossingen in Azure die momenteel in preview zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.  

In dit artikel vindt u aanbevolen procedures voor [het maken van een beheeroplossingsbestand](solutions-solution-file.md) in Azure.  Deze informatie wordt bijgewerkt wanneer aanvullende aanbevolen procedures worden geïdentificeerd.

## <a name="data-sources"></a>Gegevensbronnen
- Gegevensbronnen kunnen worden [geconfigureerd met een resourcebeheersjabloon,](../../azure-monitor/platform/template-workspace-configuration.md)maar ze mogen niet worden opgenomen in een oplossingsbestand.  De reden hiervoor is dat het configureren van gegevensbronnen momenteel niet idempotent is, wat betekent dat uw oplossing bestaande configuratie in de werkruimte van de gebruiker kan overschrijven.<br><br>Voor uw oplossing kunnen bijvoorbeeld waarschuwing- en foutgebeurtenissen uit het gebeurtenislogboek van toepassing nodig zijn.  Als u dit opgeeft als gegevensbron in uw oplossing, loopt u het risico informatiegebeurtenissen te verwijderen als de gebruiker dit in zijn werkruimte heeft geconfigureerd.  Als u alle gebeurtenissen hebt opgenomen, verzamelt u mogelijk overmatige informatiegebeurtenissen in de werkruimte van de gebruiker.

- Als uw oplossing gegevens uit een van de standaardgegevensbronnen vereist, moet u dit definiëren als een vereiste.  Staat in de documentatie dat de klant de gegevensbron zelf moet configureren.  
- Voeg een [dataflowverificatiebericht](../../azure-monitor/platform/view-designer-tiles.md) toe aan weergaven in uw oplossing om de gebruiker te instrueren over gegevensbronnen die moeten worden geconfigureerd om vereiste gegevens te verzamelen.  Dit bericht wordt weergegeven op de tegel van de weergave wanneer de vereiste gegevens niet worden gevonden.


## <a name="runbooks"></a>Runbooks
- Voeg een [automatiseringsschema](../../automation/automation-schedules.md) toe voor elk runbook in uw oplossing die volgens een planning moet worden uitgevoerd.
- Neem de [IngestionAPI-module](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) op in uw oplossing die moet worden gebruikt door runbooks die gegevens schrijven naar de Log Analytics-opslagplaats.  Configureer de oplossing om naar deze bron te [verwijzen,](solutions-solution-file.md#solution-resource) zodat deze blijft bestaan als de oplossing wordt verwijderd.  Hierdoor kunnen meerdere oplossingen de module delen.
- Gebruik [automatiseringsvariabelen](../../automation/automation-schedules.md) om waarden te geven aan de oplossing die gebruikers later mogelijk willen wijzigen.  Zelfs als de oplossing is geconfigureerd om de variabele te bevatten, kan de waarde nog steeds worden gewijzigd.

## <a name="views"></a>Weergaven
- Alle oplossingen moeten één weergave bevatten die wordt weergegeven in de portal van de gebruiker.  De weergave kan meerdere [visualisatieonderdelen](../../azure-monitor/platform/view-designer-parts.md) bevatten om verschillende sets gegevens te illustreren.
- Voeg een [dataflowverificatiebericht](../../azure-monitor/platform/view-designer-tiles.md) toe aan weergaven in uw oplossing om de gebruiker te instrueren over gegevensbronnen die moeten worden geconfigureerd om vereiste gegevens te verzamelen.
- Configureer de oplossing om de weergave te [bevatten,](solutions-solution-file.md#solution-resource) zodat deze wordt verwijderd als de oplossing wordt verwijderd.

## <a name="alerts"></a>Waarschuwingen
- Definieer de lijst met geadresseerden als parameter in het oplossingsbestand, zodat de gebruiker ze kan definiëren wanneer hij de oplossing installeert.
- Configureer de oplossing [op basis van waarschuwingsregels,](solutions-solution-file.md#solution-resource) zodat gebruikers hun configuratie kunnen wijzigen.  Ze willen mogelijk wijzigingen aanbrengen, zoals het wijzigen van de lijst met geadresseerden, het wijzigen van de drempelwaarde van de waarschuwing of het uitschakelen van de waarschuwingsregel. 


## <a name="next-steps"></a>Volgende stappen
* Loop door het basisproces van het ontwerpen en bouwen van [een managementoplossing.](solutions-creating.md)
* Meer informatie over het [maken van een oplossingsbestand](solutions-solution-file.md).
* [Voeg opgeslagen zoekopdrachten en waarschuwingen toe](solutions-resources-searches-alerts.md) aan uw beheeroplossing.
* [Weergaven toevoegen](solutions-resources-views.md) aan uw beheeroplossing.
* [Voeg automation runbooks en andere resources toe](solutions-resources-automation.md) aan uw beheeroplossing.

