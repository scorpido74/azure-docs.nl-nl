---
title: Land-/regiohiërarchie gebruikt door geografische routering - Azure Traffic Manager
description: In dit artikel wordt de hiërarchie Land/Regio weergegeven die wordt gebruikt door het geografische routeringstype Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 19445c06ccf08d2d7916545ad495c56883616c7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938633"
---
# <a name="countryregion-hierarchy-used-by-azure-traffic-manager-for-geographic-traffic-routing-method"></a>Land-/regiohiërarchie die wordt gebruikt door Azure Traffic Manager voor de routeringsmethode voor geografisch verkeer

In dit artikel worden de landen en regio's weergegeven die worden gebruikt door de **methode voor geografische** verkeersroutering in Azure Traffic Manager. U deze informatie ook programmatisch verkrijgen door de REST API van [Azure Traffic Manager](https://docs.microsoft.com/rest/api/trafficmanager/)te bellen. 

- WERELD(Wereld)

    - GEO-EU(Europa)

        - AD(Andorra)

        - AL(Albanië)

        - AT(Oostenrijk)

        - AX(Åland-eilanden)

        - BA(Bosnië en Herzegovina)

        - BE(België)

        - BG(Bulgarije)

        - BY(Wit-Rusland)

        - CH(Zwitserland)

        - CY(Cyprus)

        - CZ(Tsjechië)

        - DE(Duitsland)

        - DK(Denemarken)

        - EE(Estland)

        - ES(Spanje)

        - FI(Finland)

        - FO(Faeröer)

        - FR(Frankrijk)

        - GB(Verenigd Koninkrijk)

        - GG(Guernsey)

        - GI(Gibraltar)

        - GR(Griekenland)

        - HR(Kroatië)

        - HU(Hongarije)

        - IE(Ierland)

        - IM(Eiland Man)

        - IS(IJsland)

        - IT(Italië)

        - JE(Jersey)

        - LI(Liechtenstein)

        - LT(Litouwen)

        - LU(Luxemburg)

        - LV(Letland)

        - MC(Monaco)

        - MD(Moldavië)

        - ME(Montenegro)

        - MK(Noord-Macedonië)

        - MT(Malta)

        - NL(Nederland)

        - NO(Noorwegen)

        - PL(Polen)

        - PT(Portugal)

        - RO(Roemenië)

        - RS(Servië)

        - RU(Rusland)

        - SE(Zweden)

        - SI(Slovenië)

        - SJ(Svalbard)

        - SK(Slowakije)

        - SM(San Marino)

        - UA(Oekraïne)
            - Regio de Krim

        - VA (Vaticaanstad)

        - XJ(Jan Mayen)

        - XK(Kosovo)

    - GEO-ME(Midden-Oosten)

        - AE(Verenigde Arabische Emiraten)

        - BH(Bahrein)

        - IL(Israël)

        - IQ(Irak)

        - IR(Iran)

        - JO(Jordanië)

        - KW(Koeweit)

        - LB(Libanon)

        - OM(Oman)

        - PS(Palestijnse Autoriteit)

        - QA(Qatar)

        - SY(Syrië)

        - SA(Saoedi-Arabië)

        - TR(Turkije)

        - YE(Jemen)

    - GEO-NA (Noord-Amerika / Midden-Amerika / Caribisch gebied)

        - AG(Antigua en Barbuda)

        - AI(Anguilla)

        - AW(Aruba)

        - BB(Barbados)

        - BL(Saint Barthélemy)

        - BM(Bermuda)

        - BQ(Bonaire)

        - BS(Bahama's)

        - BZ(Belize)

        - CA(Canada)

            - CA-AB(Alberta)

            - CA-BC(British Columbia)

            - CA-MB(Manitoba)

            - CA-NB(New Brunswick)

            - CA-NL(Newfoundland en Labrador)

            - CA-NS(Nova Scotia)

            - CA-NT(Northwest Territories)

            - CA-NU(Nunavut)

            - CA-ON(Ontario)

            - CA-PE(Prins Edward Island)

            - CA-QC(Québec)

            - CA-SK(Saskatchewan)

            - CA-YT(Yukon Territory)

        - CR(Costa Rica)

        - CU(Cuba)

        - CW(CuraÃ§ao)

        - DM(Dominica)

        - DO(Dominicaanse Republiek)

        - GD(Grenada)

        - GL(Groenland)

        - GP(Guadeloupe)

        - GT(Guatemala)

        - HN(Honduras)

        - HT(Haïti)

        - JM(Jamaica)

        - KN(Saint Kitts en Nevis)

        - KY(Kaaimaneilanden)

        - LC(Saint Lucia)

        - MF(Sint Maarten)

        - MQ(Martinique)

        - MS(Montserrat)

        - MX(Mexico)

        - NI(Nicaragua)

        - PA(Panama)

        - PM(Saint Pierre en Miquelon)

        - PR(Puerto Rico)

        - SV(El Salvador)

        - SX(Sint Maarten)

        - TC(Turks- en Caicoseilanden)

        - TT(Trinidad en Tobago)

        - UM(Vs Afgelegen eilanden)

        - VS(Verenigde Staten)

            - VS-AK(Alaska)

            - US-AL(Alabama)

            - VS-AR(Arkansas)

            - VS-AZ(Arizona)

            - VS-CA(Californië)

            - VS-CO(Colorado)

            - US-CT(Connecticut)

            - US-DC(District of Columbia)

            - US-DE(Delaware)

            - VS-FL(Florida)

            - VS-GA(Georgië)

            - US-HI(Hawaï)

            - VS-IA(Iowa)

            - US-ID(Idaho)

            - VS-IL(Illinois)

            - US-IN(Indiana)

            - US-KS(Kansas)

            - US-KY(Kentucky)

            - VS-LA(Louisiana)

            - VS-MA(Massachusetts)

            - US-MD(Maryland)

            - US-ME(Maine)

            - US-MI(Michigan)

            - VS-MN(Minnesota)

            - US-MO(Missouri)

            - VS-MS(Mississippi)

            - VS-MT(Montana)

            - US-NC(North Carolina)

            - US-ND(North Dakota)

            - US-NE(Nebraska)

            - VS-NH(New Hampshire)

            - VS-NJ(New Jersey)

            - VS-NM(New Mexico)

            - US-NV(Nevada)

            - VS-NY(New York)

            - US-OH(Ohio)

            - US-OK(Oklahoma)

            - VS-OR(Oregon)

            - VS-PA(Pennsylvania)

            - US-RI(Rhode Island)

            - US-SC(South Carolina)

            - US-SD(South Dakota)

            - US-TN(Tennessee)

            - US-TX(Texas)

            - VS-UT(Utah)

            - VS-VA(Virginia)

            - VS-VT(Vermont)

            - VS-WA(Washington)

            - VS-WI(Wisconsin)

            - VS-WV(West Virginia)

            - US-WY(Wyoming)

        - VC(Saint Vincent and the Grenadines)

        - VG(Britse Maagdeneilanden)

        - VI(Amerikaanse Maagdeneilanden)

        - XE(Sint Eustatius)

        - XS(Saba)

    - GEO-AS(Azië)

        - AF(Afghanistan)

        - AM(Armenië)

        - AZ(Azerbeidzjan)

        - BD(Bangladesh)

        - BN(Brunei)

        - BT(Bhutan)

        - CC(Cocos (Keeling) Eilanden)

        - GN(China)

        - CX(Christmas Island)

        - GE(Georgië)

        - HK(Hong Kong SAR)

        - ID(Indonesië)

        - IN(India)

        - IO (British Indian Ocean Territory)

        - JP(Japan)

        - KG(Kirgizië)

        - KH(Cambodja)

        - KP(Noord-Korea)

        - KR(Korea)

        - KZ(Kazachstan)

        - LA(Laos)

        - LK(Sri Lanka)

        - MM(Myanmar)

        - MN(Mongolië)

        - MO(Macao SAR)

        - MV(Malediven)

        - MY (Maleisië)

        - NP(Nepal)

        - PH(Filippijnen)

        - PK(Pakistan)

        - SG(Singapore)

        - TH(Thailand)

        - TJ(Tadzjikistan)

        - TL(Timor_Leste)

        - TM(Turkmenistan)

        - TW(Taiwan)

        - UZ(Oezbekistan)

        - VN(Vietnam)

    - GEO-AF(Afrika)

        - AO(Angola)

        - BF(Burkina Faso)

        - BI(Burundi)

        - BJ(Benin)

        - BV(Bouvet Eiland)

        - BW(Botswana)

        - CD(Congo (DRC))

        - CF(Centraal-Afrikaanse Republiek)

        - CI(Ivoorkust)

        - CM(Kameroen)

        - CV(Cabo Verde)

        - DJ(Djibouti)

        - DZ(Algerije)

        - EG(Egypte)

        - ER(Eritrea)

        - ET(Ethiopië)

        - GA(Gabon)

        - GH(Ghana)

        - GM(Gambia)

        - GN(Guinee)

        - GQ(Equatoriaal-Guinea)

        - GW(Guinea_Bissau)

        - KE(Kenia)

        - KM(Comoren)

        - LR(Liberia)

        - LS(Lesotho)

        - LY(Libië)

        - MA(Marokko)

        - MG(Madagaskar)

        - ML(Mali)

        - MR(Mauritanië)

        - MU(Mauritius)

        - MW(Malawi)

        - MZ(Mozambique)

        - NA(Namibië)

        - NE(Niger)

        - NG(Nigeria)

        - RE(Réunion)

        - RW(Rwanda)

        - SC(Seychellen)

        - SD(Soedan)

        - SH (St Helena, Ascension, Tristan da Cunha)

        - SL(Sierra Leone)

        - SN(Senegal)

        - SO(Somalië)

        - SS(Zuid-Soedan)

        - ST(São Tomé en Príncipe)

        - SZ(Swaziland)

        - TD(Tsjaad)

        - TF(Franse Zuidelijke Gebieden)

        - TG(Togo)

        - TN(Tunesië)

        - TZ(Tanzania)

        - UG(Oeganda)

        - YT(Mayotte)

        - ZA(Zuid-Afrika)

        - ZM(Zambia)

        - ZW(Zimbabwe)

    - GEO-AN(Antarctica)

        - AQ(Antarctica)

    - GEO-SA(Zuid-Amerika)

        - AR(Argentinië)

        - BO(Bolivia)

        - BR(Brazilië)

        - CL(Chili)

        - CO(Colombia)

        - EG(Ecuador)

        - FK(Falklandeilanden)

        - GF(Frans-Guyana)

        - GS(South Georgia and South Sandwich Islands)

        - GY(Guyana)

        - PE(Peru)

        - PY(Paraguay)

        - SR(Suriname)

        - UY(Uruguay)

        - VE(Venezuela)

    - GEO-AP(Australië / Stille Oceaan)

        - AS(Amerikaans Samoa)

        - AU(Australië)

            - AU-ACT (Australian Capital Territory)

            - AU-NSW(New South Wales)

            - AU-NT(Noordelijk Territorium)

            - AU-QLD(Queensland)

            - AU-SA(Zuid-Australië)

            - AU-TAS(Tasmanië)

            - AU-VIC(Victoria)

            - AU-WA(West-Australië)

        - CK(Cook eilanden)

        - FJ(Fiji)

        - FM(Micronesië)

        - GU(Guam)

        - HM(Heard Island en McDonald Islands)

        - KI(Kiribati)

        - MH(Marshalleilanden)

        - MP (Noordelijke Marianen)

        - NC(Nieuw-Caledonië)

        - NF(Norfolk Island)

        - NR(Nauru)

        - NU(Niue)

        - NZ(Nieuw-Zeeland)

        - PF(Frans-Polynesië)

        - PG (Papoea-Nieuw-Guinea)

        - PN(Pitcairn-eilanden)

        - PW(Palau)

        - SB(Salomonseilanden)

        - TK(Tokelau)

        - NAAR(Tonga)

        - TV(Tuvalu)

        - VU(Vanuatu)

        - WF(Wallis en Futuna)

        - WS(Samoa)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [de methode voor het routeren van geografisch verkeer in Azure Traffic Manager](traffic-manager-routing-methods.md#geographic).
