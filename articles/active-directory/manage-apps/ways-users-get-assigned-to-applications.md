---
title: Begrijpen hoe gebruikers worden toegewezen aan apps in Azure Active Directory
description: Krijg inzicht in hoe gebruikers worden toegewezen aan een app die Azure Active Directory voor identiteits beheer gebruikt.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: 411e9a6e059075dfc2928ed09c0c604d600fd9be
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604152"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>Begrijpen hoe gebruikers worden toegewezen aan apps in Azure Active Directory
Dit artikel helpt u te begrijpen hoe gebruikers aan een toepassing in uw Tenant worden toegewezen.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hoe krijgen gebruikers toegewezen aan een toepassing in azure AD?
Een gebruiker kan alleen toegang krijgen tot een toepassing als deze eerst op een of andere manier aan het programma worden toegewezen. De toewijzing kan worden uitgevoerd door een beheerder, een bedrijfs gemachtigde of soms ook de gebruiker zelf. Hieronder worden de manieren beschreven waarop gebruikers kunnen worden toegewezen aan toepassingen:

*  Een beheerder [wijst een gebruiker](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) rechtstreeks aan de toepassing toe
*  Een beheerder [wijst een groep toe](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) die de gebruiker lid is van de toepassing, met inbegrip van:
    * Een groep die is gesynchroniseerd vanuit on-premises
    * Een statische beveiligings groep die in de Cloud is gemaakt
    * Een [dynamische beveiligings groep](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) die in de Cloud is gemaakt
    * Een Microsoft 365 groep die in de Cloud is gemaakt
    * De groep [alle gebruikers](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)
*  Een beheerder maakt [toegang van selfservice toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) mogelijk om een gebruiker toe te staan een toepassing toe te voegen met [mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **app** -functie toevoegen **zonder zakelijke goed keuring**
*  Een beheerder maakt [toegang van selfservice toepassingen](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) mogelijk om een gebruiker toe te staan een toepassing toe te voegen met behulp van [mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **app** -functie toevoegen, maar alleen **met voorafgaande goed keuring vanuit een geselecteerde set van zakelijke goed keurders**
*  Een beheerder kan [self-service groeps beheer](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) toestaan een gebruiker toe te voegen aan een groep waaraan een toepassing **zonder zakelijke goed keuring** is toegewezen.
*  Een beheerder kan [self-service groeps beheer](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) toestaan om een gebruiker toe te voegen aan een groep waaraan een toepassing is toegewezen, maar alleen **met voorafgaande goed keuring vanuit een geselecteerde set van goed keurders van het bedrijf**
*  Een beheerder wijst rechtstreeks een licentie toe aan een gebruiker voor een toepassing in de eerste partij, zoals [Microsoft 365](https://products.office.com/)
*  Een beheerder wijst een licentie toe aan een groep waarvan de gebruiker lid is van een toepassing voor de eerste partij, zoals [Microsoft 365](https://products.office.com/)
*  Een [beheerder wordt gestemd op een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) die door alle gebruikers moet worden gebruikt en vervolgens meldt een gebruiker zich aan bij de toepassing
* Een gebruiker wordt door gegeven aan [een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) zelf door zich aan te melden bij de toepassing

## <a name="next-steps"></a>Volgende stappen
* [Quickstartreeks over toepassingsbeheer](view-applications-portal.md)
* [Wat is toepassingsbeheer?](what-is-application-management.md)
* [Wat is eenmalige aanmelding?](what-is-single-sign-on.md)
