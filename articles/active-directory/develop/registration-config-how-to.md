---
title: Eindpunten configureren
description: De verificatie-eind punten zoeken voor een aangepaste toepassing die u ontwikkelt of registreert met Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ms.openlocfilehash: 043dc4f6e57620f58a1cf5f76db755703421800f
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778733"
---
# <a name="how-to-configure-endpoints"></a>Eind punten configureren

U kunt de verificatie-eind punten voor uw toepassing vinden in de [Azure Portal](https://portal.azure.com).

-   Navigeer naar het [Azure Portal](https://portal.azure.com).

-   Klik in het navigatie deel venster links op **Azure Active Directory**.

-   Klik op **app-registraties** en kies **eind punten**.

-   Hiermee opent u de pagina **eind punten** , die alle verificatie-eind punten voor uw Tenant vermeldt.

-   Gebruik het endpoint dat specifiek is voor het verificatie protocol dat u gebruikt, in combi natie met de toepassings-ID om de verificatie aanvraag te maken die specifiek is voor uw toepassing.

**Nationale Clouds** (bijvoorbeeld Azure AD China, Duitsland en de Amerikaanse overheid) hebben hun eigen app-registratie Portal en Azure AD-verificatie-eind punten. Meer informatie vindt u in het [overzicht van nationale Clouds](authentication-national-cloud.md).

## <a name="next-steps"></a>Volgende stappen
[Ontwikkelaarshandleiding voor Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
