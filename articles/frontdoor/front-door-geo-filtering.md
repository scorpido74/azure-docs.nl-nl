---
title: Geofiltering op een domein voor Azure Front Door | Microsoft Documenten
description: In dit artikel vindt u meer informatie over het beleid voor geofiltering voor Azure Front Door
services: frontdoor
documentationcenter: ''
author: KumudD
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: 2cdde705d0e9f0905d4c33648b5415758d838b06
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411168"
---
# <a name="what-is-geo-filtering-on-a-domain-for-azure-front-door"></a>Wat is geo-filteren op een domein voor Azure Front Door?

Azure Front Door reageert standaard op gebruikersverzoeken, ongeacht de locatie van de gebruiker die de aanvraag indient. In sommige gevallen u de toegang tot uw webtoepassingen echter per land/regio beperken. Met de WAF-service (Web application firewall) bij Front Door u een beleid definiëren met behulp van aangepaste toegangsregels voor een specifiek pad op uw eindpunt om toegang uit bepaalde landen/regio's toe te staan of te blokkeren. 

Een WAF-beleid bevat meestal een reeks aangepaste regels. Een regel bestaat uit voorwaarden voor overeenkomsten, een actie en een prioriteit. In de voorwaarde voor overeenkomst definieert u een overeenkomende variabele, een operator en een overeenkomende waarde.  Voor de ge-filterregel is REMOTE_ADDR de overeenkomende variabele, GeoMatch de operator, en de waarde is het landnummer van twee letters. U kunt een GeoMatch-voorwaarde combineren met een REQUEST_URI-tekenreeks-voorwaarde voor overeenkomst om een op een pad gebaseerde geo-filterregel te maken.

U een geofilteringsbeleid voor uw voordeur configureren door [Azure PowerShell](front-door-tutorial-geo-filtering.md) te gebruiken of door onze [quickstartsjabloon te gebruiken.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-front-door-geo-filtering)

## <a name="country-code-reference"></a>Referentie van landcode

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

- Informatie over [bescherming van de toepassingslaag met Front Door](front-door-application-security.md).
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
