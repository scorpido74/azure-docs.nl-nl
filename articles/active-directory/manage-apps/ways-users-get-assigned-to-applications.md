---
title: Gebruikers toewijzen aan toepassingen | Microsoft Documenten
description: Begrijpen hoe gebruikers worden toegewezen aan een toepassing in uw tenant
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b818fe1d8b6bbc9d2d8c5b460b4d71dccdd39366
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825975"
---
# <a name="how-to-assign-users-to-applications"></a>Gebruikers toewijzen aan toepassingen

In dit artikel u begrijpen hoe gebruikers worden toegewezen aan een toepassing in uw tenant.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hoe worden gebruikers toegewezen aan een toepassing in Azure AD?

Als een gebruiker toegang heeft tot een toepassing, moet deze eerst op de een of andere manier worden toegewezen. Toewijzing kan worden uitgevoerd door een beheerder, een zakelijke gemachtigde of soms de gebruiker zelf. Hieronder worden de manieren beschreven waarop gebruikers kunnen worden toegewezen aan toepassingen:

1.  Een beheerder [wijst een gebruiker](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) rechtstreeks aan de toepassing toe

2.  Een beheerder [wijst een groep](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) waarvan de gebruiker lid is van de toepassing toe, waaronder:

    * Een groep die is gesynchroniseerd vanuit on-premises

    * Een statische beveiligingsgroep die is gemaakt in de cloud

    * Een [dynamische beveiligingsgroep](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal) die is gemaakt in de cloud

    * Een Office 365-groep die in de cloud is gemaakt

    * De groep [Alle gebruikers](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-dedicated-groups)

3.  Een beheerder stelt [Selfservice-toepassingstoegang](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) in om een gebruiker toe te staan een toepassing toe te voegen met de functie **App toevoegen van** het [toepassingstoegangspaneel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) **zonder zakelijke goedkeuring**

4.  Een beheerder stelt [Selfservice Application Access](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) in om een gebruiker toe te staan een toepassing toe te voegen met de functie **App toevoegen van** het [Toepassingstoegangspaneel,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) maar alleen met**voorafgaande goedkeuring van een geselecteerde set zakelijke fiatteurs**

5.  Een beheerder stelt [Selfservice Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) in staat om een gebruiker toe te staan lid te worden van een groep waaraan een toepassing is toegewezen **zonder bedrijfsgoedkeuring**

6.  Een beheerder stelt [Selfservice Group Management](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) in staat om een gebruiker toe te staan lid te worden van een groep waaraan een toepassing is toegewezen, maar alleen **met voorafgaande goedkeuring van een geselecteerde set zakelijke fiatteurs**

7.  Een beheerder wijst een licentie rechtstreeks toe aan een gebruiker voor een first party-toepassing, zoals [Microsoft Office 365](https://products.office.com/)

8.  Een beheerder wijst een licentie toe aan een groep waarvan de gebruiker lid is van een first party-toepassing, zoals [Microsoft Office 365](https://products.office.com/)

9.  Een [beheerder stemt in met een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) die door alle gebruikers moet worden gebruikt en vervolgens een gebruiker zich aanmeldt bij de toepassing

10. Een gebruiker stemt zelf [in met een toepassing](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview) door zich aan te melden bij de toepassing

## <a name="next-steps"></a>Volgende stappen
[Toepassingen beheren met Azure Active Directory](what-is-application-management.md)
