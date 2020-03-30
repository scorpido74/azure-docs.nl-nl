---
title: Standaardgebruikersmachtigingen - Azure Active Directory | Microsoft Documenten
description: Meer informatie over de verschillende gebruikersmachtigingen die beschikbaar zijn in Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: ajburnle
ms.reviewer: vincesm
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 227230f2d6f46fae27e2cec69d99390f5054c7db
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366260"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Wat zijn de standaardgebruikersmachtigingen in Azure Active Directory?
In Azure Active Directory (Azure AD) wordt aan alle gebruikers een reeks standaardmachtigingen verleend. De toegang van een gebruiker bestaat uit het type gebruiker, de [roltoewijzingen](active-directory-users-assign-role-azure-portal.md)en het eigendom van afzonderlijke objecten. Dit artikel beschrijft deze standaardmachtigingen en bevat een vergelijking van de standaardinstellingen voor lid- en gastgebruikers. De standaardgebruikersmachtigingen kunnen alleen worden gewijzigd in gebruikersinstellingen in Azure AD.

## <a name="member-and-guest-users"></a>Lid- en gastgebruikers
De set ontvangen standaardmachtigingen is afhankelijk van de vraag of de gebruiker een native lid van de tenant is (lidgebruiker) of dat de gebruiker uit een andere map wordt overgehaald als b2b-samenwerkingsgast (gastgebruiker). Zie [Wat is Azure AD B2B-samenwerking?](../b2b/what-is-b2b.md)
* Lidgebruikers kunnen toepassingen registreren, hun eigen profielfoto en mobiele telefoonnummer beheren, hun eigen wachtwoord wijzigen en B2B-gasten uitnodigen. Gebruikers kunnen bovendien (op een paar uitzonderingen na) alle directorygegevens lezen. 
* Gastgebruikers hebben beperkte directorymachtigingen. Gastgebruikers kunnen bijvoorbeeld afgezien van hun eigen profielgegevens niet bladeren door informatie van de tenant. Een gastgebruiker kan echter informatie over een andere gebruiker ophalen door de Principal-naam van gebruiker of de object-id te verstrekken. Een gastgebruiker kan eigenschappen lezen van groepen waartoe hij behoort, inclusief groepslidmaatschap, ongeacht de machtigingen van **gastgebruikers die beperkt zijn.** Een gast kan geen informatie over andere tenantobjecten bekijken.

Standaardmachtigingen voor gasten zijn standaard beperkt. Gasten kunnen worden toegevoegd aan beheerdersrollen, waarmee aan hen volledige lees- en schrijftoegang voor de rol wordt verleend. Er is een aanvullende beperking beschikbaar, namelijk de mogelijkheid voor gasten om andere gasten uit te nodigen. Als de instelling **Gasten kunnen uitnodigingen versturen** op **Nee** wordt ingesteld, kunnen gasten geen andere gasten uitnodigen. Zie [Uitnodigingen delegeren voor B2B-samenwerking](../b2b/delegate-invitations.md) voor meer informatie. Als u gastgebruikers standaard dezelfde machtigingen wilt verlenen als lidgebruikers, stelt u **De machtigingen van gastgebruikers zijn beperkt** in op **Nee**. Met deze instelling worden alle gebruikersmachtigingen van leden standaard verleend aan gastgebruikers, en is het ook toegestaan gastgebruikers toe te voegen aan beheerdersrollen.

## <a name="compare-member-and-guest-default-permissions"></a>Standaardmachtigingen voor leden en gasten vergelijken

