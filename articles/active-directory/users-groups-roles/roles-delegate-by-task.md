---
title: Rollen met beperkte bevoegdheden delegeren per beheerder taak-Azure Active Directory | Microsoft Docs
description: Rollen voor het delegeren van identiteits taken in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 05/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3799496d13259c943847625a2cf6a39a8edb1d35
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70207238"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Beheerders rollen per beheer taak in Azure Active Directory

In dit artikel vindt u de informatie die nodig is voor het beperken van de beheerders machtigingen van een gebruiker door het toewijzen van Mini maal bevoegde rollen in Azure Active Directory (Azure AD). U vindt beheerders taken ingedeeld op functie gebied en de minst bevoegde rol die vereist is om elke taak uit te voeren, samen met aanvullende niet-globale beheerders rollen die de taak kunnen uitvoeren.

## <a name="application-proxy"></a>Toepassingsproxy

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Toepassings proxy-app configureren | Toepassingsbeheerder | 
Eigenschappen van connector groep configureren | Toepassingsbeheerder | 
Toepassings registratie maken wanneer de mogelijkheid voor alle gebruikers is uitgeschakeld | Toepassingsontwikkelaar | Cloud toepassings beheerder, toepassings beheerder
Connector groep maken | Toepassingsbeheerder | 
Connector groep verwijderen | Toepassingsbeheerder | 
Toepassingsproxy uitschakelen | Toepassingsbeheerder | 
Service-connector downloaden | Toepassingsbeheerder | 
Alle configuratie lezen | Toepassingsbeheerder | 

## <a name="b2c"></a>B2C

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Azure AD B2C mappen maken | Alle niet-gast gebruikers ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
B2C-toepassingen maken | Globale beheerder | 
Bedrijfs toepassingen maken | Cloudtoepassingsbeheerder | Toepassingsbeheerder
B2C-beleid maken, lezen, bijwerken en verwijderen | Globale beheerder | 
Id-providers maken, lezen, bijwerken en verwijderen | Globale beheerder | 
Gebruikers stromen voor het opnieuw instellen van wacht woorden maken, lezen, bijwerken en verwijderen | Globale beheerder | 
Gebruikers stromen voor het bewerken van profielen maken, lezen, bijwerken en verwijderen | Globale beheerder | 
Gebruikers stromen voor het aanmelden maken, lezen, bijwerken en verwijderen | Globale beheerder | 
Gebruikers stroom voor het aanmelden maken, lezen, bijwerken en verwijderen |Globale beheerder | 
Gebruikers kenmerken maken, lezen, bijwerken en verwijderen | Globale beheerder | 
Gebruikers maken, lezen, bijwerken en verwijderen | Globale beheerder ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs))
Alle configuratie lezen | Globale beheerder | 
B2C-controle logboeken lezen | Globale beheerder ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs)) | 

> [!NOTE]
> Azure AD B2C globale beheerders hebben niet dezelfde machtigingen als globale Azure AD-beheerders. Als u over Azure AD B2C globale beheerders bevoegdheden beschikt, moet u ervoor zorgen dat u zich in een Azure AD B2C Directory bevindt en niet een Azure AD-adres lijst.

## <a name="company-branding"></a>Aangepaste huisstijl

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Aangepaste huisstijl configureren | Globale beheerder | 
Alle configuratie lezen | Adreslijstlezers | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="company-properties"></a>Eigenschappen van het bedrijf

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Eigenschappen van het bedrijf configureren | Globale beheerder | 

## <a name="connect"></a>Verbinden

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Passthrough-verificatie | Globale beheerder | 
Alle configuratie lezen | Globale beheerder | 
Naadloze single sign-on | Globale beheerder | 

## <a name="connect-health"></a>Connect Health

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Services toevoegen of verwijderen | Eigenaar ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Oplossingen voor synchronisatie fout Toep assen | Inzender ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Eigenaar
Meldingen configureren | Inzender ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Eigenaar
Instellingen configureren | Eigenaar ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations)) | 
Synchronisatie meldingen configureren | Inzender ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Eigenaar
ADFS-beveiligings rapporten lezen | Beveiligingslezer | Inzender, eigenaar
Alle configuratie lezen | Lezer ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Inzender, eigenaar
Synchronisatie fouten lezen | Lezer ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Inzender, eigenaar
Synchronisatie services lezen | Lezer ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Inzender, eigenaar
Metrische gegevens en waarschuwingen weer geven | Lezer ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Inzender, eigenaar
Metrische gegevens en waarschuwingen weer geven | Lezer ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Inzender, eigenaar
Metrische gegevens en waarschuwingen voor synchronisatie service weer geven | Lezer ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context)) | Inzender, eigenaar


