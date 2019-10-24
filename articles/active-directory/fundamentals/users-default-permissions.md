---
title: Standaard gebruikersmachtigingen - Azure Active Directory | Microsoft Docs
description: Meer informatie over de andere gebruikersmachtigingen beschikbaar zijn in Azure Active Directory.
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
ms.openlocfilehash: 99ceb72930d756926bbc73508b20fbde8ea31324
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561604"
---
# <a name="what-are-the-default-user-permissions-in-azure-active-directory"></a>Wat zijn de standaardmachtigingen van de gebruiker in Azure Active Directory?
In Azure Active Directory (Azure AD) wordt aan alle gebruikers een reeks standaardmachtigingen verleend. De toegang van een gebruiker bestaat uit het type gebruiker, hun [roltoewijzingen](active-directory-users-assign-role-azure-portal.md)en hun eigendom van afzonderlijke objecten. Dit artikel beschrijft deze standaardmachtigingen en bevat een vergelijking van de standaardinstellingen voor lid- en gastgebruikers. De standaard gebruikers machtigingen kunnen alleen worden gewijzigd in gebruikers instellingen in azure AD.

## <a name="member-and-guest-users"></a>Lid- en gastgebruikers
De set standaard machtigingen die worden ontvangen, is afhankelijk van het feit of de gebruiker een native lid is van de Tenant (lid van de gebruiker) of dat de gebruiker wordt overgezet van een andere directory als B2B-samenwerkings gast (gast gebruiker). Zie [Wat is Azure AD B2B-samen werking?](../b2b/what-is-b2b.md) voor meer informatie over het toevoegen van gast gebruikers.
* Lidgebruikers kunnen toepassingen registreren, hun eigen profielfoto en mobiele telefoonnummer beheren, hun eigen wachtwoord wijzigen en B2B-gasten uitnodigen. Gebruikers kunnen bovendien (op een paar uitzonderingen na) alle directorygegevens lezen. 
* Gast gebruikers hebben beperkte mapmachtigingen. Gastgebruikers kunnen bijvoorbeeld afgezien van hun eigen profielgegevens niet bladeren door informatie van de tenant. Een gastgebruiker kan echter informatie over een andere gebruiker ophalen door de Principal-naam van gebruiker of de object-id te verstrekken. Een gast gebruiker kan eigenschappen lezen van de groepen waartoe ze behoren, met inbegrip van groepslid maatschappen, ongeacht de **machtigingen voor gast gebruikers** . Een gast kan geen informatie weer geven over andere Tenant objecten.

Standaardmachtigingen voor gasten zijn standaard beperkt. Gasten kunnen worden toegevoegd aan beheerdersrollen, waarmee aan hen volledige lees- en schrijftoegang voor de rol wordt verleend. Er is een aanvullende beperking beschikbaar, namelijk de mogelijkheid voor gasten om andere gasten uit te nodigen. Als de instelling **Gasten kunnen uitnodigingen versturen** op **Nee** wordt ingesteld, kunnen gasten geen andere gasten uitnodigen. Zie [Uitnodigingen delegeren voor B2B-samenwerking](../b2b/delegate-invitations.md) voor meer informatie. Als u gastgebruikers standaard dezelfde machtigingen wilt verlenen als lidgebruikers, stelt u **De machtigingen van gastgebruikers zijn beperkt** in op **Nee**. Met deze instelling worden alle gebruikersmachtigingen van leden standaard verleend aan gastgebruikers, en is het ook toegestaan gastgebruikers toe te voegen aan beheerdersrollen.

## <a name="compare-member-and-guest-default-permissions"></a>Standaardmachtigingen voor leden en gasten vergelijken

