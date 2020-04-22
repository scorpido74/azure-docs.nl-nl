---
title: Rollen delegeren op beheertaak - Azure Active Directory | Microsoft Documenten
description: Rollen die moeten worden gedelegeerd voor identiteitstaken in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/03/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd24650c9bf0c4de155b5bfc8723cfa1fef01548
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755412"
---
# <a name="administrator-roles-by-admin-task-in-azure-active-directory"></a>Beheerdersrollen per beheertaak in Azure Active Directory

In dit artikel vindt u de informatie die nodig is om beheerdersmachtigingen van een gebruiker te beperken door de minst bevoorrechte rollen toe te wijzen in Azure Active Directory (Azure AD). U vindt beheerderstaken die zijn georganiseerd op functiegebied en de minst bevoorrechte rol die nodig is om elke taak uit te voeren, samen met extra niet-globale beheerdersrollen die de taak kunnen uitvoeren.

## <a name="application-proxy"></a>Toepassingsproxy

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
App voor toepassingsproxy configureren | Toepassingsbeheerder | 
Eigenschappen van verbindingsgroep configureren | Toepassingsbeheerder | 
Toepassingsregistratie maken wanneer de mogelijkheid voor alle gebruikers is uitgeschakeld | Toepassingsontwikkelaar | Cloudapplicationbeheerder, toepassingsbeheerder
Connectorgroep maken | Toepassingsbeheerder | 
Connectorgroep verwijderen | Toepassingsbeheerder | 
Toepassingsproxy uitschakelen | Toepassingsbeheerder | 
Connectorservice downloaden | Toepassingsbeheerder | 
Alle configuratie lezen | Toepassingsbeheerder | 

## <a name="b2c"></a>B2C

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Azure AD B2C-mappen maken | Alle niet-gastgebruikers[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
B2C-toepassingen maken | Globale beheerder | 
Bedrijfstoepassingen maken | Beheerder van de cloudtoepassing | Toepassingsbeheerder
B2C-beleid maken, lezen, bijwerken en verwijderen | B2C IEF-beleidsbeheerder | 
Identiteitsproviders maken, lezen, bijwerken en verwijderen | Beheerder externe identiteitsprovider | 
Gebruikersstromen voor het opnieuw instellen van wachtwoorden maken, lezen, bijwerken en verwijderen | B2C-gebruikersstroombeheerder | 
Gebruikersstromen voor het bewerken van profielen maken, lezen, bijwerken en verwijderen | B2C-gebruikersstroombeheerder | 
Aanmeldingsstromen maken, lezen, bijwerken en verwijderen | B2C-gebruikersstroombeheerder | 
Gebruikersstroom aanmeldingen maken, lezen, bijwerken en verwijderen |B2C-gebruikersstroombeheerder | 
Gebruikerskenmerken maken, lezen, bijwerken en verwijderen | Beheerder van b2c-gebruikersstroomkenmerk | 
Gebruikers maken, lezen, bijwerken en verwijderen | Gebruikersbeheerder
Alle configuratie lezen | Globale lezer | 
B2C-controlelogboeken lezen | Globale lezer[(zie documentatie)](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-faqs) | 

> [!NOTE]
> Azure AD B2C Global-lezers hebben niet dezelfde machtigingen als azure AD-globale beheerders. Als u algemene beheerdersrechten van Azure AD B2C hebt, controleert u of u zich in een Azure AD B2C-map bevindt en niet in een Azure AD-map.

## <a name="company-branding"></a>Aangepaste huisstijl

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Bedrijfshuisstijl configureren | Globale beheerder | 
Alle configuratie lezen | Directorylezers | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)

## <a name="company-properties"></a>Bedrijfseigenschappen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Bedrijfseigenschappen configureren | Globale beheerder | 

## <a name="connect"></a>Verbinding maken

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Passthrough-verificatie | Hybride identiteitsbeheerder | 
Alle configuratie lezen | Globale lezer | Hybride identiteitsbeheerder |
Naadloze enkele aanmelding | Hybride identiteitsbeheerder | 

