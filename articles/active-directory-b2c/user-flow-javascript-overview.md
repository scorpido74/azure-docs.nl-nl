---
title: Versies van Java script en pagina-indeling
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van Java script en het gebruik van pagina-indelings versies in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: project-no-code, devx-track-js
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 50644ad3be226648accba6a2f43d4ea068ff977c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91258843"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Java script-en pagina-indelings versies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C biedt een set verpakte inhoud die HTML, CSS en Java script bevat voor de elementen van de gebruikers interface in uw gebruikers stromen en aangepaste beleids regels.

Java script inschakelen voor uw toepassingen:

* Schakel deze in op de gebruikers stroom met behulp van de Azure Portal
* Selecteer een [pagina-indeling](page-layout.md)
* [B2clogin.com](b2clogin.md) in uw aanvragen gebruiken

Als u van plan bent om [Java script](javascript-samples.md) -client code in te scha kelen, moeten de elementen waarop u uw Java script baseert, onveranderbaar zijn. Als ze niet onveranderbaar zijn, kunnen wijzigingen op de pagina's van uw gebruikers onverwacht gedrag veroorzaken. Als u deze problemen wilt voor komen, dwingt u het gebruik van een pagina-indeling af en geeft u een versie van de pagina-indeling op om ervoor te zorgen dat de inhouds definities waarop u uw Java script hebt gebaseerd, onveranderbaar Zelfs als u niet van plan bent java script in te scha kelen, kunt u een versie van de pagina-indeling opgeven voor uw pagina's.

## <a name="enable-javascript"></a>JavaScript inschakelen

In de **Eigenschappen**van de gebruikers stroom kunt u Java script inschakelen. Als u Java script inschakelt, wordt ook het gebruik van een pagina-indeling afgedwongen. U kunt vervolgens de versie van de pagina-indeling instellen voor de gebruikers stroom zoals beschreven in de volgende sectie.

![Eigenschappen pagina gebruikers stroom met de instelling Java script inschakelen gemarkeerd](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>Een versie van een pagina-indeling selecteren

Ongeacht of u Java script in de eigenschappen van de gebruikers stroom inschakelt, kunt u een versie van de pagina-indeling opgeven voor de pagina's van de gebruikers stroom. Open de gebruikers stroom en selecteer **pagina-indelingen**. Selecteer onder **indelings naam**een pagina gebruikers stroom en kies de **versie van de pagina-indeling (preview)**.

Zie voor meer informatie over de verschillende versies van de pagina-indeling het [logboek met versie wijzigingen](page-layout.md)voor de pagina.

![Instellingen voor pagina-indeling in portal met de vervolg keuzelijst versie van pagina-indeling](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>Volgende stappen

Voor beelden van Java script-gebruik in [Java script-voor beelden vindt u in azure Active Directory B2C](javascript-samples.md).
