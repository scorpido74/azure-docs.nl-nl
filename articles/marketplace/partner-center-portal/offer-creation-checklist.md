---
title: SaaS bieden creatie checklist - Commerciële Marktplaats voor Azure
description: De details die u verstrekken in het SaaS-proces voor het maken van de producten. - Commerciële marktplaats voor Azure
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 60111b6a23c76314383c5f95be3eb4b38f90d3e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281337"
---
# <a name="saas-offer-creation-checklist"></a>Controlelijst voor SaaS-aanbieding maken

Het SaaS-aanbodcreatieproces neemt u mee door meerdere pagina's. Hier zijn de details die u opgeven op elke pagina, met links om meer te weten te komen over elk item.

Objecten die u moet opgeven of opgeven, worden hieronder vermeld. Sommige gebieden zijn optioneel of hebben standaardwaarden die u naar wens wijzigen. U hoeft niet te werken aan deze secties in de volgorde hier vermeld.

| **Item**    | **Doel**  |
| :---------- | :-------------------|
| [**Nieuw aanbod Modaal**](#new-offer-modal) | Verzamelt identiteitsgegevens van aanbiedingen.  |
| [Pagina Aanbiedingsinstelling](#offer-setup-page) | Hiermee u zich aanmelden om belangrijke functies te gebruiken en te kiezen hoe u uw aanbieding via Microsoft verkopen.  |
| [Pagina Eigenschappen](#properties-page) | Definieer de categorieën en bedrijfstakken die worden gebruikt om uw aanbieding op de marktplaatsen, de juridische contracten die uw aanbieding ondersteunen en uw app-versie te groeperen. |
| [Aanbiedingspagina](#offer-listing-page) | Definieer de aanbiedingsgegevens die in de markt moeten worden weergegeven, inclusief beschrijvingen van uw aanbieding en marketingmiddelen. |
| [Voorbeeldpagina](#preview-page) | Definieer een beperkte preview-doelgroep voor het vrijgeven van je aanbieding voordat je je aanbieding live publiceert aan de bredere doelgroep(s). |
| [Pagina Technische configuratie aanbieden](#technical-configuration-page)  | Alleen beschikbaar als u ervoor kiest om de aanbieding via Microsoft te verkopen. Definieer de technische details (URL-pad, webhook, tenant-id en app-id) die worden gebruikt om verbinding te maken met uw aanbieding. |
| [**Nieuw plan Modaal**](#plan-identity-modal) | Verzamelt identiteitsgegevens van het plan.  |
| [Pagina Aanbieding plannen](#plan-listing-page)  | Alleen beschikbaar als u ervoor kiest om de aanbieding via Microsoft te verkopen. Definieer de details die worden gebruikt om het plan in de marktplaats weer te geven.  |
| [Pagina Prijsprijzen & plannen](#plan-pricing--availability-page)  | Alleen beschikbaar als u ervoor kiest om de aanbieding via Microsoft te verkopen.  Verzamelt de bedrijfskenmerken (prijsmodel), doelgroep en marktbeschikbaarheid voor elk plan (versie) van uw aanbieding.  |
| [Aanbiedingspagina voor teststation](#test-drive-listing-page)  | Alleen beschikbaar als u ervoor kiest om een proefrit aan te bieden voor uw aanbieding. Definieer de details die worden gebruikt om de testrit op de marktplaats weer te geven.  |
| Pagina Technische configuratie van teststation  | Alleen beschikbaar als u ervoor kiest om een proefrit aan te bieden voor uw aanbieding. Definieer de technische details voor de demonstratie (of "proefrit") waarmee klanten uw aanbieding kunnen proberen voordat ze zich ertoe verbinden deze te kopen.  |
| [Pagina bekijken en publiceren](#review-and-publish-page)  | Selecteer de wijzigingen die u wilt publiceren, bekijk de status van elke pagina en geef notities aan het certificeringsteam.  |


## <a name="new-offer-modal"></a>Nieuw aanbod modaal 

De eerste informatie die u zal worden gevraagd om te verstrekken zijn een ID en alias voor uw aanbod. 

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| Aanbiedings-id  | Vereist, kan niet worden gewijzigd na de schepping. Maximaal 50 tekens en mag alleen bestaan uit kleine letters, alfanumerieke tekens, streepjes of underscores. |
| Alias aanbieden  | Vereist. |

## <a name="offer-setup-page"></a>Pagina Aanbiedingsinstelling

De pagina met het instellen van aanbiedingen is waar u zich aanmelden voor verschillende kanalen en het verkopen van bewegingen, evenals het gebruik van belangrijke functies, zoals een testrit en klantleads. 

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------|  
| Wilt u verkopen via Microsoft?  | Vereist. Standaard: Ja |
| Hoe wilt u dat potentiële klanten interactie hebben met de aanbiedingsvermelding? (Oproep tot actie)  | Vereist als u niet via Microsoft verkoopt. Standaard: Gratis proefversie, opties: "Nu krijgen", "Gratis proefversie", "Neem contact met mij op" |
| URL van proef  | Vereist als 'Gratis proefversie' is geselecteerd, omdat klanten moeten communiceren met de aanbiedingsvermelding. |
| URL van aanbieding  | Vereist als 'Nu krijgen' is geselecteerd, omdat de manier waarop klanten moeten communiceren met de aanbiedingsvermelding |
| Kanalen  | Optioneel. Standaard: u hebt zich niet aangemeld voor het CSP-kanaal (reseller).  |
| Test Drive | Optioneel. Standaard: geen testrit ingeschakeld.  |
| Type teststation | Vereist indien ingeschakeld een proefrit. Standaard: geen geselecteerd. Opties: Azure Resource Manager, Dynamics 365 voor Business Central, Dynamics 365 voor Klantbetrokkenheid, Dynamics 365 voor Operations, Logic-app, Power BI.  |
| Leadmanagement - verbinding maken met een CRM-systeem | Vereist als u verkoopt via Microsoft, of als aanbiedingsaanbiedingen als 'Neem contact met mij op'. Standaard: geen CRM-systeem aangesloten. CRM-opties: Azure-tabel, Azure-blob, Dynamics CRM online, HTTPs' eindpunt, Marketo, Salesforce  |

## <a name="properties-page"></a>De pagina Eigenschappen

Op de pagina Eigenschappen definieert u de categorieën en bedrijfstakken die worden gebruikt om uw aanbieding op de marktplaatsen, de juridische contracten ter ondersteuning van uw aanbieding en uw app-versie te groeperen. Zorg ervoor dat u volledige en nauwkeurige details over uw aanbod op deze pagina, zodat het op de juiste manier wordt weergegeven en aangeboden aan de juiste set van klanten. 

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------|  
| Categorie en subcategorie | Vereist 1 en max 3. Standaard: geen geselecteerd. |
| Industrieën en subindustrieën | Optioneel. max 2 L1 Industries en max 2 subindustrieën binnen elke L1-industrie, Standaard: Geen geselecteerd |
| App-versie  | Optioneel. Standaard: Geen. |
| Standaardcontract gebruiken  | Optioneel. Standaard: niet geselecteerd.  | |
| Gebruiksvoorwaarden  | Vereist als het standaardcontract niet is geselecteerd.  |

## <a name="offer-listing-page"></a>Aanbiedingspagina aanbieden

Op de aanbiedingspagina geeft u de tekst en afbeeldingen op die klanten zien bij het bekijken van de vermelding van uw aanbieding op de marktplaats. 

| **Veldnaam**    | **Opmerkingen**   |
| :---------------- | :-----------| 
| Name  | Vereist, max 50 chars. |
| Samenvatting  | Vereist, max 100 chars. | 
| Beschrijving  | Vereist, max 3000 chars. |
| Instructies voor aan de slag  | Vereist, max 3000 chars. |
| Instructies voor aan de slag  | Vereist, max 3000 chars. |
| Zoektrefwoorden  | Optionele, aanbevolen, max 3 zoekwoorden. |
| URL van het privacybeleid  | Vereist. |
| URL van CSP-programmamarketingmaterialen  | Optioneel. |
| Nuttige koppelingen Titel + URL  | Optioneel. |
| Titel van ondersteunende documenten + Bestand  | Vereist, min 1 en max 3. Moet PDF-bestandsindeling zijn. |
| Schermopnamen  | Vereist, min 1 screenshot en max 5; vier of meer aanbevolen. Moet 1280 X 720 in PNG-formaat. |
| Winkellogo's (klein, gemiddeld, groot, breed, held)  | Klein (48 X 48) en Large (216 X 216) vereist; andere maten optioneel, maar aanbevolen: Medium (90 x 90), Wide (255 x 115), Hero (815 x 290). Moet in PNG-formaat. |
| Video's naam + URL + miniatuur  | Optionele, aanbevolen, max 4 video's. Miniatuur moet 1280 x 720 in PNG-indeling zijn. Video moet worden gehost in YouTube of Vimeo. |
| Contactpersonen (CSP-programma, engineering, ondersteuning)  | Technische en ondersteuningscontactpersoon vereist (naam, e-mail en telefoonnummer); CSP-programma contact optioneel, maar aanbevolen. |
| URL voor ondersteuning  | Vereist. |

## <a name="preview-page"></a>Voorbeeldpagina

Op de voorbeeldpagina geeft u de doelgroep op die toegang moet hebben tot uw aanbiedingsvoorbeeld, om te controleren of de aanbieding aan al uw vereisten voldoet voordat deze live gaat. 

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| AAD/MSA e-mail + beschrijving | Vereist, min 1 en max 10 als handmatig ingevoerd, of tot 20 als het uploaden van een CSV-bestand. |

## <a name="technical-configuration-page"></a>Pagina technische configuratie 

Op de pagina technische configuratie geeft u de technische details op die Door Microsoft worden gebruikt om verbinding te maken met uw aanbieding. Deze pagina is niet zichtbaar voor u als u hebt besloten niet te verkopen via Microsoft.

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| URL van bestemmingspagina | Vereist als u verkoopt via Microsoft. |
| Verbinding webhook | Vereist als u verkoopt via Microsoft. |
| Azure AD-tenant-id | Vereist als u verkoopt via Microsoft. |
| Azure AD-app-id | Vereist als u verkoopt via Microsoft. |

## <a name="plan-identity-modal"></a>Plan identiteit modaal

De eerste informatie die u wordt gevraagd te verstrekken zijn een naam en een ID voor uw plan. Deze pagina is niet zichtbaar voor u als u hebt besloten niet te verkopen via Microsoft.

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| Plan-id  | Vereist als u verkoopt via Microsoft. Het kan niet worden veranderd na de schepping. Maximaal 50 tekens en mag alleen bestaan uit kleine letters, alfanumerieke tekens, streepjes of underscores. |
| Naam van het plan  | Vereist als u verkoopt via Microsoft. Moet uniek zijn in alle plannen in het aanbod. Max 50 tekens. |

## <a name="plan-listing-page"></a>Aanbiedingspagina plannen

Op de pagina met abonnementsvermeldingen u de tekst die klanten kunnen zien bij het bekijken van het abonnement op de marktplaats. Deze pagina is niet zichtbaar voor u als u hebt besloten niet te verkopen via Microsoft.

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| Beschrijving van het plan   | Vereist als u verkoopt via Microsoft. Max 500 tekens. | |

## <a name="plan-pricing--availability-page"></a>Pagina Prijzen plannen & beschikbaarheid

De pagina planprijzen en beschikbaarheid is de plaats waar u de bedrijfskenmerken, doelgroep en marktbeschikbaarheid voor elk abonnement (versie) van uw aanbieding definieert. Deze pagina is niet zichtbaar voor u als u hebt besloten niet te verkopen via Microsoft.

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| Beschikbaarheid van de markt  | Vereist, min 1 en max 141. |
| Prijsmodel  | Vereist. Standaard: vast tarief. Opties: Vast tarief, per gebruiker. |
| Minimum- en maximumzitplaatsen  | Optioneel, alleen beschikbaar als het prijsmodel op basis van een stoel is geselecteerd. |
| Factureringstermijn  | Vereist. Standaard: maandelijks. Opties: Maandelijks, Jaarlijks. |
| Prijs  | Vereist USD per maand, als de maandelijkse factureringstermijn is geselecteerd; of USD per jaar als de jaarlijkse factureringstermijn is geselecteerd. |
| Doelgroep plannen  | Optioneel. Standaard: openbaar plan. Opties: Openbaar, Privé per tenant-id |
| Doelgroep met beperkte abonnement (tenant-id + beschrijving)  | Vereist als privé-abonnement is geselecteerd. Min 1 en max 10 tenant-id's als deze handmatig worden ingevoerd. Max 20000 als CSV-bestand wordt geïmporteerd. |

## <a name="test-drive-listing-page"></a>Aanbiedingspagina teststation

Alleen beschikbaar als u ervoor kiest om een proefrit aan te bieden voor uw aanbieding. Definieer de details die worden gebruikt om de testrit op de marktplaats weer te geven.

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| Beschrijving  | Vereist. |
| Gebruikersnaamnaam + bestand  | Vereist, max 1 doc. Moet PDF-formaat zijn. |
| Videonaam, URL + miniatuur  | Optioneel, aanbevolen. Miniatuur moet 533 x 324 zijn in JPGP- of PNG-indeling. Video moet worden gehost in YouTube of Vimeo. |

## <a name="review-and-publish-page"></a>Pagina bekijken en publiceren

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| Notities voor certificering  | Optioneel. |

## <a name="next-steps"></a>Volgende stappen

- [Een nieuwe SaaS-aanbieding maken](./create-new-saas-offer.md)
