---
title: Geografisch filteren op een domein voor de Azure front-deur service
description: In dit artikel maakt u kennis met het geo-filterbeleid voor Azure Front Door Service (AFD)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: eb91dfd5d774e591d3c3c3964668dcc626086181
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512572"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>Wat is geografisch filteren op een domein voor Azure front-deur?

Standaard reageert AFD op gebruikersaanvragen, ongeacht de locatie van deze gebruiker. In sommige gevallen wilt u echter mogelijk de toegang tot uw webtoepassingen beperken op basis van land/regio. Met de WAF-service (Web Application firewall) aan de voor deur kunt u een beleid definiëren met aangepaste toegangs regels voor een specifiek pad op uw eind punt om toegang toe te staan of te blok keren voor bepaalde landen/regio's. 

Een WAF-beleid bevat meestal een set aangepaste regels. Een regel bestaat uit voorwaarden voor overeenkomsten, een actie en een prioriteit. In de voorwaarde voor overeenkomst definieert u een overeenkomende variabele, een operator en een overeenkomende waarde.  Voor de ge-filterregel is REMOTE_ADDR de overeenkomende variabele, GeoMatch de operator, en de waarde is het landnummer van twee letters. U kunt een GeoMatch-voorwaarde combineren met een REQUEST_URI-tekenreeks-voorwaarde voor overeenkomst om een op een pad gebaseerde geo-filterregel te maken.

U kunt een beleid voor geofiltering configureren voor uw voor deur door gebruik te maken van [Azure PowerShell](waf-front-door-tutorial-geo-filtering.md) of door gebruik te maken van de Quick Start- [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering).

## <a name="country-code-reference"></a>Land code referentie

|Landnummer | Land naam |
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
| BL | Saint--Barthélemy|
| BN | Brunei Darussalam|
| BO | Bolivia|
| BR | Brazilië|
| BS | Bahama's|
| BT | Bhutan|
| BW | Botswana|
| BY | Belarus|
| BZ | Belize|
| CA | Canada|
| CD | Congo (DRC)|
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
| HK | Hong Kong SAR|
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

- Informatie over [bescherming van de toepassingslaag met Front Door](../../frontdoor/front-door-application-security.md).
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).