**Onderwerp** | **Gebruikersmachtigingen voor leden** | **Gebruikersmachtigingen voor gasten**
------------ | --------- | ----------
Gebruikers en contactpersonen | Alle openbare eigenschappen lezen van gebruikers en contactpersonen<br>Gasten uitnodigen<br>Eigen wachtwoord wijzigen<br>Eigen mobiele nummer beheren<br>Eigen foto beheren<br>Eigen vernieuwingstekens ongeldig verklaren | Eigen eigenschappen lezen<br>Displaynaam, e-mail, naam van het aanmeldingsteken, foto, gebruikersnaam en gebruikerstype-eigenschappen van andere gebruikers en contactpersonen lezen<br>Eigen wachtwoord wijzigen
Groepen | Beveiligingsgroepen maken<br>Office 365-groepen maken<br>Alle eigenschappen van groepen lezen<br>Niet-verborgen groepslidmaatschappen lezen<br>Verborgen Office 365-groepslidmaatschappen voor gekoppelde groep lezen<br>Eigenschappen beheren, eigendom en lidmaatschap van groepen waarvan de gebruiker eigenaar is<br>Gasten toevoegen aan groepen in eigendom<br>Instellingen voor dynamisch lidmaatschap beheren<br>Groepen in eigendom verwijderen<br>Office 365-groepen in eigendom herstellen | Alle eigenschappen van groepen lezen<br>Niet-verborgen groepslidmaatschappen lezen<br>Verborgen Office 365-groepslidmaatschappen voor gekoppelde groepen lezen<br>Groepen in eigendom beheren<br>Gasten toevoegen aan groepen in eigendom (indien toegestaan)<br>Groepen in eigendom verwijderen<br>Office 365-groepen in eigendom herstellen<br>Lees eigenschappen van groepen waartoe ze behoren, inclusief lidmaatschap.
Toepassingen | Nieuwe toepassing registreren (maken)<br>Eigenschappen van geregistreerde en bedrijfstoepassingen lezen<br>Eigenschappen, toewijzingen en referenties van toepassingen beheren voor toepassingen in eigendom<br>Toepassingswachtwoord voor gebruiker maken of verwijderen<br>Toepassingen in eigendom verwijderen<br>Toepassingen in eigendom herstellen | Eigenschappen van geregistreerde en bedrijfstoepassingen lezen<br>Eigenschappen, toewijzingen en referenties van toepassingen beheren voor toepassingen in eigendom<br>Toepassingen in eigendom verwijderen<br>Toepassingen in eigendom herstellen
Apparaten | Alle eigenschappen van apparaten lezen<br>Alle eigenschappen van apparaten in eigendom lezen<br> | Geen machtigingen<br>Apparaten in eigendom verwijderen<br>
Directory | Alle bedrijfsgegevens lezen<br>Alle domeinen lezen<br>Alle partnercontracten lezen | Weergavenaam en geverifieerde domeinen lezen
Rollen en bereiken | Alle beheerdersrollen en lidmaatschappen lezen<br>Alle eigenschappen en het lidmaatschap van beheereenheden lezen | Geen machtigingen 
Abonnementen | Alle abonnementen lezen<br>Serviceplanlid inschakelen | Geen machtigingen
Beleidsregels | Alle eigenschappen van beleid lezen<br>Alle eigenschappen van beleid in eigendom lezen | Geen machtigingen

## <a name="to-restrict-the-default-permissions-for-member-users"></a>De standaardmachtigingen voor lidgebruikers beperken

Standaardmachtigingen voor lidgebruikers kunnen op de volgende manieren worden beperkt.

Machtiging | Uitleg van de instelling
---------- | ------------
Gebruikers kunnen toepassing registreren | Als u deze optie instelt op Nee, kunnen gebruikers toepassingsregistraties maken. De mogelijkheid kan vervolgens worden toegekend terug naar specifieke personen door ze toe te voegen aan de rol Application Developer.
Gebruikers toestaan werk- of schoolaccount te koppelen aan LinkedIn | Als u deze optie instelt op Nee, kunnen gebruikers hun werk- of schoolaccount koppelen aan hun LinkedIn-account. Zie [LinkedIn-accountverbindingen delen en toestemming voor meer informatie voor LinkedIn-accountverbindingen.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent)
De mogelijkheid beveiligingsgroepen te maken | Als u deze optie op Nee instelt, kunnen gebruikers geen beveiligingsgroepen maken. Globale beheerders en gebruikersbeheerders kunnen nog steeds beveiligingsgroepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../users-groups-roles/groups-settings-cmdlets.md) voor meer informatie.
De mogelijkheid Office 365-groepen te maken | Als u deze optie op Nee instelt, kunnen gebruikers geen Office 365-groepen maken. Als u deze optie op Sommige instelt, kan een beperkte selectie van gebruikers Office 365-groepen maken. Globale beheerders en gebruikersbeheerders kunnen nog steeds Office 365-groepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../users-groups-roles/groups-settings-cmdlets.md) voor meer informatie.
De toegang tot de Azure AD-beheerportal beperken | Als u deze optie instelt op Nee, kunnen niet-beheerders de Azure AD-beheerportal gebruiken om Azure AD-resources te lezen en te beheren. Ja beperkt alle niet-beheerders om toegang te krijgen tot Azure AD-gegevens in de beheerportal. Belangrijk om op te merken: deze instelling beperkt de toegang tot Azure AD-gegevens niet met PowerShell of andere clients zoals Visual Studio. Wanneer u bent ingesteld op Ja, u een specifieke niet-beheerdersgebruiker de mogelijkheid bieden om de Azure AD-beheerportal te gebruiken, elke beheerrol toe te wijzen, zoals de rol DirectoryReaders. Met deze rol u basisgegevens van de map lezen, die gebruikers standaard hebben (gasten en serviceprincipals niet).
Mogelijkheid om andere gebruikers te lezen | Deze instelling is alleen beschikbaar in PowerShell. Als u deze vlag instelt op $false voorkomt u dat alle niet-beheerders gebruikersgegevens uit de map kunnen lezen. Deze vlag voorkomt niet dat gebruikersinformatie wordt gelezen in andere Microsoft-services, zoals Exchange Online. Deze instelling is bedoeld voor speciale omstandigheden en het instellen van deze vlag op $false wordt niet aanbevolen.

