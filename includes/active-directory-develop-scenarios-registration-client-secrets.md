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
ms.date: 04/18/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: a5d34ac7eea50b67bd679d8cb8ddecf7ca277abd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76773394"
---
## <a name="register-secrets-or-certificates"></a>Geheimen of certificaten registreren

Net als bij elke vertrouwelijke client toepassing moet u een geheim of certificaat registreren. U kunt uw toepassings geheimen registreren via de interactieve ervaring in het [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredAppsPreview) of door gebruik te maken van opdracht regel programma's (zoals Power shell).

### <a name="register-client-secrets-by-using-the-application-registration-portal"></a>Client geheimen registreren met behulp van de portal voor toepassings registratie

Het beheer van client referenties vindt plaats op de pagina **certificaten & geheimen** voor een toepassing:

![Pagina certificaten & geheimen](../articles/active-directory/develop/media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets.png)

- Het toepassings geheim (ook wel het client geheim genoemd) wordt gegenereerd door Azure AD tijdens de registratie van de vertrouwelijke client toepassing. Deze generatie gebeurt wanneer u **Nieuw client geheim**selecteert. Op dat moment moet u de geheime teken reeks naar het klem bord kopiëren voor gebruik in uw app, voordat u **Opslaan**selecteert. Deze teken reeks wordt niet meer weer gegeven.
- Tijdens de registratie van de toepassing gebruikt u de knop **certificaat uploaden** om het certificaat te uploaden. Azure AD ondersteunt alleen certificaten die rechtstreeks zijn geregistreerd bij de toepassing en die niet voldoen aan de certificaat ketens.

Voor meer informatie raadpleegt [u Quick Start: een client toepassing configureren voor toegang tot Web-api's | Voeg referenties toe aan uw toepassing](../articles/active-directory/develop/quickstart-configure-app-access-web-apis.md#add-credentials-to-your-web-application).



### <a name="register-client-secrets-by-using-powershell"></a>Client geheimen registreren met behulp van Power shell

U kunt uw toepassing ook registreren bij Azure AD met behulp van opdracht regel Programma's. Het voor beeld van de [Active-Directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) laat zien hoe u een toepassings geheim of certificaat kunt registreren bij een Azure AD-toepassing:

- Zie [AppCreationScripts/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts/Configure.ps1#L190)voor meer informatie over het registreren van een toepassings geheim.
- Zie [AppCreationScripts-withCert/configure. ps1](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/5199032b352a912e7cc0fce143f81664ba1a8c26/AppCreationScripts-withCert/Configure.ps1#L162-L178)voor meer informatie over het registreren van een certificaat bij een toepassing.