**Onderwerp** | **Gebruikersmachtigingen voor leden** | **Gebruikersmachtigingen voor gasten**
------------ | --------- | ----------
Gebruikers en contactpersonen | Alle openbare eigenschappen lezen van gebruikers en contactpersonen<br>Gasten uitnodigen<br>Eigen wachtwoord wijzigen<br>Eigen mobiele nummer beheren<br>Eigen foto beheren<br>Eigen vernieuwingstekens ongeldig verklaren | Eigen eigenschappen lezen<br>Weergave naam, e-mail adres, aanmeldings naam, foto, user principal name en gebruikers type-eigenschappen van andere gebruikers en contact personen lezen<br>Eigen wachtwoord wijzigen
Groepen | Beveiligingsgroepen maken<br>Office 365-groepen maken<br>Alle eigenschappen van groepen lezen<br>Niet-verborgen groepslidmaatschappen lezen<br>Verborgen Office 365-groepslidmaatschappen voor gekoppelde groep lezen<br>Eigenschappen, eigendom en lidmaatschap van groepen beheren waarvan de gebruiker eigenaar is<br>Gasten toevoegen aan groepen in eigendom<br>Instellingen voor dynamisch lidmaatschap beheren<br>Groepen in eigendom verwijderen<br>Office 365-groepen in eigendom herstellen | Alle eigenschappen van groepen lezen<br>Niet-verborgen groepslidmaatschappen lezen<br>Verborgen Office 365-groepslidmaatschappen voor gekoppelde groepen lezen<br>Groepen in eigendom beheren<br>Gasten toevoegen aan groepen in eigendom (indien toegestaan)<br>Groepen in eigendom verwijderen<br>Office 365-groepen in eigendom herstellen<br>Lees de eigenschappen van de groepen waartoe ze behoren, inclusief het lidmaatschap.
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
Gebruikers kunnen de toepassing registreren | Als u deze optie instelt op Nee, voor komt u dat gebruikers toepassings registraties kunnen maken. De mogelijkheid kan vervolgens worden verleend aan specifieke personen door deze toe te voegen aan de ontwikkelaar van de toepassing.
Gebruikers toestaan om hun werk- of schoolaccounts te verbinden met LinkedIn | Als u deze optie instelt op Nee, kunnen gebruikers hun werk-of school account niet verbinden met hun LinkedIn-account. Zie voor meer informatie [LinkedIn-account verbindingen gegevens delen en toestemming](https://docs.microsoft.com/azure/active-directory/users-groups-roles/linkedin-user-consent).
De mogelijkheid beveiligingsgroepen te maken | Als u deze optie op Nee instelt, kunnen gebruikers geen beveiligingsgroepen maken. Globale beheerders en gebruikers beheerders kunnen nog steeds beveiligings groepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../users-groups-roles/groups-settings-cmdlets.md) voor meer informatie.
De mogelijkheid Office 365-groepen te maken | Als u deze optie op Nee instelt, kunnen gebruikers geen Office 365-groepen maken. Als u deze optie op Sommige instelt, kan een beperkte selectie van gebruikers Office 365-groepen maken. Globale beheerders en gebruikers beheerders kunnen nog steeds Office 365-groepen maken. Zie [Azure Active Directory-cmdlets voor het configureren van groepsinstellingen](../users-groups-roles/groups-settings-cmdlets.md) voor meer informatie.
De toegang tot de Azure AD-beheerportal beperken | Als u deze optie instelt op Ja, voor komt u dat gebruikers alleen toegang hebben tot Azure Active Directory via Azure Portal.
Mogelijkheid om andere gebruikers te lezen | Deze instelling is alleen beschikbaar in PowerShell. Als u deze vlag instelt op $false, voor komt u dat alle niet-beheerders gebruikers informatie uit de map lezen. Met deze markering wordt het lezen van gebruikers gegevens in andere micro soft-Services, zoals Exchange Online, niet voor komen. Deze instelling is bedoeld voor speciale omstandigheden en het instellen van deze markering op $false wordt niet aanbevolen.

## <a name="object-ownership"></a>Eigendom van objecten

### <a name="application-registration-owner-permissions"></a>Eigenaarsmachtigingen toepassingsregistratie
Wanneer een gebruiker een toepassing registreert, wordt deze automatisch toegevoegd als een eigenaar voor de toepassing. Als eigenaar kan de gebruiker de metagegevens van de toepassing beheren, zoals de naam en de machtigingen waarom de app verzoekt. De gebruiker kan ook de tenantspecifieke configuratie van de toepassing beheren, zoals de SSO-toewijzingen voor configuratie en gebruikersbestanden. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegenstelling tot globale beheerders kunnen eigenaren alleen toepassingen beheren waarvan ze eigenaar zijn.

### <a name="enterprise-application-owner-permissions"></a>Machtigingen voor de eigenaar van de bedrijfs toepassing
Wanneer een gebruiker een nieuwe bedrijfs toepassing toevoegt, worden deze automatisch toegevoegd als een eigenaar. Als eigenaar kunnen ze de Tenant configuratie van de toepassing beheren, zoals de SSO-configuratie, het inrichten en de gebruikers toewijzingen. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegens telling tot globale beheerders kunnen eigen aren alleen de toepassingen beheren waarvan ze eigenaar zijn.

