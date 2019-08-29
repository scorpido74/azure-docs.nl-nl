---
title: Referentie voor auditactiviteiten van Azure Active Directory (Azure AD) | Microsoft Docs
description: Overzicht van de auditactiviteiten die kunnen worden geregistreerd in de auditlogboeken van Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56a94ef3f6ea5737b629efe44bd3e1cc1f36a693
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127131"
---
# <a name="azure-ad-audit-activity-reference"></a>Referentie voor auditactiviteiten van Azure AD

Met Azure Active Directory-rapporten (Azure AD) kunt u de gegevens ophalen die u nodig hebt om te bepalen hoe uw omgeving wordt uitgevoerd.

De rapportstructuur in Azure AD bestaat uit de volgende onderdelen:

- **Activiteitsrapporten** 
    - [](concept-sign-ins.md) Aanmeldingen: bevat informatie over het gebruik van beheerde toepassingen en aanmeldings activiteiten voor gebruikers
    - [Auditlogboeken](concept-audit-logs.md): traceerbaarheid via logboeken voor alle door diverse functies binnen Azure AD uitgevoerde wijzigingen. 
    
- **Beveiligingsrapporten** 
    - [Riskante aanmeldingen](concept-risky-sign-ins.md) - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is. 
    - [Gebruikers voor wie wordt aangegeven dat ze risico lopen](concept-user-at-risk.md) - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast. 

In dit artikel worden de auditactiviteiten vermeld die in uw auditlogboeken kunnen worden geregistreerd.

## <a name="access-reviews"></a>Toegangsbeoordelingen

|Controlecategorie|Activiteit|
|---|---|
|Toegangsbeoordelingen|Toegangsbeoordeling beëindigd|
|Toegangsbeoordelingen|Goedkeurder toevoegen om goedkeuring te vragen|
|Toegangsbeoordelingen|Revisor toevoegen voor toegang tot revisie|
|Toegangsbeoordelingen|Toegangsbeoordeling toepassen|
|Toegangsbeoordelingen|Toegangsbeoordeling maken|
|Toegangsbeoordelingen|Programma maken|
|Toegangsbeoordelingen|Aanvraaggoedkeuring maken|
|Toegangsbeoordelingen|Toegangsbeoordeling verwijderen|
|Toegangsbeoordelingen|Programma verwijderen|
|Toegangsbeoordelingen|Programmabeheer koppelen|
|Toegangsbeoordelingen|Onboarding bij Azure AD-toegangsbeoordelingen|
|Toegangsbeoordelingen|Revisor verwijderen uit toegangsbeoordeling|
|Toegangsbeoordelingen|Stoppen beoordeling aanvragen|
|Toegangsbeoordelingen|Toepassen van beoordelingsresultaat aanvragen|
|Toegangsbeoordelingen|RBAC-rollidmaatschap beoordelen|
|Toegangsbeoordelingen|App-toewijzing beoordelen|
|Toegangsbeoordelingen|Groepslidmaatschap beoordelen|
|Toegangsbeoordelingen|Aanvraag voor goedkeuring aanvraag beoordelen|
|Toegangsbeoordelingen|Programmabeheer ontkoppelen|
|Toegangsbeoordelingen|Toegangsbeoordeling bijwerken|
|Toegangsbeoordelingen|Status van de onboarding van Azure AD-toegangs beoordelingen bijwerken|
|Toegangsbeoordelingen|Instellingen voor de e-mailmelding van de toegangsbeoordeling bijwerken|
|Toegangsbeoordelingen|Instelling voor aantal herhalingen van de toegangs beoordeling bijwerken|
|Toegangsbeoordelingen|Instelling voor toegangs beoordeling duur van terugkeer patroon in dagen bijwerken|
|Toegangsbeoordelingen|Instelling van eind type van terugkeer patroon van toegangs beoordeling bijwerken|
|Toegangsbeoordelingen|Instelling voor toegangs beoordeling van type terugkeer patroon bijwerken|
|Toegangsbeoordelingen|Instellingen voor de herinnering van de toegangsbeoordeling bijwerken|
|Toegangsbeoordelingen|Programma bijwerken|
|Toegangsbeoordelingen|Aanvraaggoedkeuring bijwerken|
|Toegangsbeoordelingen|Gebruiker uitgeschakeld|

