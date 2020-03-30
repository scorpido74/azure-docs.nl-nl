---
title: bestand opnemen
description: bestand opnemen voor bestemmingspagina's voor vertrouwelijke clientscenario's (daemon, web-app, web-API)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773394"
---
## <a name="register-secrets-or-certificates"></a>Geheimen of certificaten registreren

Zoals voor elke vertrouwelijke client aanvraag, moet u een geheim of certificaat te registreren. U uw toepassingsgeheimen registreren via de interactieve ervaring in de [Azure-portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) of met behulp van command-line-hulpprogramma's (zoals PowerShell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Clientgeheimen registreren met behulp van de applicatieregistratieportal

Het beheer van clientreferenties gebeurt op de pagina **Certificaten & geheimen** voor een toepassing:

![Pagina Certificaten & geheimen](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Het toepassingsgeheim (ook wel het clientgeheim genoemd) wordt gegenereerd door Azure AD tijdens de registratie van de vertrouwelijke clienttoepassing. Deze generatie gebeurt wanneer u **Nieuw klantgeheim**selecteert. Op dat moment moet u de geheime tekenreeks naar het klembord kopiëren voor gebruik in uw app, voordat u **Opslaan**selecteert. Deze string zal niet langer worden gepresenteerd.
- Tijdens de registratie van de aanvraag gebruikt u de knop **Certificaat uploaden** om het certificaat te uploaden. Azure AD ondersteunt alleen certificaten die rechtstreeks op de toepassing zijn geregistreerd en geen certificaatketens volgen.

Zie [Snelstart: Een clienttoepassing configureren om toegang te krijgen tot web-API's | Referenties toevoegen aan uw toepassing.](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application)



### <a name="register-client-secrets-by-using-powershell"></a>Klantgeheimen registreren met PowerShell

U uw toepassing ook registreren bij Azure AD met behulp van opdrachtregelgereedschappen. Het voorbeeld [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) laat zien hoe u een toepassingsgeheim of certificaat registreert bij een Azure AD-toepassing:

- Zie [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)voor meer informatie over het registreren van een toepassingsgeheim.
- Zie [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)voor meer informatie over het registreren van een certificaat bij een toepassing.
