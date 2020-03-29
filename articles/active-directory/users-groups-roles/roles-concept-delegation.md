---
title: Beheerdersroldelegatie begrijpen - Azure Active Directory | Microsoft Documenten
description: Delegatiemodellen, voorbeelden en rolbeveiliging in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fa3c6bf39dbef601fe64e125999f519f725f2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67083777"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Beheer delegeren in Azure Active Directory

Met organisatorische groei komt complexiteit. Een veelgebruikte reactie is het verminderen van een deel van de werkbelasting van toegangsbeheer met AD-beheerdersrollen (Azure Active Directory). U gebruikers de minst mogelijke bevoegdheden toewijzen om toegang te krijgen tot hun apps en hun taken uit te voeren. Zelfs als u de rol Globale beheerder niet aan elke eigenaar van de toepassing toewijst, plaatst u verantwoordelijkheden voor toepassingsbeheer op de bestaande globale beheerders. Er zijn vele redenen voor een organisatie te verplaatsen naar een meer gedecentraliseerde administratie. In dit artikel u de delegatie in uw organisatie plannen.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Gecentraliseerde versus gedelegeerde machtigingen

Naarmate een organisatie groeit, kan het moeilijk zijn om bij te houden welke gebruikers specifieke beheerdersrollen hebben. Als een werknemer beheerdersrechten heeft die hij niet zou moeten hebben, kan uw organisatie gevoeliger zijn voor inbreuken op de beveiliging. In het algemeen is het aantal beheerders dat u ondersteunt en hoe gedetailleerd hun machtigingen zijn, afhankelijk van de grootte en complexiteit van uw implementatie.

* In kleine of proof-of-concept implementaties, een of enkele beheerders doen alles; Er is geen delegatie. Maak in dit geval elke beheerder met de rol Globale beheerder.
* In grotere implementaties met meer machines, toepassingen en desktops is meer delegatie nodig. Verschillende beheerders hebben mogelijk meer specifieke functionele verantwoordelijkheden (rollen). Sommige zijn bijvoorbeeld bevoorrechte identiteitsbeheerders en andere mogelijk toepassingsbeheerders. Bovendien kan een beheerder alleen bepaalde groepen objecten beheren, zoals apparaten.
* Zelfs grotere implementaties vereisen mogelijk nog gedetailleerdere machtigingen, plus mogelijk beheerders met onconventionele of hybride rollen.

In de Azure AD-portal u [alle leden van elke rol bekijken,](directory-manage-roles-portal.md)waarmee u snel uw implementatie controleren en machtigingen delegeren.

Zie [RBAC-rol (Role-based access control) toewijzen](../../role-based-access-control/role-assignments-portal.md)als u de toegang tot Azure-resources wilt delegeren in plaats van beheertoegang in Azure AD.

## <a name="delegation-planning"></a>Delegatieplanning

Het is werk om een delegatiemodel te ontwikkelen dat aan uw behoeften voldoet. Het ontwikkelen van een delegatiemodel is een iteratief ontwerpproces en we raden u aan de volgende stappen te volgen:

* Definieer de rollen die u nodig hebt
* App-beheer delegeren
* De mogelijkheid verlenen om aanvragen te registreren
* App-eigendom delegeren
* Een beveiligingsplan ontwikkelen
* Noodrekeningen opstellen
* Uw beheerdersrollen beveiligen
* Maak bevoorrechte verhoging tijdelijk

## <a name="define-roles"></a>Rollen definiëren

Bepaal de Active Directory-taken die worden uitgevoerd door beheerders en hoe ze toewijzen aan rollen. U [gedetailleerde rolbeschrijvingen bekijken](directory-manage-roles-portal.md) in de Azure-portal.

Elke taak moet worden beoordeeld op frequentie, belang en moeilijkheidsgraad. Deze criteria zijn essentiële aspecten van taakdefinitie, omdat ze bepalen of een machtiging moet worden gedelegeerd:

* Taken die je routinematig doet, een beperkt risico hebben en triviaal zijn om te voltooien zijn uitstekende kandidaten voor delegatie.
* Taken die u zelden uitvoert, maar die grote impact hebben in de hele organisatie en hoge vaardigheidsniveaus vereisen, moeten zeer zorgvuldig worden overwogen voordat u delegeren. In plaats daarvan u [een account tijdelijk naar de vereiste rol verheffen](../active-directory-privileged-identity-management-configure.md) of de taak opnieuw toewijzen.

## <a name="delegate-app-administration"></a>App-beheer delegeren

De wildgroei aan apps binnen uw organisatie kan uw delegatiemodel belasten. Als het de last voor het beheer van toepassingstoegang op de Globale Beheerder plaatst, is het waarschijnlijk dat het model zijn overhead naarmate de tijd vordert, verhoogt. Als u mensen de rol Globale beheerder hebt toegekend voor zaken als het configureren van bedrijfstoepassingen, u ze nu uitladen naar de volgende minder bevoorrechte rollen. Dit helpt om uw beveiligingshouding te verbeteren en vermindert het potentieel voor ongelukkige fouten. De meest bevoorrechte rolrollen voor toepassingsbeheerders zijn:

