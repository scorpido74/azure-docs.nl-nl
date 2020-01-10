---
title: Geocoderings dekking in Azure Maps | Microsoft Docs
description: Meer informatie over de geocoderings dekking in Microsoft Azure Maps en in welke regio's/landen u Azure Maps geocodeer kunt gebruiken.
author: walsehgal
ms.author: v-musehg
ms.date: 12/31/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 4def947c13588cde57b55aeaa9d958eafa923093
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768806"
---
# <a name="azure-maps-geocoding-coverage"></a>Geocoderings dekking Azure Maps

Wanneer u zoekt naar een locatie met Azure Maps, de zoek service, bijvoorbeeld [Zoek adres ophalen](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress), worden uw zoek termen opgehaald en worden de coördinaten voor de breedte graad en lengte graad geretourneerd. Dit proces wordt geocodering genoemd. Azure Maps heeft echter niet hetzelfde niveau van informatie en nauw keurigheid voor alle regio's en landen. Gebruik dit artikel om te bepalen met welke typen locaties u in elke regio betrouwbaar kunt zoeken. 

De mogelijkheid tot Geocode in een land/regio is afhankelijk van de dekking van de weg en de geocoderings nauwkeurigheid van de geocoderings service. De volgende categorisaties worden gebruikt om het niveau van geocoderings ondersteuning in elk land/elke regio in te stellen.
* **Adres punten** -adres gegevens kunnen worden omgezet naar een breedte graad/lengte graad binnen het perceel van het adres (eigenschaps grens). Ook wel nauw keurig aangeduid als ' Rooftop '. Dit is het hoogste nauwkeurigheids niveau dat beschikbaar is voor adressen. 
* **Huis nummers** : adressen worden geïnterpoleerd naar een coördinaat van een breedte graad/lengte graad in het adres.
* **Straat niveau** : adressen worden omgezet in de coördinaat van de breedte graad/lengte graad van de straat die het adres bevat. Het huis nummer mag niet worden verwerkt.
* Namen van **plaatsnamen en plaatsnamen** worden ondersteund.

## <a name="americas"></a>Noord- en Zuid-Amerika

| Land/regio                                       | Adres punten | Huis nummers | Straat niveau | Niveau van de stad | Interessante punten |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Anguilla                                            |                 |                |              |      ✓     |          ✓         |
| Antarctica                                          |                 |                |              |      ✓     |          ✓         |
| Antigua en Barbuda                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Argentinië                                           |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Aruba                                               |                 |                |              |      ✓     |          ✓         |
| Bahama's                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Barbados                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Belize                                              |                 |                |              |      ✓     |          ✓         |
| Bermuda                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bolivia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Bonaire, Sint Eustatius en Saba                   |                 |                |              |      ✓     |          ✓         |
| Brazilië                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Canada                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kaaimaneilanden                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Chili                                               |       ✓         |        ✓       |       ✓      |      ✓     |          ✓         |
| Colombia                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Costa Rica                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Cuba                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Dominica                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Dominicana                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Ecuador                                             |                 |                |       ✓      |      ✓     |          ✓         |
| El Salvador                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Falklandeilanden                                    |                 |                |              |      ✓     |          ✓         |
| Frans-Guyana                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Grenada                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Guadeloupe                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Guam                                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Guatemala                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Guyana                                              |                |             |           |      ✓     |                 |
| Haiti                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Honduras                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Jamaica                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Martinique                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Mexico                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Montserrat                                          |                 |                |              |      ✓     |          ✓         |
| Nicaragua                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Panama                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Paraguay                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Peru                                                |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Puerto Rico                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Saint--Barthélemy                                    |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Kitts en Nevis                               |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Lucia                                         |                 |                |              |      ✓     |          ✓         |
| Saint-Martin                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Saint-Pierre en Miquelon                           |                 |                |       ✓      |      ✓     |          ✓         |
| Saint Vincent en de Grenadines                    |                 |                |              |      ✓     |          ✓         |
| Sint Maarten                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Zuid-Georgia en de Zuidelijke Sandwicheilanden        |                 |                |              |      ✓     |          ✓         |
| Suriname                                            |                 |                |              |      ✓     |          ✓         |
| Trinidad en Tobago                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Amerikaanse ondergeschikte afgelegen eilanden                |                 |                |              |      ✓     |          ✓         |
| Verenigde Staten van Amerika                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Uruguay                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Venezuela                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Britse Maagdeneilanden                              |                 |                |              |      ✓     |          ✓         |
| Amerikaanse Maagden eilanden                                 |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |

## <a name="asia-pacific"></a>Azië-Pacific

