---
title: Versies van Java script en pagina-indeling
titleSuffix: Azure AD B2C
description: Meer informatie over het inschakelen van Java script en het gebruik van pagina-indelings versies in Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 92925dc32d7f26b224811aa852112a275f112b35
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950830"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Java script-en pagina-indelings versies in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C biedt een set verpakte inhoud die HTML, CSS en Java script bevat voor de elementen van de gebruikers interface in uw gebruikers stromen en aangepaste beleids regels. Als u Java script wilt inschakelen voor uw toepassingen, moet u een-element aan uw [aangepaste beleid](active-directory-b2c-overview-custom.md) toevoegen of in de portal inschakelen voor gebruikers stromen, een pagina-indeling selecteren en [b2clogin.com](b2clogin.md) in uw aanvragen gebruiken.

Als u van plan bent om [Java script](javascript-samples.md) -client code in te scha kelen, wilt u er zeker van zijn dat de elementen waarmee u uw Java script baseert, onveranderbaar zijn. Als dat niet het geval is, kan dit leiden tot onverwacht gedrag op de pagina's van uw gebruikers. Als u deze problemen wilt voor komen, kunt u het gebruik van een pagina-indeling afdwingen en een versie van de pagina-indeling opgeven. Dit zorgt ervoor dat alle inhouds definities die u op uw Java script hebt gebaseerd, onveranderbaar zijn. Zelfs als u niet van plan bent java script in te scha kelen, kunt u een versie van de pagina-indeling opgeven voor uw pagina's.

## <a name="user-flows"></a>Gebruikersstromen

In de **Eigenschappen**van de gebruikers stroom kunt u Java script inschakelen, waardoor ook het gebruik van een pagina-indeling afdwingt. U kunt vervolgens de versie van de pagina-indeling instellen voor de gebruikers stroom zoals beschreven in de volgende sectie.

![Eigenschappen pagina gebruikers stroom met de instelling Java script inschakelen gemarkeerd](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>Een versie van een pagina-indeling selecteren

Ongeacht of u Java script in de eigenschappen van de gebruikers stroom inschakelt, kunt u een versie van de pagina-indeling opgeven voor de pagina's van de gebruikers stroom. Open de gebruikers stroom en selecteer **pagina-indelingen**. Selecteer onder **indelings naam**een pagina gebruikers stroom en kies de **versie**van de pagina-indeling.

Voor informatie over de verschillende versies van de pagina-indeling raadpleegt u het [logboek van versie wijzigingen](page-layout.md#version-change-log).

![Instellingen voor pagina-indeling in portal met de vervolg keuzelijst versie van pagina-indeling](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>Aangepast beleid

Als u Java script in een aangepast beleid wilt inschakelen, voegt u het **ScriptExecution** -element toe aan het **RelyingParty** -element in uw aangepaste beleids bestand. Zie [Java script-voor beelden voor gebruik in azure Active Directory B2C](javascript-samples.md)voor meer informatie.

Ongeacht of u Java script in uw aangepaste beleids regels inschakelt, kunt u een versie van de pagina-indeling voor uw pagina's opgeven. Zie [een pagina-indeling selecteren in azure Active Directory B2C een aangepast beleid gebruiken](page-layout.md)voor meer informatie over het opgeven van een pagina-indeling.

## <a name="next-steps"></a>Volgende stappen

Voor informatie over de verschillende versies van de pagina-indeling raadpleegt u de sectie **versie wijzigings logboek** van [een pagina-indeling selecteren in azure Active Directory B2C aangepaste beleids regels gebruiken](page-layout.md#version-change-log).

Voor beelden van Java script-gebruik in [Java script-voor beelden vindt u in azure Active Directory B2C](javascript-samples.md).
