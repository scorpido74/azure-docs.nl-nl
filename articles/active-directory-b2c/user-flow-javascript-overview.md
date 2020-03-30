---
title: JavaScript- en pagina-indelingsversies
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van JavaScript en het gebruik van pagina-indelingsversies in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 23d345ea9f22be5c4dac20e6e8784a8de079bccb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185830"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>JavaScript- en pagina-indelingsversies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C biedt een set verpakte inhoud die HTML, CSS en JavaScript bevat voor de gebruikersinterface-elementen in uw gebruikersstromen en aangepaste beleidsregels.

Ga als u JavaScript inschakelen voor uw toepassingen:

* Deze inschakelen op de gebruikersstroom met behulp van de Azure-portal
* Een [pagina-indeling selecteren](page-layout.md)
* Gebruik [b2clogin.com](b2clogin.md) in uw verzoeken

Als u [JavaScript-clientcode](javascript-samples.md) wilt inschakelen, moeten de elementen waarop u uw JavaScript baseert onveranderlijk zijn. Als ze niet onveranderlijk zijn, kunnen wijzigingen onverwacht gedrag veroorzaken op uw gebruikerspagina's. Als u deze problemen wilt voorkomen, dwingt u het gebruik van een pagina-indeling af en geeft u een pagina-indelingsversie op om ervoor te zorgen dat de inhoudsdefinities waarop u uw JavaScript hebt gebaseerd, onveranderlijk zijn. Zelfs als u niet van plan bent JavaScript in te schakelen, u een pagina-indelingsversie voor uw pagina's opgeven.

## <a name="enable-javascript"></a>JavaScript inschakelen

In de **eigenschappen**van de gebruikersstroom u JavaScript inschakelen. Het inschakelen van JavaScript dwingt ook het gebruik van een pagina-indeling af. Vervolgens u de pagina-indelingsversie instellen voor de gebruikersstroom zoals beschreven in de volgende sectie.

![Pagina Eigenschappen van gebruikersstroom met JavaScript-instelling inschakelen gemarkeerd](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Een pagina-indelingsversie selecteren

Of u JavaScript nu wel of niet inschakelt in de eigenschappen van uw gebruikersstroom, u een pagina-indelingsversie opgeven voor uw gebruikersstroompagina's. Open de gebruikersstroom en selecteer **Pagina-indelingen**. Selecteer **onder INDELINGSNAAM**een gebruikersstroompagina en kies de **pagina-indelingsversie**.

Zie het [logboek Pagina-indelingsversie wijzigen](page-layout.md)voor informatie over de verschillende pagina-indelingsversies.

![Instellingen voor pagina-indeling in portal met vervolgkeuzelijst pagina-indelingsversie](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Volgende stappen

U voorbeelden van JavaScript-gebruik vinden in [JavaScript-voorbeelden voor gebruik in Azure Active Directory B2C](javascript-samples.md).
