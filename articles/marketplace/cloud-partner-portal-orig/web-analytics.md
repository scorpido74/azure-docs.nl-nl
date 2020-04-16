---
title: Webanalytics
description: In dit artikel vindt u instructies over hoe u Web Analytics leren en gebruiken om uw bedrijf het beste te laten groeien.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416287"
---
<a name="web-analytics"></a>Webanalytics
=============

In dit artikel vindt u instructies over hoe u Web Analytics leren en gebruiken om uw bedrijf het beste te laten groeien. Momenteel is dit tabblad Insights beschikbaar voor alle AppSource-aanbiedingen.

Nu u uw aanbieding hebt gebouwd en gepubliceerd, is het volgende\' deel van uw reis het bijhouden en meten van het succes ervan. Met **Web Analytics**hebben we de mogelijkheid toegevoegd om precies te zien hoe goed elk van uw aanbiedingen het doet op de markt. Als u uw reis wilt starten, navigeert u naar de pagina Insights aan de linkerkant van Cloud Partner Portal om het nieuwe tabblad Analytics te bekijken.

![WebAnalytics-pagina](./media/si-getting-started/WebAnalytics1.png)

U ziet een uitgebreid dashboard voor uw Publisher ID dat is gebouwd met Microsoft\' Power BI en stelt u in staat om elk van uw aanbiedingengegevens te bekijken, die dagelijks worden vernieuwd.

<a name="microsoft-campaigns"></a>**Microsoft-campagnes**
-----------------------

Om uw aanbiedingen te laten groeien en de groei van uw aanbiedingen bij te houden, hebben we de mogelijkheid ingeschakeld om **Microsoft-campagnes** te gebruiken op de Cloud Partner Portal. Campagnes zijn een nieuw ondersteunde functie voor de marktplaats waarmee u de verschillende kanalen volgen die klanten naar uw pagina met app-gegevens sturen.

### <a name="how-to-make-a-campaign"></a>**Hoe maak je een campagne**

De eenvoudigste manier om campagnes te beschrijven is dat u een aangepast woord/term toevoegt aan uw URL die op uw app-detailpagina in de marketplace wordt weergegeven. Google, Bing, LinkedIn en vele andere sites moedigen u aan om een advertentie te bouwen, link van hun site naar uw gewenste site.

In het algemeen, deze inspanningen zijn om te helpen rijden nieuwe klanten in uw product en het is essentieel om het succes van hoe elk van uw kanalen doet te meten. Dit is waar campagnes komen in.

Er zijn twee manieren om je eigen campagne te genereren.

1. Voeg aan uw URL de queryparameter **mktcmpid toe** die beschrijft wat de campagne is en van welke pagina/gebeurtenis deze klanten afkomstig zijn.

U bijvoorbeeld gebruik maken van:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Geavanceerd): Gebruik een van onze ondersteunde, generieke campagne-id's in de URL. We willen meegaand zijn met extra ref-tags die u moet gebruiken, dus we ondersteunen de conventie om deze extra tags automatisch te herkennen:
    
    1. **utm-campagne\_**
    2. **utm-bron\_**
    3. **Ref**
    4. **src**

U bijvoorbeeld gebruik maken van:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

U ervoor kiezen om een combinatie van meerdere van deze campagne-id's te hebben om meer bronnen te identificeren die verkeer genereren voor de campagne, zoals waar de klant vandaan komt (e-mail, blog, sociale mediabron, enz.).

Bijvoorbeeld:

1. Nieuwsbrief verwijzer:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. LinkedIn-verwijzer:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Ervoor zorgen dat campagnes door al uw pagina's gaan**

Er kan een scenario zijn waarin uw campagnes een tussenliggende pagina hebben waar naar u verkeer wordt geleid en die vervolgens overgaat tot het verzenden van de klanten naar de marktplaats. Het is belangrijk om uw eerste campagne-id's door te geven in de uiteindelijke URL die u naar de marktplaats verzendt.

Hier volgt een voorbeeld:

1. Marketing medewerker koopt advertenties van Google\'om verkeer ```https://contoso.com```te genereren naar de bestemmingspagina van het bedrijf. Deze bestemmingspagina \"heeft\" een proberen ```https://appsource.com```mijn product link die gaat naar .
2. Een gebruiker klikt op de\'advertentie en landt op de bestemmingspagina van zijn bedrijf.
    1.  Url van verwijzing = google.com
    2.  URL van bestemmingspagina =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. De gebruiker \"klikt op\" de link Probeer mijn product en gaat naar AppSource.
    1. Url van verwijzing =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. URL van bestemmingspagina **(Zorg ervoor\_dat deze\_URL utm-campagne en utm-bron aan deze URL heeft toegevoegd**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Hoe het succes van een campagne te evalueren
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Paginabezoeken per campagne**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Dit is de uitsplitsing van elk van uw dagelijkse paginabezoeken door de campagne waar ze vandaan komen.

### <a name="conversion-rate-by-campaign"></a>**Conversieratio per campagne**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Net als bij de manier waarop we de conversieratio van uw volledige aanbieding weergeven, u in deze grafiek de uitsplitsing zien van hoe uw verschillende campagnes het doen. Met deze grafiek u bepalen waar uw campagnes met een hogere conversieratio vandaan komen.

### <a name="distribution-by-campaign"></a>**Distributie per campagne**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Net als bij hoe we kijken naar de domeinen van uw klanten, u met deze grafiek de verdeling van uw gegevens per campagne zien waarop de gebruikers naar de markt komen. \_NoCampaign betekent dat de klant geen campagne-id in de url had toen hij naar de marktplaats navigeerde.

<a name="next-steps"></a>**Volgende stappen**
--------------

Nu je de mogelijkheid hebt om het succes van je aanbiedingen bij te houden, willen we je aanmoedigen om je eigen campagnes te maken.

Als u vragen/functieverzoeken hebt, deelt u deze via feedback in de rechterbovenhoek.

![Feedback in Cloud Partner Portal](./media/si-getting-started/WebAnalytics5.png)
