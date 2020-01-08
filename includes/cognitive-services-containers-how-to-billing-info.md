---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704265"
---
Query's naar de container worden gefactureerd op de prijs categorie van de Azure-resource die wordt gebruikt voor de `<ApiKey>`.

Azure Cognitive Services-containers mogen niet worden uitgevoerd zonder dat ze zijn verbonden met het facturerings eindpunt voor meting. U moet de containers in staat stellen om de facturerings gegevens te allen tijde te communiceren met het eind punt. Cognitive Services containers verzenden geen klant gegevens, zoals de afbeelding of de tekst die wordt geanalyseerd, naar micro soft. 

### <a name="connect-to-azure"></a>Verbinding maken met Azure

De voor de container vereiste facturerings argument waarden moeten worden uitgevoerd. Met deze waarden kan de container verbinding maken met het eind punt van de facturering. De container rapporteert het gebruik ongeveer elke 10 tot 15 minuten. Als de container geen verbinding maakt met Azure binnen het toegestane tijd venster, blijft de container actief, maar worden er geen query's verwerkt totdat het eind punt van de facturering is hersteld. De verbinding wordt 10 keer in hetzelfde tijds interval van 10 tot 15 minuten uitgevoerd. Als er binnen de 10 pogingen geen verbinding kan worden gemaakt met het facturerings eindpunt, wordt de container gestopt. 

### <a name="billing-arguments"></a>Facturerings argumenten

Voor de `docker run` opdracht om de container te starten, moeten alle drie de volgende opties worden opgegeven met geldige waarden:

| Optie | Beschrijving |
|--------|-------------|
| `ApiKey` | De API-sleutel van de Cognitive Services resource die wordt gebruikt voor het bijhouden van facturerings gegevens.<br/>De waarde van deze optie moet worden ingesteld op een API-sleutel voor de ingerichte resource die is opgegeven in `Billing`. |
| `Billing` | Het eind punt van de Cognitive Services resource die wordt gebruikt voor het bijhouden van facturerings gegevens.<br/>De waarde van deze optie moet worden ingesteld op de eindpunt-URI van een ingerichte Azure-resource.|
| `Eula` | Geeft aan dat u de licentie voor de container hebt geaccepteerd.<br/>De waarde van deze optie moet worden ingesteld op **accepteren**. |


