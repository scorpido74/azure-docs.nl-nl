---
title: Aangepaste rapporten van Verizon | Microsoft Documenten
description: 'U gebruikspatronen voor uw CDN bekijken aan de hand van de volgende rapporten: Bandbreedte, Overgedragen gegevens, treffers, cachestatussen, cachehitratio, IPV4/IPV6-gegevens overgedragen.'
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: magattus
ms.openlocfilehash: 15f17ac6556c4ff731372dc7f738d0f58bdc3e31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593296"
---
# <a name="custom-reports-from-verizon"></a>Aangepaste rapporten van Verizon

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Door Verizon Custom Reports te gebruiken via de Portal voor Verizon-profielen beheren, u het type gegevens definiëren dat moet worden verzameld voor edge CNAMEs-rapporten.


## <a name="accessing-verizon-custom-reports"></a>Toegang tot aangepaste rapporten van Verizon
1. Klik in het CDN-profielblad op de knop **Beheren.**
   
    ![Knop CDN-profiel beheren](./media/cdn-reports/cdn-manage-btn.png)
   
    Het CDN-beheerportaal wordt geopend.
2. Plaats de plaats op het tabblad **Analytics** en plaats de plaats vervolgens boven de flyout **Aangepaste rapporten.** Klik **op Edge CNAMEs**.
   
    ![CDN-beheerportal - Menu Aangepaste rapporten](./media/cdn-reports/cdn-custom-reports.png)

## <a name="edge-cnames-custom-report"></a>Aangepast rapport Edge CNAMES
Het aangepaste rapport Edge CNAMES biedt statistieken over hits en gegevensoverdracht voor edge-CNAMEs waarop aangepaste rapportlogboekregistratie is ingeschakeld. Edge CNAMEs bestaan uit Azure CDN-eindpunthostnamen en eventuele bijbehorende aangepaste domeinnamen. 

Het vastleggen van aangepaste rapportgegevens begint een uur nadat u de aangepaste rapportagemogelijkheden van een edge CNAME hebt ingeschakeld. U rapportgegevens bekijken door een Edge CNAMEs-rapport te genereren voor een specifiek platform of voor alle platforms. De dekking voor dit rapport is beperkt tot de edge CNAMEs waarvoor aangepaste rapportgegevens werden verzameld tijdens de opgegeven periode. Het edge CNAMEs-rapport bestaat uit een grafiek en gegevenstabel voor de bovenste 10 edge-CNAMEs volgens de statistiek die is gedefinieerd in de optie Metrische gegevens. 

Een aangepast rapport genereren door de volgende rapportopties te definiëren:

- Statistieken: de volgende opties worden ondersteund:

   - Hits: geeft het totale aantal aanvragen aan dat is gericht op een edge CNAME waarop de aangepaste rapportagemogelijkheid is ingeschakeld. Deze statistiek bevat niet de statuscode die naar de client is geretourneerd.

   - Overgedragen gegevens: geeft de totale hoeveelheid gegevens aan die wordt overgedragen van de randservers naar de HTTP-clients (bijvoorbeeld webbrowsers) voor aanvragen die zijn gericht op een edge CNAME waarop de aangepaste rapportagemogelijkheid is ingeschakeld. De hoeveelheid overgedragen gegevens wordt berekend door HTTP-antwoordkoppen toe te voegen aan de antwoordtekst. Als gevolg hiervan is de hoeveelheid gegevens die voor elk actief wordt overgedragen groter dan de werkelijke bestandsgrootte.

- Groeperingen: hiermee bepaalt u het type statistieken dat onder het staafdiagram wordt weergegeven. De volgende opties worden ondersteund:

   - HTTP-antwoordcodes: organiseert statistieken op HTTP-antwoordcode (bijvoorbeeld 200, 403, enz.) die naar de client wordt teruggestuurd. 

   - Cachestatus: hiermee worden statistieken op basis van cachestatus georganiseerd.