## <a name="custom-domain-names"></a>Namen van aangepaste domeinen

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Domeinen beheren | Globale beheerder | 
Alle configuratie lezen | Adreslijstlezers | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))

## <a name="domain-services"></a>Domain Services

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Azure AD Domain Services exemplaar maken | Globale beheerder | 
Alle Azure AD Domain Services taken uitvoeren | Groep Administrators van Azure AD DC ([Zie de documentatie](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain)) | 
Alle configuratie lezen | Lezer op een Azure-abonnement met AD DS-service | 

## <a name="devices"></a>Apparaten

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Apparaat uitschakelen | Cloudapparaatbeheerder | 
Apparaat inschakelen | Cloudapparaatbeheerder | 
Basis configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
BitLocker-sleutels lezen | Beveiligingslezer | Wachtwoord beheerder, beveiligings beheerder

## <a name="enterprise-applications"></a>Bedrijfstoepassingen

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Toestemming geven aan alle gedelegeerde machtigingen | Cloudtoepassingsbeheerder | Toepassingsbeheerder
Toestemming geven aan toepassings machtigingen die niet inclusief Microsoft Graph of Azure AD Graph | Cloudtoepassingsbeheerder | Toepassingsbeheerder
Toestemming geven aan toepassings machtigingen voor Microsoft Graph of Azure AD Graph | Globale beheerder | 
Toestemming geven aan toepassingen die toegang krijgen tot de eigen gegevens | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Bedrijfs toepassing maken | Cloudtoepassingsbeheerder | Toepassingsbeheerder
Toepassings proxy beheren | Toepassingsbeheerder | 
Gebruikersinstellingen beheren | Globale beheerder | 
Lees toegang voor een groep of een app | Beveiligingslezer | Beveiligings beheerder, gebruikers beheerder
Alle configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 
Bedrijfs toepassings toewijzingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud toepassings beheerder, toepassings beheerder
Eigen aren van bedrijfs toepassingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud toepassings beheerder, toepassings beheerder
Eigenschappen van bedrijfs toepassing bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud toepassings beheerder, toepassings beheerder
De inrichting van bedrijfs toepassingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud toepassings beheerder, toepassings beheerder
Self-service voor bedrijfs toepassingen bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud toepassings beheerder, toepassings beheerder
Eigenschappen voor eenmalige aanmelding bijwerken | Eigenaar van bedrijfs toepassing ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Cloud toepassings beheerder, toepassings beheerder

## <a name="entitlement-management"></a>Rechtenbeheer
Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Resources toevoegen aan een catalogus | Gebruikersbeheerder | Met het rechten beheer kunt u deze taak delegeren aan de eigenaar van de catalogus ([Zie de documentatie](../governance/entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager))
Share point online-sites toevoegen aan catalogus | Globale beheerder


## <a name="groups"></a>Groepen

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Licentie toewijzen | Gebruikersbeheerder | 
Groep maken | Gebruikersbeheerder | 
Toegangs beoordeling van een groep of een app maken, bijwerken of verwijderen | Gebruikersbeheerder | 
Groeps verloop beheren | Gebruikersbeheerder | 
Groepsinstellingen beheren | Globale beheerder | 
Alle configuraties lezen (met uitzonde ring van verborgen lidmaatschap) | Adreslijstlezers | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Verborgen lidmaatschap lezen | Groepslid | Groeps eigenaar, wachtwoord beheerder, Exchange-beheerder, share point-beheerder, team beheerder, gebruikers beheerder
Lidmaatschap van groepen met verborgen lidmaatschap lezen | Helpdeskbeheerder | Gebruikers beheerder, team beheerder
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Groepslid maatschap bijwerken | Groeps eigenaar ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Gebruikersbeheerder
Groeps eigenaren bijwerken | Groeps eigenaar ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Gebruikersbeheerder
Eigenschappen van groep bijwerken | Groeps eigenaar ([Zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | Gebruikersbeheerder

## <a name="identity-protection"></a>Identity Protection

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Waarschuwings meldingen configureren| Beveiligingsbeheerder | 
MFA-beleid configureren en in-of uitschakelen| Beveiligingsbeheerder | 
Beleid voor aanmeldings Risico's configureren en in-of uitschakelen| Beveiligingsbeheerder | 
Beleid voor gebruikers Risico's configureren en in-of uitschakelen | Beveiligingsbeheerder | 
Wekelijkse samen vattingen configureren | Beveiligingsbeheerder| 
Alle risico detecties verwijderen | Beveiligingsbeheerder | 
Probleem oplossen of negeren | Beveiligingsbeheerder | 
Alle configuratie lezen | Beveiligingslezer | 
Alle risico detecties lezen | Beveiligingslezer | 
Lees lekken | Beveiligingslezer | 

## <a name="licenses"></a>Licenties

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Licentie toewijzen | Licentiebeheerder | Gebruikersbeheerder
Alle configuratie lezen | Adreslijstlezers | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions))
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Probeer of koop een abonnement | Factureringsbeheerder | 


