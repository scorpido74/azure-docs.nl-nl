---
title: Webanalyse
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d47a67be157f71a30a6ae155790ed78a78fd6743
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814643"
---
<a name="web-analytics"></a>Webanalyse
=============

In dit artikel vindt u instructies voor het leren en gebruiken van Web Analytics om uw bedrijf het beste te laten groeien. Momenteel is dit inzichten tabblad beschikbaar voor AppSource-aanbiedingen.

Nu u uw aanbieding hebt gebouwd en gepubliceerd, is het volgende deel van uw traject het volgen en meten van de\' slagen. Met **Web Analytics**hebben we de mogelijkheid om precies te zien hoe goed elk van uw aanbiedingen op de Marketplace doet. Als u de reis wilt starten, gaat u naar de pagina inzichten aan de linkerkant van Cloud Partner-portal om het tabblad nieuwe analyse te zien.

![Webanalytics-pagina](./media/si-getting-started/WebAnalytics1.png)

U ziet een uitgebreid dash board voor uw uitgevers-ID die is gebouwd met micro soft Power BI en stelt u in staat om elk van uw aanbiedingen te bekijken\' gegevens, die dagelijks worden vernieuwd.

<a name="microsoft-campaigns"></a>**Micro soft-campagnes**
-----------------------

We hebben de mogelijkheid geboden om **micro soft-campagnes** te gebruiken op het Cloud Partner-Portal om uw aanbiedingen te kunnen uitbreiden en de groei van uw aanbiedingen te volgen. Campagnes zijn een nieuw ondersteund onderdeel voor Marketplace waarmee u de verschillende kanalen kunt volgen die klanten verzenden naar uw app-gegevens pagina.

### <a name="how-to-make-a-campaign"></a>**Een campagne maken**

De eenvoudigste manier om campagnes te beschrijven is dat u een aangepaste woord/term toevoegt aan uw URL die wordt gelandd op de detail pagina van uw app in de Marketplace. Google, Bing, LinkedIn en veel andere sites raden u aan om een aankondiging te maken, vanaf hun site te koppelen naar de gewenste site.

Over het algemeen is het belang rijk om nieuwe klanten te helpen bij uw product en het is essentieel om het succes van uw kanalen te meten. Dit is de plaats waar campagnes binnenkomen.

Er zijn twee manieren om uw eigen campagne te genereren.

1. Voeg aan uw URL toe de query parameter **mktcmpid** die beschrijft wat de campagne is en op welke pagina/gebeurtenis deze klanten afkomstig zijn.

U kunt bijvoorbeeld het volgende gebruiken: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Geavanceerd): gebruik een van onze ondersteunde, algemene campagne-Id's in de URL. We willen graag gebruikmaken van aanvullende ref-Tags die u moet gebruiken. Daarom ondersteunen we de Conventie automatisch om deze extra tags te herkennen:
    
    1. **UTM-\_campagne**
    2. **UTM\_-bron**
    3. **verwijzing**
    4. **src**

U kunt bijvoorbeeld het volgende gebruiken: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

U kunt ervoor kiezen om een combi natie van meerdere van deze campagne-Id's te hebben om meerdere bronnen te identificeren die verkeer voor de campagne aansturen, zoals de locatie van de klant (e-mail adres, blog, sociale media bron, enz.).

Bijvoorbeeld:

1. Verwijzing naar nieuws brief: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn-verwijzings bronnen: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Zorgen dat campagnes alle pagina's door lopen**

Er is mogelijk een scenario waarin uw campagnes een tussenliggende pagina hebben waarnaar u verkeer stuurt en vervolgens verdergaat om de klanten naar de Marketplace te sturen. Het is belang rijk om uw eerste campagne-Id's door te geven aan de uiteindelijke URL die u naar de Marketplace verzendt.

Hier volgt een voorbeeld:

1. Marketing werk nemer koopt advertenties van Google om verkeer naar de landings pagina van het bedrijf\'s te sturen <https://contoso.com>. Deze landings pagina heeft een \"\" koppeling van mijn product proberen die naar <https://appsource.com>gaat.
2. Een gebruiker klikt op de pagina advertentie en land op zijn of haar bedrijf\'s land.
    1.  Referentie-URL = google.com
    2.  URL van landings pagina = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. De gebruiker klikt op de \"mijn product\" koppeling proberen en gaat naar AppSource.
    1. Referentie-URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. URL van de landings pagina (**Controleer of deze URL is utm\_campagne en utm\_bron is toegevoegd aan deze URL**) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**UTM\_campagne = MyCampaignAdName & UTM\_source = MySourceAdName** ](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Het succes van een campagne evalueren
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Pagina bezoeken per campagne**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Dit is de uitsplitsing van elk van uw dagelijkse pagina bezoeken door de campagne waarvan ze afkomstig zijn.

### <a name="conversion-rate-by-campaign"></a>**Conversie prijs per campagne**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Net als bij de manier waarop we de conversie percentage van uw hele aanbieding tonen, kunt u in dit diagram zien hoe de verschillende campagnes worden uitgevoerd. Deze grafiek helpt u te bepalen waar de hogere conversie tarieven van campagnes afkomstig zijn.

### <a name="distribution-by-campaign"></a>**Distributie per campagne**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Net als bij het bekijken van de domeinen van uw klanten, kunt u met deze grafiek de distributie van uw gegevens per campagne bekijken waarvan de gebruikers naar de Marketplace gaan onder. \_geen enkele campagne betekent dat de klant geen campagne-ID in de URL heeft wanneer deze naar de Marketplace navigeert.

<a name="next-steps"></a>**Volgende stappen**
--------------

Nu u de mogelijkheid hebt om het succes van uw aanbiedingen te volgen, willen we u aanmoedigen om uw eigen campagnes te maken.

Als u vragen/functie aanvragen hebt, kunt u deze delen via feedback. Deze bevindt zich in de rechter bovenhoek.

![Feedback in Cloud Partner-portal](./media/si-getting-started/WebAnalytics5.png)