| Land/regio                                      | Adres punten |Huis nummers | Straat niveau | Niveau van de stad | Interessante punten |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Amerikaans-Samoa                                      |                 |                |       ✓      |      ✓     |          ✓         |
| Australië                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bangladesh                                          |                 |                |              |      ✓     |          ✓         |
| Bhutan                                              |                 |                |              |      ✓     |          ✓         |
| Brits Indische Oceaanterritorium                      |                 |                |              |      ✓     |          ✓         |
| Brunei                                              |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cambodja                                            |                 |                |              |      ✓     |          ✓         |
| China                                               |                 |                |              |      ✓     |          ✓         |
| Christmaseiland                                    |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Cocoseilanden                             |                 |                |              |      ✓     |          ✓         |
| Comoren                                             |                 |                |              |      ✓     |          ✓         |
| Cookeilanden                                        |                 |                |              |      ✓     |          ✓         |
| Fiji                                                |                  |                |              |      ✓     |          ✓        |
| Frans-Polynesië                                    |                 |                |              |      ✓     |          ✓         |
| Heardeiland en McDonaldeilanden                   |                 |                |              |      ✓     |          ✓         |
| Hongkong SAR                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Indonesië                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| India                                               |        ✓        |        ✓       |       ✓      |      ✓     |                   |
| Japan                                               |                 |                |              |      ✓     |          ✓         |
| Kiribati                                            |                 |                |              |      ✓     |          ✓         |
| Korea                                         |                 |                |              |      ✓     |          ✓         |
| Laos                                                |                 |                |              |      ✓     |          ✓         |
| Macau SAR                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Maleisië                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Micronesia                                          |                 |                |              |      ✓     |          ✓         |
| Mongolië                                            |                 |                |              |      ✓     |          ✓         |
| Nauru                                               |                 |                |              |      ✓     |          ✓         |
| Nepal                                               |                 |                |              |      ✓     |          ✓         |
| Nieuw-Caledonië                                       |                 |                |              |      ✓     |          ✓         |
| Nieuw-Zeeland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Niue                                                |                 |                |              |      ✓     |          ✓         |
| Norfolk                                      |                 |                |              |      ✓     |          ✓         |
| Noord-Korea                                         |                 |                |              |      ✓     |          ✓         |
| Noordelijke Marianen                            |                 |                |       ✓      |      ✓     |          ✓         |
| Pakistan                                            |                 |                |              |      ✓     |          ✓         |
| Palau                                               |                 |                |              |      ✓     |          ✓         |
| Papoea-Nieuw-Guinea                                    |                 |                |              |      ✓     |          ✓         |
| Filippijnen                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Pitcairn                                            |                 |                |              |      ✓     |          ✓         |
| Samoa                                               |                 |                |              |      ✓     |          ✓         |
| Senkakuman eilanden                                     |        ✓        |                |              |      ✓     |          ✓         |
| Singapore                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Salomonseilanden                                     |                 |                |              |      ✓     |          ✓         |
| Zuidelijk Kurils                                     |        ✓        |                |              |      ✓     |          ✓         |
| Sri Lanka                                           |                 |                |              |      ✓     |          ✓         |
| Taiwan                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Thailand                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Tokelau                                             |                 |                |              |      ✓     |          ✓         |
| Tonga                                               |                 |                |              |      ✓     |          ✓         |
| Turks- en Caicoseilanden                            |                 |                |              |      ✓     |          ✓         |
| Tuvalu                                              |                 |                |              |      ✓     |          ✓         |
| Vanuatu                                             |                 |                |              |      ✓     |          ✓         |
| Vietnam                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wallis en Futuna                                   |                 |                |              |      ✓     |          ✓         |

## <a name="europe"></a>Europa

| Land/regio                                      | Adres punten |Huis nummers | Straat niveau | Niveau van de stad | Interessante punten |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Albanië                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Andorra                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Armenië                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Oostenrijk                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Azerbeidzjan                                          |        ✓        |        ✓       |              |      ✓     |          ✓         |
| België                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bosnië-Herzegovina                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Bulgarije                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Wit-Rusland                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kroatië                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Cyprus                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Tsjechië                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Denemarken                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Estland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Faeröer                                       |                 |                |              |      ✓     |          ✓         |
| Finland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Frankrijk                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Georgië                                             |        ✓        |        ✓       |              |      ✓     |          ✓         |
| Duitsland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Gibraltar                                           |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Griekenland                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Groenland                                           |                 |                |              |      ✓     |          ✓         |
| Guernsey                                            |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Hongarije                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| IJsland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Ierland                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Isle of man                                         |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Italië                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jan Mayen                                           |        ✓        |                |              |      ✓     |          ✓         |
| Jersey                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Kazachstan                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Kosovo                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Kirgizië                                          |                 |                |              |      ✓     |          ✓         |
| Letland                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Liechtenstein                                       |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Litouwen                                           |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Luxemburg                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noord-Macedonië                                     |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Malta                                               |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Moldavië                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Monaco                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Montenegro                                          |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Nederland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Noorwegen                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Polen                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Portugal                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| \+ Azoren en Madeira                                 |                 |                |       ✓      |      ✓     |          ✓         |
| Roemenië                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Russische Federatie                                  |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| San Marino                                          |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Servië                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slowakije                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Slovenië                                            |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spanje                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Spitsbergen                                            |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Zweden                                              |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Zwitserland                                         |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Tadzjikistan                                          |                 |                |              |      ✓     |          ✓         |
| Turkije                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Turkmenistan                                        |                 |                |              |      ✓     |          ✓         |
| Oekraïne                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Verenigd Koninkrijk                                      |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Oezbekistan                                          |                 |                |              |      ✓     |          ✓         |
| Vaticaanstad                                        |                 |                |       ✓      |      ✓     |          ✓         |