* De rol **Toepassingsbeheerder,** die de mogelijkheid biedt om alle toepassingen in de map te beheren, inclusief registraties, instellingen voor één aanmelding, gebruikers- en groepstoewijzingen en licenties, instellingen voor toepassingsproxy en toestemming. Het verleent geen de mogelijkheid om voorwaardelijke toegang te beheren.
* De rol **Cloudapplicationadministrator,** die alle mogelijkheden van de toepassingsbeheerder verleent, behalve dat deze geen toegang verleent tot instellingen voor toepassingsproxy (omdat deze geen on-premises machtiging heeft).

## <a name="delegate-app-registration"></a>App-registratie delegeren

Standaard kunnen alle gebruikers toepassingsregistraties maken. Selectief de mogelijkheid verlenen om toepassingsregistraties te maken:

* **Gebruikers instellen kunnen toepassingen registreren** op Nee in **gebruikersinstellingen**
* De gebruiker toewijzen aan de rol Toepassingsontwikkelaar

Selectief toestemming verlenen om een toepassing toegang te geven tot gegevens:

* **Gebruikers instellen kunnen toestemming geven voor toepassingen die namens hen toegang hebben tot bedrijfsgegevens** in **gebruikersinstellingen**
* De gebruiker toewijzen aan de rol Toepassingsontwikkelaar

Wanneer een toepassingsontwikkelaar een nieuwe toepassingsregistratie maakt, worden deze automatisch toegevoegd als de eerste eigenaar.

## <a name="delegate-app-ownership"></a>App-eigendom delegeren

Voor zelfs fijnmazige app-toegang delegeren, u eigendom toewijzen aan individuele bedrijfstoepassingen. Dit vormt een aanvulling op de bestaande ondersteuning voor het toewijzen van eigenaren van toepassingsregistraties. Eigendom wordt per onderneming toegewezen in het blade van Enterprise Applications. Het voordeel is dat eigenaren alleen de bedrijfstoepassingen kunnen beheren die ze bezitten. U bijvoorbeeld een eigenaar toewijzen voor de Salesforce-toepassing en die eigenaar kan toegang tot en configuratie voor Salesforce beheren en geen andere toepassingen. Een bedrijfstoepassing kan veel eigenaren hebben en een gebruiker kan de eigenaar zijn voor veel bedrijfstoepassingen. Er zijn twee rollen voor app-eigenaren:

* De rol **Enterprise Application Owner** biedt de mogelijkheid om de 'bedrijfstoepassingen die de gebruiker bezit te beheren, inclusief instellingen voor één aanmelding, gebruikers- en groepstoewijzingen en het toevoegen van extra eigenaren. Het verleent niet de mogelijkheid om instellingen voor toepassingsproxy of voorwaardelijke toegang te beheren.
* De rol **Toepassingsregistratie-eigenaar** verleent de mogelijkheid om toepassingsregistraties te beheren voor de app waarvan de gebruiker eigenaar is, inclusief het toepassingsmanifest en het toevoegen van extra eigenaren.

## <a name="develop-a-security-plan"></a>Een beveiligingsplan ontwikkelen

Azure AD biedt een uitgebreide handleiding voor het plannen en uitvoeren van een beveiligingsplan voor uw Azure AD-beheerrollen, [Beveiligde bevoorrechte toegang voor hybride en cloudimplementaties.](directory-admin-roles-secure.md)

## <a name="establish-emergency-accounts"></a>Noodrekeningen opstellen

Als u toegang wilt houden tot uw identiteitsbeheerarchief wanneer zich problemen voordoen, bereidt u noodtoegangsaccounts voor op [basis van beheeraccounts voor noodgevallen maken.](directory-emergency-access.md)

## <a name="secure-your-administrator-roles"></a>Uw beheerdersrollen beveiligen

Aanvallers die controle krijgen over bevoegde accounts kunnen enorme schade aanrichten, dus bescherm deze accounts eerst, met behulp van het [basislijntoegangsbeleid](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) dat standaard beschikbaar is voor alle Azure AD-tenants (in openbare preview). Het beleid dwingt multi-factor authenticatie af op geprivilegieerde Azure AD-accounts. De volgende Azure AD-rollen vallen onder het Azure AD-basislijnbeleid:

* Globale beheerder
* SharePoint-beheerder
* Exchange-beheerder
* Beheerder van voorwaardelijke toegang
* Beveiligingsbeheerder

## <a name="elevate-privilege-temporarily"></a>Bevoegdheden tijdelijk verheffen

Voor de meeste dagelijkse activiteiten hebben niet alle gebruikers wereldwijde beheerdersrechten nodig en niet alle gebruikers moeten permanent worden toegewezen aan de rol globale beheerder. Wanneer gebruikers de machtigingen van een globale beheerder nodig hebben, moeten ze de roltoewijzing activeren in Azure AD [Privileged Identity Management](../active-directory-privileged-identity-management-configure.md) voor hun eigen account of een alternatief beheerdersaccount.

## <a name="next-steps"></a>Volgende stappen

Zie [Beheerdersrollen toewijzen in Azure AD](directory-assign-admin-roles.md) voor een verwijzing naar de functiebeschrijvingen van Azure AD
