---
title: Toegang tot Azure App-configuratie autoriseren met Azure Active Directory
description: RBAC inschakelen om de toegang tot uw Azure App-configuratie-exemplaar te autoriseren
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472620"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Toegang tot Azure App-configuratie autoriseren met Azure Active Directory
Azure App Configuration ondersteunt het gebruik van Azure Active Directory (Azure AD) om aanvragen voor app-configuratie-instanties te autoriseren.  Met Azure AD u RBAC (Role-based access control) gebruiken om machtigingen te verlenen aan een beveiligingsprincipal.  Een beveiligingsprincipal kan een gebruiker zijn of een [hoofdvan de toepassingsservice](../active-directory/develop/app-objects-and-service-principals.md).  Zie [Verschillende rollen begrijpen](../role-based-access-control/overview.md)voor meer informatie over rollen en roltoewijzingen.

## <a name="overview"></a>Overzicht
Verzoeken van security principal (een gebruiker of een toepassing) om toegang te krijgen tot een app-configuratiebron moeten zijn geautoriseerd.  Met Azure AD is toegang tot een bron een proces in twee stappen.
1. De identiteit van de beveiligingsprincipal wordt geverifieerd en een OAuth 2.0-token wordt geretourneerd.  De resourcenaam die een `https://login.microsoftonline.com/{tenantID}` `{tenantID}` token moet aanvragen, is de plaats waar de Azure Active Directory-tenant-id waartoe de serviceprincipal behoort, overeenkomt.
2. Het token wordt doorgegeven als onderdeel van een verzoek aan de app-configuratieservice om de toegang tot de opgegeven bron te autoriseren.

De verificatiestap vereist dat een toepassingsaanvraag een OAuth 2.0-toegangstoken bevat tijdens runtime.  Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure Functions-app, een Azure Web App of een Azure VM, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de bronnen.  Zie [Toegang tot Azure App Configuration resources verifiëren met Azure Active Directory en beheerde identiteiten voor Azure Resources](howto-integrate-azure-managed-service-identity.md)voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit zijn gedaan naar Azure App-configuratie.

De autorisatiestap vereist dat een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure App Configuration biedt RBAC-rollen die sets machtigingen voor app-configuratiebronnen omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die aan de principal worden verstrekt. Zie [Ingebouwde RBAC-rollen voor Azure App-configuratie voor](#built-in-rbac-roles-for-azure-app-configuration)meer informatie over RBAC-rollen. 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangsrechten
Azure Active Directory (Azure AD) geeft toegangsrechten voor beveiligde bronnen door middel [van rbac (role-based access control).](../role-based-access-control/overview.md)

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligingsprincipal, verleent Azure toegang tot deze bronnen voor die beveiligingsprincipal. De toegang is beperkt tot de bron App-configuratie. Een Azure AD-beveiligingsprincipal kan een gebruiker of een hoofdvan de toepassingsservice of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Ingebouwde RBAC-rollen voor Azure App-configuratie
Azure biedt de volgende ingebouwde RBAC-rollen voor het toestaan van toegang tot app-configuratiegegevens met Azure AD en OAuth:

- Azure App Configuration Data Owner: Gebruik deze rol om lees-/schrijftoegang te geven aan app-configuratiebronnen.
- Azure App Configuration Data Reader: Gebruik deze rol om leestoegang te geven tot app-configuratiebronnen.
- Bijdrager: gebruik deze rol om de beheerder toegang te geven tot de service zonder toegang te verlenen tot de gegevens die zijn opgeslagen in het exemplaar App-configuratie.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van [beheerde identiteiten](howto-integrate-azure-managed-service-identity.md) voor het beheer van uw app-configuratieservice.