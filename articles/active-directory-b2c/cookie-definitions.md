---
title: Cookiedefinities
titleSuffix: Azure AD B2C
description: Bevat definities voor de cookies die worden gebruikt in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189511"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Cookies definities voor Azure AD B2C

In de volgende secties vindt u informatie over de cookies die worden gebruikt in Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

De Microsoft Azure AD B2C-service is compatibel met `SameSite=None` SameSite-browserconfiguraties, inclusief ondersteuning voor het `Secure` kenmerk.

Om de toegang tot sites te waarborgen, introduceren webbrowsers een nieuw secure-by-default-model dat ervan uitgaat dat alle cookies moeten worden beschermd tegen externe toegang, tenzij anders is opgegeven. De Chrome-browser is de eerste die deze wijziging implementeert, te beginnen met [Chrome 80 in februari 2020.](https://www.chromium.org/updates/same-site) Zie [Ontwikkelaars: Klaar voor nieuwe SameSite=None voor meer informatie over de voorbereiding op de wijziging in Chrome. Beveiligde cookie-instellingen](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) op de Chromium Blog.

Ontwikkelaars moeten de nieuwe cookie-instelling `SameSite=None`gebruiken om cookies aan te wijzen voor toegang tot verschillende locaties. Wanneer `SameSite=None` het kenmerk aanwezig `Secure` is, moet een extra attribuut worden gebruikt, zodat cross-site cookies alleen toegankelijk zijn via HTTPS-verbindingen. Valideer en test al uw toepassingen, inclusief de toepassingen die Azure AD B2C gebruiken.

Zie voor meer informatie:

* [SameSite-cookiewijzigingen in Chrome-browser verwerken](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Effect op websites van klanten en Microsoft-services en -producten in Chrome-versie 80 of hoger](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

In de volgende tabel worden de cookies weergegeven die worden gebruikt in Azure AD B2C.

| Name | Domain | Verloopdatum | Doel |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Einde [browsersessie](session-behavior.md) | Houdt gebruikerslidmaatschapsgegevens vast voor huurders. De tenants waarvan een gebruiker lid is en het lidmaatschapsniveau (beheerder of gebruiker). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Wordt gebruikt om aanvragen door te sturen naar de juiste productie-instantie. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Wordt gebruikt voor het bijhouden van de transacties (aantal verificatieaanvragen voor Azure AD B2C) en de huidige transactie. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Wordt gebruikt voor het onderhouden van de SSO-sessie. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md), succesvolle verificatie | Wordt gebruikt voor het onderhouden van de aanvraagstatus. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Cross-Site Request Forgery-token dat wordt gebruikt voor CRSF-bescherming. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Wordt gebruikt voor Azure AD B2C-netwerkroutering. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Context |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Wordt gebruikt voor het opslaan van lidmaatschapsgegevens voor de tenant van de resourceprovider. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, branded domein | Einde [browsersessie](session-behavior.md) | Gebruikt voor het opslaan van de relais cookie. |
