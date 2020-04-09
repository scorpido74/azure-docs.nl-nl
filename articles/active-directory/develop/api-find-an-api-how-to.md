---
title: Een API zoeken voor een op maat gemaakte app | Azure
description: De machtigingen configureren die u nodig hebt om toegang te krijgen tot een bepaalde API in uw speciaal ontwikkelde Azure AD-toepassing
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ms.openlocfilehash: cd3b21050c6a442284647212fdf7c5707943ffc1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885613"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Een specifieke API vinden die nodig is voor een op maat ontwikkelde toepassing

Toegang tot API's vereist configuratie van toegangsscopen en rollen. Als u de web-API's van uw brontoepassing wilt blootstellen aan clienttoepassingen, moet u toegangsscopen en -rollen voor de API configureren. Als u wilt dat een clienttoepassing toegang krijgt tot een web-API, moet u machtigingen configureren om toegang te krijgen tot de API in de app-registratie.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Een resourcetoepassing configureren voor het beschikbaar maken van web-API's

Wanneer u uw web-API blootstelt, wordt de API weergegeven in de lijst **Een API selecteren** wanneer machtigingen worden toegevoegd aan een app-registratie. Als u toegangsscopen wilt toevoegen, voert u de stappen uit die zijn beschreven in [Een toepassing configureren om web-API's bloot te leggen.](quickstart-configure-app-expose-web-apis.md)

## <a name="configuring-a-client-application-to-access-web-apis"></a>Een clienttoepassing configureren om toegang te krijgen tot web-API's

Wanneer u machtigingen toevoegt aan uw app-registratie, u **API-toegang toevoegen** aan blootgestelde web-API's. Als u toegang wilt krijgen tot web-API's, volgt u de stappen die zijn beschreven in [Een clienttoepassing configureren om toegang te krijgen tot web-API's](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Volgende stappen

- [Het Azure Active Directory-toepassingsmanifest begrijpen](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)