## <a name="connect-health"></a>Connect Health

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Services toevoegen of verwijderen | Eigenaar[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations) | 
Oplossingen toepassen op synchronisatiefout | Bijdrager[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Eigenaar
Meldingen configureren | Bijdrager[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Eigenaar
Instellingen configureren | Eigenaar[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations) | 
Synchronisatiemeldingen configureren | Bijdrager[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Eigenaar
ADFS-beveiligingsrapporten lezen | Beveiligingslezer | Bijdrager, eigenaar
Alle configuratie lezen | Reader[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Bijdrager, eigenaar
Synchronisatiefouten lezen | Reader[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Bijdrager, eigenaar
Synchronisatieservices lezen | Reader[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Bijdrager, eigenaar
Statistieken en waarschuwingen weergeven | Reader[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Bijdrager, eigenaar
Statistieken en waarschuwingen weergeven | Reader[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Bijdrager, eigenaar
Statistieken en waarschuwingen voor synchronisatieservice weergeven | Reader[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context) | Bijdrager, eigenaar

## <a name="custom-domain-names"></a>Aangepaste domeinnamen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Domeinen beheren | Globale beheerder | 
Alle configuratie lezen | Directorylezers | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)

## <a name="domain-services"></a>Domain Services

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Instantie Azure AD Domain Services maken | Globale beheerder | 
Alle Azure AD Domain Services-taken uitvoeren | Azure AD DC Administrators-groep[(zie documentatie)](../../active-directory-domain-services/tutorial-create-management-vm.md#administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain) | 
Alle configuratie lezen | Reader op Azure-abonnement met AD DS-service | 

## <a name="devices"></a>Apparaten

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Apparaat uitschakelen | Beheerder van cloudapparaten | 
Apparaat inschakelen | Beheerder van cloudapparaten | 
Basisconfiguratie lezen | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
BitLocker-toetsen lezen | Beveiligingslezer | Wachtwoordbeheerder, beveiligingsbeheerder

## <a name="enterprise-applications"></a>Bedrijfstoepassingen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Toestemming voor gedelegeerde machtigingen | Beheerder van cloudtoepassingen | Toepassingsbeheerder
Toestemming voor toepassingsmachtigingen die Microsoft Graph niet meesmetten | Beheerder van cloudtoepassingen | Toepassingsbeheerder
Toestemming voor toepassingsmachtigingen voor Microsoft Graph | Beheerder van een bevoorrechte rol | 
Toestemming voor toepassingen die toegang hebben tot eigen gegevens | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Bedrijfstoepassing maken | Beheerder van cloudtoepassingen | Toepassingsbeheerder
Toepassingsproxy beheren | Toepassingsbeheerder | 
Gebruikersinstellingen beheren | Globale beheerder | 
Toegangscontrole van een groep of van een app lezen | Beveiligingslezer | Beveiligingsbeheerder, gebruikersbeheerder
Alle configuratie lezen | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Toewijzingen van bedrijfstoepassingen bijwerken | Eigenaar van ondernemingstoepassingen[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Beheerder van cloudtoepassingen, toepassingsbeheerder
Eigenaren van bedrijfstoepassingen bijwerken | Eigenaar van ondernemingstoepassingen[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Beheerder van cloudtoepassingen, toepassingsbeheerder
Eigenschappen van bedrijfstoepassingen bijwerken | Eigenaar van ondernemingstoepassingen[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Beheerder van cloudtoepassingen, toepassingsbeheerder
Inrichting van bedrijfstoepassingen bijwerken | Eigenaar van ondernemingstoepassingen[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Beheerder van cloudtoepassingen, toepassingsbeheerder
Selfservice voor bedrijfstoepassingen bijwerken | Eigenaar van ondernemingstoepassingen[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Beheerder van cloudtoepassingen, toepassingsbeheerder
Eigenschappen voor eenmalig aanmelden bijwerken | Eigenaar van ondernemingstoepassingen[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Beheerder van cloudtoepassingen, toepassingsbeheerder

## <a name="entitlement-management"></a>Rechtenbeheer
Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Bronnen toevoegen aan een catalogus | Gebruikersbeheerder | Met beheer van rechten u deze taak delegeren aan de eigenaar van de catalogus[(zie documentatie)](../governance/entitlement-management-catalog-create.md#add-additional-catalog-owners)
SharePoint Online-sites toevoegen aan catalogus | Globale beheerder


## <a name="groups"></a>Groepen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Licentie toewijzen | Gebruikersbeheerder | 
Groep maken | Gebruikersbeheerder | 
Toegangscontrole van een groep of van een app maken, bijwerken of verwijderen | Gebruikersbeheerder | 
Groepsverloop beheren | Gebruikersbeheerder | 
Groepsinstellingen beheren | Groepsbeheerder | Gebruikersbeheerder | 
Alle configuratie lezen (behalve verborgen lidmaatschap) | Directorylezers | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)
Lees verborgen lidmaatschap | Groepslid | Groepseigenaar, wachtwoordbeheerder, Exchange-beheerder, SharePoint-beheerder, Teambeheerder, Gebruikersbeheerder
Lees lidmaatschap van groepen met verborgen lidmaatschap | Helpdeskbeheerder | Gebruikersbeheerder, Team-beheerder
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Groepslidmaatschap bijwerken | Groepseigenaar[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Gebruikersbeheerder
Groepseigenaren bijwerken | Groepseigenaar[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Gebruikersbeheerder
Groepseigenschappen bijwerken | Groepseigenaar[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | Gebruikersbeheerder

## <a name="identity-protection"></a>Identiteitsbeveiliging

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Waarschuwingsmeldingen configureren| Beveiligingsbeheer | 
Mfa-beleid configureren en inschakelen of uitschakelen| Beveiligingsbeheer | 
Aanmeldingsrisicobeleid configureren en inschakelen of uitschakelen| Beveiligingsbeheer | 
Gebruikersrisicobeleid configureren en uitschakelen | Beveiligingsbeheer | 
Wekelijkse samenvattingen configureren | Beveiligingsbeheer| 
Alle risicodetecties afwijzen | Beveiligingsbeheer | 
Kwetsbaarheid oplossen of verwijderen | Beveiligingsbeheer | 
Alle configuratie lezen | Beveiligingslezer | 
Lees alle risicodetecties | Beveiligingslezer | 
Kwetsbaarheden lezen | Beveiligingslezer | 

## <a name="licenses"></a>Licenties

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Licentie toewijzen | Licentiebeheerder | Gebruikersbeheerder
Alle configuratie lezen | Directorylezers | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions)
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Abonnement proberen of kopen | Factureringsbeheerder | 


## <a name="monitoring---audit-logs"></a>Monitoring - Controlelogboeken

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Controlelogboeken lezen | Rapporten lezer | Beveiligingslezer, beveiligingsbeheerder

## <a name="monitoring---sign-ins"></a>Controle - Aanmeldingen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Aanmeldingslogboeken lezen | Rapporten lezer | Beveiligingslezer, beveiligingsbeheerder

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Alle bestaande app-wachtwoorden die door de geselecteerde gebruikers zijn gegenereerd, verwijderen | Globale beheerder | 
MFA uitschakelen | Globale beheerder | 
MFA inschakelen | Globale beheerder | 
MFA-service-instellingen beheren | Globale beheerder | 
Geselecteerde gebruikers vereisen dat ze opnieuw contactmethoden verstrekken | Verificatiebeheerder | 
Multi-factor verificatie herstellen op alle onthouden apparaten  | Verificatiebeheerder | 

## <a name="mfa-server"></a>MFA-server

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Gebruikers blokkeren/deblokkeren | Globale beheerder | 
Accountuitsluiting configureren | Globale beheerder | 
Caching-regels configureren | Globale beheerder | 
Fraudewaarschuwing configureren | Globale beheerder
Meldingen configureren | Globale beheerder | 
Eenmalige bypass configureren | Globale beheerder | 
Instellingen voor bellen configureren | Globale beheerder | 
Providers configureren | Globale beheerder | 
Serverinstellingen configureren | Globale beheerder | 
Activiteitenrapport lezen | Globale lezer | 
Alle configuratie lezen | Globale lezer | 
Serverstatus lezen | Globale lezer |  

## <a name="organizational-relationships"></a>Organisatierelaties

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Identiteitsproviders beheren | Beheerder externe identiteitsprovider | 
Instellingen beheren | Globale beheerder | 
Gebruiksvoorwaarden beheren | Globale beheerder | 
Alle configuratie lezen | Globale lezer | 

## <a name="password-reset"></a>Wachtwoord opnieuw instellen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Verificatiemethoden configureren | Globale beheerder |
Aanpassing configureren | Globale beheerder |
Melding configureren | Globale beheerder |
On-premises integratie configureren | Globale beheerder |
Eigenschappen voor het opnieuw instellen van wachtwoorden configureren | Gebruikersbeheerder | Globale beheerder
Registratie configureren | Globale beheerder |
Alle configuratie lezen | Beveiligingsbeheer | Gebruikersbeheerder |

## <a name="privileged-identity-management"></a>Privileged Identity Management

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Gebruikers toewijzen aan rollen | Bevoegde rolbeheerder | 
Rolinstellingen configureren | Bevoegde rolbeheerder | 
Controleactiviteit weergeven | Beveiligingslezer | 
Rollidmaatschappen weergeven | Beveiligingslezer | 

## <a name="roles-and-administrators"></a>Rollen en beheerders

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Roltoewijzingen beheren | Bevoegde rolbeheerder | 
Toegangscontrole van een Azure AD-rol lezen  | Beveiligingslezer | Beveiligingsbeheerder, bevoegde rolbeheerder
Alle configuratie lezen | Standaardgebruikersrol[(zie documentatie)](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 

## <a name="security---authentication-methods"></a>Beveiliging - Verificatiemethoden

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Verificatiemethoden configureren | Globale beheerder | 
Alle configuratie lezen | Globale lezer | 

## <a name="security---conditional-access"></a>Beveiliging - Voorwaardelijke toegang

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
MFA vertrouwde IP-adressen configureren | Beheerder van voorwaardelijke toegang | 
Aangepaste besturingselementen maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Benoemde locaties maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Beleidsregels maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiksvoorwaarden maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
VPN-connectiviteitscertificaat maken | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Klassiek beleid verwijderen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiksvoorwaarden verwijderen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
VPN-connectiviteitscertificaat verwijderen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Klassiek beleid uitschakelen | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Aangepaste besturingselementen beheren | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Benoemde locaties beheren | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Gebruiksvoorwaarden beheren | Beheerder van voorwaardelijke toegang | Beveiligingsbeheerder
Alle configuratie lezen | Beveiligingslezer | Beveiligingsbeheerder
Benoemde locaties lezen | Beveiligingslezer | Beheerder voorwaardelijke toegang, beveiligingsbeheerder

## <a name="security---identity-security-score"></a>Beveiliging - Score voor identiteitsbeveiliging

Taak | Minst bevoorrechte rol | Extra rollen | 
---- | --------------------- | ----------------
Alle configuratie lezen | Beveiligingslezer | Beveiligingsbeheerder
Beveiligingsscore lezen | Beveiligingslezer | Beveiligingsbeheerder
Gebeurtenisstatus bijwerken | Beveiligingsbeheerder | 

## <a name="security---risky-sign-ins"></a>Beveiliging - Riskante aanmeldingen

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Alle configuratie lezen | Beveiligingslezer | 
Riskante aanmeldingen lezen | Beveiligingslezer | 

## <a name="security---users-flagged-for-risk"></a>Beveiliging - Gebruikers die zijn gemarkeerd voor risico's

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Alle gebeurtenissen sluiten | Beveiligingsbeheer | 
Alle configuratie lezen | Beveiligingslezer | 
Lees gebruikers gemarkeerd voor risico | Beveiligingslezer | 

## <a name="users"></a>Gebruikers

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Gebruiker toevoegen aan directoryrol | Bevoegde rolbeheerder | 
Gebruiker toevoegen aan groep | Gebruikersbeheerder | 
Licentie toewijzen | Licentiebeheerder | Gebruikersbeheerder
Gastgebruiker maken | Gast genodigden | Gebruikersbeheerder
Gebruiker maken | Gebruikersbeheerder | 
Gebruikers verwijderen | Gebruikersbeheerder | 
Vernieuwingstokens van beperkte beheerders ongeldig maken (zie documentatie) | Gebruikersbeheerder | 
Vernieuwingstokens van niet-beheerders ongeldig maken (zie documentatie) | Wachtwoordbeheerder | Gebruikersbeheerder
Vernieuwingstokens van bevoorrechte beheerders ongeldig maken (zie documentatie) | Beheerder van geprivilegieerde verificatie | 
Basisconfiguratie lezen | Standaardgebruikersrol[(zie documentatie](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions) | 
Wachtwoord opnieuw instellen voor beperkte beheerders (zie documentatie) | Gebruikersbeheerder | 
Wachtwoord van niet-beheerders opnieuw instellen (zie documentatie) | Wachtwoordbeheerder | Gebruikersbeheerder
Wachtwoord van bevoegde beheerders opnieuw instellen | Beheerder van geprivilegieerde verificatie | 
Licentie intrekken | Licentiebeheerder | Gebruikersbeheerder
Alle eigenschappen bijwerken, behalve de naam van de gebruikersnaam | Gebruikersbeheerder | 
Gebruikersnaam bijwerken voor beperkte beheerders (zie documentatie) | Gebruikersbeheerder | 
Eigenschap Gebruikersnaam bijwerken op bevoorrechte beheerders (zie documentatie) | Globale beheerder | 
Gebruikersinstellingen bijwerken | Globale beheerder | 


## <a name="support"></a>Ondersteuning

Taak | Minst bevoorrechte rol | Extra rollen
---- | --------------------- | ----------------
Ondersteuningsticket verzenden | Servicebeheerder | Application Administrator, Azure Information Protection Administrator, Billing Administrator, Cloud Application Administrator, Compliance Administrator, Dynamics 365 Administrator, Desktop Analytics Administrator, Exchange Administrator, Password Administrator, Intune Administrator, Skype for Business Administrator, Power BI Administrator, Privileged Authentication Administrator, SharePoint Administrator, Teams Communications Administrator, Teams Administrator, User Administrator, Workplace Analytics Administrator

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-beheerdersrollen toewijzen of verwijderen](directory-manage-roles-portal.md)
* [Naslaginformatie over Azure AD-beheerdersrollen](directory-assign-admin-roles.md)