Als u het datumbereik voor het rapport wilt instellen, u een vooraf gedefinieerd datumbereik selecteren, zoals **Vandaag** of **Deze week,** in de vervolgkeuzelijst of u **Aangepast** selecteren en handmatig een datumbereik invoeren door op de agendapictogrammen te klikken. 

Nadat u het datumbereik hebt geselecteerd, klikt u op **Gaan** om het rapport te genereren.

U de gegevens exporteren in Excel-indeling door rechts van de knop **Naar** door op het Excel-symbool te klikken.

![CNAMEs-rapport](./media/cdn-reports/cdn-cnames-report.png)

## <a name="edge-cnames-custom-report-fields"></a>Aangepaste rapportvelden edge CNAMES

| Veld                     | Beschrijving   |
|---------------------------|---------------|
| 2xx                       | Geeft het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de edge CNAME die resulteert in een 2xx HTTP-statuscode (bijvoorbeeld 200 OK). |
| 3xx 3xx                       | Geeft het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de rand-CNAME die resulteert in een 3xx HTTP-statuscode (bijvoorbeeld 302 Gevonden of 304 niet gewijzigd. |
| 4xx                       | Geeft het totale aantal overgezette aanvragen of gegevens (MB) aan voor de edge CNAME die resulteert in een 4xx HTTP-statuscode (bijvoorbeeld 400 Bad Request, 403 Verboden of 404 niet gevonden). |
| 5xx                       | Geeft het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de edge CNAME die resulteert in een 5xx HTTP-statuscode (bijvoorbeeld 500 interne serverfout of 502 Bad Gateway). |
| Cache Hit %               | Hiermee geeft u het percentage cachebare aanvragen aan dat rechtstreeks vanuit de cache naar de aanvrager is aangeboden. |
| Cache Hits                | Geeft het totale aantal aanvragen of gegevens aan dat wordt overgedragen (MB) voor de edge CNAME die resulteert in een cachehit (bijvoorbeeld TCP_EXPIRED_HIT, TCP_HIT of TCP_PARTIAL_HIT). Er treedt een cachehit op wanneer een in de cache opgeslagen versie van de gevraagde inhoud wordt gevonden. |
| Overgedragen gegevens (MB)     | Geeft de totale hoeveelheid gegevens aan die wordt overgedragen (MB) van de randservers naar HTTP-clients (webbrowsers) voor de edge CNAME. De hoeveelheid overgedragen gegevens wordt berekend door de HTTP-antwoordkoppen toe te voegen aan de antwoordtekst. Als gevolg hiervan is de hoeveelheid gegevens die voor elk actief wordt overgedragen groter dan de werkelijke bestandsgrootte. |
| Beschrijving               | Identificeert een edge CNAME aan de hostnaam |
| Treffers                      | Geeft het totale aantal aanvragen aan de edge CNAME aan |
| Mist                    | Geeft het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de edge CNAME die resulteert in een cachemiss (bijvoorbeeld TCP_CLIENT_REFRESH_MISS, TCP_EXPIRED_MISS of TCP_MISS). Er treedt een cachefout op wanneer de gevraagde inhoud niet in de cache is opgeslagen op de edge-server die het verzoek heeft gehonoreerd. | 
| Geen cache                  | Geeft het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de edge CNAME die resulteert in een statuscode van een CONFIG_NOCACHE cache.  |
| Overige                     | Hiermee geeft u het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de edge CNAME die is aangegeven dat dit resulteert in een HTTP-statuscode die buiten het bereik 2xx - 5xx valt. |
| Platform                  | Geeft het platform aan dat het verkeer van de randCNAME verwerkt. |
| Niet toegewezen               | Geeft het totale aantal aanvragen of overgedragen gegevens (MB) aan voor de edge CNAME waarvoor de statuscode van de cache of de HTTP-statuscodeniet is geregistreerd.  |
| Niet in cache kunnen worden opgeslagen               | Geeft het totale aantal aanvragen of gegevens aan dat is overgedragen (MB) voor de rand-CNAME die resulteert in een statuscode voor uncacheable cache.  |


## <a name="considerations"></a>Overwegingen
Rapporten kunnen alleen worden gegenereerd in de afgelopen 18 maanden.

