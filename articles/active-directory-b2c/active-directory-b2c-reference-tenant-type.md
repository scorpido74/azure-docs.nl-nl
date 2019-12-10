---
title: Beschik baarheid van regio's en gegevens locatie
titleSuffix: Azure AD B2C
description: Beschik baarheid van regio's, gegevens locatie en informatie over Azure Active Directory B2C preview-tenants.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1b09eb85df6748fed042731ac90ebbf20c65b702
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950507"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Beschik baarheid van regio's & gegevens locatie

Beschik baarheid van regio's en gegevens locatie zijn twee verschillende concepten die verschillend van toepassing zijn op Azure AD B2C van de rest van Azure. In dit artikel vindt u informatie over de verschillen tussen deze twee concepten en vergelijkt u hoe deze van toepassing zijn op Azure versus Azure AD B2C.

Azure AD B2C is **over het algemeen wereld wijd beschikbaar** met de optie voor **gegevens locatie** in de **Verenigde Staten, Europa of Azië en Stille Oceaan**.

[Beschik baarheid van regio's](#region-availability) verwijst naar waar een service beschikbaar is voor gebruik.

De [gegevens locatie](#data-residency) verwijst naar de locatie waar de gebruikers gegevens worden opgeslagen.

## <a name="region-availability"></a>Regionale beschikbaarheid

Azure AD B2C is wereld wijd beschikbaar via de open bare Azure-Cloud.

Dit wijkt af van het model, gevolgd door de meeste andere Azure-Services, die doorgaans *Beschik baarheid* met *gegevens locatie*. U kunt voor beelden van dit bekijken op de pagina [producten van Azure die beschikbaar zijn per regio](https://azure.microsoft.com/regions/services/) en de [Active Directory B2C prijs calculator](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Gegevensresidentie

Azure AD B2C gebruikers gegevens worden opgeslagen in Verenigde Staten, Europa of de regio van Azië en Stille Oceaan.

Data locatie wordt bepaald door het land/de regio die u selecteert bij het [maken van een Azure AD B2C-Tenant](active-directory-b2c-get-started.md):

![Scherm opname van een preview-Tenant](./media/active-directory-b2c-reference-tenant-type/data-residency-b2c-tenant.png)

De gegevens bevinden zich in de **Verenigde Staten** voor de volgende landen/regio's:

> Verenigde Staten, Canada, Costa Rica, Dominicaanse Republiek, El Salvador, Guatemala, Mexico, Panama, Puerto Rico en Trinidad & Tobago

Gegevens bevinden zich in **Europa** voor de volgende landen/regio's:

> Algerije, Oosten rijk, Azerbeidzjan, Bahrein, Belarus, België, Bulgarije, Kroatië, Cyprus, Tsjechië, Denemarken, Egypte, Estland, Finland, Frank rijk, Duitsland, Grieken land, Hongarije, IJsland, Ierland, Israël, Italië, Jordanië, Kazachstan, Kenia, Koeweit, Letland, Libanon, Liechtenstein, Litouwen, Luxemburg, Noord-Macedonië, Malta, Montenegro, Marokko, Nederland, Nigeria, Noor wegen, Oman, Pakistan, Polen, Portugal, Qatar, Roemenië, Rusland, Saudi-Arabië, Servië, Slowakije, Slovenië, Zuid-Afrika, Spanje, Zweden, Zwitser land, Tunesië, Turkije, Oekraïne, Verenigde Arabische Emiraten en het Verenigd Konink rijk.

De gegevens bevinden zich in **Azië en Stille Oceaan** voor de volgende landen/regio's:

> Afghanistan, Hong Kong SAR, India, Indonesië, Japan, Korea, Maleisië, Filipijnen, Singapore, Sri Lanka, Taiwan en Thai land.

De volgende landen/regio's worden toegevoegd aan de lijst. U kunt nu nog steeds Azure AD B2C gebruiken door een van de bovenstaande landen/regio's te kiezen.

> Argentinië, Australië, Brazilië, Chili, Colombia, Ecuador, Irak, Nieuw-Zeeland, Paraguay, Peru, Uruguay en Venezuela.

## <a name="preview-tenant"></a>Preview-Tenant

Als u een B2C-Tenant hebt gemaakt tijdens de preview-periode van Azure AD B2C's, is het waarschijnlijk dat uw **Tenant type** **een preview-Tenant**bevat.

Als dit het geval is, moet u uw Tenant alleen gebruiken voor ontwikkelings-en test doeleinden. Gebruik geen voor beeld-Tenant voor productie toepassingen.

**Er is geen migratie-pad** van een preview B2C-Tenant naar een productie-Scale B2C-Tenant. U moet een nieuwe B2C-Tenant maken voor uw productie toepassingen.

Er zijn bekende problemen bij het verwijderen van een preview B2C-Tenant en het maken van een B2C-Tenant met dezelfde domein naam. *U moet een B2C-Tenant met een productie schaal maken met een andere domein naam*.

![Scherm opname van een preview-Tenant](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)