---
title: bestand opnemen
description: bestand insluiten voor vertrouwelijke client scenario landings pagina's (daemon, Web-app, Web-API)
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
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a3acdbb93dd20f0b89e4f99d64f5f7a30ce40623
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102598"
---
## <a name="register-secrets-or-certificates"></a>Geheimen of certificaten registreren

Net als bij elke vertrouwelijke client toepassing moet u een geheim of certificaat registreren. U kunt uw toepassings geheimen registreren via de interactieve ervaring in het [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) of door gebruik te maken van opdracht regel programma's (zoals Power shell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Client geheimen registreren met behulp van de portal voor toepassings registratie

Het beheer van client referenties vindt plaats op de pagina **certificaten & geheimen** voor een toepassing:

![Pagina certificaten & geheimen](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- U maakt een *client geheim* door **Nieuw client geheim** te selecteren in de registratie van de app in de Azure Portal. Wanneer u een client geheim maakt, _moet_ u de teken reeks van het geheim vastleggen voordat u naar het deel venster **certificaten & geheimen** gaat navigeren. De teken reeks van het geheim wordt nooit opnieuw weer gegeven.
- Tijdens de registratie van de toepassing gebruikt u de knop **certificaat uploaden** om het certificaat te uploaden. Azure AD ondersteunt alleen certificaten die rechtstreeks zijn geregistreerd bij de toepassing en die niet voldoen aan de certificaat ketens.

Voor meer informatie raadpleegt [u Quick Start: een client toepassing configureren voor toegang tot Web-api's | Voeg referenties toe aan uw toepassing](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).

### <a name="register-client-secrets-by-using-powershell"></a>Client geheimen registreren met behulp van Power shell

U kunt uw toepassing ook registreren bij Azure AD met behulp van opdracht regel Programma's. Het voor beeld van de [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) laat zien hoe u een toepassings geheim of certificaat kunt registreren bij een Azure AD-toepassing:

- Zie [AppCreationScripts/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)voor meer informatie over het registreren van een toepassings geheim.
- Zie [AppCreationScripts-withCert/Configure.ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)voor meer informatie over het registreren van een certificaat bij een toepassing.
