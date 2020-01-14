---
title: Cookie definities-Azure Active Directory B2C | Microsoft Docs
description: Bevat definities voor de cookies die in Azure Active Directory B2C worden gebruikt.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930893"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Cookie definities voor Azure Active Directory B2C

De volgende tabel geeft een lijst van de cookies die worden gebruikt in Azure Active Directory B2C.

| Name | Domain | Verlooptijd | Doel |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Einde van [browser sessie](session-behavior.md) | Bevat gegevens van het lidmaatschap van gebruikers via tenants. De tenants een gebruiker is lid van en het niveau van lidmaatschap (beheerder of gebruiker). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het routeren van aanvragen naar het geschikte productie-exemplaar. |
| x-MS-CPIM-trans actie | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het bijhouden van de trans acties (aantal authenticatie aanvragen naar Azure AD B2C) en de huidige trans actie. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het onderhouden van de SSO-sessie. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md), geslaagde verificatie | Wordt gebruikt voor het onderhouden van de status van de aanvraag. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Token voor aanvraag vervalsing op meerdere sites gebruikt voor CRSF-beveiliging. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor Azure AD B2C netwerk routering. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Context |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Gebruikt voor het opslaan van lidmaatschaps gegevens voor de resource provider Tenant. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, brandend domein | Einde van [browser sessie](session-behavior.md) | Wordt gebruikt voor het opslaan van de relay-cookie. |
