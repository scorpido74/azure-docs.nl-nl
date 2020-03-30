---
title: Extensies-app in Azure Active Directory B2C | Microsoft Documenten
description: De b2c-extensies-app herstellen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 547b625996a65999c32c1b73699e3b408be01de3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188593"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: app voor extensies

Wanneer een Azure AD B2C-map `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` wordt gemaakt, wordt er automatisch een gebelde app gemaakt in de nieuwe map. Deze app, aangeduid als de **b2c-extensies-app,** is zichtbaar in *app-registraties*. Het wordt gebruikt door de Azure AD B2C-service om informatie over gebruikers en aangepaste kenmerken op te slaan. Als de app wordt verwijderd, werkt Azure AD B2C niet goed en wordt uw productieomgeving beïnvloed.

> [!IMPORTANT]
> Verwijder de b2c-extensies-app niet, tenzij u van plan bent uw tenant onmiddellijk te verwijderen. Als de app langer dan 30 dagen wordt verwijderd, gaan gebruikersgegevens permanent verloren.

## <a name="verifying-that-the-extensions-app-is-present"></a>Controleren of de extensies-app aanwezig is

Ga als volgt te werk om te controleren of de b2c-extensies-app aanwezig is:

1. Klik in uw Azure AD B2C-tenant op **Alle services** in het linkernavigatiemenu.
1. **App-registraties**zoeken en openen .
1. Zoek naar een app die begint met **b2c-extensies-app**

## <a name="recover-the-extensions-app"></a>De extensies-app herstellen

Als u per ongeluk de b2c-extensies-app hebt verwijderd, hebt u 30 dagen de tijd om deze te herstellen. U de app herstellen met de Graph API:

1. Blader [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/)naar .
1. Meld u aan bij de site als globale beheerder voor de Azure AD B2C-map waarvoor u de verwijderde app wilt herstellen. Deze globale beheerder moet een e-mailadres `username@{yourTenant}.onmicrosoft.com`hebben dat vergelijkbaar is met het volgende: .
1. Geef een HTTP GET `https://graph.windows.net/myorganization/deletedApplications` uit tegen de URL met api-versie=1.6. Deze bewerking geeft een overzicht van alle toepassingen die in de afgelopen 30 dagen zijn verwijderd.
1. Zoek de toepassing in de lijst waar de naam begint met 'b2c-extension-app' en kopieer de `objectid` eigendomswaarde.
1. Geef een HTTP-BERICHT `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore`uit tegen de URL . Vervang `{OBJECTID}` het gedeelte van `objectid` de URL door het gedeelte van de vorige stap.

U moet nu [de herstelde app](#verifying-that-the-extensions-app-is-present) in de Azure-portal kunnen zien.

> [!NOTE]
> Een toepassing kan alleen worden hersteld als deze in de afgelopen 30 dagen is verwijderd. Als het meer dan 30 dagen is geweest, gaan gegevens permanent verloren. Voor meer hulp dient u een ondersteuningsticket in.
