---
title: Controle lijst voor het maken van SaaS-aanbiedingen in micro soft Commercial Marketplace
description: De details die u kunt opgeven in het proces voor het maken van SaaS-aanbiedingen in Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: b30afd4ef69b9b4ecb49b99f4455bf136a6e6eb2
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85374403"
---
# <a name="saas-offer-creation-checklist-in-partner-center"></a>Controle lijst voor het maken van SaaS-aanbiedingen in partner centrum

Het proces voor het maken van SaaS-aanbiedingen leidt u door meerdere pagina's.  Hier vindt u de details die u op elke pagina kunt opgeven, met koppelingen voor meer informatie over elk item.

De items die u moet opgeven of opgeven, worden hieronder vermeld.  Sommige gebieden zijn optioneel of bevatten standaard waarden, die u naar wens kunt wijzigen.  U hoeft niet aan deze secties te werken in de volg orde die hier wordt vermeld.

>[!Note]
>Als u een voor transactable SaaS-aanbieding maakt, zorg er dan voor dat u integratie implementeert met [SaaS-fulfillment-api's](./pc-saas-fulfillment-apis.md).  Integratie met de Api's is de enige manier om de trans acties op Marketplace goed te laten werken.

| **Item**    | **Doel**  |
| :---------- | :-------------------|
| [**Nieuwe aanbieding modaal**](#new-offer-modal) | Verzamelt identiteits gegevens van de aanbieding.  |
| [Pagina aanbieding instellen](#offer-setup-page) | Met kunt u zich aanmelden voor belang rijke functies en kiezen hoe u uw aanbieding via micro soft wilt verkopen.  |
| [Eigenschappen pagina](#properties-page) | Definieer de categorieën en industrieën die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, de juridische contracten die uw aanbieding ondersteunen en uw app-versie. |
| [Aanbiedings pagina](#offer-listing-page) | Definieer de details van de aanbieding die moeten worden weer gegeven op Marketplace, inclusief beschrijvingen van uw aanbieding en marketing assets.|
| [Voorbeeld pagina](#preview-page) | Definieer een beperkte preview-doel groep voor het vrijgeven van uw aanbieding voordat u uw aanbieding Live publiceert naar de bredere doel groep (en) van de Marketplace.|
| [Pagina technische configuratie aanbieding](#technical-configuration-page)  |  Alleen beschikbaar als u selecteert om het aanbod via micro soft te verkopen.  Definieer de technische details (URL van de landings pagina, de URL van de verbindings webhook, de Azure AD-Tenant-ID en de Azure AD-App-ID) die wordt gebruikt door Marketplace om verbinding te maken met uw aanbieding.  Deze para meters zijn vereist om correct te kunnen integreren met SaaS-uitvoering en de Marketplace-Api's met gemeetde facturering.|
| [**Nieuw plan modaal**](#plan-identity-modal) | Verzamelt informatie over plan identiteit.  |
| [Aanbiedings pagina plannen](#plan-listing-page)  | Alleen beschikbaar als u selecteert om het aanbod via micro soft te verkopen. Definieer de details die worden gebruikt om het abonnement in de Marketplace weer te geven.  |
| [Pagina met prijs & Beschik baarheid plannen](#plan-pricing--availability-page)  | Alleen beschikbaar als u selecteert om het aanbod via micro soft te verkopen.  Verzamelt de bedrijfs kenmerken (prijs model), de beschik baarheid van de doel groep en de markt voor elk abonnement (versie) van uw aanbieding.  |
| [Lijst pagina test station](#test-drive-listing-page)  | Deze optie is alleen beschikbaar als u een test drive voor uw aanbieding aanbiedt. Definieer de details die worden gebruikt om de test drive in de Marketplace weer te geven.  |
| Pagina voor technische configuratie van test station  | Deze optie is alleen beschikbaar als u een test drive voor uw aanbieding aanbiedt. Definieer de technische Details voor de demonstratie (of ' test drive ') waarmee klanten uw aanbieding kunnen proberen voordat ze zich door voeren om deze aan te schaffen.  |
| [Pagina controleren en publiceren](#review-and-publish-page)  | Selecteer de wijzigingen die u wilt publiceren, Bekijk de status van elke pagina en geef notities op het certificerings team.  |


## <a name="new-offer-modal"></a>Nieuwe aanbieding modaal 

De eerste informatie die u moet opgeven, is een ID en alias voor uw aanbieding. 

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| Aanbiedings-id  | Vereist, kan niet worden gewijzigd na het maken. Maxi maal 50 tekens en mag alleen bestaan uit kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. |
| Aanbiedings alias  | Vereist. |

## <a name="offer-setup-page"></a>Pagina aanbieding instellen

Op de pagina aanbieding instellen kunt u kiezen uit verschillende kanalen en verkoop bewegingen, en kunt u het gebruik van belang rijke functies, zoals test drive en leads van klanten, declareren. 

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------|  
| Wilt u door micro soft verkopen?  | Vereist. Standaardinstelling: Ja |
| Hoe wilt u dat potentiële klanten met de aanbieding kunnen communiceren? (Actie aanroepen)  | Vereist als niet wordt verkocht via micro soft. Standaard: gratis proef versie, opties: ' nu downloaden ', ' gratis proef versie ', ' contact met mij opnemen '. |
| URL van proef versie  | Vereist als ' gratis proef versie ' is geselecteerd, als de manier waarop klanten moeten communiceren met de aanbieding. |
| Aanbiedings-URL  | Vereist als ' nu ophalen ' is geselecteerd, als de manier waarop klanten moeten communiceren met de aanbiedings vermelding |
| Kanalen  | Optioneel. Standaard: geen gekozen voor het CSP-kanaal (reseller).  |
| Test Drive | Optioneel. Standaard: er is geen test drive ingeschakeld.  |
| Type van het test station | Vereist als een test drive is ingeschakeld. Standaard: geen geselecteerd. Opties: Azure Resource Manager, Dynamics 365 voor bedrijven Central, Dynamics 365 voor klant betrokkenheid, Dynamics 365 voor Operations, Logic app, Power BI.  |
| Leads van klanten-verbinding maken met een CRM-systeem | Vereist als er wordt verkocht via micro soft, of als een aanbieding wordt aangeboden als ' contact met mij opnemen '. Standaard: er is geen CRM-systeem verbonden. CRM-opties: Azure Table, Azure-Blob, Dynamics CRM Online, HTTPs-eind punt, Marketo, Sales Force  |

## <a name="properties-page"></a>De pagina Eigenschappen

Op de pagina eigenschappen definieert u de categorieën en industrieën die worden gebruikt voor het groeperen van uw aanbieding op Marketplace, de juridische contracten die uw aanbieding ondersteunen en uw app-versie. Zorg ervoor dat u volledige en nauw keurige Details opgeeft over uw aanbieding op deze pagina, zodat deze correct wordt weer gegeven en aan de juiste set klanten wordt aangeboden. 

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------|  
| Categorie en subcategorie | Vereist 1 en Maxi maal 3. Standaard: geen geselecteerd. |
| Industrieën en subbranches | Optioneel. Maxi maal 2 L1-industrieën en Maxi maal 2 subbranches binnen elke L1-industrie, standaard: geen geselecteerd |
| App-versie  | Optioneel. Standaard: geen. |
| Standaard contract gebruiken  | Optioneel. Standaard: niet geselecteerd.  | |
| Gebruiksvoorwaarden  | Vereist als het standaard contract niet is geselecteerd.  |

## <a name="offer-listing-page"></a>Aanbiedings pagina

De pagina met de vermelding bevat de tekst en afbeeldingen die klanten zien wanneer ze de aanbieding van uw aanbieding op Marketplace bekijken. 

| **Veldnaam**    | **Opmerkingen**   |
| :---------------- | :-----------| 
| Naam  | Vereist, Maxi maal 50 tekens. |
| Samenvatting  | Vereist, Maxi maal 100 tekens. | 
| Beschrijving  | Vereist, Maxi maal 3000 tekens. |
| Aan de slag-instructies  | Vereist, Maxi maal 3000 tekens. |
| Aan de slag-instructies  | Vereist, Maxi maal 3000 tekens. |
| Tref woorden zoeken  | Optioneel, aanbevolen, Maxi maal 3 tref woorden. |
| URL van privacybeleid  | Vereist. |
| URL van het CSP-programma marketing materiaal  | Optioneel. |
| Titel van handige koppelingen + URL  | Optioneel. |
| Titel van ondersteunende documenten + bestand  | Vereist, min 1 en Maxi maal 3. Moet PDF-bestands indeling zijn. |
| Schermopnamen  | Vereist, min 1 scherm afbeelding en maximum 5; vier of meer aanbevolen. Moet 1280 X 720 zijn in PNG-indeling. |
| Archief logo's (klein, normaal, groot, breed)  | Klein (48 X 48) en groot (216 X 216) vereist; andere grootten zijn optioneel, maar aanbevolen: gemiddeld (90 x 90), breed (255 x 115). Moet in zijn. PNG-indeling. |
| Video's naam + URL + miniatuur  | Optioneel, aanbevolen, Maxi maal 4 Video's. De miniatuur moet 1280 x 720 zijn in PNG-indeling. Video moet worden gehost in YouTube of Vimeo. |
| Contacts (CSP-programma, techniek, ondersteuning)  | Technische hulp en ondersteuning vereist (naam, e-mail adres en telefoon nummer); CSP-programma contact opnemen optioneel, maar aanbevolen. |
| Ondersteunings-URL  | Vereist. |

## <a name="preview-page"></a>Voorbeeld pagina

Op de voorbeeld pagina kunt u opgeven dat de doel groep toegang heeft tot uw Preview-voor beeld om te controleren of de aanbieding aan al uw vereisten voldoet voordat deze live gaat. 

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| AAD/MSA e-mail + beschrijving | Vereist, min 1 en maximum 10 als deze hand matig worden ingevoerd of Maxi maal 20 als een CSV-bestand wordt geüpload. |

## <a name="technical-configuration-page"></a>Pagina technische configuratie 

Op de pagina technische configuratie kunt u de technische gegevens opgeven die door micro soft worden gebruikt om verbinding te maken met uw aanbieding. Deze pagina is niet zichtbaar voor u als u ervoor hebt gekozen om niet via micro soft te verkopen.

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| URL van landings pagina | Vereist als er wordt verkocht via micro soft. |
| Verbindings-webhook | Vereist als er wordt verkocht via micro soft. |
| Azure AD-tenant-id | Vereist als er wordt verkocht via micro soft. |
| Azure AD-App-ID | Vereist als er wordt verkocht via micro soft. |

## <a name="plan-identity-modal"></a>De identiteit van het modale abonnement

De eerste stukjes informatie die u wilt opgeven, zijn een naam en een ID voor uw abonnement. Deze pagina is niet zichtbaar voor u als u hebt besloten niet via micro soft te verkopen.

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| Plan-ID  | Vereist als er wordt verkocht via micro soft. Het kan niet worden gewijzigd nadat het is gemaakt. Maxi maal 50 tekens en mag alleen bestaan uit kleine letters, alfanumerieke tekens, streepjes of onderstrepingen. |
| Naam van het plan  | Vereist als er wordt verkocht via micro soft. Moet uniek zijn in alle abonnementen van de aanbieding. Maxi maal 50 tekens. |

## <a name="plan-listing-page"></a>Aanbiedings pagina plannen

Op de pagina plan vermelding kunt u de tekst opgeven die klanten kunnen zien wanneer ze het plan in de Marketplace bekijken. Deze pagina is niet zichtbaar voor u als u ervoor hebt gekozen om niet via micro soft te verkopen.

| **Veldnaam**    | **Opmerkingen**   |  
| :---------------- | :-----------| 
| Plan beschrijving   | Vereist als er wordt verkocht via micro soft. Maxi maal 500 tekens. | |

## <a name="plan-pricing--availability-page"></a>Pagina met prijs & Beschik baarheid plannen

Op de pagina prijzen en beschik baarheid plannen kunt u de bedrijfs kenmerken, de doel groep en de beschik baarheid van de markt voor elk abonnement (versie) van uw aanbieding definiëren. Deze pagina is niet zichtbaar voor u als u ervoor hebt gekozen om niet via micro soft te verkopen.

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| Beschik baarheid van de markt  | Vereist, min 1 en Max. 141. |
| Prijs model  | Vereist. Standaard: vast aantal. Opties: vast aantal, per gebruiker. |
| Minimum-en maximum aantal stoelen  | Optioneel: alleen beschikbaar als het prijs model op basis van de Seat is geselecteerd. |
| Facturerings termijn  | Vereist. Standaard: maandelijks. Opties: maandelijks, jaarlijks. |
| Prijs  | Vereist USD per maand, als de maandelijkse facturerings periode is geselecteerd. of USD per jaar als de jaarlijkse facturerings periode is geselecteerd. |
| Doel groep plannen  | Optioneel. Standaard: openbaar abonnement. Opties: openbaar, privé op Tenant-ID |
| Doel groep voor beperkt abonnement (Tenant-ID + beschrijving)  | Vereist als privé plan is geselecteerd. Min 1 en Maxi maal 10 Tenant-Id's, indien hand matig ingevoerd. Maxi maal 20000 als CSV-bestand wordt geïmporteerd. |

## <a name="test-drive-listing-page"></a>Lijst pagina test station

Deze optie is alleen beschikbaar als u een test drive voor uw aanbieding aanbiedt. Definieer de details die worden gebruikt om de test drive in de Marketplace weer te geven.

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| Beschrijving  | Vereist. |
| Hand matige naam en bestand van de gebruiker  | Vereist, Maxi maal 1 document. moet PDF-indeling hebben. |
| Video naam, URL + miniatuur  | Optioneel, aanbevolen. De miniatuur moet 533 x 324 zijn in JPGP-of PNG-indeling. Video moet worden gehost in YouTube of Vimeo. |

## <a name="review-and-publish-page"></a>Pagina controleren en publiceren

| **Veldnaam**    | **Opmerkingen**   | 
| :---------------- | :-----------| 
| Opmerkingen voor certificering  | Optioneel. |

## <a name="next-steps"></a>Volgende stappen

- [Een nieuwe SaaS-aanbieding maken](./create-new-saas-offer.md)