## <a name="account-provisioning"></a>Account inrichten

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|V2-toepassingsmachtigingen ophalen|
|Toepassingsbeheer|V2-toepassingsservice-principals in de huidige tenant ophalen|
|Toepassingsbeheer|V1-toepassing bijwerken|
|Toepassingsbeheer|V2-toepassing bijwerken|
|Toepassingsbeheer|V2-toepassingsmachtiging bijwerken|
|Toepassingsbeheer|OAuth2PermissionGrant toevoegen|
|Toepassingsbeheer|App-roltoewijzing aan service-principal toevoegen|

## <a name="application-proxy"></a>Toepassingsproxy

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|Toepassing toevoegen|
|Toepassingsbeheer|Eigenaar aan toepassing toevoegen|
|Toepassingsbeheer|Eigenaar aan service-principal toevoegen|
|Toepassingsbeheer|Beleid aan service-principal toevoegen|
|Directory Management|Service-principal toevoegen|
|Directory Management|Referenties voor service-principal toevoegen|
|Directory Management|Instemmen met toepassing|
|Directory Management|Toepassing verwijderen|
|Directory Management|Toepassing definitief verwijderen|
|Directory Management|OAuth2PermissionGrant verwijderen|
|Directory Management|App-roltoewijzing uit service-principal verwijderen|
|Directory Management|Eigenaar uit toepassing verwijderen|
|Resource|Eigenaar uit service-principal verwijderen|
|Resource|Beleid uit service-principal verwijderen|
|Resource|Service-principal verwijderen|


## <a name="automated-password-rollover"></a>Automatische wachtwoordoverschakeling

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|Referenties voor service-principal verwijderen|


## <a name="b2c"></a>B2C

