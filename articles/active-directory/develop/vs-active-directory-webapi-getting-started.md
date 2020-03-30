---
title: Aan de slag met Azure AD in WebApi-projecten van Visual Studio
description: Azure Active Directory gebruiken in WebApi-projecten nadat u verbinding hebt gemaakt met of een Azure AD hebt gemaakt met behulp van visual studio-verbonden services
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77159451"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Aan de slag met Azure Active Directory (WebApi-projecten)

> [!div class="op_single_selector"]
> - [Aan de slag](vs-active-directory-webapi-getting-started.md)
> - [Wat is er gebeurd](vs-active-directory-webapi-what-happened.md)

Dit artikel biedt extra richtlijnen nadat u Active Directory hebt toegevoegd aan een ASP.NET WebAPI-project via de opdracht **Project > Connected Services** van Visual Studio. Als u de service nog niet aan uw project hebt toegevoegd, u dit op elk gewenst moment doen.

Zie [Wat is er gebeurd met mijn WebAPI-project?](vs-active-directory-webapi-what-happened.md) voor de wijzigingen die zijn aangebracht in uw project bij het toevoegen van de verbonden service.

## <a name="requiring-authentication-to-access-controllers"></a>Verificatie vereisen voor toegang tot controllers

Alle controllers in uw project `[Authorize]` waren versierd met het attribuut. Dit kenmerk vereist dat de gebruiker wordt geverifieerd voordat hij toegang krijgt tot de API's die door deze controllers zijn gedefinieerd. Als u wilt dat de controller anoniem wordt geopend, verwijdert u dit kenmerk van de controller. Als u de machtigingen op een gedetailleerder niveau wilt instellen, past u het kenmerk toe op elke methode waarvoor autorisatie vereist is in plaats van deze toe te passen op de klasse controller.

## <a name="next-steps"></a>Volgende stappen

- [Verificatiescenario's voor Azure Active Directory](authentication-scenarios.md)
- [Aanmelding met Microsoft toevoegen aan een ASP.NET-web-app](quickstart-v2-aspnet-webapp.md)
