---
title: Gebruikers toewijzen aan toepassingen | Microsoft Docs
description: Inzicht krijgen in hoe gebruikers worden toegewezen aan een toepassing in uw Tenant
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45124862fffa3a1ef6f601733407fbbea4eb5e74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84762952"
---
# <a name="how-to-assign-users-to-applications"></a>Gebruikers toewijzen aan toepassingen

Dit artikel helpt u te begrijpen hoe gebruikers aan een toepassing in uw Tenant worden toegewezen.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hoe krijgen gebruikers toegewezen aan een toepassing in azure AD?

Een gebruiker kan alleen toegang krijgen tot een toepassing als deze eerst op een of andere manier aan het programma worden toegewezen. De toewijzing kan worden uitgevoerd door een beheerder, een bedrijfs gemachtigde of soms ook de gebruiker zelf. Hieronder worden de manieren beschreven waarop gebruikers kunnen worden toegewezen aan toepassingen:

1.  Een beheerder [wijst een gebruiker](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) rechtstreeks aan de toepassing toe

2.  Een beheerder [wijst een groep toe](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) die de gebruiker lid is van de toepassing, met inbegrip van:

    * Een groep die is gesynchroniseerd vanuit on-premises

    * Een statische beveiligings groep die in de Cloud is gemaakt

    * Een [dynamische beveiligings groep](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) die in de Cloud is gemaakt

    * Een Office 365-groep die in de Cloud is gemaakt

    * De groep [alle gebruikers](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Een beheerder maakt [toegang van selfservice toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) mogelijk om een gebruiker toe te staan een toepassing toe te voegen met behulp van het [toegangs venster](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **app toevoegen** **zonder zakelijke goed keuring** .

4.  Een beheerder maakt [toegang van selfservice toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) mogelijk om een gebruiker toe te staan een toepassing toe te voegen met behulp van het [toegangs venster](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **app toevoegen** van de toepassing, maar alleen**de voorafgaande goed keuring van een geselecteerde set van zakelijke goed keurders**

5.  Een beheerder kan [self-service groeps beheer](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) toestaan een gebruiker toe te voegen aan een groep waaraan een toepassing **zonder zakelijke goed keuring** is toegewezen.

6.  Een beheerder kan [self-service groeps beheer](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) toestaan om een gebruiker toe te voegen aan een groep waaraan een toepassing is toegewezen, maar alleen **met voorafgaande goed keuring vanuit een geselecteerde set van goed keurders van het bedrijf**

7.  Een beheerder wijst rechtstreeks een licentie toe aan een gebruiker voor een toepassing in de eerste partij, zoals [Microsoft Office 365](https://products.office.com/)

8.  Een beheerder wijst een licentie toe aan een groep waarvan de gebruiker lid is van een toepassing voor de eerste partij, zoals [Microsoft Office 365](https://products.office.com/)

9.  Een [beheerder wordt gestemd op een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) die door alle gebruikers moet worden gebruikt en vervolgens meldt een gebruiker zich aan bij de toepassing

10. Een gebruiker wordt door gegeven aan [een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) zelf door zich aan te melden bij de toepassing

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
