---
title: Azure Notification Hubs-sjablonen
description: Meer informatie over het gebruik van sjablonen voor Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7d88f57fe92b9da62cc9f90d64bdec4c27642fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263741"
---
# <a name="templates"></a>Sjablonen

Met sjablonen kan een clienttoepassing de exacte indeling opgeven van de meldingen die deze wil ontvangen. Met behulp van sjablonen kan een app verschillende voordelen realiseren, waaronder:

- Een platform-agnostische backend
- Gepersonaliseerde meldingen
- Client-versie onafhankelijkheid
- Eenvoudige lokalisatie

In deze sectie worden twee diepgaande voorbeelden gegeven van het gebruik van sjablonen om platformagnostische meldingen te verzenden die zich richten op al uw apparaten op verschillende platforms en om de melding van uitzendingen naar elk apparaat te personaliseren.

## <a name="using-templates-cross-platform"></a>Sjablonen cross-platform gebruiken

De standaardmanier om pushmeldingen te verzenden is door voor elke te verzenden melding een specifieke payload te sturen naar platformmeldingsservices (WNS, APNS). Als u bijvoorbeeld een waarschuwing naar APNS wilt verzenden, is de payload een JSON-object van het volgende formulier:

```json
{"aps": {"alert" : "Hello!" }}
```

Als u een vergelijkbaar pop-upbericht wilt verzenden op een Windows Store-toepassing, ziet de XML-payload als volgt uit:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

U vergelijkbare payloads maken voor MPNS-platforms (Windows Phone) en FCM (Android).

Deze vereiste dwingt de app backend om verschillende payloads te produceren voor elk platform, en maakt effectief de backend verantwoordelijk voor een deel van de presentatie laag van de app. Enkele zorgen zijn lokalisatie en grafische lay-outs (vooral voor Windows Store-apps die meldingen bevatten voor verschillende soorten tegels).

Met de sjabloonfunctie Meldinghubs kan een client-app speciale registraties maken, sjabloonregistraties genoemd, waaronder naast de set tags een sjabloon. Met de sjabloonfunctie Meldinghubs kan een client-app apparaten koppelen aan sjablonen, ongeacht of u werkt met Installaties (voorkeurs) of Registraties. Gezien de voorgaande payload voorbeelden, de enige platform-onafhankelijke informatie is de werkelijke waarschuwingsbericht (Hallo!). Een sjabloon is een set instructies voor de meldingshub voor het opmaken van een platformonafhankelijk bericht voor de registratie van die specifieke client-app. In het voorgaande voorbeeld is het platformonafhankelijke `message = Hello!`bericht één eigenschap: .

De volgende afbeelding illustreert het proces:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

De sjabloon voor de registratie van de iOS-clientapp is als volgt:

```json
{"aps": {"alert": "$(message)"}}
```

De bijbehorende sjabloon voor de Windows Store-client-app is:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Merk op dat het werkelijke bericht wordt vervangen door de expressie $(bericht). Deze expressie instrueert de Meldingshub, wanneer deze een bericht naar deze specifieke registratie verzendt, om een bericht te maken dat volgt en de gemeenschappelijke waarde inschakelt.

Als u met installatiemodel werkt, bevat de installatiesleutel "sjablonen" een JSON van meerdere sjablonen. Als u met registratiemodel werkt, kan de clienttoepassing meerdere registraties maken om meerdere sjablonen te gebruiken. bijvoorbeeld een sjabloon voor waarschuwingsberichten en een sjabloon voor tegelupdates. Clienttoepassingen kunnen ook native registraties (registraties zonder sjabloon) en sjabloonregistraties mengen.

De meldingshub stuurt één melding voor elke sjabloon zonder na te gaan of deze tot dezelfde client-app behoren. Dit gedrag kan worden gebruikt om platformonafhankelijke meldingen te vertalen naar meer meldingen. Hetzelfde platformonafhankelijke bericht naar de Meldingshub kan bijvoorbeeld naadloos worden vertaald in een pop-upwaarschuwing en een tegelupdate, zonder dat de backend hiervan op de hoogte moet zijn. Sommige platforms (bijvoorbeeld iOS) kunnen meerdere meldingen naar hetzelfde apparaat samenvouwen als ze in een korte periode worden verzonden.

## <a name="using-templates-for-personalization"></a>Sjablonen gebruiken voor personalisatie