|Controlecategorie|Activiteit|
|---|---|
|Toepassingsbeheer|Toepassing herstellen|
|Toepassingsbeheer|Toestemming intrekken|
|Toepassingsbeheer|Toepassing bijwerken|
|Toepassingsbeheer|Externe geheimen bijwerken|
|Toepassingsbeheer|Service-principal bijwerken|
|Toepassingsbeheer|Een toegangstoken voor de toepassing uitgeven|
|Toepassingsbeheer|Een autorisatiecode aan de toepassing uitgeven|
|Toepassingsbeheer|Een id_token voor de toepassing uitgeven|
|Toepassingsbeheer|De referenties voor lokale accounts valideren|
|Toepassingsbeheer|Verificatie van de gebruiker valideren|
|Toepassingsbeheer|V2-toepassingsmachtigingen toevoegen|
|Toepassingsbeheer|Een sleutel op basis van een ASCII-geheim aan een CPIM-sleutelcontainer toevoegen|
|Toepassingsbeheer|Een sleutel aan een CPIM-sleutelcontainer toevoegen|
|Toepassingsbeheer|AdminPolicyDatas-SetResources|
|Toepassingsbeheer|AdminUserJourneys-GetResources|
|Toepassingsbeheer|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|IdentityProvider maken|
|Authentication|V1-toepassing maken|
|Authentication|V2-toepassing maken|
|Authentication|Aangepaste domeinen in de tenant maken|
|Authorization|Een nieuwe AdminUserJourney maken|
|Authorization|Gelokaliseerde resource-json maken|
|Authorization|Nieuwe aangepaste IDP maken|
|Authorization|Nieuwe IDP maken|
|Authorization|Een B2C-mapresource maken of bijwerken|
|Authorization|Beleid maken|
|Authorization|TrustFramework-beleid maken|
|Authorization|TrustFramework-beleid maken met configureerbaar voorvoegsel|
|Authorization|Gebruikerskenmerk maken|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Een nieuwe AdminUserJourney maken of bijwerken|
|Authorization|IDP verwijderen|
|Authorization|IdentityProvider verwijderen|
|Authorization|V1-toepassing verwijderen|
|Authorization|V2-toepassing verwijderen|
|Authorization|V2-toepassingsmachtiging verwijderen|
|Authorization|Een B2C-mapresource verwijderen|
|Authorization|Een CPIM-sleutelcontainer verwijderen|
|Authorization|TrustFramework-beleid verwijderen|
|Authorization|Gebruikerskenmerk verwijderen|
|Authorization|B2C-functie inschakelen|
|Authorization|B2C-mapresources in een abonnement ophalen|
|Authorization|Aangepaste IDP ophalen|
|Authorization|IDP ophalen|
|Authorization|V1- en V2-toepassingen ophalen|
|Authorization|V1-toepassing ophalen|
|Authorization|V1-toepassingen ophalen|
|Authorization|V2-toepassing ophalen|
|Authorization|V2-toepassingen ophalen|
|Authorization|Een B2C-Directory resource ophalen|
|Authorization|Een lijst met aangepaste domeinen in de tenant ophalen|
|Authorization|Een gebruikersbeleving ophalen|
|Authorization|Toegestane toepassingsclaims voor gebruikersbeleving ophalen|
|Authorization|Toegestane zelf-gecontroleerde claims voor gebruikersbeleving ophalen|
|Authorization|Toegestane zelf-gecontroleerde claims voor beleid ophalen|
|Authorization|Lijst met beschikbare uitvoerclaims ophalen|
|Authorization|Inhouddefinities voor gebruikersbeleving ophalen|
|Authorization|IDP's voor een specifieke beheerstroom ophalen|
|Authorization|Actieve sleutelmetagegevens voor sleutelcontainer in JWK ophalen|
|Authorization|Lijst van alle beheerstromen ophalen|
|Authorization|Lijst met labels voor alle beheerstromen voor alle gebruikers ophalen|
|Authorization|Lijst met tenants voor een gebruiker ophalen|
|Authorization|Zelf-gecontroleerde claims voor lokale accounts ophalen|
|Authorization|Gelokaliseerde resource-json ophalen|
|Authorization|Bewerkingen van de resourceprovider Microsoft.AzureActiveDirectory ophalen|
|Authorization|Beleidsregels ophalen|
|Authorization|Beleid ophalen|
|Authorization|Resource-eigenschappen van een tenant ophalen|
|Authorization|Lijst met ondersteunde IDP's ophalen|
|Authorization|Lijst met ondersteunde IDP's van de gebruikersbeleving ophalen|
|Authorization|Tenantgegevens ophalen|
|Authorization|Voor tenant toegestane functies ophalen|
|Authorization|Voor tenant gedefinieerde, aangepaste IDP-lijst ophalen|
|Authorization|Voor tenant gedefinieerde IDP-lijst ophalen|
|Authorization|Voor tenant gedefinieerde, lokale IDP-lijst ophalen|
|Authorization|Tenantgegevens voor een gebruiker ophalen voor het maken van resources|
|Authorization|Tenantlijst ophalen|
|Authorization|TenantDomains ophalen|
|Authorization|De standaard ondersteunde cultuur voor CPIM ophalen|
|Authorization|De gegevens van een beheerstroom ophalen|
|Authorization|De lijst met UserJourneys voor deze tenant ophalen|
|Authorization|De set beschikbare ondersteunde culturen voor CPIM ophalen|
|Authorization|TrustFramework-beleid ophalen|
|Authorization|TrustFramework-beleid als xml ophalen|
|Authorization|Gebruikerskenmerk ophalen|
|Authorization|Gebruikerskenmerken ophalen|
|Authorization|Lijst met gebruikersbelevingen ophalen|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Hiermee haalt u een CPIM-sleutelcontainer in jwk-indeling op|
|Authorization|Hiermee haalt u een lijst met sleutelcontainers in de tenant op|
|Authorization|Hiermee wordt het type tenant opgehaald|
|Authorization|MigrateTenantMetadata|
|Authorization|IdentityProvider patchen|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Een gebruikersbeleving verwijderen|
|Authorization|Een back-up van een CPIM-sleutelcontainer herstellen|
|Authorization|V2-toepassingsmachtigingen ophalen|
|Authorization|V2-toepassingsservice-principals in de huidige tenant ophalen|
|Authorization|Aangepaste IDP bijwerken|
|Authorization|IDP bijwerken|
|Authorization|De lokale IDP bijwerken|
|Authorization|V1-toepassing bijwerken|
|Authorization|V2-toepassing bijwerken|
|Authorization|V2-toepassingsmachtiging bijwerken|
|Authorization|Beleid bijwerken|
|Authorization|Gebruikerskenmerk bijwerken|
|Authorization|Een versleutelde CPIM-sleutel uploaden|
|Authorization|Gebruikers autorisatie: API is uitgeschakeld voor Tenant functieset|
|Authorization|Gebruikers autorisatie: Gebruiker heeft toegang verleend als Tenant admin|
|Authorization|Gebruikers autorisatie: Gebruiker heeft toegangs rechten van geverifieerde gebruikers verleend|
|Authorization|Controleren of de B2C-functie is ingeschakeld|
|Authorization|Controleren of de functie is ingeschakeld|
|Authorization|Programma maken|
|Authorization|Programma verwijderen|
|Authorization|Programmabeheer koppelen|
|Authorization|Onboarding bij Azure AD-toegangsbeoordelingen|
|Authorization|Programmabeheer ontkoppelen|
|Authorization|Programma bijwerken|
|Authorization|Desktop-SSO uitschakelen|
|Authorization|Desktop-SSO uitschakelen voor een specifiek domein|
|Authorization|Toepassingsproxy uitschakelen|
|Authorization|Passthrough-verificatie uitschakelen|
|Authorization|Desktop-SSO inschakelen|
|Directory Management|Desktop-SSO inschakelen voor een specifiek domein|
|Directory Management|Toepassingsproxy inschakelen|
|Directory Management|Passthrough-verificatie inschakelen|
|Directory Management|Aangepaste domeinen in de tenant maken|
|Directory Management|B2C-functie inschakelen|
|Directory Management|Een lijst met aangepaste domeinen in de tenant ophalen|
|Directory Management|Resource-eigenschappen van een tenant ophalen|
|Directory Management|Tenantgegevens ophalen|
|Mapbeheer|Voor tenant toegestane functies ophalen|
|Directory Management|TenantDomains ophalen|
|Sleutel|Hiermee wordt het type tenant opgehaald|
|Sleutel|Controleren of de B2C-functie is ingeschakeld|
|Sleutel|Controleren of de functie is ingeschakeld|
|Sleutel|Partner aan bedrijf toevoegen|
|Sleutel|Niet-geverifieerd domein toevoegen|
|Sleutel|Geverifieerd domein toevoegen|
|Sleutel|Bedrijf maken|
|Sleutel|Bedrijfsinstellingen maken|
|Sleutel|Bedrijfsinstellingen verwijderen|
|Sleutel|Niveau van partner verlagen|
|Sleutel|Map verwijderd|
|Overige|Map permanent verwijderd|
|Overige|Map staat gepland voor verwijdering|
|Resource|Niveau van bedrijf verhogen tot partner|
|Resource|Rights Management-eigenschappen leegmaken|
|Resource|Partner uit bedrijf verwijderen|
|Resource|Niet-geverifieerd domein verwijderen|
|Resource|Geverifieerd domein verwijderen|
|Resource|Bedrijfsgegevens instellen|
|Resource|DirSync-functie instellen|
|Resource|DirSyncEnabled-vlag instellen|
|Resource|Partnerschap instellen|
|Resource|Drempelwaarde voor onopzettelijk verwijderen instellen|
|Resource|Door het bedrijf toegestane gegevenslocatie instellen|
|Resource|Multinationale functie inschakelen|
|Resource|Mapfunctie voor tenant instellen|
|Resource|Domeinverificatie instellen|
|Resource|Federatie-instellingen voor domein instellen|
|Resource|Wachtwoordbeleid instellen|
|Resource|Rights Management-eigenschappen instellen|
|Resource|Bedrijf bijwerken|
|Resource|Bedrijfsinstellingen bijwerken|
|Resource|Domein bijwerken|
|Resource|Domein verifiëren|
|Resource|Door e-mail geverifieerd domein verifiëren|
|Resource|Onboarding|
|Resource|Waarschuwingsinstellingen bijwerken|
|Resource|Instellingen voor wekelijkse samenvatting bijwerken|
|Resource|Write-back van wachtwoord voor map uitschakelen|
|Resource|Write-back van wachtwoord voor map inschakelen|
|Resource|App-roltoewijzing aan groep toevoegen|
|Resource|Groep toevoegen|
|Resource|Lid toevoegen aan groep|
|Resource|Eigenaar aan groep toevoegen|
|Resource|Groepsinstellingen maken|
|Resource|Groep verwijderen|
|Resource|Groepsinstellingen verwijderen|
|Resource|Het toepassen van een groepslicentie op gebruikers voltooien|
|Resource|Groep definitief verwijderen|
|Resource|App-roltoewijzing uit groep verwijderen|
|Resource|Lid uit groep verwijderen|
|Resource|Eigenaar uit groep verwijderen|
|Resource|Groep herstellen|
|Resource|Groepslicentie instellen|
|Resource|Groep instellen op beheer door gebruiker|
|Resource|Starten met het toepassen van een groepslicentie op gebruikers|
|Resource|Herberekening van groepslicentie activeren|
|Resource|Groep bijwerken|
|Resource|Groepsinstellingen bijwerken|
|Resource|Lid toevoegen|
|Resource|Groep maken|
|Resource|Groep verwijderen|
|Resource|Lid verwijderen|
|Resource|Groep bijwerken|
|Resource|Aanvraag in behandeling voor deelname aan een groep goedkeuren|
|Resource|Aanvraag in behandeling voor deelname aan een groep annuleren|
|Resource|Levenscyclusbeheerbeleid maken|
|Resource|Aanvraag in behandeling voor deelname aan een groep verwijderen|
|Resource|Aanvraag in behandeling voor deelname aan een groep weigeren|
|Resource|Groep verlengen|
|Resource|Deelname aan een groep aanvragen|
|Resource|Eigenschappen voor dynamische groep instellen|
|Resource|Levenscyclusbeheerbeleid bijwerken|
|Resource|Een sleutel op basis van een ASCII-geheim aan een CPIM-sleutelcontainer toevoegen|
|Resource|Een sleutel aan een CPIM-sleutelcontainer toevoegen|
|Resource|Een CPIM-sleutelcontainer verwijderen|
|Resource|Een sleutelcontainer verwijderen|
|Resource|Actieve sleutelmetagegevens voor sleutelcontainer in JWK ophalen|
|Resource|Metagegevens voor sleutelcontainer ophalen|
|Resource|Hiermee haalt u een CPIM-sleutelcontainer in jwk-indeling op|
|Resource|Hiermee haalt u een lijst met sleutelcontainers in de tenant op|
|Resource|Een back-up van een CPIM-sleutelcontainer herstellen|
|Resource|Sleutelcontainer opslaan|
|Resource|Een versleutelde CPIM-sleutel uploaden|
|Resource|Een autorisatiecode aan de toepassing uitgeven|
|Resource|Een id_token voor de toepassing uitgeven|


