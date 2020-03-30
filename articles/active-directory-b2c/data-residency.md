---
title: Beschikbaarheid van regio's en dataresidentie
titleSuffix: Azure AD B2C
description: Beschikbaarheid van regio's, gegevensresidentie en informatie over azure Active Directory B2C-voorbeeldtenants.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3df0f581d0d2a1e5ca02202b4eeaede5a1dd5362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188845"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: beschikbaarheid van regio's & gegevensresidentie

Beschikbaarheid van regio's en gegevensresidentie zijn twee zeer verschillende concepten die anders van toepassing zijn op Azure AD B2C dan de rest van Azure. In dit artikel worden de verschillen tussen deze twee concepten uitgelegd en wordt uitgelegd hoe ze van toepassing zijn op Azure versus Azure AD B2C.

Azure AD B2C is **over de hele wereld beschikbaar** met de optie voor **dataresidency** in de **Verenigde Staten, Europa of Azië-Pacific.**

[Beschikbaarheid in de regio](#region-availability) verwijst naar waar een service beschikbaar is voor gebruik.

[Data residency](#data-residency) verwijst naar waar gebruikersgegevens worden opgeslagen.

## <a name="region-availability"></a>Beschikbaarheid in regio’s

Azure AD B2C is wereldwijd beschikbaar via de Azure public cloud.

Dit verschilt van het model gevolgd door de meeste andere Azure-services, die doorgaans *de beschikbaarheid* koppelen aan *dataresidency.* U voorbeelden hiervan zien op zowel de [pagina Producten beschikbaar per regio](https://azure.microsoft.com/regions/services/) van Azure als de active directory [B2C-prijscalculator](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Gegevenslocatie

Azure AD B2C slaat gebruikersgegevens op in de Verenigde Staten, Europa of de regio Azië-Pacific.

De woonplaats van gegevens wordt bepaald door het land/de regio die u selecteert wanneer u [een Azure AD B2C-tenant maakt:](tutorial-create-tenant.md)

![Schermafbeelding van een voorbeeldtenant](./media/data-residency/data-residency-b2c-tenant.png)

Gegevens bevinden zich in de **Verenigde Staten** voor de volgende landen/regio's:

> Verenigde Staten, Canada, Costa Rica, Dominicaanse Republiek, El Salvador, Guatemala, Mexico, Panama, Puerto Rico en Trinidad & Tobago

De gegevens bevinden zich in **Europa** voor de volgende landen/regio's:

> Algerije, Oostenrijk, Azerbeidzjan, Bahrein, Wit-Rusland, België, Bulgarije, Kroatië, Cyprus, Tsjechië, Denemarken, Egypte, Estland, Finland, Frankrijk, Duitsland, Griekenland, Hongarije, IJsland, Ierland, Israël, Italië, Jordanië, Kazachstan, Kenia, Koeweit, Letland, Libanon, Liechtenstein, Litouwen, Luxemburg, Noord-Macedonië, Malta, Montenegro, Marokko, Nederland, Nigeria, Noorwegen, Oman, Pakistan, Polen, Portugal, Qatar, Roemenië, Rusland, Saoedi-Arabië, Servië, Slowakije, Slovenië, Zuid-Afrika, Spanje, Zweden, Zwitserland, Tunesië, Turkije, Oekraïne, Verenigde Arabische Emiraten en het Verenigd Koninkrijk.

De gegevens bevinden zich in **Azië-Pacific** voor de volgende landen/regio's:

> Afghanistan, Hong Kong SAR, India, Indonesië, Japan, Korea, Maleisië, Filippijnen, Singapore, Sri Lanka, Taiwan en Thailand.

De volgende landen/regio's worden momenteel aan de lijst toegevoegd. Op dit moment u Azure AD B2C nog steeds gebruiken door een van de bovenstaande landen/regio's te kiezen.

> Argentinië, Australië, Brazilië, Chili, Colombia, Ecuador, Irak, Nieuw-Zeeland, Paraguay, Peru, Uruguay en Venezuela.

## <a name="preview-tenant"></a>Voorbeeld van tenant

Als u een B2C-tenant hebt gemaakt tijdens de previewperiode van Azure AD B2C, is het waarschijnlijk dat in het **type Tenant** **voorbeeldtenant wordt weergegeven.**

Als dit het geval is, moet u uw tenant alleen gebruiken voor ontwikkelings- en testdoeleinden. Gebruik GEEN voorbeeldtenant voor productietoepassingen.

**Er is geen migratiepad** van een voorbeeld-B2C-tenant naar een B2C-tenant op productieschaal. U moet een nieuwe B2C-tenant maken voor uw productietoepassingen.

Er zijn bekende problemen wanneer u een voorbeeld van B2C-tenant verwijdert en een B2C-tenant op productieschaal maakt met dezelfde domeinnaam. *U moet een B2C-tenant op productieschaal maken met een andere domeinnaam.*

![Schermafbeelding van een voorbeeldtenant](./media/data-residency/preview-b2c-tenant.png)