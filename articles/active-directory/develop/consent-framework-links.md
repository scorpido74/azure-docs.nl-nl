---
title: Hoe toepassingstoestemming werkt
description: Meer informatie over hoe het Azure AD toestemmings raamwerk werkt om te zien hoe u deze kunt gebruiken bij het ontwikkelen van toepassingen in azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.openlocfilehash: 15270a998aff174c04acf2969d984eb022852635
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117359"
---
# <a name="how-application-consent-works"></a>Hoe toepassingstoestemming werkt

In dit artikel vindt u meer informatie over de werking van het Azure AD instemming-Framework, zodat u toepassingen effectiever kunt ontwikkelen.

## <a name="recommended-documents"></a>Aanbevolen documenten

- Bekijk algemene informatie [over hoe toestemming een resource-eigenaar kan de toegang tot bronnen van een toepassing te bepalen](./developer-glossary.md#consent).
- Een stapsgewijze overzicht van [de manier waarop het Azure AD toestemmings raamwerk toestemming implementeert](./quickstart-register-app.md).
- Meer informatie over [hoe een multi tenant-toepassing het toestemming raamwerk kan gebruiken voor het](./howto-convert-app-to-be-multi-tenant.md) implementeren van de toestemming ' gebruiker ' en ' beheerder ', zodat meer geavanceerde toepassings patronen met meerdere lagen worden ondersteund.
- Meer informatie over [hoe toestemming wordt ondersteund op de OAuth 2,0-protocol laag tijdens de autorisatie code toekenning stroom.](../azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code)

## <a name="next-steps"></a>Volgende stappen
[AzureAD stack overflow](https://stackoverflow.com/questions/tagged/azure-active-directory)