## <a name="monitoring---audit-logs"></a>Controle logboeken

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Audit logboeken lezen | Rapportenlezer | Beveiligings lezer, beveiligings beheerder

## <a name="monitoring---sign-ins"></a>Bewakings-aanmeldingen

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Aanmeld logboeken lezen | Rapportenlezer | Beveiligings lezer, beveiligings beheerder

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Alle bestaande app-wacht woorden verwijderen die zijn gegenereerd door de geselecteerde gebruikers | Globale beheerder | 
MFA uitschakelen | Globale beheerder | 
MFA inschakelen | Globale beheerder | 
Instellingen voor MFA-service beheren | Globale beheerder | 
Vereisen dat geselecteerde gebruikers opnieuw contact methoden opgeven | Verificatiebeheerder | 
Multi-factor Authentication herstellen op alle onthouden apparaten  | Verificatiebeheerder | 

## <a name="mfa-server"></a>MFA-server

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Gebruikers blok keren/deblokkeren | Globale beheerder | 
Account vergrendeling configureren | Globale beheerder | 
Cache regels configureren | Globale beheerder | 
Fraude waarschuwing configureren | Globale beheerder
Meldingen configureren | Globale beheerder | 
Eenmalige bypass configureren | Globale beheerder | 
Instellingen voor telefoon gesprek configureren | Globale beheerder | 
Providers configureren | Globale beheerder | 
Server instellingen configureren | Globale beheerder | 
Activiteiten rapport lezen | Globale beheerder | 
Alle configuratie lezen | Globale beheerder | 
Server status lezen | Globale beheerder |  

## <a name="organizational-relationships"></a>Organisatierelaties

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Id-providers beheren | Globale beheerder | 
Instellingen beheren | Globale beheerder | 
Gebruiks voorwaarden beheren | Globale beheerder | 
Alle configuratie lezen | Globale beheerder | 

## <a name="password-reset"></a>Wachtwoord opnieuw instellen

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Verificatie methoden configureren | Globale beheerder |
Aanpassing configureren | Globale beheerder |
Melding configureren | Globale beheerder |
On-premises integratie configureren | Globale beheerder |
Eigenschappen voor het opnieuw instellen van wacht woorden configureren | Gebruikerbeheerder | Globale beheerder
Registratie configureren | Globale beheerder |
Alle configuratie lezen | Beveiligingsbeheerder | Gebruikerbeheerder |

## <a name="privileged-identity-management"></a>Privileged identity management

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Gebruikers toewijzen aan rollen | Beheerder voor bevoorrechte rollen | 
Rolinstellingen configureren | Beheerder voor bevoorrechte rollen | 
Controle activiteit weer geven | Beveiligingslezer | 
Rollidmaatschap weer geven | Beveiligingslezer | 

## <a name="roles-and-administrators"></a>Rollen en beheerders

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Roltoewijzingen beheren | Beheerder voor bevoorrechte rollen | 
Lees toegang voor een Azure AD-rol  | Beveiligingslezer | Beveiligings beheerder, beheerder van geprivilegieerde rol
Alle configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)) | 

## <a name="security---authentication-methods"></a>Methoden voor beveiligings verificatie

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Verificatie methoden configureren | Globale beheerder | 
Alle configuratie lezen | Globale beheerder | 

