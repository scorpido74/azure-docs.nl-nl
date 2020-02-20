---
title: Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory
description: RBAC inschakelen om toegang tot uw Azure-app-configuratie-exemplaar te verlenen
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 18fa1b60b15b7eef96efa8dcc4fbf9cd7c4dc7f7
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472620"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory
Azure-app configuratie ondersteunt het gebruik van Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor app-configuratie-exemplaren.  Met Azure AD kunt u toegangs beheer op basis van rollen (RBAC) gebruiken om machtigingen toe te kennen aan een beveiligings-principal.  Een beveiligingsprincipal kan een gebruiker of een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md)van de toepassing zijn.  Zie voor meer informatie over rollen en roltoewijzingen [wat verschillende rollen zijn](../role-based-access-control/overview.md).

## <a name="overview"></a>Overzicht
Aanvragen van een beveiligingsprincipal (een gebruiker of een toepassing) voor toegang tot een app-configuratie resource moeten worden geautoriseerd.  Met Azure AD is toegang tot een resource een proces dat uit twee stappen bestaat.
1. De identiteit van de beveiligingsprincipal wordt geverifieerd en er wordt een OAuth 2,0-token geretourneerd.  De resource naam voor het aanvragen van een token is `https://login.microsoftonline.com/{tenantID}` waarbij `{tenantID}` overeenkomt met de Tenant-ID van Azure Active Directory waartoe de service-principal behoort.
2. Het token wordt door gegeven als onderdeel van een aanvraag bij de app Configuration-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime.  Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure Functions-app, een Azure-web-app of een Azure-VM, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources.  Zie voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit worden gemaakt naar Azure-app configuratie [verifiëren van toegang tot Azure-app configuratie resources met Azure Active Directory en beheerde identiteiten voor Azure-resources](howto-integrate-azure-managed-service-identity.md).

Voor de autorisatie stap moeten een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure-app-configuratie biedt RBAC-rollen die sets machtigingen voor app-configuratie resources omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die aan de principal worden gegeven. Zie voor meer informatie over RBAC-rollen [Ingebouwde RBAC-rollen voor Azure-app configuratie](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md).

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang tot de configuratie bron van de app wordt beperkt. Een beveiligings-principal van Azure AD kan een gebruiker of een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Ingebouwde RBAC-rollen voor Azure-app configuratie
Azure biedt de volgende ingebouwde RBAC-rollen voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- Azure-app eigenaar van de configuratie gegevens: gebruik deze rol om lees-/schrijftoegang te verlenen aan de configuratie resources van de app.
- Azure-app configuratie gegevens lezer: gebruik deze rol om Lees toegang te verlenen tot de configuratie resources van de app.
- Inzender: gebruik deze rol om beheerders toegang te geven tot de service zonder toegang te verlenen tot de gegevens die zijn opgeslagen in het app-configuratie-exemplaar.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van [beheerde identiteiten](howto-integrate-azure-managed-service-identity.md) voor het beheren van uw app-configuratie service.