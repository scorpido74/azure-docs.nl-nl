---
title: Prijzen voor aanbiedingen voor virtuele machines | Azure Marketplace
description: Legt de drie methoden voor het specificeren van de prijsstelling van virtuele machine aanbiedingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 487e66b39bc63363497cb3497d32158efd0c6c8a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255886"
---
<a name="pricing-for-virtual-machine-offers"></a>Prijzen voor virtuele machine-aanbiedingen
==================================

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de wijzigingen in [Cloud Partner Portal API Reference](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

Er zijn drie manieren om prijzen voor aanbiedingen van virtuele machines op te geven: aangepaste basisprijzen, prijzen per core en spreadsheetprijzen.


<a name="customized-core-pricing"></a>Aangepaste kernprijzen
-----------------------

Prijzen zijn specifiek voor elke regio en kerncombinatie. Elke regio in de verkooplijst moet worden opgegeven in het gedeelte **virtualMachinePricingPrices**/**regionPrices** van de definitie.  Gebruik de juiste valutacodes voor elke [regio](#regions) in uw aanvraag.  In het volgende voorbeeld worden de volgende vereisten aangetoond:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```


<a name="per-core-pricing"></a>Prijzen per core
----------------

In dit geval geven de uitgevers één prijs in USD op voor hun SKU en worden alle andere prijzen automatisch gegenereerd. De prijs per kern wordt opgegeven in de **enkele** parameter in de aanvraag.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```


<a name="spreadsheet-pricing"></a>Spreadsheetprijzen
-------------------

De uitgever kan ook hun prijsspreadsheet uploaden naar een tijdelijke opslaglocatie en vervolgens de URI opnemen in het verzoek zoals andere bestandsartefacten. De spreadsheet wordt vervolgens geüpload, vertaald om het opgegeven prijsschema te evalueren en werkt de aanbieding ten slotte bij met de prijsinformatie. Volgende GET-aanvragen voor de aanbieding retourneren de spreadsheet URI en de geëvalueerde prijzen voor de regio.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

<a name="new-core-sizes-added-on-722019"></a>Nieuwe kernmaten toegevoegd op 7/2/2019
---------------------------

VM-uitgevers werden op 2 juli 2019 op de hoogte gebracht van de toevoeging van nieuwe prijzen voor nieuwe Azure-virtuele machineformaten (op basis van het aantal cores).  De nieuwe prijzen zijn voor de kernmaten 10, 44, 48, 60, 120, 208 en 416.  Voor bestaande VM-aanbiedingen werden nieuwe prijzen voor deze kernen-maten automatisch berekend op basis van de huidige prijzen.  Uitgevers hebben tot 1 augustus 2019 de tijd om de extra prijzen te bekijken en eventuele gewenste wijzigingen door te voeren.  Na deze datum, indien deze nog niet opnieuw is gepubliceerd door de uitgever, worden de automatisch berekende prijzen voor deze nieuwe kerngroottes van kracht.


<a name="regions"></a>Regio's
-------

In de volgende tabel worden de verschillende regio's weergegeven die u opgeven voor aangepaste basisprijzen en de bijbehorende valutacodes.

| **Regio** | **Naam**             | **Valutacode** |
|------------|----------------------|-------------------|
| DZ         | Algerije              | DZD (DZD)               |
| AR         | Argentinië            | ARS               |
| AU         | Australië            | AUD               |
| AT         | Oostenrijk              | EUR               |
| BH         | Bahrein              | Bhd               |
| BY         | Belarus              | RUB               |
| BE         | België              | EUR               |
| BR         | Brazilië               | USD               |
| BG         | Bulgarije             | Bgn               |
| CA         | Canada               | CAD               |
| CL         | Chili                | Clp               |
| CO         | Colombia             | Cop               |
| CR         | Costa Rica           | Crc               |
| HR         | Kroatië              | Hrk               |
| CY         | Cyprus               | EUR               |
| CZ         | Tsjechië       | Czk               |
| DK         | Denemarken              | DKK               |
| DO         | Dominicaanse Republiek   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egypte                | Egp               |
| SV         | El Salvador          | USD               |
| EE         | Estland              | EUR               |
| FI         | Finland              | EUR               |
| FR         | Frankrijk               | EUR               |
| DE         | Duitsland              | EUR               |
| GR         | Griekenland               | EUR               |
| GT         | Guatemala            | GTQ (GTQ)               |
| HK         | Hongkong SAR        | HKD               |
| HU         | Hongarije              | Huf               |
| IS         | IJsland              | Isk               |
| IN         | India                | INR               |
| Id         | Indonesië            | IDR               |
| IE         | Ierland              | EUR               |
| IL         | Israël               | Ils               |
| IT         | Italië                | EUR               |
| JP         | Japan                | JPY               |
| JO         | Jordanië               | Jod               |
| KZ         | Kazachstan           | KZT (KZT)               |
| KE         | Kenia                | Kes               |
| KR         | Korea                | KRW               |
| KW         | Koeweit               | Kwd               |
| LV         | Letland               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Litouwen            | EUR               |
| LU         | Luxemburg           | EUR               |
| MK         | Noord-Macedonië      | MKD (MKD)               |
| MY         | Maleisië             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Mexico               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marokko              | Mad               |
| NL         | Nederland          | EUR               |
| NZ         | Nieuw-Zeeland          | NZD               |
| NG         | Nigeria              | Ngn               |
| NO         | Noorwegen               | NOK               |
| OM         | Oman                 | Omr               |
| PK         | Pakistan             | Pkr               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | Pyg               |
| PE         | Peru                 | Pen               |
| PH         | Filipijnen          | PHP               |
| PL         | Polen               | Pln               |
| PT         | Portugal             | EUR               |
| PR         | Puerto Rico          | USD               |
| QA         | Qatar                | Qar               |
| RO         | Roemenië              | Ron               |
| RU         | Rusland               | RUB               |
| SA         | Saoedi-Arabië         | SAR               |
| RS         | Servië               | Rsd               |
| SG         | Singapore            | SGA               |
| SK         | Slowakije             | EUR               |
| SI         | Slovenië             | EUR               |
| ZA         | Zuid-Afrika         | ZAR               |
| ES         | Spanje                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Zweden               | SEK               |
| CH         | Zwitserland          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailand             | Thb               |
| TT         | Trinidad en Tobago  | Ttd               |
| TN         | Tunesië              | Tnd               |
| TR         | Turkije               | TRY               |
| UA         | Oekraïne              | Uah               |
| AE         | Verenigde Arabische Emiraten | EUR               |
| GB         | Verenigd Koninkrijk       | GBP               |
| VS         | Verenigde Staten        | USD               |
| UY         | Uruguay              | UYU UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