## <a name="security---conditional-access"></a>Beveiliging-voorwaardelijke toegang

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Vertrouwde IP-adressen voor MFA configureren | Beheerder voor voorwaardelijke toegang | 
Aangepaste besturings elementen maken | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Benoemde locaties maken | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Beleidsregels maken | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiks voorwaarden maken | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
VPN-connectiviteits certificaat maken | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Klassiek beleid verwijderen | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiks voorwaarden verwijderen | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
VPN-connectiviteits certificaat verwijderen | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Klassiek beleid uitschakelen | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Aangepaste besturings elementen beheren | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Benoemde locaties beheren | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiks voorwaarden beheren | Beheerder voor voorwaardelijke toegang | Beveiligingsbeheerder
Alle configuratie lezen | Beveiligingslezer | Beveiligingsbeheerder
Benoemde locaties lezen | Beveiligingslezer | Beheerder van voorwaardelijke toegang, beveiligings beheerder

## <a name="security---identity-security-score"></a>Beveiligings Score beveiliging identiteit

Taak | Minst geprivilegieerde rol | Aanvullende rollen | 
---- | --------------------- | ----------------
Alle configuratie lezen | Beveiligingslezer | Beveiligingsbeheerder
Beveiligings Score lezen | Beveiligingslezer | Beveiligingsbeheerder
Gebeurtenis status bijwerken | Beveiligingsbeheerder | 

## <a name="security---risky-sign-ins"></a>Beveiligings risico op aanmeldingen

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Alle configuratie lezen | Beveiligingslezer | 
Risk ante aanmeldingen lezen | Beveiligingslezer | 

## <a name="security---users-flagged-for-risk"></a>Beveiliging-gebruikers die zijn gemarkeerd voor risico

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Alle gebeurtenissen sluiten | Beveiligingsbeheerder | 
Alle configuratie lezen | Beveiligingslezer | 
Gebruikers lezen die zijn gemarkeerd voor risico | Beveiligingslezer | 

## <a name="users"></a>Gebruikers

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Gebruiker toevoegen aan Directory-rol | Beheerder voor bevoorrechte rollen | 
Gebruiker toevoegen aan groep | Gebruikersbeheerder | 
Licentie toewijzen | Licentiebeheerder | Gebruikersbeheerder
Gast gebruiker maken | Afzender van gastuitnodigingen | Gebruikersbeheerder
Gebruiker maken | Gebruikersbeheerder | 
Gebruikers verwijderen | Gebruikersbeheerder | 
Vernieuwings tokens van beperkte beheerders ongeldig maken (Zie de documentatie) | Gebruikersbeheerder | 
Vernieuwen van tokens van niet-beheerders ongeldig maken (zie documentatie) | Wachtwoordbeheerder | Gebruikersbeheerder
Vernieuwen van tokens van bevoegde beheerders ongeldig maken (zie documentatie) | Globale beheerder | 
Basis configuratie lezen | Standaard gebruikersrol ([Zie de documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Wacht woord opnieuw instellen voor beperkte beheerders (Zie de documentatie) | Gebruikersbeheerder | 
Het wacht woord van niet-beheerders opnieuw instellen (Zie de documentatie) | Wachtwoordbeheerder | Gebruikersbeheerder
Wacht woord van bevoegde beheerders opnieuw instellen | Globale beheerder | 
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Alle eigenschappen bijwerken met uitzonde ring van Principal-naam van gebruiker | Gebruikersbeheerder | 
Principal-naam van gebruiker voor beperkte beheerders bijwerken (Zie de documentatie) | Gebruikersbeheerder | 
De eigenschap User Principal name voor bevoegde beheerders bijwerken (Zie de documentatie) | Globale beheerder | 
Gebruikers instellingen bijwerken | Globale beheerder | 


## <a name="support"></a>Ondersteuning

Taak | Minst geprivilegieerde rol | Aanvullende rollen
---- | --------------------- | ----------------
Ondersteunings ticket verzenden | Servicebeheerder | Toepassings beheerder, Azure Information Protection beheerder, facturerings beheerder, Cloud toepassings beheerder, nalevings beheerder, Dynamics 365-beheerder, Desktop Analytics-beheerder, Exchange-beheerder, wacht woord Beheerder, intune-beheerder, Skype voor bedrijven-beheerder, Power BI-beheerder, bevoegde verificatie beheerder, share point-beheerder, teams communicatie beheerder, team beheerder, gebruikers beheerder, Werkplek Analytics-beheerder

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-beheerders rollen toewijzen of verwijderen](directory-manage-roles-portal.md)
* [Naslag informatie over Azure AD-beheerders rollen](directory-assign-admin-roles.md)
