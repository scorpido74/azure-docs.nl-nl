---
title: Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory
description: RBAC inschakelen om toegang tot uw Azure-app-configuratie-exemplaar te verlenen
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: c2812219e689cb42fd871f85300239a10ab0da0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84116726"
---
# <a name="authorize-access-to-azure-app-configuration-using-azure-active-directory"></a>Toegang tot Azure-app configuratie machtigen met behulp van Azure Active Directory
Naast het gebruik van op hash gebaseerde Message Authentication Code (HMAC), ondersteunt Azure-app configuratie het gebruik van Azure Active Directory (Azure AD) voor het machtigen van aanvragen voor app-configuratie-exemplaren.  Met Azure AD kunt u toegangs beheer op basis van rollen (RBAC) gebruiken om machtigingen toe te kennen aan een beveiligings-principal.  Een beveiligingsprincipal kan een gebruiker, een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) of een [Application Service-Principal](../active-directory/develop/app-objects-and-service-principals.md)zijn.  Zie voor meer informatie over rollen en roltoewijzingen [wat verschillende rollen zijn](../role-based-access-control/overview.md).

## <a name="overview"></a>Overzicht
Aanvragen van een beveiligingsprincipal om toegang te krijgen tot een app-configuratie bron moeten worden geautoriseerd. Met Azure AD is toegang tot een resource een proces in twee stappen:
1. De identiteit van de beveiligingsprincipal wordt geverifieerd en er wordt een OAuth 2,0-token geretourneerd.  De naam van de resource om een token aan te vragen, `https://login.microsoftonline.com/{tenantID}` `{tenantID}` komt overeen met de Azure Active Directory Tenant-ID waartoe de service-principal behoort.
2. Het token wordt door gegeven als onderdeel van een aanvraag bij de app Configuration-service om toegang tot de opgegeven bron te autoriseren.

De verificatie stap vereist dat een toepassings aanvraag een OAuth 2,0-toegangs token bevat tijdens runtime.  Als een toepassing wordt uitgevoerd binnen een Azure-entiteit, zoals een Azure Functions-app, een Azure-web-app of een Azure-VM, kan deze een beheerde identiteit gebruiken om toegang te krijgen tot de resources.  Zie voor meer informatie over het verifiëren van aanvragen die door een beheerde identiteit worden gemaakt naar Azure-app configuratie [verifiëren van toegang tot Azure-app configuratie resources met Azure Active Directory en beheerde identiteiten voor Azure-resources](howto-integrate-azure-managed-service-identity.md).

Voor de autorisatie stap moeten een of meer RBAC-rollen worden toegewezen aan de beveiligingsprincipal. Azure-app-configuratie biedt RBAC-rollen die sets machtigingen voor app-configuratie resources omvatten. De rollen die zijn toegewezen aan een beveiligingsprincipal bepalen de machtigingen die aan de principal worden gegeven. Zie voor meer informatie over RBAC-rollen [Ingebouwde RBAC-rollen voor Azure-app configuratie](#built-in-rbac-roles-for-azure-app-configuration). 

## <a name="assign-rbac-roles-for-access-rights"></a>RBAC-rollen toewijzen voor toegangs rechten
Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [op rollen gebaseerd toegangs beheer (RBAC)](../role-based-access-control/overview.md).

Wanneer een RBAC-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang tot de configuratie bron van de app wordt beperkt. Een beveiligings-principal van Azure AD kan een gebruiker of een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)zijn.

## <a name="built-in-rbac-roles-for-azure-app-configuration"></a>Ingebouwde RBAC-rollen voor Azure-app configuratie
Azure biedt de volgende ingebouwde RBAC-rollen voor het verlenen van toegang tot app-configuratie gegevens met behulp van Azure AD en OAuth:

- **Eigenaar van app-configuratie gegevens**: gebruik deze rol voor het verlenen van toegang voor lezen/schrijven/verwijderen naar app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.
- **Gegevens lezer app-configuratie**: gebruik deze rol om Lees toegang te geven tot app-configuratie gegevens. Hiermee wordt geen toegang verleend tot de bron van de configuratie van de app.
- **Inzender**: gebruik deze rol om de configuratie bron van de app te beheren. Terwijl de app-configuratie gegevens kunnen worden geopend met behulp van toegangs sleutels, verleent deze rol geen directe toegang tot de gegevens met behulp van Azure AD.
- **Lezer**: gebruik deze rol om Lees toegang te verlenen aan de configuratie bron van de app. Hiermee wordt geen toegang verleend tot de toegangs sleutels van de resource en evenmin op de gegevens die zijn opgeslagen in de app-configuratie.

> [!NOTE]
> Op dit moment ondersteunt de Azure Portal en CLI alleen HMAC-verificatie voor toegang tot app-configuratie gegevens. Azure AD-verificatie wordt niet ondersteund. Daarom moeten gebruikers van de Azure Portal en CLI de rol *Inzender* gebruiken om de toegangs sleutels van de configuratie bron van de app op te halen. Het verlenen van de *gegevens lezer* van de app-configuratie of de eigenaar van de *app-configuratie gegevens* heeft geen invloed op de toegang via de portal en cli.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van [beheerde identiteiten](howto-integrate-azure-managed-service-identity.md) voor het beheren van uw app-configuratie service.
