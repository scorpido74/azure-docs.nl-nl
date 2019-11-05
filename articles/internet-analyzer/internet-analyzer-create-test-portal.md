---
title: Een Internet Analyzer-test maken met behulp van de portal | Microsoft Docs
description: In dit artikel leert u hoe u uw eerste Internet Analyzer-test kunt maken.
services: internet-analyzer
author: megan-beatty
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 64bbed9b558d4c20889b28a5247e2113d20daa77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73501769"
---
# <a name="create-an-internet-analyzer-test-using-portal-preview"></a>Een Internet Analyzer-test maken met behulp van de portal (preview)

Er zijn twee manieren om een Internet Analyzer-resource te maken: met behulp van de Azure Portal of [cli](internet-analyzer-cli.md). Deze sectie helpt u bij het maken van een nieuwe Azure Internet Analyzer-resource met onze portal-ervaring.

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Voordat u begint

De open bare preview is beschikbaar voor gebruik wereld wijd. gegevens opslag is echter beperkt tot *US West 2* tijdens de preview-versie.

## <a name="basics"></a>Basisbeginselen

1. Toegang tot internet Analyzer preview verkrijgen door de **Hoe kan ik deel te nemen aan de preview-versie** van de [Veelgestelde vragen over Azure Internet Analyzer](internet-analyzer-faq.md).
2. Klik op de start pagina in de [Azure Portal](https://preview.portal.azure.com)op **+ een resource maken**. Internet Analyzer is momenteel alleen beschikbaar vanuit de preview-versie van de Azure Portal.
3. Zoek op de pagina **Nieuw** naar ' Internet Analyzer ' in het veld *Marketplace doorzoeken* .
4. Klik op **Internet Analyzer (preview-versie)** . Zorg ervoor dat de uitgever van *micro soft* is en dat de categorie *netwerk*is.
5. Klik op de pagina **Internet Analyzer (preview)** op **maken** om de pagina **een Internet Analyzer maken** te openen.
6. Geef de volgende configuratie-instellingen op voor uw Internet Analyzer-resource:

    * **Abonnement:** Het Azure-abonnement voor het hosten van de nieuwe Internet Analyzer-resource. ***Gebruik dezelfde abonnements-ID die wordt gebruikt om Preview-toegang aan te vragen.***
    * **Resource groep:** De Azure-resource groep waarin de nieuwe Internet Analyzer-resource wordt gemaakt. Als u geen bestaande resource groep hebt, kunt u een nieuwe maken.
    * **Naam:** De naam van het nieuwe Internet Analyzer-resource profiel.
    * **Regio:** De open bare Azure-regio waarin de resource wordt gemaakt. Tijdens de preview-periode is alleen *VS West 2* beschikbaar.

7. Wanneer u klaar bent met het opgeven van de profiel instellingen, klikt u op **controleren + maken**.

## <a name="configuration"></a>Configuratie

Het volt ooien van de basis stappen is een vereiste voor het configureren van een test en het insluiten van de Java script-client. Nadat u een profiel hebt gemaakt, gaat u naar **instellingen > configuratie** om uw eerste test te configureren.

1. Geef uw test een naam in het vak **test naam** .
2. Voeg in het veld **Beschrijving** een beschrijving voor uw test toe.
3. Klik op **eind punt configureren** : er wordt een tabblad weer gegeven aan de rechter kant. Selecteer het type eind punt dat u wilt configureren: één Azure-regio, meerdere Azure-regio's of een aangepast eind punt.

    >
    ***Vooraf geconfigureerde eind punten: een combi natie van één en meerdere Azure-regio's***
    * Selecteer een regio of set regio's uit een [vooraf geconfigureerde lijst met Azure-eind punten](internet-analyzer-faq.md).
    * Selecteer vervolgens het type toepassing of de architectuur voor het leveren van inhoud die u wilt evalueren.
        * Eén Azure-regio: site versnelling ([Azure front deur](https://azure.microsoft.com/services/frontdoor/)), statische inhoud in de cache opslaan ([Azure CDN voor micro soft](https://azure.microsoft.com/services/cdn/)) of geen.
        * Meerdere Azure-regio's: site versnelling ([Azure front-deur](https://azure.microsoft.com/services/frontdoor/)), DNS-besturing ([Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/))  

    ***Aangepaste eind punten***
    * Volg de instructies op de pagina [aangepast eind punt maken](internet-analyzer-custom-endpoint.md) .
    * Plak de locatie van de HTTPS-URL van de afbeelding met één pixel in de portal.
    >

4. Klik op **toevoegen** om het eind punt toe te voegen aan uw test.
5. Herhaal de stappen 1-4 om uw tweede eind punt te configureren. Eind punt B wordt altijd relatief gemeten ten opzichte van eind punt A: Houd bij het configureren van eind punten rekening met het eind punt dat u wilt testen.
6. Klik op de knop **Opslaan** om uw test op te slaan. Wanneer u een test hebt opgeslagen, kunt u de eind punten van een bepaalde test niet meer bewerken.
7. Selecteer de test (s) die u wilt starten en klik op **Test starten**. Hiermee wordt de ***status*** van uw test (s) gewijzigd in ***wordt uitgevoerd***. U kunt testen op elk gewenst moment starten, maar de Java script-client moet zijn Inge sloten voor de test om te beginnen met het verzamelen van metingen.
8. Voeg op elk moment meer tests toe. Houd er rekening mee dat de unieke java script-client pas wordt gegenereerd als er een test is gemaakt.

## <a name="embed-client"></a>Client insluiten

Als u een test wilt starten, moet de Java script-client zijn Inge sloten in uw webtoepassing. Nadat u ten minste één test hebt geconfigureerd, klikt u op **controleren + maken**, gaat u naar **Instellingen > configuratie**en kopieert u de Java script-client. Specifieke instructies vindt u op de pagina [Internet Analyzer insluiten-client](internet-analyzer-embed-client.md) .  

## <a name="next-steps"></a>Volgende stappen

* Lees de [Veelgestelde vragen over Internet analyse](internet-analyzer-faq.md)
* Meer informatie over het insluiten van de [Internet Analyzer-client](internet-analyzer-embed-client.md) en het maken van een [aangepast eind punt](internet-analyzer-custom-endpoint.md).
