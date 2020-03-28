---
title: Een Internet Analyzer-test maken met Portal | Microsoft Documenten
description: In dit artikel leert u hoe u uw eerste Internet Analyzer-test maakt.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73501769"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Een Internet Analyzer-test maken met Portal (Preview)

Er zijn twee manieren om een Internet Analyzer-bron te maken: met behulp van de Azure-portal of met [CLI.](internet-analyzer-cli.md) Met deze sectie u een nieuwe Azure Internet Analyzer-bron maken met behulp van onze portalervaring.

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

De openbare preview is wereldwijd beschikbaar voor gebruik; gegevensopslag is echter beperkt tot *US West 2* tijdens de preview.

## <a name="basics"></a>Basisbeginselen

1. Krijg toegang tot internetanalyzer-preview's door de [Azure Internet Analyzer FAQ](internet-analyzer-faq.md) **voorbeeldinstructies te volgen?**
2. Klik op de startpagina in de [Azure-portal](https://preview.portal.azure.com)op **+ Een resource maken**. Internet Analyzer is momenteel alleen beschikbaar via de previewversie van de Azure-portal.
3. Zoek op de pagina **Nieuw** naar 'Internet Analyzer' in het veld *Zoeken in de marketplace.*
4. Klik op **Internet Analyzer (voorbeeld)**. Zorg ervoor dat de uitgever *Microsoft* is en de categorie *Netwerken*.
5. Klik op de pagina **Internet Analyzer (preview)** op **Maken** om de pagina **Een internetanalyzer maken** te openen.
6. Geef de volgende configuratie-instellingen op voor uw Internet Analyzer-bron:

    * **Abonnement:** Het Azure-abonnement om de nieuwe Internet Analyzer-bron te hosten. ***Gebruik dezelfde abonnements-id die wordt gebruikt om preview-toegang aan te vragen.***
    * **Resourcegroep:** De Azure-brongroep waarin de nieuwe Internet Analyzer-bron wordt gemaakt. Als u geen bestaande resourcegroep hebt, u een nieuwe groep maken.
    * **Naam:** De naam van het nieuwe internetanalyzer-bronprofiel.
    * **Regio:** De openbare azure-regio waarin de bron wordt gemaakt. Tijdens de preview is alleen *US West 2* beschikbaar.

7. Wanneer u klaar bent met het opgeven van uw profielinstellingen, klikt u op **Controleren + maken**.

## <a name="configuration"></a>Configuratie

Het voltooien van de basisstappen is een vereiste voor het configureren van een test en het insluiten van de JavaScript-client. Zodra u een profiel hebt gemaakt, gaat u naar **Instellingen > Configuratie** om uw eerste test te configureren.

1. Geef uw test een naam in het vak **Testnaam.**
2. Voeg een beschrijving toe voor uw test in het veld **Beschrijving.**
3. Klik **op Eindpunt configureren** - er verschijnt een tabblad aan de rechterkant. Selecteer het type eindpunt dat u wilt configureren: één Azure-regio, meerdere Azure-regio's of een aangepast eindpunt.

    >
    ***Vooraf geconfigureerde eindpunten: enkele en meerdere Azure-regiocombinaties***
    * Selecteer een regio of een reeks regio's in een [vooraf geconfigureerde lijst met Azure-eindpunten](internet-analyzer-faq.md).
    * Selecteer vervolgens het type toepassings- of contentdelivery-architectuur dat u wilt evalueren.
        * Eén Azure-regio: siteversnelling[(Azure Front Door),](https://azure.microsoft.com/services/frontdoor/)statische inhoudscache[(Azure CDN voor Microsoft)](https://azure.microsoft.com/services/cdn/)of geen.
        * Meerdere Azure-regio's: siteversnelling[(Azure Front Door),](https://azure.microsoft.com/services/frontdoor/)DNS-besturing ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Aangepaste eindpunten***
    * Volg de instructies op de pagina [Aangepast eindpunt maken.](internet-analyzer-custom-endpoint.md)
    * Plak de HTTPS URL-locatie van de afbeelding met één pixel in de portal.
    >

4. Klik **op Toevoegen** om het eindpunt aan uw test toe te voegen.
5. Herhaal stap 1-4 om uw tweede eindpunt te configureren. Eindpunt B wordt altijd gemeten ten opzichte van Eindpunt A - overweeg bij het configureren van eindpunten welk eindpunt uw testbesturingselement moet zijn.
6. Klik **op** de knop Opslaan om de test op te slaan. Zodra u een test hebt opgeslagen, u de eindpunten van een bepaalde test niet meer bewerken.
7. Selecteer de test(s) die u wilt starten en klik op **Starttest**. Hiermee wordt de ***status*** van uw test(en) gewijzigd in ***Uitvoeren.*** U op elk gewenst moment met tests beginnen, maar de JavaScript-client moet zijn ingesloten om te kunnen beginnen met het verzamelen van metingen.
8. Voeg meer tests toe op elk gewenst moment. Houd er rekening mee dat de unieke JavaScript-client pas wordt gegenereerd als er één test is gemaakt.

## <a name="embed-client"></a>Client insluiten

Om een test te starten, moet de JavaScript-client zijn ingesloten in uw webtoepassing. Nadat u ten minste één test hebt geconfigureerd, klikt u op **Controleren + maken,** gaat u naar **Instellingen > Configuratie**en kopieert u de JavaScript-client. Specifieke instructies zijn te vinden op de pagina [Embed Internet Analyzer Client.](internet-analyzer-embed-client.md)  

## <a name="next-steps"></a>Volgende stappen

* Lees de [veelgestelde vragen over internetanalyse](internet-analyzer-faq.md)
* Meer informatie over het insluiten van de [Internet Analyzer-client](internet-analyzer-embed-client.md) en het maken van een [aangepast eindpunt](internet-analyzer-custom-endpoint.md).
