---
title: Veelvoorkomende scenario's in beheer van rechten - Azure AD
description: Lees de stappen op hoog niveau die u moet volgen voor veelvoorkomende scenario's in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9d259c6e2a6ac9ced5f9a1c29d4aec08010f4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190548"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Veelvoorkomende scenario's in Azure AD-rechtenbeheer

Er zijn verschillende manieren waarop u het rechtbeheer voor uw organisatie configureren. Als u echter nog maar net begint, is het handig om de algemene scenario's te begrijpen voor beheerders, cataloguseigenaren, toegangspakketbeheerders, fiatteurs en aanvragers.

## <a name="delegate"></a>Delegeren

### <a name="administrator-delegate-management-of-resources"></a>Beheerder: beheer van resources delegeren

1. [Bekijk de video: Delegatie van IT naar afdelingsmanager](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [Gebruikers delegeren aan de rol voor maker van de catalogus](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>Maker van de catalogus: beheer van resources delegeren

- [Een nieuwe catalogus maken](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>Cataloguseigenaar: beheer van resources delegeren

1. [Mede-eigenaren toevoegen aan de catalogus](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [Bronnen toevoegen aan de catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>Cataloguseigenaar: Beheer van toegangspakketten delegeren

1. [Bekijk de video: Delegatie van cataloguseigenaar naar toegang tot pakketbeheer](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [Gebruikers delegeren om toegang te krijgen tot de rol package manager](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>Toegang regelen voor gebruikers in uw organisatie

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>Access package manager: geef medewerkers in uw organisatie de mogelijkheid om toegang te vragen tot bronnen

1. [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md#start-new-access-package)
1. [Groepen, Teams, toepassingen of SharePoint-sites toevoegen om toegang te krijgen tot pakket](entitlement-management-access-package-create.md#resource-roles)
1. [Een aanvraagbeleid toevoegen zodat gebruikers in uw directory toegang kunnen aanvragen](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [Verloopinstellingen opgeven](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>Aanvrager: toegang tot bronnen aanvragen

1. [Aanmelden bij de My Access-portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Toegangspakket zoeken
1. [Toegang aanvragen](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Goedkeurder: aanvragen voor resources goedkeuren

1. [Aanvraag openen in Mijn Access-portal](entitlement-management-request-approve.md#open-request)
1. [Toegangsaanvraag goedkeuren of weigeren](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>Verzoeker: bekijk de bronnen waartoe u al toegang hebt

1. [Aanmelden bij de My Access-portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Actieve toegangspakketten weergeven

## <a name="govern-access-for-users-outside-your-organization"></a>Toegang regelen voor gebruikers buiten uw organisatie

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>Beheerder: Samenwerken met een externe partnerorganisatie

1. [Lees hoe toegang werkt voor externe gebruikers](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [Instellingen voor externe gebruikers controleren](entitlement-management-external-users.md#settings-for-external-users)
1. [Een verbinding toevoegen aan de externe organisatie](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>Access package manager: Samenwerken met een externe partnerorganisatie

1. [Een nieuw toegangspakket maken](entitlement-management-access-package-create.md#start-new-access-package)
1. [Groepen, Teams, toepassingen of SharePoint-sites toevoegen om toegang te krijgen tot pakket](entitlement-management-access-package-resources.md#add-resource-roles)
1. [Een aanvraagbeleid toevoegen zodat gebruikers die niet in uw map zijn om toegang te vragen, toegang kunnen aanvragen](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [Verloopinstellingen opgeven](entitlement-management-access-package-create.md#lifecycle)
1. [De koppeling kopiëren om het toegangspakket aan te vragen](entitlement-management-access-package-settings.md)
1. Stuur de link naar uw externe partner voor contactpersonen om te delen met hun gebruikers

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>Aanvrager: toegang tot bronnen aanvragen als externe gebruiker

1. Zoek de koppeling naar het toegangspakket dat u van uw contactpersoon hebt ontvangen
1. [Aanmelden bij de My Access-portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [Toegang aanvragen](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>Goedkeurder: aanvragen voor resources goedkeuren

1. [Aanvraag openen in Mijn Access-portal](entitlement-management-request-approve.md#open-request)
1. [Toegangsaanvraag goedkeuren of weigeren](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>Verzoeker: bekijk de bronnen waartoe u al toegang hebt

1. [Aanmelden bij de My Access-portal](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. Actieve toegangspakketten weergeven

## <a name="day-to-day-management"></a>Dagelijks beheer

### <a name="access-package-manager-update-the-resources-for-a-project"></a>Access-pakketbeheer: de bronnen voor een project bijwerken

1. [Bekijk de video: Dagelijks beheer: dingen zijn veranderd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Het toegangspakket openen
1. [Groepen, Teams, toepassingen of SharePoint-sites toevoegen of verwijderen](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>Access package manager: de duur van een project bijwerken

1. [Bekijk de video: Dagelijks beheer: dingen zijn veranderd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. Het toegangspakket openen
1. [De levenscyclusinstellingen openen](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [De vervaldatums bijwerken](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>Access package manager: update hoe toegang wordt goedgekeurd voor een project

1. [Bekijk de video: Dagelijks beheer: dingen zijn veranderd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Een bestaand beleid van aanvraag- en goedkeuringsinstellingen openen](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [De goedkeuringsinstellingen bijwerken](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>Access package manager: de personen voor een project bijwerken

1. [Bekijk de video: Dagelijks beheer: dingen zijn veranderd](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [Gebruikers verwijderen die geen toegang meer nodig hebben](entitlement-management-access-package-assignments.md)
1. [Een bestaand beleid van aanvraag- en goedkeuringsinstellingen openen](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [Gebruikers toevoegen die toegang nodig hebben](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>Access package manager: Direct specifieke gebruikers toewijzen aan een toegangspakket

1. [Als gebruikers verschillende levenscyclusinstellingen nodig hebben, voegt u een nieuw beleid toe aan het toegangspakket](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [Specifieke gebruikers rechtstreeks toewijzen aan het toegangspakket](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>Opdrachten en rapporten

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>Beheerder: bekijken wie toewijzingen heeft voor een toegangspakket

1. Een toegangspakket openen
1. [Opdrachten weergeven](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)
1. [Archiefrapporten en logboeken](entitlement-management-logs-and-reporting.md)

### <a name="administrator-view-resources-assigned-to-users"></a>Beheerder: resources weergeven die zijn toegewezen aan gebruikers

1. [Toegangspakketten voor een gebruiker weergeven](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [Resourcetoewijzingen voor een gebruiker weergeven](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>Programmatisch beheer

U ook toegangspakketten, catalogi, beleidsregels, aanvragen en toewijzingen beheren met Microsoft Graph.  Een gebruiker in een geschikte rol met `EntitlementManagement.ReadWrite.All` een toepassing met de gedelegeerde machtiging kan de [API voor het beheer van rechten](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)aanroepen.

## <a name="next-steps"></a>Volgende stappen

- [Delegering en rollen](entitlement-management-delegate.md)
- [Proces aanvragen en e-mailmeldingen](entitlement-management-process.md)
