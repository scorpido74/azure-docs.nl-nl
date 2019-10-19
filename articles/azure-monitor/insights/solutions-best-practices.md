---
title: Beheer oplossing in Aanbevolen procedures voor Azure | Microsoft Docs
description: ''
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/27/2017
ms.openlocfilehash: 28ae01fe28b1b2d6af95567e529c7c9ae17920e4
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553927"
---
# <a name="best-practices-for-creating-management-solutions-in-azure-preview"></a>Aanbevolen procedures voor het maken van beheer oplossingen in azure (preview)
> [!NOTE]
> Dit is voorlopige documentatie voor het maken van beheer oplossingen in azure die momenteel als preview-versie beschikbaar zijn. Elk schema dat hieronder wordt beschreven, kan worden gewijzigd.  

In dit artikel worden aanbevolen procedures beschreven voor [het maken van een beheer oplossings bestand](solutions-solution-file.md) in Azure.  Deze informatie wordt bijgewerkt wanneer er aanvullende aanbevolen procedures worden geïdentificeerd.

## <a name="data-sources"></a>Gegevensbronnen
- Gegevens bronnen kunnen worden [geconfigureerd met een resource manager-sjabloon](../../azure-monitor/platform/template-workspace-configuration.md), maar ze mogen niet worden opgenomen in een oplossings bestand.  De reden hiervoor is dat het configureren van gegevens bronnen momenteel niet idempotent dat uw oplossing bestaande configuratie in de werk ruimte van de gebruiker kan overschrijven.<br><br>Uw oplossing vereist bijvoorbeeld waarschuwings-en fout gebeurtenissen in het logboek voor toepassings gebeurtenissen.  Als u deze gegevens bron in uw oplossing opgeeft, kunt u een risico voor het verwijderen van informatie gebeurtenissen als de gebruiker deze in hun werk ruimte had geconfigureerd.  Als u alle gebeurtenissen hebt opgenomen, verzamelt u mogelijk overmatige informatie gebeurtenissen in de werk ruimte van de gebruiker.

- Als uw oplossing gegevens van een van de standaard gegevens bronnen vereist, moet u deze definiëren als een vereiste.  Status in de documentatie dat de klant zelf de gegevens bron moet configureren.  
- Voeg een verificatie bericht voor de [gegevens stroom](../../azure-monitor/platform/view-designer-tiles.md) toe aan alle weer gaven in uw oplossing om de gebruiker te instrueren over gegevens bronnen die moeten worden geconfigureerd voor de gegevens die moeten worden verzameld.  Dit bericht wordt weer gegeven op de tegel van de weer gave wanneer vereiste gegevens niet worden gevonden.


## <a name="runbooks"></a>Runbooks
- Voeg een [Automation-schema](../../automation/automation-schedules.md) toe voor elk runbook in uw oplossing die volgens een schema moet worden uitgevoerd.
- Neem de [IngestionAPI-module](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) op in uw oplossing die door runbooks wordt gebruikt voor het schrijven van gegevens naar de log Analytics opslag plaats.  Configureer de oplossing om te [verwijzen](solutions-solution-file.md#solution-resource) naar deze bron, zodat deze blijft als de oplossing wordt verwijderd.  Hiermee kunnen meerdere oplossingen de module delen.
- Gebruik [automatiserings variabelen](../../automation/automation-schedules.md) om waarden op te geven voor de oplossing die gebruikers mogelijk later willen wijzigen.  Zelfs als de oplossing zo is geconfigureerd dat deze de variabele bevat, kan de waarde ervan nog steeds worden gewijzigd.

## <a name="views"></a>Weergaven
- Alle oplossingen moeten één weer gave bevatten die wordt weer gegeven in de portal van de gebruiker.  De weer gave kan meerdere [visualisatie onderdelen](../../azure-monitor/platform/view-designer-parts.md) bevatten voor het illustreren van verschillende gegevens sets.
- Voeg een verificatie bericht voor de [gegevens stroom](../../azure-monitor/platform/view-designer-tiles.md) toe aan alle weer gaven in uw oplossing om de gebruiker te instrueren over gegevens bronnen die moeten worden geconfigureerd voor de gegevens die moeten worden verzameld.
- Configureer de oplossing zodanig dat deze de weer gave [bevat](solutions-solution-file.md#solution-resource) zodat deze wordt verwijderd als de oplossing wordt verwijderd.

## <a name="alerts"></a>Waarschuwingen
- Definieer de lijst met ontvangers als een para meter in het oplossings bestand, zodat de gebruiker deze kan definiëren bij de installatie van de oplossing.
- Configureer de oplossing om te [verwijzen](solutions-solution-file.md#solution-resource) naar waarschuwings regels zodat de configuratie van de gebruiker kan worden gewijzigd.  Ze willen mogelijk wijzigingen aanbrengen, zoals het wijzigen van de lijst met geadresseerden, het wijzigen van de drempel waarde van de waarschuwing of het uitschakelen van de waarschuwings regel. 


## <a name="next-steps"></a>Volgende stappen
* Door loop het basis proces van het [ontwerpen en bouwen van een beheer oplossing](solutions-creating.md).
* Meer informatie over het [maken van een oplossings bestand](solutions-solution-file.md).
* [Voeg opgeslagen Zoek opdrachten en waarschuwingen](solutions-resources-searches-alerts.md) toe aan uw beheer oplossing.
* [Voeg weer gaven](solutions-resources-views.md) toe aan uw beheer oplossing.
* [Voeg Automation-runbooks en andere resources](solutions-resources-automation.md) toe aan uw beheer oplossing.