## <a name="core-directory"></a>Hoofddirectory

|Controlecategorie|Activiteit|
|---|---|
|Administratieve eenheden beheren|Een enkel type risico detectie downloaden|
|Administratieve eenheden beheren|Beheerders en status van aanmelding voor wekelijkse samenvatting downloaden|
|Administratieve eenheden beheren|Alle typen risico detectie downloaden|
|Administratieve eenheden beheren|Beschik bare risico detecties voor gebruikers downloaden|
|Administratieve eenheden beheren|Gebruikers voor wie wordt aangegeven dat ze risico lopen downloaden|
|Toepassingsbeheer|Verwerkte batchuitnodigingen|
|Toepassingsbeheer|Geüploade batchuitnodigingen|
|Toepassingsbeheer|Eigenaar aan beleid toevoegen|
|Toepassingsbeheer|Beleid toevoegen|
|Toepassingsbeheer|Beleid verwijderen|
|Toepassingsbeheer|Beleidsreferenties verwijderen|
|Toepassingsbeheer|Beleid bijwerken|
|Toepassingsbeheer|Beleid voor MFA-registratie instellen|
|Toepassingsbeheer|Beleid voor aanmeldingsrisico's instellen|
|Toepassingsbeheer|Beleid voor gebruikersrisico's instellen|
|Toepassingsbeheer|Gebruiksvoorwaarden accepteren|
|Toepassingsbeheer|Gebruiksvoorwaarden maken|
|Toepassingsbeheer|Gebruiksvoorwaarden afwijzen|
|Toepassingsbeheer|Gebruiksvoorwaarden verwijderen|
|Toepassingsbeheer|Gebruiksvoorwaarden bewerken|
|Toepassingsbeheer|Gebruiksvoorwaarden publiceren|
|Toepassingsbeheer|Publicatie van gebruiksvoorwaarden ongedaan maken|
|Toepassingsbeheer|SSL-certificaat voor de toepassing toevoegen|
|Toepassingsbeheer|SSL-binding verwijderen|
|Toepassingsbeheer|Connector registreren|
|Toepassingsbeheer|AdminPolicyDatas-RemoveResources|
|Toepassingsbeheer|AdminPolicyDatas-SetResources|
|Toepassingsbeheer|AdminUserJourneys-GetResources|
|Directory Management|AdminUserJourneys-RemoveResources|
|Directory Management|AdminUserJourneys-SetResources|
|Directory Management|IdentityProvider maken|
|Directory Management|Een nieuwe AdminUserJourney maken|
|Directory Management|Gelokaliseerde resource-json maken|
|Directory Management|Nieuwe aangepaste IDP maken|
|Directory Management|Nieuwe IDP maken|
|Directory Management|Een B2C-mapresource maken of bijwerken|
|Directory Management|Beleid maken|
|Directory Management|TrustFramework-beleid maken|
|Directory Management|TrustFramework-beleid maken met configureerbaar voorvoegsel|
|Directory Management|Gebruikerskenmerk maken|
|Directory Management|CreateTrustFrameworkPolicy|
|Directory Management|IDP verwijderen|
|Directory Management|IdentityProvider verwijderen|
|Directory Management|Een B2C-mapresource verwijderen|
|Directory Management|TrustFramework-beleid verwijderen|
|Directory Management|Gebruikerskenmerk verwijderen|
|Directory Management|B2C-mapresources in een resourcegroep ophalen|
|Directory Management|B2C-mapresources in een abonnement ophalen|
|Directory Management|Aangepaste IDP ophalen|
|Directory Management|IDP ophalen|
|Directory Management|Een B2C-Directory resource ophalen|
|Directory Management|Een gebruikersbeleving ophalen|
|Directory Management|Toegestane toepassingsclaims voor gebruikersbeleving ophalen|
|Directory Management|Toegestane zelf-gecontroleerde claims voor gebruikersbeleving ophalen|
|Directory Management|Toegestane zelf-gecontroleerde claims voor beleid ophalen|
|Directory Management|Lijst met beschikbare uitvoerclaims ophalen|
|Directory Management|Inhouddefinities voor gebruikersbeleving ophalen|
|Directory Management|IDP's voor een specifieke beheerstroom ophalen|
|Directory Management|Lijst van alle beheerstromen ophalen|
|Directory Management|Lijst met labels voor alle beheerstromen voor alle gebruikers ophalen|
|Groepsbeheer|Lijst met tenants voor een gebruiker ophalen|
|Groepsbeheer|Zelf-gecontroleerde claims voor lokale accounts ophalen|
|Groepsbeheer|Gelokaliseerde resource-json ophalen|
|Groepsbeheer|Bewerkingen van de resourceprovider Microsoft.AzureActiveDirectory ophalen|
|Groepsbeheer|Beleidsregels ophalen|
|Groepsbeheer|Beleid ophalen|
|Groepsbeheer|Lijst met ondersteunde IDP's ophalen|
|Groepsbeheer|Lijst met ondersteunde IDP's van de gebruikersbeleving ophalen|
|Groepsbeheer|Voor tenant gedefinieerde, aangepaste IDP-lijst ophalen|
|Groepsbeheer|Voor tenant gedefinieerde IDP-lijst ophalen|
|Groepsbeheer|Voor tenant gedefinieerde, lokale IDP-lijst ophalen|
|Groepsbeheer|Tenantgegevens voor een gebruiker ophalen voor het maken van resources|
|Groepsbeheer|De standaard ondersteunde cultuur voor CPIM ophalen|
|Groepsbeheer|De gegevens van een beheerstroom ophalen|
|Groepsbeheer|De lijst met UserJourneys voor deze tenant ophalen|
|Groepsbeheer|De set beschikbare ondersteunde culturen voor CPIM ophalen|
|Groepsbeheer|TrustFramework-beleid ophalen|
|Groepsbeheer|TrustFramework-beleid als xml ophalen|
|Groepsbeheer|Gebruikerskenmerk ophalen|
|Beleidsbeheer|Gebruikerskenmerken ophalen|
|Beleidsbeheer|Lijst met gebruikersbelevingen ophalen|
|Beleidsbeheer|GetIEFPolicies|
|Beleidsbeheer|GetIdentityProviders|
|Beleidsbeheer|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Resources verplaatsen|
|Resource|IdentityProvider patchen|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Een gebruikersbeleving verwijderen|
|Resource|Aangepaste IDP bijwerken|
|Resource|IDP bijwerken|
|Resource|De lokale IDP bijwerken|
|Resource|Een B2C-mapresource bijwerken|
|Resource|Beleid bijwerken|
|Resource|Abonnementstatus bijwerken|
|Rolbeheer|Gebruikerskenmerk bijwerken|
|Rolbeheer|Resources verplaatsen valideren|
|Rolbeheer|Apparaat toevoegen|
|Rolbeheer|Apparaatconfiguratie toevoegen|
|Rolbeheer|Geregistreerde eigenaar aan apparaat toevoegen|
|Rolbeheer|Geregistreerde gebruikers aan apparaat toevoegen|
|Rolbeheer|Apparaat verwijderen|
|Rolbeheer|Apparaatconfiguratie verwijderen|
|Rolbeheer|Apparaat niet meer conform|
|Rolbeheer|Apparaat wordt niet meer beheerd|
|Gebruikersbeheer|Geregistreerde eigenaar van apparaat verwijderen|
|Gebruikersbeheer|Geregistreerde gebruikers van apparaat verwijderen|
|Gebruikersbeheer|Apparaat bijwerken|
|Gebruikersbeheer|Apparaatconfiguratie bijwerken|
|Gebruikersbeheer|In aanmerking komend lid aan rol toevoegen|
|Gebruikersbeheer|Lid aan rol toevoegen|
|Gebruikersbeheer|Roltoewijzing aan roldefinitie toevoegen|
|Gebruikersbeheer|Rol vanuit sjabloon toevoegen|
|Gebruikersbeheer|Scoped lid aan rol toevoegen|
|Gebruikersbeheer|In aanmerking komen lid uit rol verwijderen|
|Gebruikersbeheer|Lid uit rol verwijderen|
|Gebruikersbeheer|Roltoewijzing uit roldefinitie verwijderen|
|Gebruikersbeheer|Scoped lid uit rol verwijderen|
|Gebruikersbeheer|Rol bijwerken|
|Gebruikersbeheer|AccessReview_Review|
|Gebruikersbeheer|AccessReview_Update|
|Gebruikersbeheer|ActivationAborted|
|Gebruikersbeheer|ActivationApproved|
|Gebruikersbeheer|ActivationCanceled|
|Gebruikersbeheer|ActivationRequested|
|Gebruikersbeheer|Toegevoegd|
|Gebruikersbeheer|Toewijzen|


