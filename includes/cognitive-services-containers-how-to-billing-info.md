---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77474271"
---
Query's in de container worden gefactureerd op de prijscategorie van `ApiKey`de Azure-bron die wordt gebruikt voor de .

Azure Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat ze zijn verbonden met het eindpunt voor meting/ facturering. U moet de containers te allen tijde in staat stellen factureringsgegevens met het factureringseindpunt te communiceren. Cognitive Services-containers verzenden geen klantgegevens, zoals de afbeelding of tekst die wordt geanalyseerd, naar Microsoft.

### <a name="connect-to-azure"></a>Verbinding maken met Azure

De container heeft de factureringsargumentwaarden nodig om uit te voeren. Met deze waarden kan de container verbinding maken met het factureringseindpunt. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten. Als de container binnen het toegestane tijdvenster geen verbinding maakt met Azure, blijft de container worden uitgevoerd, maar worden query's niet weergegeven totdat het factureringseindpunt is hersteld. De verbinding wordt 10 keer geprobeerd op hetzelfde tijdsinterval van 10 tot 15 minuten. Als het geen verbinding kan maken met het factureringseindpunt binnen de 10 pogingen, stopt de container met het serveren van aanvragen.

### <a name="billing-arguments"></a>Factureringsargumenten

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` De <span class="docon docon-navigate-external x-hidden-focus"></span> </a> opdracht start de container wanneer alle drie de volgende opties zijn voorzien van geldige waarden:

| Optie | Beschrijving |
|--------|-------------|
| `ApiKey` | De API-sleutel van de resource Cognitive Services die wordt gebruikt om factureringsgegevens bij te houden.<br/>De waarde van deze optie moet worden ingesteld op een API-sleutel voor de ingerichte resource die is opgegeven in `Billing`. |
| `Billing` | Het eindpunt van de resource Cognitive Services die wordt gebruikt om factureringsgegevens bij te houden.<br/>De waarde van deze optie moet worden ingesteld op het eindpunt URI van een ingerichte Azure-bron.|
| `Eula` | Geeft aan dat u de licentie voor de container hebt geaccepteerd.<br/>De waarde van deze optie moet worden ingesteld om te **accepteren**. |
