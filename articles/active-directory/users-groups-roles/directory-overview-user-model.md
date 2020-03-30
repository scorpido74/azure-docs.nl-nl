---
title: Overzicht van gebruikers, groepen, licenties en rollen - Azure AD | Microsoft Documenten
description: De relatie tussen gebruikers en toegewezen licenties, beheerdersrollen, groepslidmaatschap in Azure Active Directory
keywords: ''
author: curtand
manager: daveba
ms.author: curtand
ms.reviewer: vincesm
ms.date: 11/08/2019
ms.topic: overview
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
services: active-directory
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3cc2de5a2f297e8133011905ff2961b44476d6b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046327"
---
# <a name="users-groups-licensing-and-roles-for-large-organizations"></a>Gebruikers, groepen, licentieverlening en rollen voor grote organisaties

In dit artikel maakt de Azure AD-beheerder kennis met de relatie tussen de belangrijkste taken voor [identiteitsbeheer](/azure/active-directory/fundamentals/identity-fundamentals?context=azure/active-directory/users-groups-roles/context/ugr-context) voor gebruikers, wat betreft hun groepen, licenties, geïmplementeerde bedrijfsapps en beheerdersrollen. Naarmate uw organisatie groeit, kunt u Azure AD-groepen en beheerdersrollen gebruik voor het volgende:

* Licenties toewijzen aan groepen in plaats van aan personen
* Machtigingen overdragen om het werk van Azure AD-beheer te delegeren aan rollen met minder bevoegdheden
* Groepen toegang geven tot bedrijfsapps

## <a name="assign-users-to-groups"></a>Gebruikers toewijzen aan groepen

U kunt groepen in Azure AD gebruiken om licenties toe te wijzen aan grote aantallen gebruikers, of om gebruikerstoegang toe te wijzen aan geïmplementeerde bedrijfsapps. U groepen gebruiken om alle beheerdersrollen toe te wijzen, behalve globale beheerder in Azure AD, of u toegang verlenen tot externe bronnen, zoals SaaS-toepassingen of SharePoint-sites.

Voor meer flexibiliteit en om de hoeveelheid werk voor het beheer van groepslidmaatschappen te verminderen, kunt u [dynamische groepen](groups-create-rule.md) in Azure AD gebruiken om groepslidmaatschappen automatisch uit te breiden of te beperken. U hebt een Azure AD Premium P1-licentie nodig voor elke unieke gebruiker die lid is van een of meer dynamische groepen.

## <a name="assign-licenses-to-groups"></a>Licenties toewijzen aan groepen

Het toewijzen of verwijderen van licenties van afzonderlijke gebruikers kan veel tijd en aandacht vragen. Als u in plaats daarvan [licenties toewijst aan groepen](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), kunt u grootschalig licentiebeheer eenvoudiger maken.

Wanneer gebruikers in Azure AD behoren tot een groep die een licentie heeft, krijgen de gebruikers automatisch de juiste licenties toegewezen. Wanneer gebruikers de groep verlaten, worden hun licentietoewijzingen in Azure AD verwijderd. Zonder Azure AD-groepen zou u een PowerShell-script moeten schrijven of Graph API moeten gebruiken om gebruikerslicenties bulksgewijs toe te voegen of te verwijderen voor gebruikers die toetreden tot de organisatie of de organisatie verlaten.

Als er onvoldoende licenties beschikbaar zijn of als er problemen optreden, bijvoorbeeld serviceplannen die niet op hetzelfde moment kunnen worden toegewezen, kunt u de status van alle licentieproblemen voor de groep in Azure Portal bekijken.

>[!NOTE]
>De functie voor groepslicenties is momenteel beschikbaar als openbare preview. Tijdens de preview is de functie beschikbaar in elk betaald Azure AD-licentieplan (Azure Active Directory).

## <a name="delegate-administrator-roles"></a>Beheerdersrollen delegeren

Veel grote organisaties willen opties om hun gebruikers voldoende bevoegdheden te kunnen geven voor hun taken, zonder dat ze deze gebruikers de krachtige rol van globale beheerder moeten toewijzen. Dit geldt bijvoorbeeld voor gebruikers die toepassingen moeten registreren. Hier volgt een voorbeeld van de nieuwe Azure AD-beheerdersrollen waarmee u het werk van toepassingsbeheer nauwkeuriger kunt distribueren:

 Rolnaam | Overzicht van bevoegdheden
 --------- | -------------------
 **Toepassingsbeheerder** | Kan bedrijfstoepassingen en toepassingsregistraties toevoegen en beheren en de proxyinstellingen van een toepassing configureren. Toepassingsbeheerders kunnen beleid en apparaten voor voorwaardelijke toegang weergeven, maar deze niet beheren.
 **Beheerder van de cloudtoepassing** | Kan bedrijfstoepassingen en bedrijfsapp-registraties toevoegen en beheren. Deze rol heeft alle bevoegdheden van de toepassingsbeheerder behalve dat een gebruiker met deze rol geen instellingen van de toepassingsproxy kan beheren.
**Toepassingsontwikkelaar** | Kan toepassingsregistraties toevoegen en beheren maar kan geen bedrijfstoepassingen beheren of de proxyinstellingen van een toepassing configureren.

Er worden meer nieuwe Azure AD-beheerdersrollen toegevoegd. Controleer de Azure Portal of de [naslagwerken over de bevoegdheden van de beheerdersrol](directory-assign-admin-roles.md) voor rollen die momenteel beschikbaar zijn.

## <a name="assign-app-access"></a>App-toegang toewijzen

U kunt Azure AD gebruiken voor het toewijzen van groepstoegang tot de [bedrijfsapps die zijn geïmplementeerd in uw Azure AD-tenant](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context). Als u dynamische groepen combineert met groepstoewijzing voor apps, kunt u de gebruikerstoegang tot apps automatiseren wanneer uw organisatie groeit. U hebt een Azure Active Directory Premium P1- of Premium P2-licentie nodig om toegang tot bedrijfsapps toe te wijzen.

Azure AD geeft u nauwkeurigere controle over de gegevens die tussen de app en de groepen stromen waartoe u toegang verleent. Open in [Bedrijfstoepassingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) een app en selecteer **Inrichten** om het volgende te doen:

* Automatische inrichting instellen voor apps die deze mogelijkheid ondersteunen
* Referenties verstrekken om verbinding te maken met de API voor gebruikersbeheer van de app
* De toewijzingen instellen die regelen welke gebruikerskenmerken er tussen Azure AD en de app stromen wanneer gebruikersaccounts worden ingericht of bijgewerkt
* De Azure AD-inrichtingsservice voor een app starten en stoppen, de inrichtingscache wissen of de service opnieuw starten
* Het **Activiteitenoverzicht voor de inrichting** bekijken dat een logboek bevat van alle gebruikers en groepen die zijn gemaakt, bijgewerkt en verwijderd tussen Azure AD en de app, en het **Foutrapport voor de inrichting** bekijken dat gedetailleerdere foutberichten bevat

## <a name="next-steps"></a>Volgende stappen

Als u een beginnende Azure AD-beheerder bent, vindt u de basisbeginselen in de [Basisprincipes van Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/index).

Of u kunt beginnen met [het maken van groepen](/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context), [toewijzen van licenties](/azure/active-directory/fundamentals/license-users-groups?context=azure/active-directory/users-groups-roles/context/ugr-context), [het toewijzen van app-toegang](/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups?context=azure/active-directory/users-groups-roles/context/ugr-context) of [het toewijzen van beheerdersrollen](directory-assign-admin-roles.md).
