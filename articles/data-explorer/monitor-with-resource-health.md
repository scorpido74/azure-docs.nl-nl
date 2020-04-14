---
title: Azure Data Explorer bewaken met resourcestatus
description: Gebruik Azure Resource Health om azure data explorer-bronnen te controleren.
author: orspod
ms.author: orspodek
ms.reviewer: prvavill
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: ee42f45d8c96e908061142b5a8e6b2e5cee21993
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262057"
---
# <a name="monitor-azure-data-explorer-using-resource-health-preview"></a>Azure Data Explorer bewaken met resourcestatus (voorbeeld)

[Resourcestatus](/azure/service-health/resource-health-overview) voor Azure Data Explorer informeert u over de status van uw Azure Data Explorer-bron en biedt bruikbare aanbevelingen om problemen op te lossen. Resourcestatus wordt elke 1-2 minuten bijgewerkt en rapporteert de huidige en vroegere status van uw resources. 

Resourcestatus bepaalt de status van uw Azure Data Explorer-bron door verschillende statuscontroles te onderzoeken, zoals:
* Beschikbaarheid van resources
* Queryfouten

## <a name="access-resource-health-reporting"></a>Rapportage over resourcestatus

1. Selecteer In de [Azure-portal](https://portal.azure.com/)de optie **Monitor** uit de lijst met services.
1. Selecteer de status **van de servicestatus** > **van resources**.
1. Selecteer uw abonnement in de vervolgkeuzelijst **Abonnement.** Selecteer **Azure Data Explorer**in de vervolgkeuzelijst **Resourcetype** .
1. In de resulterende tabel worden alle bronnen in het gekozen abonnement weergegeven. Elke resource heeft een statuspictogram dat de resourcestatus aangeeft.
1. Selecteer uw resource om de status en aanbevelingen [van de resourcestatus](#resource-health-status) weer te geven.

    ![Overzicht](media/monitor-with-resource-health/resource-health-overview.png)

## <a name="resource-health-status"></a>Status van resourcestatus

De status van een resource wordt weergegeven met een van de volgende statussen, beschikbaar, niet beschikbaar en onbekend.

### <a name="available"></a>Beschikbaar

Een status van **Beschikbaar** geeft aan dat uw Azure Data Explorer-bron in orde is en geen problemen heeft.

![Beschikbaar](media/monitor-with-resource-health/available.png)

### <a name="unavailable"></a>Niet beschikbaar

Een status van **Niet beschikbaar** geeft aan dat er een doorlopend probleem is met uw Azure Data Explorer-bron waardoor deze niet beschikbaar is voor query's en inname. Knooppunten in uw Azure Data Explorer-bron zijn bijvoorbeeld onverwacht opnieuw opgestart. Neem contact op met ondersteuning als uw Azure Data Explorer-bron gedurende een langere periode in deze status blijft staan.

![Niet beschikbaar](media/monitor-with-resource-health/unavailable.png)

### <a name="unknown"></a>Onbekend

Een status van **Onbekend** geeft aan dat **Resource Health** al meer dan 10 minuten geen informatie heeft ontvangen over deze Azure Data Explorer-bron. Deze status is geen definitieve indicatie van de bronstatus van Azure Data Explorer, maar is een belangrijk gegevenspunt in het probleemoplossingsproces. Als uw Azure Data Explorer-cluster werkt zoals verwacht, wordt de status binnen enkele minuten gewijzigd in **Beschikbaar.** De **status Onbekende** kan suggereren dat een gebeurtenis in het platform van invloed is op de bron. 

> [!TIP]
> De status van de Azure Data Explorer-clusterbron is **onbekend** als deze in een gestopte status staat.

![Onbekend](media/monitor-with-resource-health/unknown.png)

## <a name="historical-information"></a>Historische informatie

In het deelvenster **Resourcestatus** > **Statusgeschiedenis**toegang tot maximaal vier weken informatie over de status van resources. Selecteer de pijl voor meer informatie over de problemen met de statusgebeurtenis die in dit deelvenster worden gerapporteerd. 

![Geschiedenis](media/monitor-with-resource-health/healthhistory.png)

## <a name="next-steps"></a>Volgende stappen

* [Waarschuwingen voor resourcestatus configureren](https://docs.microsoft.com/azure/service-health/resource-health-alert-arm-template-guide)
* [Zelfstudie: Gegevens over het innemen en querybewaking in Azure Data Explorer](ingest-data-no-code.md)
* [Prestaties, status en gebruik van Azure Data Explorer bewaken met statistieken](using-metrics.md)