## <a name="middle-east-and-africa"></a>Midden-Oosten en Afrika

| Land/regio                                      | Adres punten |Huis nummers | Straat niveau | Niveau van de stad | Interessante punten |
|-----------------------------------------------------|:---------------:|:--------------:|:------------:|:----------:|:------------------:|
| Afghanistan                                         |                 |                |              |      ✓     |          ✓         |
| Algerije                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Angola                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Bahrein                                             |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Benin                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Botswana                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Bouveteiland                                       |                 |                |              |      ✓     |          ✓         |
| Burkina Faso                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Burundi                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Kameroen                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Cabo Verde                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Centraal-Afrikaanse Republiek                            |                 |                |       ✓      |      ✓     |          ✓         |
| Tsjaad                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Congo-Brazzaville                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Ivoorkust (Côte d’Ivoire)                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Congo (DRC)                    |                 |                |       ✓      |      ✓     |          ✓         |
| Djibouti                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Egypte                                               |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Equatoriaal-Guinea, Republiek                      |                 |                |       ✓      |      ✓     |          ✓         |
| Eritrea                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Ethiopië                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Franse Gebieden in de zuidelijke Indische Oceaan|                        |                |              |      ✓     |          ✓         |
| Gabon                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Gambia                                              |                 |                |              |      ✓     |          ✓         |
| Ghana                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Guinee                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Guinee-Bissau                                       |                 |                |       ✓      |      ✓     |          ✓         |
| Iran                                                |                 |                |              |      ✓     |          ✓         |
| Irak                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Israël                                              |        ✓        |       ✓        |              |      ✓     |          ✓         |
| Jordanië                                              |        ✓        |       ✓        |       ✓      |      ✓     |          ✓         |
| Kenia                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Koeweit                                              |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Libanon                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Lesotho                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Liberia                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Libië                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Madagascar                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Malawi                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Maldiven                                            |                 |                |              |      ✓     |          ✓         |
| Mali                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Marshalleilanden                                    |                 |                |              |      ✓     |          ✓         |
| Mauretanië                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Mauritius                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Mayotte                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Marokko                                             |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Mozambique                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Myanmar                                             |                 |                |              |      ✓     |          ✓         |
| Namibië                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Niger                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Nigeria                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Oman                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Qatar                                               |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Réunion                                             |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Rwanda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Sint-Helena                                        |                 |                |              |      ✓     |          ✓         |
| Saoedi-Arabië                                        |                 |        ✓       |       ✓      |      ✓     |          ✓         |
| Senegal                                             |                 |                |       ✓      |      ✓     |          ✓         |
| Seychellen                                          |                 |                |       ✓      |      ✓     |          ✓         |
| Sierra Leone                                        |                 |                |       ✓      |      ✓     |          ✓         |
| Somalië                                             |                 |                |              |      ✓     |          ✓         |
| Zuid-Afrika                                        |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Zuid-Sudan                                         |                 |                |       ✓      |      ✓     |          ✓         |
| Soedan                                               |                 |                |       ✓      |      ✓     |          ✓         |
| Swaziland                                           |                 |                |       ✓      |      ✓     |          ✓         |
| Syrië                                               |                 |                |              |      ✓     |          ✓         |
| São Tomé en Principe                               |                 |                |       ✓      |      ✓     |          ✓         |
| Tanzania                                            |                 |                |       ✓      |      ✓     |          ✓         |
| Togo                                                |                 |                |       ✓      |      ✓     |          ✓         |
| Tunesië                                             |        ✓        |                |       ✓      |      ✓     |          ✓         |
| Uganda                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Verenigde Arabische Emiraten                                |        ✓        |        ✓       |       ✓      |      ✓     |          ✓         |
| Jemen                                               |                 |                |              |      ✓     |          ✓         |
| Zambia                                              |                 |                |       ✓      |      ✓     |          ✓         |
| Zimbabwe                                            |                 |                |       ✓      |      ✓     |          ✓         |



## <a name="next-steps"></a>Volgende stappen

Zie de pagina met [Zoek](https://docs.microsoft.com/rest/api/maps/search) informatie voor meer informatie over Azure Maps geocodering.

Meer informatie over de [dekkings gebieden voor de service Maps-verkeer](traffic-coverage.md). 

