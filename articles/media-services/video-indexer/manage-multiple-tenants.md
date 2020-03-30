---
title: Meerdere tenants beheren met Video Indexer - Azure
description: In dit artikel worden verschillende integratieopties voorgesteld voor het beheren van meerdere tenants met Video Indexer.
services: media-services
documentationcenter: ''
author: ika-microsoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/15/2019
ms.author: ikbarmen
ms.openlocfilehash: 18f2cf3daa281400151ba223e1735e7138d97e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76990501"
---
# <a name="manage-multiple-tenants"></a>Meerdere tenants beheren

In dit artikel worden verschillende opties besproken voor het beheren van meerdere tenants met Video Indexer. Kies een methode die het meest geschikt is voor uw scenario:

* Video Indexer-account per tenant
* Eén video-indexeraccount voor alle huurders
* Azure-abonnement per tenant

## <a name="video-indexer-account-per-tenant"></a>Video Indexer-account per tenant

Bij het gebruik van deze architectuur wordt voor elke tenant een Video Indexer-account gemaakt. De tenants hebben volledige isolatie in de permanente en rekenlaag.  

![Video Indexer-account per tenant](./media/manage-multiple-tenants/video-indexer-account-per-tenant.png)

### <a name="considerations"></a>Overwegingen

* Klanten delen geen opslagaccounts (tenzij handmatig geconfigureerd door de klant).
* Klanten delen geen rekenkracht (gereserveerde eenheden) en hebben geen invloed op de verwerkingstakentijden van elkaar.
* U een tenant eenvoudig uit het systeem verwijderen door het Video Indexer-account te verwijderen.
* Er is geen mogelijkheid om aangepaste modellen te delen tussen huurders.

    Zorg ervoor dat er geen zakelijke vereiste is om aangepaste modellen te delen.
* Moeilijker te beheren dankzij meerdere Video Indexer -accounts (en bijbehorende Media Services) accounts per tenant.

> [!TIP]
> Maak een beheerdersgebruiker voor uw systeem in De Ontwikkelaarsportal voor [video-indexeren](https://api-portal.videoindexer.ai/) en gebruik de autorisatie-API om uw tenants het relevante [accounttoegangstoken te](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token)bieden.

## <a name="single-video-indexer-account-for-all-users"></a>Eén video-indexeraccount voor alle gebruikers

Bij het gebruik van deze architectuur is de klant verantwoordelijk voor het isoleren van huurders. Alle tenants moeten één Video Indexer-account gebruiken met één Azure Media Service-account. Bij het uploaden, zoeken of verwijderen van inhoud moet de klant de juiste resultaten voor die tenant filteren.

![Eén video-indexeraccount voor alle gebruikers](./media/manage-multiple-tenants/single-video-indexer-account-for-all-users.png)

Met deze optie kunnen aanpassingsmodellen (Persoon, Taal en Merken) worden gedeeld of geïsoleerd tussen huurders door de modellen te filteren op tenant.

Bij [het uploaden van video's](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)u per tenant een ander partitiekenmerk opgeven. Hierdoor kan isolatie in de [zoek-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Search-videos?)worden toegestaan. Door het partitiekenmerk op te geven in de zoek-API krijgt u alleen resultaten van de opgegeven partitie. 

### <a name="considerations"></a>Overwegingen

* Mogelijkheid om inhoud en aanpassingsmodellen tussen huurders te delen.
* De ene huurder heeft invloed op de prestaties van andere huurders.
* De klant moet een complexe beheerlaag bovenop Video Indexer bouwen.

> [!TIP]
> U het [prioriteitskenmerk](upload-index-videos.md) gebruiken om prioriteit te geven aan taken van tenants.

## <a name="azure-subscription-per-tenant"></a>Azure-abonnement per tenant 

Wanneer u deze architectuur gebruikt, heeft elke tenant zijn eigen Azure-abonnement. Voor elke gebruiker maakt u een nieuw Video Indexer-account in het tenantabonnement.

![Azure-abonnement per tenant](./media/manage-multiple-tenants/azure-subscription-per-tenant.png)

### <a name="considerations"></a>Overwegingen

* Dit is de enige optie die factureringsscheiding mogelijk maakt.
* Deze integratie heeft meer beheeroverhead dan Video Indexer-account per tenant. Als facturering geen vereiste is, wordt aanbevolen om een van de andere opties in dit artikel te gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Overzicht](video-indexer-overview.md)