## <a name="identity-protection"></a>Identiteitsbeveiliging

|Controlecategorie|Activiteit|
|---|---|
|Directory Management|Verhogen|
|Directory Management|Verwijderd|
|Directory Management|Wijzigingen van de rolinstelling|
|Overige|ScanAlertsNow|
|Overige|Registreren|
|Overige|Bevoegdheden verlagen|
|Overige|UpdateAlertSettings|
|Overige|UpdateCurrentState|
|Beleidsbeheer|Toegangsbeoordeling beëindigd|
|Beleidsbeheer|Goedkeurder toevoegen om goedkeuring te vragen|
|Beleidsbeheer|Revisor toevoegen voor toegang tot revisie|
|Gebruikersbeheer|Toegangsbeoordeling toepassen|
|Gebruikersbeheer|Toegangsbeoordeling maken|


## <a name="invited-users"></a>Uitgenodigde gebruikers

|Controlecategorie|Activiteit|
|---|---|
|Overige|Aanvraaggoedkeuring maken|
|Overige|Toegangsbeoordeling verwijderen|
|Gebruikersbeheer|Revisor verwijderen uit toegangsbeoordeling|
|Gebruikersbeheer|Toepassen van beoordelingsresultaat aanvragen|
|Gebruikersbeheer|Stoppen beoordeling aanvragen|
|Gebruikersbeheer|App-toewijzing beoordelen|
|Gebruikersbeheer|Groepslidmaatschap beoordelen|
|Gebruikersbeheer|RBAC-rollidmaatschap beoordelen|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Controlecategorie|Activiteit|
|---|---|
|Groepsbeheer|Aanvraag voor goedkeuring aanvraag beoordelen|
|Groepsbeheer|Toegangsbeoordeling bijwerken|
|Groepsbeheer|Instellingen voor de e-mailmelding van de toegangsbeoordeling bijwerken|
|Groepsbeheer|Instelling voor aantal herhalingen van de toegangs beoordeling bijwerken|
|Groepsbeheer|Instelling voor toegangs beoordeling duur van terugkeer patroon in dagen bijwerken|
|Gebruikersbeheer|Instelling van eind type van terugkeer patroon van toegangs beoordeling bijwerken|
|Gebruikersbeheer|Instelling voor toegangs beoordeling van type terugkeer patroon bijwerken|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Controlecategorie|Activiteit|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Toegevoegd|
|PIM|AddedOutsidePIM|
|PIM|Toewijzen|
|PIM|DismissAlert|
|PIM|Verhogen|
|PIM|ReactivateAlert|
|PIM|Verwijderd|
|PIM|RemovedOutsidePIM|
|PIM|Stoppen beoordeling aanvragen|
|PIM|Wijzigingen van de rolinstelling|
|PIM|ScanAlertsNow|
|PIM|Registreren|
|PIM|Toewijzing|
|PIM|Bevoegdheden verlagen|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Groepsbeheer via selfservice