### <a name="group-owner-permissions"></a>Machtigingen groepseigenaar
Wanneer een gebruiker een groep maakt, wordt deze automatisch toegevoegd als een eigenaar voor die groep. Als eigenaar kunnen ze de eigenschappen van de groep, zoals de naam, beheren en groepslid maatschap beheren. Een eigenaar kan ook andere eigenaren toevoegen of verwijderen. In tegens telling tot globale beheerders en gebruikers beheerders kunnen eigen aren alleen groepen beheren waarvan ze eigenaar zijn. Zie [Eigenaren beheren voor een groep](active-directory-accessmanagement-managing-group-owners.md) voor informatie over het toewijzen van een groepseigenaar.

### <a name="ownership-permissions"></a>Eigendoms machtigingen
De volgende tabellen bevatten een beschrijving van de specifieke machtigingen in Azure Active Directory gebruikers die lid zijn van hun objecten. De gebruiker heeft alleen deze machtigingen voor objecten waarvan ze eigenaar zijn.

#### <a name="owned-application-registrations"></a>Toepassings registraties in eigendom
Gebruikers kunnen de volgende acties uitvoeren op toepassings registraties van de eigenaar.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/applications/audience/update | Werk de eigenschap applications.audience bij in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Werk de eigenschap applications.authentication bij in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Werk de basiseigenschappen voor toepassingen bij in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Werk de eigenschap applications.credentials bij in Azure Active Directory. |
| microsoft.directory/applications/delete | Verwijder toepassingen in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Werk de eigenschap applications.owners bij in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Werk de eigenschap applications.permissions bij in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Werk de eigenschap applications.policies bij in Azure Active Directory. |
| microsoft.directory/applications/restore | Herstel toepassingen in Azure Active Directory. |

#### <a name="owned-enterprise-applications"></a>Bedrijfs toepassingen in eigendom
Gebruikers kunnen de volgende acties uitvoeren op bedrijfs toepassingen die eigendom zijn van de onderneming. Een bedrijfs toepassing bestaat uit Service-Principal, een of meer toepassings beleid en soms een toepassings object in dezelfde Tenant als de Service-Principal.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Alle eigenschappen (inclusief bevoegde eigenschappen) lezen in auditLogs in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Werk de basiseigenschappen voor beleid bij in Azure Active Directory. |
| microsoft.directory/policies/delete | Verwijder beleid in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Werk de eigenschap policies.owners bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | Werk de eigenschap servicePrincipals.appRoleAssignedTo bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | Werk de eigenschap users.appRoleAssignments bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | De eigenschap servicePrincipals.audience bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | De eigenschap servicePrincipals.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Werk de basiseigenschappen voor servicePrincipals bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | De eigenschap servicePrincipals.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | Verwijder servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Werk de eigenschap servicePrincipals.owners bij in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | De eigenschap servicePrincipals.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Werk de eigenschap servicePrincipals.policies bij in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Alle eigenschappen (inclusief bevoegde eigenschappen) lezen in signInReports in Azure Active Directory. |

#### <a name="owned-devices"></a>Apparaten in eigendom
Gebruikers kunnen de volgende acties uitvoeren op apparaten die eigendom zijn van het apparaat.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | De eigenschap devices.bitLockerRecoveryKeyss lezen in Azure Active Directory. |
| microsoft.directory/devices/disable | Schakel apparaten uit in Azure Active Directory. |

#### <a name="owned-groups"></a>Groepen in eigendom
Gebruikers kunnen de volgende acties uitvoeren op groepen in eigendom.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/groups/appRoleAssignments/update | Werk de eigenschap groups.appRoleAssignments bij in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Werk de basiseigenschappen voor groepen bij in Azure Active Directory. |
| microsoft.directory/groups/delete | Verwijder groepen in Azure Active Directory. |
| microsoft.directory/groups/dynamicMembershipRule/update | Werk de eigenschap groups.dynamicMembershipRule bij in Azure Active Directory. |
| microsoft.directory/groups/members/update | Werk de eigenschap groups.members bij in Azure Active Directory. |
| microsoft.directory/groups/owners/update | Werk de eigenschap groups.owners bij in Azure Active Directory. |
| microsoft.directory/groups/restore | Herstel groepen in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Werk de eigenschap groups.settings bij in Azure Active Directory. |

## <a name="next-steps"></a>Volgende stappen

* Zie voor meer informatie over hoe u Azure AD-beheerdersrollen toewijzen [een gebruiker toewijzen aan beheerdersrollen in Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
* Als u meer wilt weten over hoe de toegang tot resources wordt beheerd in Microsoft Azure, ziet u [Inzicht krijgen in toegang tot resources in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](active-directory-how-subscriptions-associated-directory.md) voor meer informatie over hoe Azure Active Directory aan uw Azure-abonnement wordt gekoppeld
* [Gebruikers beheren](add-users-azure-active-directory.md)
