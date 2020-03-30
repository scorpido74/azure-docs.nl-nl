---
title: Internet Analyzer-client insluiten | Microsoft Documenten
description: In dit artikel leest u hoe u de JavaScript-client van Internet Analyzer in uw toepassing insluit.
services: internet-analyzer
author: mattcalder
ms.service: internet-analyzer
ms.topic: quickstart
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: f9ecb8d731945847160b49c68c554fafdd7285d9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74896387"
---
# <a name="embed-the-internet-analyzer-client"></a>De Internet Analyzer-client insluiten

In dit artikel ziet u hoe u de JavaScript-client in uw toepassing insluit. Installatie van deze client is noodzakelijk om tests uit te voeren en scorecard analytics te ontvangen. **De profielspecifieke JavaScript-client wordt geleverd nadat de eerste test is geconfigureerd.** Van daaruit u tests aan dat profiel blijven toevoegen of verwijderen zonder dat u een nieuw script hoeft in te sluiten. Zie het [overzicht](internet-analyzer-overview.md)voor meer informatie over Internet Analyzer. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

Internet Analyzer vereist toegang tot Azure en andere Microsoft-services om correct te functioneren. Geef netwerktoegang `fpc.msedge.net` tot en vooraf geconfigureerde eindpunt-URL's (zichtbaar via [CLI)](internet-analyzer-cli.md)voordat u de client insluit.

## <a name="find-the-client-script-url"></a>De URL van het clientscript zoeken

De url van het script kan worden gevonden via de Azure-portal of de Azure CLI nadat een test is geconfigureerd. Zie [Een internetanalyzerbron maken](internet-analyzer-create-test-portal.md)voor meer informatie.

Optie 1. Gebruik [deze koppeling](https://aka.ms/InternetAnalyzerPreviewPortal) in de Azure-portal om de voorbeeldportalpagina voor Azure Internet Analyzer te openen. Navigeer naar uw Internet Analyzer-profiel om de URL van het script te bekijken door naar **Instellingen > Configuratie**te gaan.

Optie 2. Controleer de `scriptFileUri` eigenschap met de Azure CLI.
```azurecli-interactive
    az extension add --name internet-analyzer    
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
```

## <a name="client-details"></a>Klantgegevens

Het script wordt speciaal gegenereerd voor uw profiel en tests. Na het laden wordt het script uitgevoerd op een vertraging van 2 seconden. Eerst neemt het contact op met de Internet Analyzer-service om de lijst met eindpunten op te halen die in uw tests zijn geconfigureerd. Vervolgens worden de metingen uitgevoerd en worden de getimede resultaten teruggeüpload naar de Internet Analyzer-service.

## <a name="client-examples"></a>Voorbeelden van klanten

In deze voorbeelden worden een aantal basismethoden weergegeven om javascript voor de client in te sluiten in uw webpagina of toepassing. We `0bfcb32638b44927935b9df86dcfe397` gebruiken als voorbeeld profiel-ID voor de script-URL.

### <a name="run-on-page-load"></a>Uitvoeren op paginabelasting
De eenvoudigste methode is het gebruik van de scripttag in het metatagblok. Deze tag voert het script één keer per paginabelasting uit.

```html
<html>
<meta>
    <script src="//fpc.msedge.net/client/v2/0bfcb32638b44927935b9df86dcfe397/ab.min.js"></script>
</meta>
<body></body>
</html>
```

## <a name="next-steps"></a>Volgende stappen

Lees de [veelgestelde vragen over internetanalyse](internet-analyzer-faq.md)