Een ander voordeel van het gebruik van sjablonen is de mogelijkheid om Notification Hubs te gebruiken om personalisatie per registratie van meldingen uit te voeren. Denk bijvoorbeeld aan een weer-app die een tegel weergeeft met de weersomstandigheden op een specifieke locatie. Een gebruiker kan kiezen tussen Celsius of Fahrenheit graden, en een enkele of vijf-daagse voorspelling. Met behulp van sjablonen kan elke installatie van de client-app zich registreren voor het vereiste formaat (1-daagse Celsius, 1-daagse Fahrenheit, 5-daagse Celsius, 5-dagen Fahrenheit) en de backend één bericht laten verzenden dat alle informatie bevat die nodig is om deze sjablonen in te vullen (bijvoorbeeld een vijfdaagse voorspelling met Celsius en Fahrenheit graden).

De sjabloon voor de eendaagse voorspelling met Celsius-temperaturen is als volgt:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Het bericht dat naar de meldingshub wordt verzonden, bevat alle volgende eigenschappen:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Door dit patroon te gebruiken, stuurt de backend slechts één bericht zonder specifieke personalisatieopties op te slaan voor de app-gebruikers. De volgende afbeelding illustreert dit scenario:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Sjablonen registreren

Zie [Registratiebeheer](notification-hubs-push-notification-registration-management.md)als u zich wilt registreren met sjablonen met behulp van het installatiemodel (voorkeursmodel) of het registratiemodel.

## <a name="template-expression-language"></a>Taal voor sjabloonexpressie

Sjablonen zijn beperkt tot XML- of JSON-documentindelingen. U ook alleen expressies op bepaalde plaatsen plaatsen; bijvoorbeeld nodekenmerken of waarden voor XML, eigenschapswaarden voor JSON.

In de volgende tabel ziet u de taal die is toegestaan in sjablonen:

| Expressie       | Beschrijving |
| ---------------- | --- |
| $(prop)          | Verwijzing naar een gebeurteniseigenschap met de voornaam. Eigendomsnamen zijn niet hoofdlettergevoelig. Deze expressie wordt omgezet in de tekstwaarde van de eigenschap of in een lege tekenreeks als de eigenschap niet aanwezig is. |
| $(prop, n)       | Zoals hierboven, maar de tekst wordt expliciet geknipt op n tekens, bijvoorbeeld $(titel, 20) clips de inhoud van de eigenschap titel op 20 tekens. |
| . (rekwisieten, n)       | Zoals hierboven, maar de tekst is achtervoegsel met drie stippen als het wordt geknipt. De totale grootte van de geknipte tekenreeks en het achtervoegsel overschrijdt de n-tekens niet. . (titel, 20) met een input eigenschap van "Dit is de titel regel" resultaten in **Dit is de titel ...** |
| %(steun)          | Vergelijkbaar met $(naam), behalve dat de uitvoer is URI-gecodeerd. |
| #(steun)          | Wordt gebruikt in JSON-sjablonen (bijvoorbeeld voor iOS- en Android-sjablonen).<br><br>Deze functie werkt precies hetzelfde als $(prop) die eerder is opgegeven, behalve wanneer deze wordt gebruikt in JSON-sjablonen (bijvoorbeeld Apple-sjablonen). In dit geval, als deze functie niet is omgeven door "{','}" (bijvoorbeeld 'myJsonProperty' : '#(name)'), en het evalueert tot een nummer in Javascript-formaat,\. bijvoorbeeld regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*)(&#91;0-9&#93;+)? (e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, dan is de uitvoer JSON een getal.<br><br>Bijvoorbeeld, 'badge: '#(naam)" wordt 'badge' : 40 (en niet '40'). |
| 'tekst' of 'tekst' | Een letterlijke. Literals bevatten willekeurige tekst ingesloten in enkele of dubbele aanhalingstekens. |
| expr1 + expr2    | De samenvoegingsoperator voegt twee expressies samen in één tekenreeks. |

De expressies kunnen een van de voorgaande formulieren zijn.

Bij het gebruik van samenvoeging moet `{}`de volledige expressie worden omgeven met . Bijvoorbeeld `{$(prop) + ‘ - ’ + $(prop2)}`.

De volgende sjabloon is bijvoorbeeld geen geldige XML-sjabloon:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Zoals eerder uitgelegd, bij het gebruik van concatenatie, uitdrukkingen moeten worden verpakt in krullende beugels. Bijvoorbeeld:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Azure Notification Hubs](notification-hubs-push-notification-overview.md)