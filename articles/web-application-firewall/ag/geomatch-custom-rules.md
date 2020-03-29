---
title: Aangepaste regels voor Azure Web Application Firewall (WAF) Geomatch
description: Dit artikel is een overzicht van de aangepaste regels van Web Application Firewall (WAF) op Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 01/31/2020
ms.author: victorh
ms.openlocfilehash: 6725e1de21dbd103850071f7511e2800c6bd7b69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961086"
---
# <a name="geomatch-custom-rules-preview"></a>Aangepaste regels voor Geomatch (voorbeeld)

Met aangepaste regels u aangepaste regels maken die voldoen aan de exacte behoeften van uw toepassingen en beveiligingsbeleid. U nu de toegang tot uw webtoepassingen per land/regio beperken. Zoals met alle aangepaste regels, kan deze logica worden samengesteld met andere regels om de behoeften van uw toepassing aan te passen.

Als u een aangepaste regel voor geofiltering wilt maken, selecteert u *Geo-locatie* als het type Match en selecteert u vervolgens het land of de landen die u wilt toestaan/blokkeren van uw toepassing. Zie [hoe u aangepaste regels maakt in Powershell](configure-waf-custom-rules.md) en meer aangepaste regelvoorbeelden (create-custom-waf-rules.md) voor meer informatie.

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="country-codes"></a>Landcodes

Als u de Geomatch-operator gebruikt, kunnen de selectors een van de volgende landcodes van twee cijfers zijn. 

|Landcode | Landnaam |
| ----- | ----- |
| AD | Andorra |
| AE | Verenigde Arabische Emiraten|
| AF | Afghanistan|
| AG | Antigua en Barbuda|
| AL | Albanië|
| AM | Armenië|
| AO | Angola|
| AR | Argentinië|
| AS | Amerikaans-Samoa|
| AT | Oostenrijk|
| AU | Australië|
| AZ | Azerbeidzjan|
| BA | Bosnië en Herzegovina|
| BB | Barbados|
| BD | Bangladesh|
| BE | België|
| BF | Burkina Faso|
| BG | Bulgarije|
| BH | Bahrein|
| BI | Burundi|
| BJ | Benin|
| BL | Sint-Barthélemie|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brazilië|
| BS | Bahama's|
| BT | Bhutan|
| BW | Botswana|
| BY | Belarus|
| BZ | Belize|
| CA | Canada|
| CD | Democratische Republiek Congo|
| CF | Centraal-Afrikaanse Republiek|
| CH | Zwitserland|
| CI | Cote d'Ivoire|
| CL | Chili|
| CM | Kameroen|
| CN | China|
| CO | Colombia|
| CR | Costa Rica|
| CU | Cuba|
| CV | Cabo Verde|
| CY | Cyprus|
| CZ | Tsjechië|
| DE | Duitsland|
| DK | Denemarken|
| DO | Dominicaanse Republiek|
| DZ | Algerije|
| EC | Ecuador|
| EE | Estland|
| EG | Egypte|
| ES | Spanje|
| ET | Ethiopië|
| FI | Finland|
| FJ | Fiji|
| FM | Micronesië, Federale Staten van|
| FR | Frankrijk|
| GB | Verenigd Koninkrijk|
| GE | Georgië|
| GF | Frans-Guyana|
| GH | Ghana|
| GN | Guinee|
| GP | Guadeloupe|
| GR | Griekenland|
| GT | Guatemala|
| GY | Guyana|
| HK | Hongkong SAR|
| HN | Honduras|
| HR | Kroatië|
| HT | Haiti|
| HU | Hongarije|
| Id | Indonesië|
| IE | Ierland|
| IL | Israël|
| IN | India|
| IQ | Irak|
| IR | Iran, Islamitische Republiek|
| IS | IJsland|
| IT | Italië|
| JM | Jamaica|
| JO | Jordanië|
| JP | Japan|
| KE | Kenia|
| KG | Kirgizië|
| KH | Cambodja|
| KI | Kiribati|
| KN | Saint Kitts en Nevis|
| KP | Noord-Korea|
| KR | Zuid-Korea|
| KW | Koeweit|
| KY | Kaaimaneilanden|
| KZ | Kazachstan|
| LA | Laos|
| LB | Libanon|
| LI | Liechtenstein|
| LK | Sri Lanka|
| LR | Liberia|
| LS | Lesotho|
| LT | Litouwen|
| LU | Luxemburg|
| LV | Letland|
| LY | Libië |
| MA | Marokko|
| MD | Moldavië, Republiek|
| MG | Madagascar|
| MK | Noord-Macedonië|
| ML | Mali|
| MM | Myanmar|
| MN | Mongolië|
| MO | Macau SAR|
| MQ | Martinique|
| MR | Mauretanië|
| MT | Malta|
| MV | Maldiven|
| MW | Malawi|
| MX | Mexico|
| MY | Maleisië|
| MZ | Mozambique|
| N.v.t. | Namibië|
| NE | Niger|
| NG | Nigeria|
| NI | Nicaragua|
| NL | Nederland|
| NO | Noorwegen|
| NP | Nepal|
| NR | Nauru|
| NZ | Nieuw-Zeeland|
| OM | Oman|
| PA | Panama|
| PE | Peru|
| PH | Filipijnen|
| PK | Pakistan|
| PL | Polen|
| PR | Puerto Rico|
| PT | Portugal|
| PW | Palau|
| PY | Paraguay|
| QA | Qatar|
| RE | Réunion|
| RO | Roemenië|
| RS | Servië|
| RU | Russische Federatie|
| RW | Rwanda|
| SA | Saoedi-Arabië|
| SD | Soedan|
| SE | Zweden|
| SG | Singapore|
| SI | Slovenië|
| SK | Slowakije|
| SN | Senegal|
| SO | Somalië|
| SR | Suriname|
| SS | Zuid-Soedan|
| SV | El Salvador|
| SY | Arabische Republiek Syrië|
| SZ | Swaziland|
| TC | Turks- en Caicoseilanden|
| TG | Togo|
| TH | Thailand|
| TN | Tunesië|
| TR | Turkije|
| TT | Trinidad en Tobago|
| TW | Taiwan|
| TZ | Tanzania, Verenigde Republiek|
| UA | Oekraïne|
| UG | Oeganda|
| VS | Verenigde Staten|
| UY | Uruguay|
| UZ | Oezbekistan|
| VC | Saint Vincent en de Grenadines|
| VE | Venezuela|
| VG | Britse Maagdeneilanden|
| VI | Amerikaanse Maagdeneilanden|
| VN | Vietnam|
| ZA | Zuid-Afrika|
| ZM | Zambia|
| ZW | Zimbabwe|

## <a name="next-steps"></a>Volgende stappen

Nadat u meer te weten bent over aangepaste regels, [maakt u uw eigen aangepaste regels.](create-custom-waf-rules.md)
