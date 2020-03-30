---
title: Veelgestelde vragen (FAQ) - Azure Active Directory | Microsoft Documenten
description: Veelvoorkomende vragen en antwoorden over Azure en Azure Active Directory, wachtwoordbeheer en toepassingstoegang.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb0b5e37843413667e51112f52e6a69534e3425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063625"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Veelgestelde vragen over Azure Active Directory
Azure Active Directory (Azure AD) is een uitgebreide IDaaS-oplossing (Identity as a Service) waarin alle aspecten van identiteit, toegangsbeheer en beveiliging zijn opgenomen.

Zie [Wat is Azure Active Directory?](active-directory-whatis.md) voor meer informatie.


## <a name="access-azure-and-azure-active-directory"></a>Toegang tot Azure en Azure Active Directory
**V: Waarom wordt 'Geen abonnementen gevonden' weergegeven wanneer ik Azure AD wil openen in Azure Portal?**

**A:** Voor toegang tot Azure Portal moet elke gebruiker machtigingen hebben in een Azure-abonnement. Als u een betaald Office 365- of [https://aka.ms/accessAAD](https://aka.ms/accessAAD) Azure AD-abonnement hebt, gaat u naar voor een eenmalige activeringsstap. Anders moet u een gratis [Azure-account](https://azure.microsoft.com/pricing/free-trial/) activeren of een betaald abonnement afsluiten.

Zie voor meer informatie:

* [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

---
**V: Wat is de relatie tussen Azure AD, Office 365 en Azure?**

**A:** Azure AD biedt u een algemene identiteit en toegang tot alle webservices. Als u Office 365, Microsoft Azure, Intune of andere toepassingen gebruikt, gebruikt u Azure AD al bij het inschakelen van aanmeldings- en toegangsbeheer voor deze services.

Alle gebruikers die webservices kunnen gebruiken, worden gedefinieerd als gebruikersaccounts in een of meer exemplaren van Azure AD. U kunt deze accounts instellen voor gratis Azure AD-functies, zoals toegang tot cloudtoepassingen.

Betaalde Azure AD-services zoals Enterprise Mobility + Security vormen een aanvulling op andere webservices, zoals Office 365 en Microsoft Azure, met uitgebreide oplossingen voor beheer en beveiliging die ook geschikt zijn voor grote organisaties.

---

**V: Wat zijn de verschillen tussen eigenaar en globale beheerder?**

**A:** De persoon die zich aanmeldt voor een Azure-abonnement krijgt standaard de rol Eigenaar voor Azure-resources toegewezen. Een eigenaar kan een Microsoft-account of een werk- of schoolaccount gebruiken uit de map waarmee het Azure-abonnement is gekoppeld.  Deze rol is gemachtigd om services in Azure Portal te beheren.

Als anderen zich moeten aanmelden en toegang moeten krijgen tot services met hetzelfde abonnement, u ze de juiste [ingebouwde rol](../../role-based-access-control/built-in-roles.md)toewijzen. Zie [Toegang beheren met RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie.

De persoon die zich aanmeldt voor een Azure-abonnement krijgt standaard de rol Globale beheerder voor de map toegewezen. De globale beheerder heeft toegang tot alle Azure AD-mapfuncties. Azure AD heeft een andere set beheerdersrollen om de directory- en identiteitsgerelateerde functies te beheren. Deze beheerders hebben toegang tot verschillende functies in de Azure-portal. De rol van de beheerder bepaalt wat ze kunnen doen, zoals gebruikers maken of bewerken, beheerdersrollen toewijzen aan anderen, gebruikerswachtwoorden opnieuw instellen, gebruikerslicenties beheren of domeinen beheren.  Zie [Een gebruiker toewijzen aan beheerdersrollen in Azure Active Directory](active-directory-users-assign-role-azure-portal.md) en [Beheerdersvan beheerdersrollen toewijzen in Azure Active Directory en beheerdersrollen toewijzen in Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Daarnaast vormen betaalde Azure AD-services zoals Enterprise Mobility + Security een aanvulling op andere webservices, zoals Office 365 en Microsoft Azure, met uitgebreide oplossingen voor beheer en beveiliging die ook geschikt zijn voor grote organisaties.

---
**V: Is er een rapport waarin staat wanneer mijn Azure AD-gebruikerslicenties verlopen?**

**A:** №.  Dit is momenteel niet beschikbaar.

---

## <a name="get-started-with-hybrid-azure-ad"></a>Aan de slag met hybride Azure AD


**V: Hoe verlaat ik een tenant wanneer ik ben toegevoegd als samenwerker?**

**A:** Wanneer u als samenwerker bent toegevoegd aan de tenant van een andere organisatie, kunt u de 'tenantschakelaar' in de rechterbovenhoek gebruiken om te schakelen tussen tenants.  Op dit moment is er geen manier om de organisatie die u heeft uitgenodigd, te verlaten. Microsoft is bezig om deze functie te ontwikkelen.  Tot deze functie beschikbaar is, kunt u de organisatie die u heeft uitgenodigd, vragen om u uit de tenant te verwijderen.

---
**V: Hoe kan ik mijn on-premises directory verbinden met Azure AD?**

**A:** U kunt uw on-premises directory verbinden met Azure AD via Azure AD Connect.

Zie [Integrating your on-premises identities with Azure Active Directory](../hybrid/whatis-hybrid-identity.md) (Uw on-premises identiteiten integreren met Azure Active Directory) voor meer informatie.

---
**V: Hoe stel ik eenmalige aanmelding in tussen mijn on-premises directory en mijn cloudtoepassingen?**

**A:** U hoeft eenmalige aanmelding (SSO) alleen in te stellen tussen uw on-premises directory en Azure AD. Zo lang u uw cloudtoepassingen opent via Azure AD, wordt er met de service automatisch voor gezorgd dat uw gebruikers juist worden geverifieerd met hun on-premises referenties.

Het implementeren van SSO vanuit on-premises kan eenvoudig worden bereikt met federatieoplossingen zoals Active Directory Federation Services (AD FS) of door wachtwoordhashsynchronisatie te configureren. U beide opties eenvoudig implementeren met de wizard Azure AD Connect-configuratie.

Zie [Integrating your on-premises identities with Azure Active Directory](../hybrid/whatis-hybrid-identity.md) (Uw on-premises identiteiten integreren met Azure Active Directory) voor meer informatie.

---
**V: Bevat Azure AD een selfserviceportal voor gebruikers in mijn organisatie?**

**A:** Ja, Azure AD bevat een speciaal [toegangsvenster](https://myapps.microsoft.com). Dit dient als selfserviceportal voor gebruikers en geeft toegang tot toepassingen. Als u een Office 365-klant bent, u veel van dezelfde mogelijkheden vinden in de [Office 365-portal.](https://portal.office.com)

Zie [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Inleiding tot het toegangsvenster) voor meer informatie.

---
**V: Helpt Azure AD mij bij het beheren van mijn on-premises infrastructuur?**

**A:** Ja. Azure AD Connect Health is opgenomen in de Azure AD Premium-versie. Azure AD Connect Health helpt u om inzicht te verkrijgen in uw on-premises infrastructuur voor identiteiten en de synchronisatieservices.  

Zie [Monitor your on-premises identity infrastructure and synchronization services in the cloud](../hybrid/whatis-hybrid-identity-health.md) (Uw on-premises infrastructuur voor identiteiten en synchronisatieservices in de cloud controleren) voor meer informatie.  

---
## <a name="password-management"></a>Wachtwoordbeheer
**V: Kan ik het terugschrijven van Azure AD-wachtwoordwachtwoorden gebruiken zonder wachtwoordsynchronisatie? (Is het in dit scenario mogelijk om Azure AD selfservice password reset (SSPR) te gebruiken met terugschrijfgegevens van wachtwoorden en geen wachtwoorden op te slaan in de cloud?)**

**A:** U hoeft uw Active Directory-wachtwoorden niet te synchroniseren naar Azure AD om terugschrijven in te kunnen schakelen. In een federatieve omgeving is eenmalige aanmelding van Azure AD (SSO) afhankelijk van de on-premises directory voor het verifiëren van de gebruiker. In dit geval is er geen on-premises wachtwoord vereist dat moet worden bijgehouden in Azure AD.

---
**V: Hoelang duurt het voordat een wachtwoord on-premises is teruggeschreven naar Active Directory?**

**A:** Het terugschrijven van het wachtwoord wordt in realtime uitgevoerd.

Zie voor meer informatie [Getting started with password management](../authentication/quickstart-sspr.md).

---
**V: Kan ik de functie Terugschrijven van wachtwoord gebruiken met wachtwoorden die worden beheerd door een beheerder?**

**A:** Ja, als u Terugschrijven van wachtwoord hebt ingeschakeld, worden de wachtwoordbewerkingen van een beheerder teruggeschreven naar uw on-premises omgeving.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>Zie [Password management frequently asked questions](../authentication/active-directory-passwords-faq.md) (Veelgestelde vragen over wachtwoordbeheer) voor meer antwoorden op vragen over wachtwoorden.
---
**V: Wat moet ik doen als ik mijn bestaande Office 365-/Azure AD-wachtwoord niet meer ken als ik het probeer te wijzigen?**

**A:** In een dergelijke situatie zijn er meerdere oplossingen.  Gebruik selfservice voor wachtwoordherstel (SSPR) als deze optie beschikbaar is.  Of SSPR werkt, is afhankelijk van de configuratie.  Zie voor meer informatie [Hoe werkt de portal voor wachtwoordherstel?](../authentication/howto-sspr-deployment.md).

Office 365-gebruikers kunnen hun beheerder vragen het wachtwoord opnieuw in te stellen via de stappen die worden beschreven in [Gebruikerswachtwoorden opnieuw instellen](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Beheerders kunnen wachtwoorden van Azure AD-accounts via een van de volgende opties opnieuw instellen:

- [Accounts in Azure Portal opnieuw instellen](active-directory-users-reset-password-azure-portal.md)
- [PowerShell gebruiken](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>Beveiliging
**V: Worden accounts na een bepaald aantal mislukte pogingen vergrendeld of wordt een meer geavanceerde strategie gebruikt?**

We gebruiken een geavanceerdere strategie om account te vergrendelen.  Dit is gebaseerd op het IP-adres van de aanvraag en op de ingevoerde wachtwoorden. De duur van de vergrendeling wordt ook langer op basis van de kans dat het een aanval betreft.  

**V: Bepaalde (algemene) wachtwoorden worden geweigerd met de melding dat het wachtwoord te vaak is gebruikt. Heeft dit betrekking op wachtwoorden die worden gebruikt in de huidige Active Directory?**

Dit verwijst naar wachtwoorden die wereldwijd gebruikelijk zijn, zoals varianten van "Wachtwoord" en "123456".

**V: Worden aanmeldingsaanvragen van twijfelachtige bronnen (botnets, tor-eindpunten) in een B2C-tenant geblokkeerd of is hiervoor een Basic- of Premium-tenant vereist?**

We hebben wel een gateway waarmee aanvragen worden gefilterd en die enige bescherming biedt tegen botnets. Deze wordt toegepast op alle B2C-tenants.

## <a name="application-access"></a>Toegang tot toepassingen

**V: Waar vind ik een lijst met toepassingen die vooraf zijn geïntegreerd met Azure AD en de bijbehorende mogelijkheden?**

**A:** Azure AD bevat meer dan 2.600 vooraf geïntegreerde toepassingen van Microsoft, toepassingsserviceproviders en partners. Alle vooraf geïntegreerde toepassingen bieden ondersteuning voor eenmalige aanmelding (SSO). Via SSO kunt u uw bedrijfsreferenties gebruiken om toegang te krijgen tot uw apps. Een aantal toepassingen ondersteunt ook geautomatiseerde inrichting en het ongedaan maken van de inrichting.

Zie de [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/) voor een volledige lijst met vooraf geïntegreerde toepassingen.

---
**V: Wat moet ik doen als de benodigde toepassing niet beschikbaar in de Azure AD Marketplace?**

**A:** Met Azure AD Premium kunt u elke gewenste toepassing toevoegen en configureren. U kunt eenmalige aanmelding en geautomatiseerde inrichting configureren, afhankelijk van de mogelijkheden van uw toepassing en uw voorkeuren.  

Zie voor meer informatie:

* [Configuring single sign-on to applications that are not in the Azure Active Directory application gallery](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md) (Eenmalige aanmelding configureren voor toepassingen die zich niet in de Azure Active Directory-toepassingsgalerie bevinden)
* [Using SCIM to enable automatic provisioning of users and groups from Azure Active Directory to applications](../app-provisioning/use-scim-to-provision-users-and-groups.md) (SCIM gebruiken om in te stellen dat gebruikers en groepen van Azure Active Directory automatisch worden ingericht voor toepassingen)

---
**V: Hoe melden gebruikers zich met behulp van Azure AD aan bij toepassingen?**

**A:** In Azure AD kunnen gebruikers op verschillende manieren toepassingen weergeven en openen, zoals:

* Het deelvenster Azure AD Access
* Het startprogramma voor toepassingen van Office 365
* Directe aanmelding bij federatieve apps
* Dieptekoppelingen naar federatieve apps, op basis van wachtwoorden, of bestaande apps

Zie [Ervaringen van eindgebruikers voor toepassingen voor](../manage-apps/end-user-experiences.md)meer informatie.

---
**V: Op welke manieren kunnen met behulp van Azure AD verificatie en eenmalige aanmelding worden ingesteld voor toepassingen?**

**A:** Azure AD biedt ondersteuning voor een groot aantal gestandaardiseerde protocollen voor verificatie en autorisatie, zoals SAML 2.0, OpenID Connect, OAuth 2.0 en WS-Federation. Daarnaast ondersteunt Azure AD het gebruik van wachtwoordkluizen en mogelijkheden voor automatische aanmelding voor apps die alleen ondersteuning bieden voor verificatie op basis van formulieren.  

Zie voor meer informatie:

* [Authentication Scenarios for Azure AD](../develop/authentication-scenarios.md) (Verificatiescenario's voor Azure AD)
* [Verificatie- en autorisatieprotocollen](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Eén aanmelding voor toepassingen in Azure AD](../manage-apps/what-is-single-sign-on.md)

---
**V: Kan ik toepassingen toevoegen die ik on-premises uitvoer?**

**A:** Via de Azure AD-toepassingsproxy hebt u eenvoudig en veilig toegang tot on-premises webtoepassingen die u kiest. U kunt deze toepassingen in Azure AD op dezelfde manier openen als uw SaaS-apps (Software as a Service). U hebt geen VPN nodig en u hoeft uw netwerkinfrastructuur niet te wijzigen.  

Zie [How to provide secure remote access to on-premises applications](../manage-apps/application-proxy.md) (Beveiligde externe toegang bieden voor on-premises toepassingen) voor meer informatie.

---
**V: Hoe maak ik Multi-Factor Authentication verplicht voor gebruikers die toegang hebben tot een bepaalde toepassing?**

**A:** Met Voorwaardelijke toegang tot Azure AD u voor elke toepassing een uniek toegangsbeleid toewijzen. In uw beleid kunt u aangeven dat Multi-Factor Authentication altijd is vereist of alleen wanneer gebruikers niet zijn verbonden met het lokale netwerk.  

Zie [Securing access to Office 365 and other apps connected to Azure Active Directory](../active-directory-conditional-access-azure-portal.md) (De toegang beveiligen tot Office 365 en andere apps die zijn verbonden met Azure Active Directory) voor meer informatie.

---
**V: Wat is geautomatiseerde gebruikersinrichting voor SaaS-apps?**

**A:** Met Azure AD kunt u het maken, onderhouden en verwijderen van gebruikers-ID's in veel populaire cloud-apps (SaaS) automatiseren.

Zie [Automate user provisioning and deprovisioning to SaaS applications with Azure Active Directory](../app-provisioning/user-provisioning.md) (Automatisch gebruikers inrichten en de inrichting ongedaan maken voor SaaS-toepassingen met Azure Active Directory) voor meer informatie.

---
**V: Kan ik een veilige LDAP-verbinding instellen met Azure AD?**

**A:** Nee. Azure AD ondersteunt niet rechtstreeks het LDAP-protocol (Lightweight Directory Access Protocol) of Secure LDAP. Het is echter mogelijk om Azure AD Domain Services (Azure AD DS)-instantie in te schakelen op uw Azure AD-tenant met goed geconfigureerde netwerkbeveiligingsgroepen via Azure Networking om LDAP-connectiviteit te bereiken. Zie https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap voor meer informatie.