|Controlecategorie|Activiteit|
|---|---|
|Groepsbeheer|Gebruikerswachtwoord opnieuw instellen|
|Groepsbeheer|Gebruiker herstellen|
|Groepsbeheer|Geforceerd wijzigen van gebruikerswachtwoord instellen|
|Groepsbeheer|Gebruikersbeheerder instellen|
|Groepsbeheer|Instellen dat oath-token-metagegevens voor gebruikers zijn ingeschakeld|
|Groepsbeheer|Tijdstempel StsRefreshTokenValidFrom bijwerken|
|Groepsbeheer|Externe geheimen bijwerken|
|Groepsbeheer|Gebruiker bijwerken|
|Groepsbeheer|Beheerder genereert een tijdelijk wachtwoord|


## <a name="self-service-password-management"></a>Wachtwoordbeheer via selfservice

|Controlecategorie|Activiteit|
|---|---|
|Directory Management|Beheerders vereisen dat de gebruiker het wachtwoord opnieuw instelt|
|Directory Management|Externe gebruiker aan toepassing toewijzen|
|Gebruikersbeheer|E-mail niet verzonden, gebruiker heeft zich afgemeld|
|Gebruikersbeheer|Externe gebruiker uitnodigen|
|Gebruikersbeheer|Uitnodiging externe gebruiker inwisselen|
|Gebruikersbeheer|Virale tenant maken|
|Gebruikersbeheer|Virale gebruiker maken|
|Gebruikersbeheer|Gebruikerswachtwoordregistratie|
|Gebruikersbeheer|Gebruikerswachtwoordherstel|
|Gebruikersbeheer|Selfservice voor wachtwoord opnieuw instellen geblokkeerd|


## <a name="terms-of-use"></a>Gebruiksvoorwaarden

|Controlecategorie|Activiteit|
|---|---|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden accepteren|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden maken|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden afwijzen|
|Gebruiksvoorwaarden|Toestemming verwijderen|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden verwijderen|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden bewerken|
|Gebruiksvoorwaarden|Gebruiks voorwaarden verlopen|
|Gebruiksvoorwaarden|Gebruiks voorwaarden voor hard verwijderen|
|Gebruiksvoorwaarden|Gebruiksvoorwaarden publiceren|
|Gebruiksvoorwaarden|Publicatie van gebruiksvoorwaarden ongedaan maken|


## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure AD-rapporten](overview-reports.md).
- [Rapport controle logboeken](concept-audit-logs.md). 
- [Programmatische toegang tot Azure AD-rapporten](concept-reporting-api.md)
