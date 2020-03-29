---
title: Alle voorgebouwde entiteiten - LUIS
titleSuffix: Azure Cognitive Services
description: Dit artikel bevat lijsten met de vooraf gebouwde entiteiten die zijn opgenomen in Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219718"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entiteiten per cultuur in uw LUIS-model

Language Understanding (LUIS) biedt vooraf gebouwde entiteiten. Wanneer een vooraf gebouwde entiteit in uw toepassing is opgenomen, bevat LUIS de bijbehorende entiteitsvoorspelling in het eindpuntantwoord. Alle voorbeelduitingen worden ook gelabeld met de entiteit. Het gedrag van vooraf gebouwde entiteiten **kan niet** worden gewijzigd. Tenzij anders vermeld, zijn vooraf gebouwde entiteiten beschikbaar in alle LUIS-toepassingslandinstellingen (culturen). In de volgende tabel worden de vooraf gebouwde entiteiten weergegeven die voor elke cultuur worden ondersteund.

|Cultuur|Subculturen|Opmerkingen|
|--|--|--|
|Chinees|[zh-CN](#chinese-entity-support)||
|Nederlands|[nl-NL](#dutch-entity-support)||
|Engels|[nl-VS (Amerikaans)](#english-american-entity-support)||
|Frans|[fr-CA (Canada)](#french-canadian-entity-support), [fr-FR (Frankrijk)](#french-france-entity-support), ||
|Duits|[de-DE](#german-entity-support)||
|Italiaans|[it-IT](#italian-entity-support)||
|Japans|[ja-JP](#japanese-entity-support)||
|Koreaans|[ko-KR](#korean-entity-support)||
|Portugees|[pt-BR (Brazilië)](#portuguese-brazil-entity-support)||
|Spaans|[es-ES (Spanje)](#spanish-spain-entity-support), [es-MX (Mexico)](#spanish-mexico-entity-support)||
|Turks|[Turks](#turkish-entity-support)|Geen voorgebouwde entiteiten ondersteund in het Turks|

## <a name="prediction-endpoint-runtime"></a>Eindpuntruntime voorspellen

De beschikbaarheid van een vooraf gebouwde entiteit in een bepaalde taal wordt bepaald door de versie van het voorspellingseindpunt runtime.

## <a name="chinese-entity-support"></a>Ondersteuning voor Chinese entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```zh-CN``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Ondersteuning nederlandse entiteit

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```nl-NL``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Ondersteuning voor Engelse (Amerikaanse) entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```en-US``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Franse (Frankrijk) entiteit steun

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```fr-FR``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |   -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Ondersteuning voor Franse (Canadese) entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```fr-CA``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Ondersteuning van Duitse entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```de-DE``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Ondersteuning van Italiaanse entiteiten

Italiaanse vooraf gebouwde leeftijd, valuta, dimensie, aantal, percentage _resolutie_ veranderd van V2 en V3 preview.

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```it-IT``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Ondersteuning voor Japanse entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```ja-JP``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, -   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Ondersteuning voor Koreaanse entiteiten

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```ko-KR``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    -   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Ondersteuning voor Portugese entiteit (Brazilië)

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```pt-BR``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Spaanse (Spanje) entiteit steun

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```es-ES``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    V2, V3   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    V2, V3   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    V2, V3   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Ondersteuning voor Spaanse (Mexico) entiteit

De volgende entiteiten worden ondersteund:

|Vooraf gebouwde entiteit|```es-MX``` |
------|:------:|
[Leeftijd](luis-reference-prebuilt-age.md):<br>jaar<br>maand<br>Week<br>day   |    -   |
[Valuta (geld)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractionele eenheid (bijvoorbeeld penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>tijd<br>tijdbereik   |    -   |
[Dimensie](luis-reference-prebuilt-dimension.md):<br>volume<br>gebied<br>Gewicht<br>informatie (bijvoorbeeld bit/byte)<br>lengte (ex: meter)<br>snelheid (bijvoorbeeld mijl per uur)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Keyphrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Nummer](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinale](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[Persoonsnaam](luis-reference-prebuilt-person.md)   |    -   |
[Telefoonnummer](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatuur](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>Delisle<br>Celsius   |    -   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

Zie notities over [afgeschafte vooraf gebouwde entiteiten](luis-reference-prebuilt-deprecated.md)

KeyPhrase is niet beschikbaar in alle subculturen ```pt-BR```van het Portugees (Brazilië) - .

## <a name="turkish-entity-support"></a>Ondersteuning van Turkse entiteiten

**Er zijn geen voorgebouwde entiteiten die in het Turks worden ondersteund.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Bijdragen aan vooraf gebouwde entiteitsculturen
De voorgebouwde entiteiten zijn ontwikkeld in het Open-source project Recognizers-Text. [Draag bij](https://github.com/Microsoft/Recognizers-Text) aan het project. Dit project bevat voorbeelden van valuta per cultuur.

GeographyV2 en PersonName zijn niet opgenomen in het project Recognizers-Text. Voor problemen met deze vooraf gebouwde entiteiten opent u een [ondersteuningsverzoek.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [getal,](luis-reference-prebuilt-number.md) [datetimeV2](luis-reference-prebuilt-datetimev2.md)en [valutaentiteiten.](luis-reference-prebuilt-currency.md)