## <a name="object-ownership"></a>Eigendom van objecten

### <a name="application-registration-owner-permissions"></a>Eigenaarsmachtigingen toepassingsregistratie
Wanneer een gebruiker een toepassing registreert, wordt deze automatisch toegevoegd als een eigenaar voor de toepassing. Als eigenaar kan de gebruiker de metagegevens van de toepassing beheren, zoals de naam en de machtigingen waarom de app verzoekt. De gebruiker kan ook de tenantspecifieke configuratie van de toepassing beheren, zoals de SSO-toewijzingen voor configuratie en gebruikersbestanden. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen toepassingen beheren waarvan ze eigenaar zijn.

### <a name="enterprise-application-owner-permissions"></a>Machtigingen voor de eigenaar van de bedrijfstoepassing
Wanneer een gebruiker een nieuwe bedrijfstoepassing toevoegt, worden deze automatisch toegevoegd als eigenaar. Als eigenaar kunnen ze de tenantspecifieke configuratie van de toepassing beheren, zoals de SSO-configuratie, inrichting en gebruikerstoewijzingen. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen de toepassingen beheren die ze bezitten.

### <a name="group-owner-permissions"></a>Machtigingen groepseigenaar
Wanneer een gebruiker een groep maakt, wordt deze automatisch toegevoegd als een eigenaar voor die groep. Als eigenaar kunnen ze eigenschappen van de groep beheren, zoals de naam, en groepslidmaatschap beheren. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders en gebruikersbeheerders kunnen eigenaren alleen groepen beheren die eigenaar zijn. Zie [Eigenaren beheren voor een groep](active-directory-accessmanagement-managing-group-owners.md) voor informatie over het toewijzen van een groepseigenaar.

### <a name="ownership-permissions"></a>Eigendomsmachtigingen
In de volgende tabellen worden de specifieke machtigingen beschreven die gebruikers van Azure Active Directory-leden hebben over eigendom van objecten. De gebruiker heeft deze machtigingen alleen op objecten die ze bezitten.

#### <a name="owned-application-registrations"></a>Eigen registraties van eigen gegevens
Gebruikers kunnen de volgende acties uitvoeren op registraties van eigen apparaten.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/applications/audience/update | Eigenschap applications.audience bijwerken in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Eigenschap applications.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Basiseigenschappen bijwerken voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Eigenschap applications.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/applications/delete | Toepassingen verwijderen in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Eigenschap applications.owners bijwerken in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Eigenschap applications.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Eigenschap applications.policies bijwerken in Azure Active Directory. |
| microsoft.directory/applications/restore | Toepassingen herstellen in Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Bedrijfsapplicaties in eigendom
Gebruikers kunnen de volgende acties uitvoeren op bedrijfstoepassingen die eigendom zijn. Een bedrijfstoepassing bestaat uit serviceprincipal, een of meer toepassingsbeleidsregels en soms een toepassingsobject in dezelfde tenant als de serviceprincipal.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Basiseigenschappen bijwerken voor beleidsregels in Azure Active Directory. |
| microsoft.directory/policies/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Eigenschap van updatepolicies.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | ServicePrincipals.appRoleAssignedTo-eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Gebruikers.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Eigenschap servicePrincipals.audience bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Eigenschap servicePrincipals.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen bijwerken op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Eigenschap servicePrincipals.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | ServicePrincipals verwijderen in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Eigenschap servicePrincipals.owners bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Eigenschap servicePrincipals.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Eigenschap servicePrincipals.policies bijwerken in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |

#### <a name="owned-devices"></a>Eigen apparaten
Gebruikers kunnen de volgende acties uitvoeren op apparaten die eigendom zijn.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/disable | Apparaten uitschakelen in Azure Active Directory. |

#### <a name="owned-groups"></a>Eigen groepen
Gebruikers kunnen de volgende acties uitvoeren op eigen groepen.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Groepen.appRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Basiseigenschappen bijwerken op groepen in Azure Active Directory. |
| microsoft.directory/groups/delete | Groepen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Groepen.dynamicMembershipRule-eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/update | Eigenaren van groepen bijwerken in Azure Active Directory. |
| microsoft.directory/groups/restore | Groepen herstellen in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Groep.settings bijwerken in Azure Active Directory. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Een gebruiker toewijzen aan beheerdersrollen in Azure Active Directory](active-directory-users-assign-role-azure-portal.md) voor meer informatie over het toewijzen van Azure AD-beheerdersrollen.
* Als u meer wilt weten over hoe de toegang tot resources wordt beheerd in Microsoft Azure, ziet u [Inzicht krijgen in toegang tot resources in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) voor meer informatie over hoe Azure Active Directory aan uw Azure-abonnement wordt gekoppeld
* [Gebruikers beheren](add-users-azure-active-directory.md)
