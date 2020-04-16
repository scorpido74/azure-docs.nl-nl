---
title: Beschrijvingen en machtigingen voor beheerdersrollen - Azure AD | Microsoft Documenten
description: Een beheerdersrol kan gebruikers toevoegen, beheerdersrollen toewijzen, gebruikerswachtwoorden opnieuw instellen, gebruikerslicenties beheren of domeinen beheren.
services: active-directory
author: curtand
manager: daveba
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: reference
ms.date: 04/14/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: b3f284efd6a9a2fd83c8e2a8f9fb7a962c1cacc1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406462"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Machtigingen voor beheerrol in Azure Active Directory

Met Azure Ad (Azure AD) u beperkte beheerders aanwijzen voor het beheren van identiteitstaken in minder bevoorrechte rollen. Beheerders kunnen worden toegewezen voor doeleinden zoals het toevoegen of wijzigen van gebruikers, het toewijzen van beheerdersrollen, het opnieuw instellen van gebruikerswachtwoorden, het beheren van gebruikerslicenties en het beheren van domeinnamen. De [standaardgebruikersmachtigingen](../fundamentals/users-default-permissions.md) kunnen alleen worden gewijzigd in gebruikersinstellingen in Azure AD.

## <a name="limit-use-of-global-administrator"></a>Het gebruik van globale beheerder beperken

Gebruikers die zijn toegewezen aan de functie Globale beheerder, kunnen elke beheerinstelling in uw Azure AD-organisatie lezen en wijzigen. De persoon die zich aanmeldt voor een Azure-abonnement krijgt standaard de globale beheerdersrol voor de Azure AD-organisatie toegewezen. Alleen globale beheerders en beheerders van bevoorrechte rollen kunnen beheerdersrollen delegeren. Om het risico voor uw bedrijf te verminderen, raden we u aan deze rol toe te wijzen aan zo min mogelijk mensen in uw organisatie.

Als aanbevolen functie raden we u aan deze rol toe te wijzen aan minder dan vijf mensen in uw organisatie. Als u meer dan vijf beheerders hebt toegewezen aan de rol Globale beheerder in uw organisatie, zijn hier enkele manieren om het gebruik ervan te verminderen.

### <a name="find-the-role-you-need"></a>Vind de rol die je nodig hebt

Als het frustrerend is dat u de rol vindt die u nodig hebt in een lijst met vele rollen, kan Azure AD u subsets van de rollen laten zien op basis van rolcategorieën. Bekijk ons nieuwe **filter Type** voor [Azure AD-rollen en -beheerders](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators) om u alleen de rollen in het geselecteerde type te laten zien.

### <a name="a-role-exists-now-that-didnt-exist-when-you-assigned-the-global-administrator-role"></a>Er bestaat nu een rol die niet bestond toen u de globale beheerdersrol toewees

Het is mogelijk dat een rol of rol aan Azure AD is toegevoegd die gedetailleerdere machtigingen biedt die geen optie waren toen u sommige gebruikers naar Globale beheerder verhoogde. Na verloop van tijd rollen we uit te rollen die taken uitvoeren die alleen de globale beheerderrol eerder zou kunnen uitvoeren. U deze zien in de volgende [beschikbare rollen](#available-roles).

## <a name="assign-or-remove-administrator-roles"></a>Beheerdersrollen toewijzen of verwijderen

Zie [Beheerdersrollen weergeven en toewijzen in Azure Active Directory](directory-manage-roles-portal.md)voor meer informatie over het toewijzen van beheerdersrollen aan een gebruiker in Azure Active Directory.

## <a name="available-roles"></a>Beschikbare rollen

De volgende beheerdersrollen zijn beschikbaar:

### <a name="application-administrator"></a>[Toepassingsbeheerder](#application-administrator-permissions)

Gebruikers in deze rol kunnen alle aspecten van bedrijfstoepassingen, toepassingsregistraties en proxy-instellingen voor toepassingen maken en beheren. Houd er rekening mee dat gebruikers die aan deze rol zijn toegewezen, niet als eigenaar worden toegevoegd bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

Toepassingsbeheerders kunnen toepassingsreferenties beheren waarmee ze zich kunnen voordoen als de toepassing. Gebruikers die aan deze rol zijn toegewezen, kunnen dus alleen toepassingsreferenties beheren van toepassingen die niet zijn toegewezen aan azure AD-rollen of toepassingen die zijn toegewezen aan volgende beheerdersrollen:
* Toepassingsbeheerder
* Toepassingsontwikkelaar
* Beheerder van de cloudtoepassing
* Directorylezers

Als een toepassing is toegewezen aan een andere rol die hierboven niet wordt vermeld, kan toepassingsbeheerder de referenties van die toepassing niet beheren. 
 
Deze rol biedt ook de mogelijkheid om in te _stemmen met_ gedelegeerde machtigingen en toepassingsmachtigingen, met uitzondering van machtigingen voor de Microsoft Graph API.

> [!IMPORTANT]
> Deze uitzondering betekent dat u nog steeds toestemming geven voor machtigingen voor _andere_ apps (bijvoorbeeld apps of apps van derden die u hebt geregistreerd), maar niet met machtigingen voor Azure AD zelf. U deze machtigingen nog steeds _aanvragen_ als onderdeel van de app-registratie, maar _voor het verlenen_ (d.w.z. toestemming geven aan) deze machtigingen is een Azure AD-beheerder vereist. Dit betekent dat een kwaadwillende gebruiker zijn machtigingen niet gemakkelijk kan verhogen, bijvoorbeeld door een app te maken en in te stemmen die naar de hele directory kan schrijven en via de machtigingen van die app zichzelf kan verheffen tot een globale beheerder.

### <a name="application-developer"></a>[Toepassingsontwikkelaar](#application-developer-permissions)

Gebruikers in deze rol kunnen toepassingsregistraties maken wanneer de instelling 'Gebruikers kunnen toepassingen registreren' is ingesteld op Nr. Deze rol verleent ook toestemming om namens hen toestemming te geven wanneer de instelling 'Gebruikers kunnen instemmen met apps die namens hen toegang hebben tot bedrijfsgegevens' is ingesteld op Nr. Gebruikers die aan deze rol zijn toegewezen, worden als eigenaars toegevoegd bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

### <a name="authentication-administrator"></a>[Verificatiebeheerder](#authentication-administrator-permissions)

De functie Verificatiebeheerder bevindt zich momenteel in een openbare preview. Gebruikers met deze rol kunnen niet-wachtwoordreferenties instellen of opnieuw instellen en kunnen wachtwoorden bijwerken voor alle gebruikers. Verificatiebeheerders kunnen gebruikers verplichten zich opnieuw te registreren tegen bestaande niet-wachtwoordreferenties (bijvoorbeeld MFA of FIDO) en **onthoud MFA op het apparaat**in te trekken, wat vraagt om MFA bij de volgende aanmelding van gebruikers die niet-beheerders zijn of alleen de volgende rollen hebben toegewezen:

* Verificatiebeheerder
* Directorylezers
* Gast genodigden
* Berichtcentrumlezer
* Rapporten Reader

> [!IMPORTANT]
> Gebruikers met deze rol kunnen referenties wijzigen voor mensen die mogelijk toegang hebben tot gevoelige of privégegevens of kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van de referenties van een gebruiker kan de mogelijkheid betekenen om de identiteit en machtigingen van die gebruiker aan te nemen. Bijvoorbeeld:
>
>- Eigenaren van toepassingsregistratie en bedrijfstoepassingen, die referenties van apps die eigenaar zijn, kunnen beheren. Deze apps hebben mogelijk bevoorrechte machtigingen in Azure AD en elders die niet worden verleend aan verificatiebeheerders. Via dit pad kan een verificatiebeheerder mogelijk de identiteit van een toepassingseigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of privégegevens of kritieke configuratie in Azure.
>- Eigenaren van beveiligingsgroep en Office 365-groep, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of privé-informatie of kritieke configuratie in Azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en personeelssystemen.
>- Niet-beheerders zoals leidinggevenden, juridisch adviseur en personeelsmedewerkers die mogelijk toegang hebben tot gevoelige of privé-informatie.

### <a name="azure-devops-administrator"></a>[Azure DevOps-beheerder](#azure-devops-administrator-permissions)

Gebruikers met deze rol kunnen het Azure DevOps-beleid beheren om nieuwe Azure DevOps-organisatiecreatie te beperken tot een set configureerbare gebruikers of groepen. Gebruikers in deze rol kunnen dit beleid beheren via elke Azure DevOps-organisatie die wordt ondersteund door de Azure AD-organisatie van het bedrijf.

Alle enterprise Azure DevOps-beleidsregels kunnen worden beheerd door gebruikers in deze rol.

### <a name="azure-information-protection-administrator"></a>[Azure-beheerder voor gegevensbeveiliging](#azure-information-protection-administrator-permissions)

Gebruikers met deze rol hebben alle machtigingen in de Azure Information Protection-service. Met deze rol u labels configureren voor het Azure Information Protection-beleid, beveiligingssjablonen beheren en beveiliging activeren. Deze rol verleent geen machtigingen in identity protection center, privileged identity management, Monitor Office 365 Service Health of Office 365 Security & Compliance Center.

### <a name="b2c-user-flow-administrator"></a>[B2C-gebruikersstroombeheerder](#b2c-user-flow-administrator-permissions)

Gebruikers met deze rol kunnen B2C-gebruikersstromen (ook wel 'ingebouwd' beleid genoemd) maken en beheren in de Azure-portal.Door gebruikersstromen te maken of te bewerken, kunnen deze gebruikers de html/CSS/javascript-inhoud van de gebruikerservaring wijzigen, MFA-vereisten per gebruikersstroom wijzigen, claims in het token wijzigen en sessie-instellingen aanpassen voor alle beleidsregels in de tenant. Aan de andere kant bevat deze rol niet de mogelijkheid om gebruikersgegevens te bekijken of wijzigingen aan te brengen in de kenmerken die zijn opgenomen in het tenantschema.Wijzigingen in het beleid van Identity Experience Framework (ook wel aangepast) beleid genoemd, vallen ook buiten het bereik van deze rol.

### <a name="b2c-user-flow-attribute-administrator"></a>[Beheerder van b2c-gebruikersstroomkenmerk](#b2c-user-flow-attribute-administrator-permissions)

Gebruikers met deze rol voegen aangepaste kenmerken toe die beschikbaar zijn voor alle gebruikersstromen in de tenant.Als zodanig kunnen gebruikers met deze rol nieuwe elementen wijzigen of toevoegen aan het eindgebruikersschema en het gedrag van alle gebruikersstromen beïnvloeden en indirect resulteren in wijzigingen in welke gegevens van eindgebruikers kunnen worden gevraagd en uiteindelijk als claims naar toepassingen worden verzonden.Deze rol kan gebruikersstromen niet bewerken.

### <a name="b2c-ief-keyset-administrator"></a>[B2C IEF Keyset Administrator](#b2c-ief-keyset-administrator-permissions)

De gebruiker kan beleidssleutels en -geheimen maken en beheren voor tokenversleuteling, tokenhandtekeningen en versleuteling/decryptie claimen.Door nieuwe sleutels toe te voegen aan bestaande sleutelcontainers, kan deze beperkte beheerder geheimen naar behoefte omrollen zonder dat dit gevolgen heeft voor bestaande toepassingen.Deze gebruiker kan de volledige inhoud van deze geheimen en hun vervaldatums zien, zelfs na hun creatie.

> [!IMPORTANT]
> Dit is een gevoelige rol.De rol van keysetbeheerder moet zorgvuldig worden gecontroleerd en met zorg worden toegewezen tijdens de preproductie en productie.

### <a name="b2c-ief-policy-administrator"></a>[B2C IEF-beleidsbeheerder](#b2c-ief-policy-administrator-permissions)

Gebruikers in deze rol hebben de mogelijkheid om alle aangepaste beleidsregels in Azure AD B2C te maken, te lezen, bij te werken en te verwijderen en hebben daarom volledige controle over het Identity Experience Framework in de relevante Azure AD B2C-tenant. Door beleid te bewerken, kan deze gebruiker directe federatie met externe identiteitsproviders opzetten, het directoryschema wijzigen, alle gebruikersgerichte inhoud wijzigen (HTML, CSS, JavaScript), de vereisten wijzigen om een verificatie te voltooien, nieuwe gebruikers maken, gebruikersgegevens naar externe systemen sturen, inclusief volledige migraties, en alle gebruikersinformatie bewerken, waaronder gevoelige velden zoals wachtwoorden en telefoonnummers. Omgekeerd kan deze rol de versleutelingssleutels niet wijzigen of de geheimen bewerken die worden gebruikt voor federatie in de tenant.

> [!IMPORTANT]
> De B2 IEF Beleidsbeheerder is een zeer gevoelige rol die op een zeer beperkte basis moet worden toegewezen voor huurders in productie.Activiteiten van deze gebruikers moeten nauwlettend worden gecontroleerd, met name voor huurders in productie.

### <a name="billing-administrator"></a>[Factureringsbeheerder](#billing-administrator-permissions)

Doet aankopen, beheert abonnementen, beheert ondersteuningstickets en bewaakt de servicestatus.

### <a name="cloud-application-administrator"></a>[Beheerder van de cloudtoepassing](#cloud-application-administrator-permissions)

Gebruikers in deze rol hebben dezelfde machtigingen als de rol Toepassingsbeheerder, met uitzondering van de mogelijkheid om de proxy van toepassingen te beheren. Deze rol geeft de mogelijkheid om alle aspecten van bedrijfstoepassingen en toepassingsregistraties te maken en te beheren. Deze rol biedt ook de mogelijkheid om in te stemmen met gedelegeerde machtigingen en toepassingsmachtigingen met uitzondering van de Microsoft Graph API. Gebruikers die aan deze rol zijn toegewezen, worden niet als eigenaars toegevoegd bij het maken van nieuwe toepassingsregistraties of bedrijfstoepassingen.

Cloudapplicatiebeheerders kunnen toepassingsreferenties beheren waarmee ze zich kunnen voordoen als de toepassing. Gebruikers die aan deze rol zijn toegewezen, kunnen dus alleen toepassingsreferenties beheren van toepassingen die niet zijn toegewezen aan azure AD-rollen of toepassingen die zijn toegewezen aan volgende beheerdersrollen:
* Toepassingsontwikkelaar
* Beheerder van de cloudtoepassing
* Directorylezers

Als een toepassing is toegewezen aan een andere rol die hierboven niet wordt vermeld, kan cloudtoepassingsbeheerder de referenties van die toepassing niet beheren.

### <a name="cloud-device-administrator"></a>[Beheerder van cloudapparaten](#cloud-device-administrator-permissions)

Gebruikers in deze rol kunnen apparaten in Azure AD in- en uitschakelen en verwijderen en Windows 10 BitLocker-sleutels (indien aanwezig) lezen in de Azure-portal. De rol verleent geen machtigingen voor het beheren van andere eigenschappen op het apparaat.

### <a name="compliance-administrator"></a>[Beheerder voor naleving](#compliance-administrator-permissions)

Gebruikers met deze rol hebben machtigingen voor het beheren van nalevingsgerelateerde functies in het Microsoft 365-compliancecenter, het Microsoft 365-beheercentrum, Azure en Office 365-beveiliging & Compliance Center. Toegewezens kunnen ook alle functies beheren in het Exchange-beheercentrum en Teams & Skype voor Bedrijven-beheercentra en ondersteuningstickets maken voor Azure en Microsoft 365. Meer informatie is beschikbaar bij [Over Office 365-beheerdersrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

In | Kan doen
----- | ----------
[Microsoft 365-compliancecentrum](https://protection.office.com) | De gegevens van uw organisatie beveiligen en beheren in Microsoft 365-services<br>Nalevingswaarschuwingen beheren
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De nalevingsactiviteiten van uw organisatie bijhouden, toewijzen en verifiëren
[Office 365-beveiligingscentrum & compliancecentrum](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gegevensbeheer beheren beheren beheren<br>Juridisch en dataonderzoek uitvoeren<br>Verzoek om betrokkenen beheren<br><br>Deze rol heeft dezelfde machtigingen als de [RoleGroup voor compliancebeheerders](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & Compliance Center, toegangsbeheer op basis van een compliancecenter.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alle Intune-controlegegevens weergeven
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Heeft alleen-lezen machtigingen en kan waarschuwingen beheren<br>Kan bestandsbeleid maken en wijzigen en bestandsbeheeracties toestaan<br>Kan alle ingebouwde rapporten bekijken onder Gegevensbeheer

### <a name="compliance-data-administrator"></a>[Beheerder voor nalevingsgegevens](#compliance-data-administrator-permissions)

Gebruikers met deze rol hebben machtigingen om gegevens bij te houden in het Microsoft 365-compliancecenter, het Microsoft 365-beheercentrum en Azure. Gebruikers kunnen ook nalevingsgegevens bijhouden in het Exchange-beheercentrum, Compliance Manager en Teams & Skype voor Bedrijven-beheercentrum en ondersteuningstickets maken voor Azure en Microsoft 365.

In | Kan doen
----- | ----------
[Microsoft 365-compliancecentrum](https://protection.office.com) | Nalevingsbeleid bewaken voor Microsoft 365-services<br>Nalevingswaarschuwingen beheren
[Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | De nalevingsactiviteiten van uw organisatie bijhouden, toewijzen en verifiëren
[Office 365-beveiligingscentrum & compliancecentrum](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gegevensbeheer beheren beheren beheren<br>Juridisch en dataonderzoek uitvoeren<br>Verzoek om betrokkenen beheren<br><br>Deze rol heeft dezelfde machtigingen als de [RoleGroup voor compliancegegevensbeheerder](https://docs.microsoft.com/microsoft-365/security/office-365-security/permissions-in-the-security-and-compliance-center#permissions-needed-to-use-features-in-the-security--compliance-center) in Office 365 Security & Compliance Center, toegangsbeheer op basis van een compliancecenter.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alle Intune-controlegegevens weergeven
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Heeft alleen-lezen machtigingen en kan waarschuwingen beheren<br>Kan bestandsbeleid maken en wijzigen en bestandsbeheeracties toestaan<br>Kan alle ingebouwde rapporten bekijken onder Gegevensbeheer

### <a name="conditional-access-administrator"></a>[Beheerder van voorwaardelijke toegang](#conditional-access-administrator-permissions)

Gebruikers met deze rol hebben de mogelijkheid om instellingen voor voorwaardelijke toegang in Azure Active Directory te beheren.
> [!NOTE]
> Als u het beleid voor voorwaardelijke toegang van Exchange ActiveSync in Azure wilt implementeren, moet de gebruiker ook een globale beheerder zijn.

### <a name="customer-lockbox-access-approver"></a>[Klant Lockbox-toegangsgoedkeuringr](#customer-lockbox-access-approver-permissions)

Beheert [aanvragen voor klantvergrendeling](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) in uw organisatie. Ze ontvangen e-mailmeldingen voor verzoeken om klantvergrendeling en kunnen aanvragen van het Microsoft 365-beheercentrum goedkeuren en weigeren. Ze kunnen ook de functie Klantlockbox in- of uitschakelen. Alleen globale beheerders kunnen de wachtwoorden van personen die aan deze rol zijn toegewezen, opnieuw instellen.

### <a name="desktop-analytics-administrator"></a>[Beheerder van bureaubladanalyse](#desktop-analytics-administrator-permissions)


Gebruikers in deze rol kunnen de services Desktop Analytics en Office Customization & policy beheren. Voor Desktop Analytics omvat dit de mogelijkheid om de voorraad van activa te bekijken, implementatieplannen te maken, implementatie en status weer te geven. Met deze functie kunnen gebruikers office-beleid beheren voor Office-&-beleidsservice.

### <a name="device-administrator"></a>[Apparaatbeheerder](#device-administrators-permissions)

Deze rol is alleen beschikbaar voor toewijzing als extra lokale beheerder in [apparaatinstellingen](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gebruikers met deze rol worden lokale machinebeheerders op alle Windows 10-apparaten die zijn gekoppeld aan Azure Active Directory. Ze hebben niet de mogelijkheid om apparatenobjecten in Azure Active Directory te beheren.

### <a name="directory-readers"></a>[Directorylezers](#directory-readers-permissions)

Gebruikers in deze rol kunnen basismapinformatie lezen. Deze rol moet worden gebruikt voor:
* Het verlenen van een specifieke set gastgebruikers leestoegang in plaats van het aan alle gastgebruikers te verlenen.
* Een specifieke set niet-beheerdersgebruikers toegang verlenen tot azure-portal wanneer 'Toegang tot Azure AD-portal beperken tot alleen beheerders' is ingesteld op 'Ja'.
* Serviceprincipals toegang verlenen tot directory waar Directory.Read.All geen optie is.

### <a name="directory-synchronization-accounts"></a>[Adreslijstsynchronisatieaccounts](#directory-synchronization-accounts-permissions)

Niet gebruiken. Deze rol wordt automatisch toegewezen aan de Azure AD Connect-service en is niet bedoeld of ondersteund voor enig ander gebruik.

### <a name="directory-writers"></a>[Directory Schrijvers](#directory-writers-permissions)

Dit is een verouderde rol die moet worden toegewezen aan toepassingen die het [Consent Framework](../develop/quickstart-register-app.md)niet ondersteunen. Het mag niet worden toegewezen aan gebruikers.

### <a name="dynamics-365-administrator--crm-administrator"></a>[Dynamics 365-beheerder / CRM-beheerder](#crm-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Dynamics 365 Online, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteuningstickets te beheren en de servicestatus te controleren. Meer informatie bij [Gebruik de functie servicebeheer om uw tenant te beheren.](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Dynamics 365 Service Administrator'. Het is "Dynamics 365 Administrator" in de [Azure-portal.](https://portal.azure.com)

### <a name="exchange-administrator"></a>[Exchange-beheerder](#exchange-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Exchange Online, wanneer de service aanwezig is. Heeft ook de mogelijkheid om alle Office 365-groepen te maken en te beheren, ondersteuningstickets te beheren en de servicestatus te controleren. Meer informatie bij [Over Office 365-beheerdersrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Exchange Service Administrator'. Het is "Exchange Administrator" in de [Azure-portal.](https://portal.azure.com) Het is "Exchange Online administrator" in het [Exchange-beheercentrum.](https://go.microsoft.com/fwlink/p/?LinkID=529144)

### <a name="external-identity-provider-administrator"></a>[Beheerder externe identiteitsprovider](#external-identity-provider-administrator-permissions)

Deze beheerder beheert de federatie tussen Azure Active Directory-tenants en externe identiteitsproviders.Met deze rol kunnen gebruikers nieuwe identiteitsproviders toevoegen en alle beschikbare instellingen configureren (bijvoorbeeld verificatiepad, service-id, toegewezen sleutelcontainers).Deze gebruiker kan de tenant in staat stellen verificaties van externe identiteitsproviders te vertrouwen.De resulterende impact op de ervaringen van eindgebruikers is afhankelijk van het type tenant:

* Azure Active Directory-tenants voor werknemers en partners: de toevoeging van een federatie (bijvoorbeeld met Gmail) heeft onmiddellijk invloed op alle gastuitnodigingen die nog niet zijn ingewisseld. Zie [Google toevoegen als identiteitsprovider voor B2B-gastgebruikers](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
* Azure Active Directory B2C-tenants: de toevoeging van een federatie (bijvoorbeeld met Facebook of met een andere Azure AD-organisatie) heeft niet onmiddellijk invloed op eindgebruikersstromen totdat de identiteitsprovider als optie in een gebruikersstroom is toegevoegd (ook wel een ingebouwd beleid genoemd). Zie [Een Microsoft-account configureren als identiteitsprovider](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) voor een voorbeeld.Om gebruikersstromen te wijzigen, is de beperkte rol van "B2C User Flow Administrator" vereist.

### <a name="global-administrator--company-administrator"></a>[Globale beheerder / bedrijfsbeheerder](#company-administrator-permissions)

Gebruikers met deze rol hebben toegang tot alle beheerfuncties in Azure Active Directory, evenals services die Azure Active Directory-identiteiten gebruiken, zoals Microsoft 365-beveiligingscentrum, Microsoft 365-compliancecenter, Exchange Online, SharePoint Online en Skype voor Bedrijven Online. De persoon die zich aanmeldt voor de Azure Active Directory-tenant wordt een globale beheerder. Er kunnen meer dan één globale beheerder bij uw bedrijf zijn. Globale beheerders kunnen het wachtwoord voor elke gebruiker en alle andere beheerders opnieuw instellen.

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Bedrijfsbeheerder'. Het is "Globale beheerder" in de [Azure-portal.](https://portal.azure.com)
>
>

### <a name="global-reader"></a>[Algemene lezer](#global-reader-permissions)

Gebruikers in deze rol kunnen instellingen en administratieve gegevens lezen in Microsoft 365-services, maar kunnen geen beheeracties uitvoeren. Wereldwijde lezer is de alleen-lezen tegenhanger van Global administrator. Globale lezer toewijzen in plaats van globale beheerder voor planning, audits of onderzoeken. Gebruik Globale lezer in combinatie met andere beperkte beheerdersrollen zoals Exchange Administrator om het gemakkelijker te maken om werk gedaan te krijgen zonder de toewijzing van de rol Globale beheerder. Global reader werkt met Microsoft 365-beheercentrum, Exchange-beheercentrum, SharePoint-beheercentrum, Teams-beheercentrum, Beveiligingscentrum, Compliance Center, Azure AD-beheercentrum en beheercentrum voor apparaatbeheer.

> [!NOTE]
> Wereldwijde lezer rol heeft een paar beperkingen op dit moment -
>
>- [OneDrive-beheercentrum](https://admin.onedrive.com/) - OneDrive-beheercentrum biedt geen ondersteuning voor de rol Globale lezer.
>- [Azure AD-portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) - Globale lezer kan de inrichtingsmodus van een bedrijfsapp niet lezen.
>- [M365-beheercentrum](https://admin.microsoft.com/Adminportal/Home#/homepage) - Wereldwijde lezer kan aanvragen voor klantlockboxniet lezen. U vindt het tabblad Aanvragen van de **vergrendelingsbox van** de klant niet onder **Ondersteuning** in het linkerdeelvenster van het M365-beheercentrum.
>- [M365 Security center](https://security.microsoft.com/homepage) - Global reader can't read sensitivity and retention labels. U vindt geen **tabbladen Gevoeligheidslabels,** **Bewaarlabels**en **Labelanalyses** in het linkerdeelvenster van het M365-beveiligingscentrum.
>- [Office Security & Compliance Center](https://sip.protection.office.com/homepage) - Wereldwijde lezer kan geen SCC-controlelogboeken lezen, inhoudzoeken of Secure Score bekijken.
>- [Teams-beheercentrum](https://admin.teams.microsoft.com) - Globale lezer kan de **levenscyclus van Teams**niet lezen, analyses & **rapporten,** **apparaatbeheer van IP-telefoons** en **app-catalogus**.
>- [Pam (Privileged Access Management)](https://docs.microsoft.com/office365/securitycompliance/privileged-access-management-overview) ondersteunt de rol van de wereldwijde lezer niet.
>- [Azure Information Protection](https://docs.microsoft.com/azure/information-protection/what-is-information-protection) - Global reader wordt alleen ondersteund [voor centrale rapportage](https://docs.microsoft.com/azure/information-protection/reports-aip) en wanneer uw Azure AD-organisatie zich niet op het unified [labeling-platform bevindt.](https://docs.microsoft.com/azure/information-protection/faqs#how-can-i-determine-if-my-tenant-is-on-the-unified-labeling-platform)
>
> Deze functies zijn momenteel in ontwikkeling.
>

### <a name="groups-administrator"></a>[Groepsbeheerder](#groups-administrator-permissions)

Gebruikers in deze rol kunnen groepen en instellingen maken/beheren, zoals naamgevings- en vervaldatumbeleid. Het is belangrijk om te begrijpen dat het toewijzen van een gebruiker aan deze rol hen de mogelijkheid geeft om alle groepen in de tenant te beheren in verschillende workloads, zoals Teams, SharePoint, Yammer naast Outlook. Ook zal de gebruiker in staat zijn om de verschillende groepen instellingen te beheren in verschillende admin portals zoals Microsoft Admin Center, Azure portal, evenals workload specifieke degenen zoals Teams en SharePoint Admin Centers.

### <a name="guest-inviter"></a>[Gast genodigden](#guest-inviter-permissions)

Gebruikers in deze rol kunnen uitnodigingen voor Azure Active Directory B2B-gastgebruikers beheren wanneer de **gebruikersinstelling** van leden is ingesteld op Nee. Meer informatie over B2B-samenwerking bij [Over Azure AD B2B-samenwerking](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Het bevat geen andere machtigingen.

### <a name="helpdesk-administrator"></a>[Helpdeskbeheerder](#helpdesk-administrator-permissions)

Gebruikers met deze rol kunnen wachtwoorden wijzigen, vernieuwingstokens ongeldig maken, serviceaanvragen beheren en de servicestatus controleren. Als u een vernieuwingstoken ongeldig maakt, moet de gebruiker zich opnieuw aanmelden. Helpdeskbeheerders kunnen wachtwoorden opnieuw instellen en vernieuwingstokens ongeldig maken van andere gebruikers die niet-beheerders zijn of alleen de volgende rollen toegewezen hebben:

* Directorylezers
* Gast genodigden
* Helpdeskbeheerder
* Berichtcentrumlezer
* Rapporten Reader

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wachtwoorden wijzigen voor mensen die mogelijk toegang hebben tot gevoelige of privégegevens of kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van het wachtwoord van een gebruiker kan de mogelijkheid betekenen om de identiteit en machtigingen van die gebruiker aan te nemen. Bijvoorbeeld:
>
>- Eigenaren van toepassingsregistratie en bedrijfstoepassingen, die referenties van apps die eigenaar zijn, kunnen beheren. Deze apps hebben mogelijk bevoorrechte machtigingen in Azure AD en elders die niet zijn verleend aan helpdeskbeheerders. Via dit pad kan een helpdeskbeheerder mogelijk de identiteit van de eigenaar van een toepassing aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of privégegevens of kritieke configuratie in Azure.
>- Eigenaren van beveiligingsgroep en Office 365-groep, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of privé-informatie of kritieke configuratie in Azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en personeelssystemen.
>- Niet-beheerders zoals leidinggevenden, juridisch adviseur en personeelsmedewerkers die mogelijk toegang hebben tot gevoelige of privé-informatie.

Het delegeren van administratieve machtigingen voor subsets van gebruikers en het toepassen van beleidsregels op een subset van gebruikers is mogelijk met [administratieve eenheden (nu in openbare preview).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units)

Deze rol werd voorheen 'Wachtwoordbeheerder' genoemd in de [Azure-portal.](https://portal.azure.com/) De naam 'Helpdeskbeheerder' in Azure AD komt nu overeen met de naam in Azure AD PowerShell en de Microsoft Graph API.

### <a name="hybrid-identity-administrator"></a>[Hybride identiteitsbeheerder](#hybrid-identity-administrator-permissions)

Gebruikers in deze rol kunnen services en instellingen met betrekking tot het inschakelen van hybride identiteit in Azure AD inschakelen, configureren en beheren. Met deze rol Azure AD worden geconfigureerd voor een van de drie ondersteunde verificatiemethoden, Password hash synchronization (PHS), Pass-through authentication (PTA) of Federation (AD FS of 3rd party federation provider) en gerelateerde on-premises infrastructuur implementeren om deze infrastructuur mogelijk te maken. On-prem infrastructuur omvat Provisioning en PTA agenten. Deze rol biedt de mogelijkheid om Seamless Single Sign-On (S-SSO) in te schakelen om naadloze verificatie in te schakelen op niet-Windows 10-apparaten of niet-Windows Server 2016-computers. Bovendien biedt deze rol de mogelijkheid om aanmeldingslogboeken en toegang tot gezondheids- en analyseprogramma's te bekijken voor het bewaken en oplossen van problemen. 

### <a name="intune-administrator"></a>[Intune-beheerder](#intune-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Intune Online, wanneer de service aanwezig is. Bovendien bevat deze rol de mogelijkheid om gebruikers en apparaten te beheren om beleid te koppelen en groepen te maken en te beheren. Meer informatie bij [Role-based administration control (RBAC) met Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control).

Deze rol kan alle beveiligingsgroepen maken en beheren. Intune Admin heeft echter geen beheerdersrechten ten opzichte van Office-groepen. Dat betekent dat de beheerder eigenaren of lidmaatschappen van alle Office-groepen in de tenant niet kan bijwerken. Hij/zij kan echter de Office-groep beheren die hij maakt en die deel uitmaakt van zijn/haar rechten voor eindgebruikers. Dus, elke Office-groep (niet beveiligingsgroep) die hij / zij maakt moet worden geteld tegen zijn / haar quotum van 250.

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als "Intune Service Administrator". Het is "Intune Administrator" in de [Azure-portal.](https://portal.azure.com)

### <a name="kaizala-administrator"></a>[Kaizala-beheerder](#kaizala-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen om instellingen te beheren binnen Microsoft Kaizala, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteuningstickets te beheren en de servicestatus te controleren. Bovendien heeft de gebruiker toegang tot rapporten met betrekking tot adoptie & gebruik van Kaizala door leden van de organisatie en bedrijfsrapporten die zijn gegenereerd met behulp van de Kaizala-acties.

### <a name="license-administrator"></a>[Licentiebeheerder](#license-administrator-permissions)

Gebruikers in deze rol kunnen licentietoewijzingen toevoegen, verwijderen en bijwerken voor gebruikers, groepen (met groepslicenties) en de gebruikslocatie voor gebruikers beheren. De rol biedt niet de mogelijkheid om abonnementen te kopen of te beheren, groepen te maken of te beheren of gebruikers te maken of te beheren buiten de gebruikslocatie. Deze rol heeft geen toegang tot het bekijken, maken of beheren van ondersteuningstickets.

### <a name="message-center-privacy-reader"></a>[Privacylezer van het Berichtencentrum](#message-center-privacy-reader-permissions)

Gebruikers in deze rol kunnen alle meldingen in het Berichtencentrum controleren, inclusief gegevensprivacyberichten. Privacylezers van het Berichtencentrum ontvangen e-mailmeldingen, waaronder meldingen die betrekking hebben op gegevensprivacy en ze kunnen zich afmelden met behulp van voorkeuren voor het Berichtencentrum. Alleen de globale beheerder en de privacylezer van het Berichtencentrum kunnen privacyberichten voor gegevens lezen. Bovendien bevat deze rol de mogelijkheid om groepen, domeinen en abonnementen weer te geven. Deze rol heeft geen toestemming om serviceaanvragen te bekijken, te maken of te beheren.

### <a name="message-center-reader"></a>[Berichtcentrumlezer](#message-center-reader-permissions)

Gebruikers in deze rol kunnen meldingen en adviesstatusupdates controleren in [het Office 365 Message Center](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) voor hun organisatie op geconfigureerde services zoals Exchange, Intune en Microsoft Teams. BerichtencentrumLezers ontvangen wekelijkse e-mailsamenvattingen van berichten, updates en kunnen berichten in het berichtencentrum delen in Office 365. In Azure AD hebben gebruikers die aan deze rol zijn toegewezen alleen-lezen toegang op Azure AD-services, zoals gebruikers en groepen. Deze rol heeft geen toegang tot het bekijken, maken of beheren van ondersteuningstickets.

### <a name="network-administrator"></a>[Netwerkbeheerder](#network-administrator-permissions)

Gebruikers in deze rol kunnen aanbevelingen voor netwerkperimeterarchitectuur van Microsoft bekijken die zijn gebaseerd op netwerktelemetrie vanaf hun gebruikerslocaties. Netwerkprestaties voor Office 365 zijn gebaseerd op een zorgvuldige perimeterarchitectuur voor bedrijfsklanten, die over het algemeen specifiek is voor de locatie van de gebruiker. Deze rol maakt het mogelijk om ontdekte gebruikerslocaties en de configuratie van netwerkparameters voor die locaties te bewerken om verbeterde telemetriemetingen en ontwerpaanbevelingen te vergemakkelijken. 

### <a name="office-apps-administrator"></a>[Beheerder van Office Apps](#office-apps-administrator-permissions)

Gebruikers in deze rol kunnen de cloudinstellingen van Office 365-apps beheren. Dit omvat het beheren van cloudbeleid, selfservicedownloadbeheer en de mogelijkheid om office-apps gerelateerd rapport te bekijken. Deze rol biedt bovendien de mogelijkheid om ondersteuningstickets te beheren en de servicestatus te controleren binnen het hoofdbeheercentrum. Gebruikers die aan deze rol zijn toegewezen, kunnen ook de communicatie van nieuwe functies in Office-apps beheren. 

### <a name="partner-tier1-support"></a>[Ondersteuning voor Partner Tier1](#partner-tier1-support-permissions)

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst uit Azure AD verwijderd. Deze rol is bedoeld voor gebruik door een klein aantal Microsoft-wederverkooppartners en is niet bedoeld voor algemeen gebruik.

### <a name="partner-tier2-support"></a>[Ondersteuning voor Partner Tier2](#partner-tier2-support-permissions)

Niet gebruiken. Deze rol is afgeschaft en wordt in de toekomst uit Azure AD verwijderd. Deze rol is bedoeld voor gebruik door een klein aantal Microsoft-wederverkooppartners en is niet bedoeld voor algemeen gebruik.

### <a name="password-administrator"></a>[Wachtwoordbeheerder](#password-administrator-permissions)

Gebruikers met deze rol hebben beperkte mogelijkheden om wachtwoorden te beheren. Deze rol biedt geen mogelijkheid om serviceaanvragen te beheren of de servicestatus te controleren. Wachtwoordbeheerders kunnen wachtwoorden opnieuw instellen van andere gebruikers die alleen niet-beheerders of leden van de volgende rollen zijn:

* Directorylezers
* Gast genodigden
* Wachtwoordbeheerder

### <a name="power-bi-administrator"></a>[Power BI-beheerder](#power-bi-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Power BI, wanneer de service aanwezig is, evenals de mogelijkheid om ondersteuningstickets te beheren en de servicestatus te controleren. Meer informatie op [Inzicht in de Power BI-beheerrol](https://docs.microsoft.com/power-bi/service-admin-role).

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Power BI Service Administrator'. Het is "Power BI Administrator" in de [Azure-portal.](https://portal.azure.com)

### <a name="power-platform-administrator"></a>[Beheerder van het Power-platform](#power-platform-administrator-permissions)

Gebruikers in deze rol kunnen alle aspecten van omgevingen, PowerApps, Flows, Data Loss Prevention policies creëren en beheren. Bovendien hebben gebruikers met deze rol de mogelijkheid om ondersteuningstickets te beheren en de servicestatus te controleren.

### <a name="printer-administrator"></a>[Printerbeheerder](#printer-administrator-permissions)

Gebruikers in deze rol kunnen printers registreren en alle aspecten van alle printerconfiguraties beheren in de Microsoft Universal Print-oplossing, inclusief de instellingen voor universele afdrukconnector. Ze kunnen instemmen met alle gedelegeerde aanvragen voor afdruktoestemming. Printerbeheerders hebben ook toegang tot afdrukrapporten.

### <a name="printer-technician"></a>[Printertechnicus](#printer-technician-permissions)

Gebruikers met deze rol kunnen printers registreren en de printerstatus beheren in de Microsoft Universal Print-oplossing. Ze kunnen ook alle connectorinformatie lezen. Belangrijke taak die een printertechnicus niet kan uitvoeren, is gebruikersmachtigingen instellen voor printers en printers delen.

### <a name="privileged-authentication-administrator"></a>[Beheerder van geprivilegieerde verificatie](#privileged-authentication-administrator-permissions)

Gebruikers met deze rol kunnen niet-wachtwoordreferenties instellen of opnieuw instellen voor alle gebruikers, inclusief globale beheerders, en kunnen wachtwoorden bijwerken voor alle gebruikers. Beheerders van geprivilegieerde verificatie kunnen gebruikers dwingen zich opnieuw te registreren tegen bestaande niet-wachtwoordreferenties (bijvoorbeeld MFA, FIDO) en 'Remember MFA op het apparaat' in te trekken, wat wordt gevraagd om MFA bij de volgende aanmelding van alle gebruikers.

### <a name="privileged-role-administrator"></a>[Beheerder van een bevoorrechte rol](#privileged-role-administrator-permissions)

Gebruikers met deze rol kunnen roltoewijzingen beheren in Azure Active Directory en binnen Azure AD Privileged Identity Management. Daarnaast maakt deze rol het beheer van alle aspecten van Privileged Identity Management en administratieve eenheden mogelijk.

> [!IMPORTANT]
> Met deze rol u toewijzingen beheren voor alle Azure AD-rollen, inclusief de rol Globale beheerder. Deze rol bevat geen andere bevoorrechte mogelijkheden in Azure AD, zoals het maken of bijwerken van gebruikers. Gebruikers die aan deze rol zijn toegewezen, kunnen zichzelf of anderen echter extra bevoegdheden verlenen door extra rollen toe te wijzen.

### <a name="reports-reader"></a>[Rapporten Reader](#reports-reader-permissions)

Gebruikers met deze rol kunnen gebruiksrapportagegegevens en het dashboard rapporten bekijken in het Microsoft 365-beheercentrum en het contextpakket voor adoptie in Power BI. Bovendien biedt de rol toegang tot aanmeldingsrapporten en -activiteiten in Azure AD en gegevens die worden geretourneerd door de Microsoft Graph-rapportage-API. Een gebruiker die is toegewezen aan de rol Rapportenlezer heeft alleen toegang tot relevante gebruiks- en acceptatiestatistieken. Ze hebben geen beheerdersmachtigingen om instellingen te configureren of toegang te krijgen tot de productspecifieke beheercentra zoals Exchange. Deze rol heeft geen toegang tot het bekijken, maken of beheren van ondersteuningstickets.

### <a name="search-administrator"></a>[Zoekbeheerder](#search-administrator-permissions)

Gebruikers in deze rol hebben volledige toegang tot alle functies voor Microsoft Search-beheer in het Microsoft 365-beheercentrum. Zoekbeheerders kunnen de rollen Zoekbeheerders en zoekeditor delegeren aan gebruikers en inhoud maken en beheren, zoals bladwijzers, Q-&Als en locaties. Bovendien kunnen deze gebruikers het berichtencentrum bekijken, de servicestatus controleren en serviceaanvragen maken.

### <a name="search-editor"></a>[Zoekeditor](#search-editor-permissions)

Gebruikers in deze rol kunnen inhoud voor Microsoft Search maken, beheren en verwijderen in het Microsoft 365-beheercentrum, inclusief bladwijzers, Q&As en locaties.

### <a name="security-administrator"></a>[Beveiligingsbeheerder](#security-administrator-permissions)

Gebruikers met deze rol hebben machtigingen voor het beheren van beveiligingsfuncties in het Microsoft 365-beveiligingscentrum, Azure Active Directory Identity Protection, Azure Information Protection en Office 365 Security & Compliance Center. Meer informatie over Office 365-machtigingen is beschikbaar bij [Machtigingen in het Office 365 Security & Compliance Center.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

In | Kan doen
--- | ---
[Microsoft 365-beveiligingscentrum](https://protection.office.com) | Beveiligingsbeleid voor Microsoft 365-services bewaken<br>Beveiligingsbedreigingen en waarschuwingen beheren<br>Rapporten weergeven
Centrum voor identiteitsbescherming | Alle machtigingen van de rol Beveiligingslezer<br>Bovendien u alle bewerkingen van het Identity Protection Center uitvoeren, behalve voor het opnieuw instellen van wachtwoorden
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alle machtigingen van de rol Beveiligingslezer<br>**Kan** azure AD-roltoewijzingen of -instellingen niet beheren
[Office 365-beveiligingscentrum & compliancecentrum](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid beheren<br>Beveiligingsbedreigingen bekijken, onderzoeken en erop reageren<br>Rapporten weergeven
Azure Advanced Threat Protection | Verdachte beveiligingsactiviteiten controleren en erop reageren
Windows Defender ATP en EDR | Rollen toewijzen<br>Machinegroepen beheren<br>Detectie van endpointbedreigingen en geautomatiseerde herstelconfigureren configureren<br>Waarschuwingen weergeven, onderzoeken en erop reageren
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Bekijkt gebruikers-, apparaat-, inschrijvings-, configuratie- en toepassingsgegevens<br>Kan geen wijzigingen aanbrengen in Intune
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Beheerders toevoegen, beleid en instellingen toevoegen, logboeken uploaden en governanceacties uitvoeren
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan beveiligingsbeleid bekijken, beveiligingstoestanden bekijken, beveiligingsbeleid bewerken, waarschuwingen en aanbevelingen bekijken, waarschuwingen en aanbevelingen afwijzen
[Status van Office 365-service](https://docs.microsoft.com/office365/enterprise/view-service-health) | De status van Office 365-services weergeven

### <a name="security-operator"></a>[Beveiligingsoperator](#security-operator-permissions)

Gebruikers met deze rol kunnen waarschuwingen beheren en wereldwijde alleen-lezen toegang hebben voor beveiligingsfuncties, waaronder alle informatie in het Microsoft 365-beveiligingscentrum, Azure Active Directory, Identiteitsbeveiliging, Privileged Identity Management en Office 365 Security & Compliance Center. Meer informatie over Office 365-machtigingen is beschikbaar bij [Machtigingen in het Office 365 Security & Compliance Center.](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center)

In | Kan doen
--- | ---
[Microsoft 365-beveiligingscentrum](https://protection.office.com) | Alle machtigingen van de rol Beveiligingslezer<br>Waarschuwingen voor beveiligingsbedreigingen bekijken, onderzoeken en erop reageren
Centrum voor identiteitsbescherming | Alle machtigingen van de rol Beveiligingslezer<br>Bovendien u alle bewerkingen van het Identity Protection Center uitvoeren, behalve voor het opnieuw instellen van wachtwoorden
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alle machtigingen van de rol Beveiligingslezer
[Office 365-beveiligingscentrum & compliancecentrum](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alle machtigingen van de rol Beveiligingslezer<br>Beveiligingswaarschuwingen weergeven, onderzoeken en erop reageren
Windows Defender ATP en EDR | Alle machtigingen van de rol Beveiligingslezer<br>Beveiligingswaarschuwingen weergeven, onderzoeken en erop reageren
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alle machtigingen van de rol Beveiligingslezer
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Alle machtigingen van de rol Beveiligingslezer
[Status van Office 365-service](https://docs.microsoft.com/office365/enterprise/view-service-health) | De status van Office 365-services weergeven

### <a name="security-reader"></a>[Beveiligingslezer](#security-reader-permissions)

Gebruikers met deze rol hebben algemene alleen-lezen toegang over beveiligingsgerelateerde functies, inclusief alle informatie in microsoft 365-beveiligingscentrum, Azure Active Directory, Identiteitsbeveiliging, Privileged Identity Management en de mogelijkheid om aanmeldingsrapporten en controlelogboeken in Azure Active Directory te lezen en in Office 365 Security & Compliance Center. Meer informatie over Office 365-machtigingen is beschikbaar bij [Machtigingen in het Office 365 Security & Compliance Center.](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)

In | Kan doen
--- | ---
[Microsoft 365-beveiligingscentrum](https://protection.office.com) | Beveiligingsbeleid voor Microsoft 365-services weergeven<br>Beveiligingsbedreigingen en waarschuwingen weergeven<br>Rapporten weergeven
Centrum voor identiteitsbescherming | Alle beveiligingsrapporten en instellingeninformatie voor beveiligingsfuncties lezen<br><ul><li>Anti-spam<li>Versleuteling<li>Preventie van gegevensverlies<li>Anti-malware<li>Geavanceerde beveiliging tegen bedreigingen<li>Anti-phishing<li>Mailflow-regels
[Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Heeft alleen-lezen toegang tot alle informatie die is opgedoken in Azure AD Privileged Identity Management: beleidsregels en rapporten voor Azure AD-roltoewijzingen en beveiligingsbeoordelingen.<br>**U kunt** zich niet aanmelden voor Azure AD Privileged Identity Management of wijzigingen aanbrengen. In de portal Voor beheer van privileged identity of via PowerShell kan iemand in deze rol extra rollen activeren (bijvoorbeeld globale beheerder of bevoorrechte rolbeheerder), als de gebruiker hiervoor in aanmerking komt.
[Office 365-beveiligingscentrum & compliancecentrum](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Beveiligingsbeleid bekijken<br>Beveiligingsbedreigingen bekijken en onderzoeken<br>Rapporten weergeven
Windows Defender ATP en EDR | Waarschuwingen weergeven en onderzoeken. Wanneer u op rollen gebaseerde toegangscontrole inschakelt in Windows Defender ATP, verliezen gebruikers met alleen-lezen machtigingen, zoals de Azure AD Security-lezerrol, de toegang totdat ze zijn toegewezen aan een ATP-rol van Windows Defender.
[Intune](https://docs.microsoft.com/intune/role-based-access-control) | kan alleen gebruikers-, apparaat-, inschrijvings-, configuratie- en toepassingsgegevens weergeven. Kan geen wijzigingen aan Intune aanbrengen.
[Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Heeft alleen-lezen machtigingen en kan waarschuwingen beheren
[Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan aanbevelingen en waarschuwingen bekijken, beveiligingsbeleid bekijken, beveiligingsstatussen bekijken, maar geen wijzigingen aanbrengen
[Status van Office 365-service](https://docs.microsoft.com/office365/enterprise/view-service-health) | De status van Office 365-services weergeven

### <a name="service-support-administrator"></a>[Beheerder van serviceondersteuning](#service-support-administrator-permissions)

Gebruikers met deze rol kunnen ondersteuningsaanvragen openen met Microsoft voor Azure- en Office 365-services en het servicedashboard en het berichtencentrum bekijken in de [Azure-portal](https://portal.azure.com) en [het Microsoft 365-beheercentrum.](https://admin.microsoft.com) Meer informatie bij [Over beheerdersrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> Voorheen heette deze rol 'Serviceadministrator' in [Azure-portal](https://portal.azure.com) en [Microsoft 365-beheercentrum.](https://admin.microsoft.com) We hebben de naam 'Service Support Administrator' gewijzigd om af te stemmen op de naam voor het verwijderen in Microsoft Graph API, Azure AD Graph API en Azure AD PowerShell.

### <a name="sharepoint-administrator"></a>[SharePoint-beheerder](#sharepoint-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft SharePoint Online, wanneer de service aanwezig is, evenals de mogelijkheid om alle Office 365-groepen te maken en te beheren, ondersteuningstickets te beheren en de servicestatus te controleren. Meer informatie bij [Over beheerdersrollen](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'SharePoint Service Administrator'. Het is 'SharePoint Administrator' in de [Azure-portal.](https://portal.azure.com)

### <a name="skype-for-business--lync-administrator"></a>[Skype voor Bedrijven / Lync-beheerder](#lync-service-administrator-permissions)

Gebruikers met deze rol hebben algemene machtigingen binnen Microsoft Skype voor Bedrijven, wanneer de service aanwezig is, evenals skype-specifieke gebruikerskenmerken beheren in Azure Active Directory. Bovendien biedt deze rol de mogelijkheid om ondersteuningstickets te beheren en de servicestatus te controleren en toegang te krijgen tot het Teams en Skype voor Bedrijven-beheercentrum. Het account moet ook een licentie hebben voor Teams of het kan teams PowerShell-cmdlets niet uitvoeren. Meer informatie [over de functie Skype voor Bedrijven-beheerders](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) en licentiegegevens teams bij Skype voor Bedrijven en Microsoft [Teams-invoeglicenties](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

> [!NOTE]
> In de Microsoft Graph API en Azure AD PowerShell wordt deze rol geïdentificeerd als 'Lync Service Administrator'. Het is 'Skype voor Bedrijven-beheerder' in de [Azure-portal.](https://portal.azure.com/)

### <a name="teams-communications-administrator"></a>[Teams Communicatiebeheerder](#teams-communications-administrator-permissions)

Gebruikers in deze rol kunnen aspecten van de Microsoft Teams-workload beheren die betrekking hebben op spraak- & telefonie. Dit omvat de beheertools voor het toewijzing van telefoonnummers, spraak- en vergaderbeleid en volledige toegang tot de toolset voor oproepanalyses.

### <a name="teams-communications-support-engineer"></a>[Teams Communications Support Engineer](#teams-communications-support-engineer-permissions)

Gebruikers in deze rol kunnen communicatieproblemen binnen Microsoft Teams oplossen & Skype voor Bedrijven met behulp van de hulpprogramma's voor het oplossen van problemen met gebruikersoproepen in het Microsoft Teams-& Skype voor Bedrijven-beheercentrum. Gebruikers in deze rol kunnen volledige informatie over oproepgegevens bekijken voor alle betrokken deelnemers. Deze rol heeft geen toegang tot het bekijken, maken of beheren van ondersteuningstickets.

### <a name="teams-communications-support-specialist"></a>[Teams Communications Support Specialist](#teams-communications-support-specialist-permissions)

Gebruikers in deze rol kunnen communicatieproblemen binnen Microsoft Teams oplossen & Skype voor Bedrijven met behulp van de hulpprogramma's voor het oplossen van problemen met gebruikersoproepen in het Microsoft Teams-& Skype voor Bedrijven-beheercentrum. Gebruikers in deze rol kunnen alleen gebruikersgegevens bekijken in de oproep voor de specifieke gebruiker die ze hebben opgezocht. Deze rol heeft geen toegang tot het bekijken, maken of beheren van ondersteuningstickets.

### <a name="teams-service-administrator"></a>[Teams Service Beheerder](#teams-service-administrator-permissions)

Gebruikers in deze rol kunnen alle aspecten van de Microsoft Teams-workload beheren via het Microsoft Teams-& Skype voor Bedrijven-beheercentrum en de respectieve PowerShell-modules. Dit omvat onder andere alle beheertools met betrekking tot telefonie, messaging, vergaderingen en de teams zelf. Deze rol biedt bovendien de mogelijkheid om alle Office 365-groepen te maken en te beheren, ondersteuningstickets te beheren en de servicestatus te controleren.

### <a name="user-administrator"></a>[Gebruikersbeheerder](#user-administrator-permissions)

Gebruikers met deze rol kunnen gebruikers maken en alle aspecten van gebruikers beheren met een aantal beperkingen (zie hieronder) en kunnen het vervaldatumsbeleid van wachtwoorden bijwerken. Bovendien kunnen gebruikers met deze rol alle groepen maken en beheren. Deze rol omvat ook de mogelijkheid om gebruikersweergaven te maken en te beheren, ondersteuningstickets te beheren en de servicestatus te controleren. Gebruikersbeheerders hebben geen toestemming om bepaalde gebruikerseigenschappen voor gebruikers in de meeste beheerdersrollen te beheren. Gebruiker met deze rol heeft geen tolerante gegevens om MFA te beheren. De rollen die uitzonderingen zijn op deze beperking worden weergegeven in de volgende tabel.

| | |
| --- | --- |
|Algemene machtigingen|<p>Gebruikers en groepen maken</p><p>Gebruikersweergaven maken en beheren</p><p>Office-ondersteuningstickets beheren<p>Beleid voor het verlopen van wachtwoorden bijwerken|
|<p>Op alle gebruikers, inclusief alle beheerders</p>|<p>Licenties beheren</p><p>Alle gebruikerseigenschappen beheren, behalve gebruikersnaam</p>
|Alleen op gebruikers die niet-beheerders zijn of in een van de volgende beperkte beheerdersrollen:<ul><li>Directorylezers<li>Gast genodigden<li>Helpdeskbeheerder<li>Berichtcentrumlezer<li>Rapporten Reader<li>Gebruikersbeheerder|<p>Verwijderen en herstellen</p><p>Uitschakelen en inschakelen</p><p>Vernieuwingstokens ongeldig maken</p><p>Alle gebruikerseigenschappen beheren, inclusief gebruikersnaam</p><p>Wachtwoord opnieuw instellen</p><p>Fido-apparaatsleutels (Bijwerken)</p>|

> [!IMPORTANT]
> Gebruikers met deze rol kunnen wachtwoorden wijzigen voor mensen die mogelijk toegang hebben tot gevoelige of privégegevens of kritieke configuratie binnen en buiten Azure Active Directory. Het wijzigen van het wachtwoord van een gebruiker kan de mogelijkheid betekenen om de identiteit en machtigingen van die gebruiker aan te nemen. Bijvoorbeeld:
>
>- Eigenaren van toepassingsregistratie en bedrijfstoepassingen, die referenties van apps die eigenaar zijn, kunnen beheren. Deze apps hebben mogelijk bevoorrechte machtigingen in Azure AD en elders die niet aan gebruikersbeheerders zijn verleend. Via dit pad kan een gebruikersbeheerder mogelijk de identiteit van een toepassingseigenaar aannemen en vervolgens de identiteit van een bevoorrechte toepassing verder aannemen door de referenties voor de toepassing bij te werken.
>- Eigenaren van Azure-abonnementen, die mogelijk toegang hebben tot gevoelige of privégegevens of kritieke configuratie in Azure.
>- Eigenaren van beveiligingsgroep en Office 365-groep, die groepslidmaatschap kunnen beheren. Deze groepen kunnen toegang verlenen tot gevoelige of privé-informatie of kritieke configuratie in Azure AD en elders.
>- Beheerders in andere services buiten Azure AD, zoals Exchange Online, Office Security and Compliance Center en personeelssystemen.
>- Niet-beheerders zoals leidinggevenden, juridisch adviseur en personeelsmedewerkers die mogelijk toegang hebben tot gevoelige of privé-informatie.

## <a name="role-permissions"></a>Functiemachtigingen

In de volgende tabellen worden de specifieke machtigingen in Azure Active Directory beschreven die aan elke rol worden gegeven. Sommige rollen hebben mogelijk aanvullende machtigingen in Microsoft-services buiten Azure Active Directory.

### <a name="application-administrator-permissions"></a>Machtigingen voor toepassingsbeheerder

Kan alle aspecten van app-registraties en bedrijfsapps maken en beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/Application/appProxyAuthentication/update | App Proxy-verificatieeigenschappen bijwerken op serviceprincipals in Azure Active Directory. |
| microsoft.directory/Application/appProxyUrlSettings/update | Interne en externe URLS van de toepassingsproxy bijwerken in Azure Active Directory. |
| microsoft.directory/applications/applicationProxy/read | Lees alle eigenschappen van App Proxy. |
| microsoft.directory/applications/applicationProxy/update | Werk alle eigenschappen van app-proxy bij. |
| microsoft.directory/applications/audience/update | Eigenschap applications.audience bijwerken in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Eigenschap applications.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Basiseigenschappen bijwerken voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/create | Toepassingen maken in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Eigenschap applications.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/applications/delete | Toepassingen verwijderen in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Eigenschap applications.owners bijwerken in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Eigenschap applications.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Eigenschap applications.policies bijwerken in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Maak appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/read | Lees appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | AppRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | AppRoleAssignments verwijderen in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/connectorGroepen/alles/lezen | Eigenschappen van de groep voor toepassingsproxy-groepen lezen in Azure Active Directory. |
| microsoft.directory/connectorGroepen/alles/update | Werk alle eigenschappen van de toepassingsproxyconnector groep in Azure Active Directory bij. |
| microsoft.directory/connectorGroepen/maken | Toepassingsproxyconnectorsgroepen maken in Azure Active Directory. |
| microsoft.directory/connectorGroepen/verwijderen | Groepen voor toepassingsproxy-groepen verwijderen in Azure Active Directory. |
| microsoft.directory/connectors/alles/lezen | Lees alle eigenschappen van de toepassingsproxyconnector in Azure Active Directory. |
| microsoft.directory/connectors/maken | Toepassingsproxyconnectors maken in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Lees de eigenschap policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Beleidsbeheer.applicationConfiguratie in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Beleid maken in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Lees de eigenschap policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Beleidsbeheer.applicationConfiguratie in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Lees de eigenschap policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | ServicePrincipals.appRoleAssignedTo-eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | ServicePrincipals.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Eigenschap servicePrincipals.audience bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Eigenschap servicePrincipals.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen bijwerken op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | ServicePrincipals maken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Eigenschap servicePrincipals.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | ServicePrincipals verwijderen in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Eigenschap servicePrincipals.owners bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Eigenschap servicePrincipals.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Eigenschap servicePrincipals.policies bijwerken in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="application-developer-permissions"></a>Machtigingen voor toepassingsontwikkelaars

Kan toepassingsregistraties maken onafhankelijk van de instelling 'Gebruikers kunnen toepassingen registreren'.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/applications/createAsOwner | Toepassingen maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/appRoleAssignments/createAsOwner | Maak appRoleAssignments in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/oAuth2PermissionGrants/createAsOwner | OAuth2PermissionGrants maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/servicePrincipals/createAsOwner | ServicePrincipals maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |

### <a name="authentication-administrator-permissions"></a>Machtigingen voor verificatiebeheerder

Toegestaan om verificatiemethodegegevens voor elke niet-beheerdersgebruiker te bekijken, in te stellen en opnieuw in te stellen.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Sterke verificatie-eigenschappen zoals MFA-referentiegegevens bijwerken. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in de Office 365-organisatie. Zie online documentatie voor meer details. |

### <a name="azure-devops-administrator-permissions"></a>Machtigingen voor Azure DevOps Administrator

Kan het organisatiebeleid en de instellingen van Azure DevOps beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie [bovenstaande rolbeschrijving](#azure-devops-administrator) voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.devOps/allEntiteiten/allTasks | Azure DevOps lezen en configureren. |

### <a name="azure-information-protection-administrator-permissions"></a>Machtigingen voor Azure Information Protection Administrator

Kan alle aspecten van de Azure Information Protection-service beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie [bovenstaande rolbeschrijving](#) voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.informationProtection/allEntiteiten/allTasks | Beheer alle aspecten van Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="b2c-user-flow-administrator-permissions"></a>Machtigingen voor B2C User Flow Administrator

Maak en beheer alle aspecten van gebruikersstromen.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Gebruikersstromen lezen en configureren in Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator-permissions"></a>Beheerdersvan b2C-gebruikersstroomkenmerkbeheerders

Maak en beheer het kenmerkschema dat beschikbaar is voor alle gebruikersstromen.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.b2c/userKenmerken/allTasks | Gebruikerskenmerken lezen en configureren in Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator-permissions"></a>Machtigingen voor Ad-C IEF Keyset Administrator

Beheer geheimen voor federatie en versleuteling in het Identity Experience Framework.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Belangrijkesets lezen en configureren in Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator-permissions"></a>Machtigingen voor B2C IEF-beleidsbeheerder

Het frameworkbeleid voor vertrouwensrelaties maken en beheren in het Identity Experience Framework.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Aangepaste beleidsregels lezen en configureren in Azure Active Directory B2C. |

### <a name="billing-administrator-permissions"></a>Machtigingen voor factureringsbeheerder

Kan veelvoorkomende factureringsgerelateerde taken uitvoeren, zoals het bijwerken van betalingsgegevens.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/organization/basic/update | Basiseigenschappen op organisatie bijwerken in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.commerce.billing/allEntiteiten/allTasks | Beheer alle aspecten van Office 365-facturering. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="cloud-application-administrator-permissions"></a>Machtigingen voor cloudtoepassingsbeheerder

Kan alle aspecten van app-registraties en bedrijfsapps maken en beheren, behalve App Proxy.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/applications/audience/update | Eigenschap applications.audience bijwerken in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Eigenschap applications.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/applications/basic/update | Basiseigenschappen bijwerken voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/create | Toepassingen maken in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Eigenschap applications.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/applications/delete | Toepassingen verwijderen in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Eigenschap applications.owners bijwerken in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Eigenschap applications.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Eigenschap applications.policies bijwerken in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/create | Maak appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | AppRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | AppRoleAssignments verwijderen in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/create | Beleid maken in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/read | Lees de eigenschap policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/basic/update | Beleidsbeheer.applicationConfiguratie in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/read | Lees de eigenschap policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/owners/update | Beleidsbeheer.applicationConfiguratie in Azure Active Directory. |
| microsoft.directory/policies/applicationConfiguration/policyAppliedTo/read | Lees de eigenschap policies.applicationConfiguration in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | ServicePrincipals.appRoleAssignedTo-eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | ServicePrincipals.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Eigenschap servicePrincipals.audience bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Eigenschap servicePrincipals.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen bijwerken op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | ServicePrincipals maken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Eigenschap servicePrincipals.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | ServicePrincipals verwijderen in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Eigenschap servicePrincipals.owners bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Eigenschap servicePrincipals.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Eigenschap servicePrincipals.policies bijwerken in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="cloud-device-administrator-permissions"></a>Machtigingen voor cloudapparaatbeheerder

Volledige toegang tot het beheren van apparaten in Azure AD.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/delete | Apparaten verwijderen in Azure Active Directory. |
| microsoft.directory/devices/disable | Apparaten uitschakelen in Azure Active Directory. |
| microsoft.directory/devices/enable | Apparaten inschakelen in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |

### <a name="company-administrator-permissions"></a>Machtigingen voor bedrijfsbeheerder

Kan alle aspecten van Azure AD- en Microsoft-services beheren die gebruikmaken van Azure AD-identiteiten. Deze rol wordt ook wel de rol globale beheerder genoemd. 

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntiteiten/allTasks | Maak en verwijder alle bronnen en lees en werk standaardeigenschappen in microsoft.aad.cloudAppSecurity. |
| microsoft.directory/administrativeEenheden/allProperties/allTasks | Beheerinstellingen maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/applications/allProperties/allTasks | Maak en verwijder toepassingen en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/allProperties/allTasks | Maak en verwijder appRoleAssignments en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/contacts/allProperties/allTasks | Contactpersonen maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/contracts/allProperties/allTasks | Maak en verwijder contracten en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/devices/allProperties/allTasks | Apparaten maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/directoryRoles/allProperties/allTasks | Maprollen maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/directoryRoleTemplates/allProperties/allTasks | Maak en verwijder directoryRoleTemplates en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/domains/allProperties/allTasks | Domeinen maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/groups/allProperties/allTasks | Maak en verwijder groepen en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/groupInstellingen/allProperties/allTasks | Groepsinstellingen maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/groupSettingTemplates/allProperties/allTasks | GroepSettingTemplates maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/loginTenantBranding/allProperties/allTasks | LoginTenantBranding maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/oAuth2PermissionGrants/allProperties/allTasks | Maak en verwijder oAuth2PermissionGrants en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/organization/allProperties/allTasks | Maak en verwijder organisatie en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/policies/allProperties/allTasks | Hiermee u beleid maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/roleOpdrachten/allProperties/allTasks | Rollen maken en verwijderenOpdrachten en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/roleDefinities/allProperties/allTasks | Roldefinities maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/scopedRoleMemberships/allProperties/allTasks | ScopedRoleMemberships maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/serviceAction/activateService | De actie Activerende service uitvoeren in Azure Active Directory |
| microsoft.directory/serviceAction/disableDirectoryFeature | Kan de actie Disabledirectoryfeature service uitvoeren in Azure Active Directory |
| microsoft.directory/serviceAction/enableDirectoryFeature | De actie Enabledirectoryfeature service uitvoeren in Azure Active Directory |
| microsoft.directory/serviceAction/getAvailableExtentionProperties | De service actie Getavailableextentionproperties uitvoeren in Azure Active Directory |
| microsoft.directory/servicePrincipals/allProperties/allTasks | Maak en verwijder servicePrincipals en lees en werk alle eigenschappen in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.directory/subscribedSkus/allProperties/allTasks | SubscribedSkus maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directory/users/allProperties/allTasks | Gebruikers maken en verwijderen en alle eigenschappen in Azure Active Directory lezen en bijwerken. |
| microsoft.directorySync/allEntities/allTasks | Voer alle acties uit in Azure AD Connect. |
| microsoft.aad.identityProtection/allEntiteiten/allTasks | Maak en verwijder alle bronnen en lees en werk standaardeigenschappen in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntiteiten/gelezen | Lees alle bronnen in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntiteiten/lezen | Lees alle bronnen in microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntiteiten/allTasks | Beheer alle aspecten van Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.commerce.billing/allEntiteiten/allTasks | Beheer alle aspecten van Office 365-facturering. |
| microsoft.intune/allEntiteiten/alleTaken | Beheer alle aspecten van Intune. |
| microsoft.office365.complianceManager/allEntiteiten/allTasks | Alle aspecten van Office 365 Compliance Manager beheren |
| microsoft.office365.desktopAnalytics/allEntiteiten/allTasks | Beheer alle aspecten van Desktop Analytics. |
| microsoft.office365.exchange/allEntiteiten/allTasks | Beheer alle aspecten van Exchange Online. |
| microsoft.office365.lockbox/allEntiteiten/allTasks | Alle aspecten van de klantenkluis van Office 365 beheren |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityBerichten/gelezen | Lees securityBerichten in microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntiteiten/allTasks | Beheer alle aspecten van Office 365 Protection Center. |
| microsoft.office365.securityComplianceCenter/allEntiteiten/allTasks | Maak en verwijder alle bronnen en lees en werk standaardeigenschappen in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.sharepoint/allEntiteiten/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Beheer alle aspecten van Skype voor Bedrijven Online. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntiteiten/allTasks | Beheer alle aspecten van Dynamics 365. |
| microsoft.powerApps.powerBI/allEntiteiten/allTasks | Beheer alle aspecten van Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntiteiten/lezen | Lees alle bronnen in microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator-permissions"></a>Machtigingen voor nalevingsbeheerders

Kan nalevingsconfiguratie en rapporten lezen en beheren in Azure AD en Office 365.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntiteiten/allTasks | Alle aspecten van Office 365 Compliance Manager beheren |
| microsoft.office365.exchange/allEntiteiten/allTasks | Beheer alle aspecten van Exchange Online. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.sharepoint/allEntiteiten/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Beheer alle aspecten van Skype voor Bedrijven Online. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="compliance-data-administrator-permissions"></a>Machtigingen voor nalevingsgegevensbeheerder

Hiermee maakt en beheert u nalevingsinhoud.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntiteiten/allTasks | Microsoft Cloud App Security lezen en configureren. |
| microsoft.azure.informationProtection/allEntiteiten/allTasks | Beheer alle aspecten van Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntiteiten/allTasks | Alle aspecten van Office 365 Compliance Manager beheren |
| microsoft.office365.exchange/allEntiteiten/allTasks | Beheer alle aspecten van Exchange Online. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.sharepoint/allEntiteiten/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Beheer alle aspecten van Skype voor Bedrijven Online. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="conditional-access-administrator-permissions"></a>Machtigingen voor beheerders van voorwaardelijke toegang

Kan de mogelijkheden voor voorwaardelijke toegang beheren.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/policies/conditionalAccess/basic/read | Lees de eigenschap policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/update | Update policies.conditionalAccess property in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/create | Beleid maken in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/read | Lees de eigenschap policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/owners/update | Update policies.conditionalAccess property in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/policiesAppliedTo/read | Lees de eigenschap policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/tenantDefault/update | Update policies.conditionalAccess property in Azure Active Directory. |

### <a name="crm-service-administrator-permissions"></a>Machtigingen voor CRM-servicebeheerder

Kan alle aspecten van het Dynamics 365-product beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.powerApps.dynamics365/allEntiteiten/allTasks | Beheer alle aspecten van Dynamics 365. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="customer-lockbox-access-approver-permissions"></a>Machtigingen voor klantlockboxtoegangsgoeder

Kan ondersteuningsverzoeken van Microsoft goedkeuren om toegang te krijgen tot organisatiegegevens van klanten.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntiteiten/allTasks | Alle aspecten van de klantenkluis van Office 365 beheren |

### <a name="desktop-analytics-administrator-permissions"></a>Machtigingen voor desktopanalytics-beheerders

Kan de services Desktop Analytics en Office Customization & beheren. Voor Desktop Analytics omvat dit de mogelijkheid om de voorraad van activa te bekijken, implementatieplannen te maken, implementatie en status weer te geven. Met deze functie kunnen gebruikers office-beleid beheren voor Office-&-beleidsservice.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntiteiten/allTasks | Beheer alle aspecten van Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="device-administrators-permissions"></a>Machtigingen voor apparaatbeheerders

Gebruikers die aan deze rol zijn toegewezen, worden toegevoegd aan de groep lokale beheerders op apparaten die zijn verbonden met Azure AD.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/groupInstellingen/basis/gelezen | Lees basiseigenschappen in groepInstellingen in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Lees basiseigenschappen op groepsinstellingSjablonen in Azure Active Directory. |

### <a name="directory-readers-permissions"></a>Machtigingen voor directorylezers
Kan basisdirectory-informatie lezen. Voor het verlenen van toegang tot toepassingen, niet bedoeld voor gebruikers.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/administrativeEenheden/basis/gelezen | Lees basiseigenschappen op beheerEenheden in Azure Active Directory. |
| microsoft.directory/administrativeEenheden/leden/lezen | Lees de eigenschap administrativeUnits.members in Azure Active Directory. |
| microsoft.directory/applications/basic/read | Lees basiseigenschappen voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/owners/read | Lees de eigenschap applications.owners in Azure Active Directory. |
| microsoft.directory/applications/policies/read | Lees de eigenschap applications.policies in Azure Active Directory. |
| microsoft.directory/contacts/basic/read | Lees basiseigenschappen voor contactpersonen in Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read | Lees de eigenschap contacts.memberOf in Azure Active Directory. |
| microsoft.directory/contracts/basic/read | Lees basiseigenschappen voor contracten in Azure Active Directory. |
| microsoft.directory/devices/basic/read | Lees basiseigenschappen op apparaten in Azure Active Directory. |
| microsoft.directory/devices/memberOf/read | Lees devices.memberOf eigenschap in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read | Lees de eigenschap Devices.registeredOwners in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read | Lees de eigenschap devices.registeredUsers in Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read | Lees basiseigenschappen in directoryRollen in Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read | Lees de eigenschap directoryRoles.eligibleMembers in Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read | Lees de eigenschap directoryRoles.members in Azure Active Directory. |
| microsoft.directory/domains/basic/read | Lees basiseigenschappen op domeinen in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read | Lees de eigenschap groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/read | Lees basiseigenschappen in groepen in Azure Active Directory. |
| microsoft.directory/groups/memberOf/read | Lees groups.memberOf eigenschap in Azure Active Directory. |
| microsoft.directory/groepen/leden/lezen | Lees de eigenschap groups.members in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/lezen | De eigenschap groups.owners lezen in Azure Active Directory. |
| microsoft.directory/groups/settings/read | De eigenschap groups.settings lezen in Azure Active Directory. |
| microsoft.directory/groupInstellingen/basis/gelezen | Lees basiseigenschappen in groepInstellingen in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read | Lees basiseigenschappen op groepsinstellingSjablonen in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read | Lees basiseigenschappen op oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/organization/basic/read | Lees basiseigenschappen op organisatie in Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read | Lees de eigenschap organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.directory/roleOpdrachten/basic/read | Lees basiseigenschappen op rollenToewijzingen in Azure Active Directory. |
| microsoft.directory/roleDefinities/basis/gelezen | Lees basiseigenschappen op rolDefinities in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lees de eigenschap ServicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Lees de eigenschap ServicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Lees basiseigenschappen op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Lees de eigenschap ServicePrincipals.memberOf in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lees de eigenschap ServicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Lees de eigenschap ServicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Lees de eigenschap servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Lees de eigenschap servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read | Lees basiseigenschappen op geabonneerdSkus in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read | Lees de eigenschap user.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read | Lees basiseigenschappen voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/directReports/read | Lees de eigenschap users.directReports in Azure Active Directory. |
| microsoft.directory/users/manager/read | Lees de eigenschap users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Lees de eigenschap users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Lees de eigenschap users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Lees de eigenschap users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Lees de eigenschap users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Lees de eigenschap users.registeredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts-permissions"></a>Machtigingen voor adreslijstsynchronisatieaccounts

Alleen gebruikt door Azure AD Connect-service.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/organisatie/dirSync/update | Organization.dirSync bijwerken, eigenschap in Azure Active Directory. |
| microsoft.directory/policies/create | Beleid maken in Azure Active Directory. |
| microsoft.directory/policies/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/basic/read | Lees basiseigenschappen op beleidsregels in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Basiseigenschappen bijwerken voor beleidsregels in Azure Active Directory. |
| microsoft.directory/policies/owners/read | Lees de eigenschap policies.owners in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Eigenschap van updatepolicies.owners in Azure Active Directory. |
| microsoft.directory/policies/policiesAppliedTo/read | Lees policies.policies.policiesAppliedTo property in Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Update policies.tenantStandaardeigenschap in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read | Lees de eigenschap ServicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/update | ServicePrincipals.appRoleAssignedTo-eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read | Lees de eigenschap ServicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/update | ServicePrincipals.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/audience/update | Eigenschap servicePrincipals.audience bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Eigenschap servicePrincipals.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read | Lees basiseigenschappen op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen bijwerken op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | ServicePrincipals maken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Eigenschap servicePrincipals.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read | Lees de eigenschap ServicePrincipals.memberOf in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Lees de eigenschap ServicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read | Lees de eigenschap servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Eigenschap servicePrincipals.owners bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read | Lees de eigenschap ServicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Eigenschap servicePrincipals.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read | Lees de eigenschap servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Eigenschap servicePrincipals.policies bijwerken in Azure Active Directory. |
| microsoft.directorySync/allEntities/allTasks | Voer alle acties uit in Azure AD Connect. |

### <a name="directory-writers-permissions"></a>Machtigingen voor Directory Writers

Kan lezen & basismapinformatie te schrijven. Voor het verlenen van toegang tot toepassingen, niet bedoeld voor gebruikers.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/groups/create | Groepen maken in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Groepen maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/groups/appRoleAssignments/update | Groepen.appRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Basiseigenschappen bijwerken op groepen in Azure Active Directory. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/update | Eigenaren van groepen bijwerken in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Groep.settings bijwerken in Azure Active Directory. |
| microsoft.directory/groupInstellingen/basis/update | Basiseigenschappen bijwerken op groepsinstellingen in Azure Active Directory. |
| microsoft.directory/groupInstellingen/maken | Groepsinstellingen maken in Azure Active Directory. |
| microsoft.directory/groupInstellingen/verwijderen | Groepsinstellingen verwijderen in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Gebruikers.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Licenties beheren voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/basic/update | Basiseigenschappen bijwerken voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/manager/update | Eigenschap users.manager bijwerken in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | De eigenschap userPrincipalName van users.userIn bijwerken in Azure Active Directory. |

### <a name="exchange-service-administrator-permissions"></a>Machtigingen voor Exchange Service Administrator

Kan alle aspecten van het Exchange-product beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Eigenschap groepen.unified bijwerken in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Basiseigenschappen van Office 365-groepen bijwerken. |
| microsoft.directory/groups/unified/create | Office 365-groepen maken. |
| microsoft.directory/groups/unified/delete | Office 365-groepen verwijderen. |
| microsoft.directory/groups/unified/members/update | Het lidmaatschap van Office 365-groepen bijwerken. |
| microsoft.directory/groups/unified/owners/update | Het eigendom van Office 365-groepen bijwerken. |
| microsoft.office365.exchange/allEntiteiten/allTasks | Beheer alle aspecten van Exchange Online. |
| microsoft.office365.network/performance/allProperties/read | Lees netwerkprestatiepagina's in het M365-beheercentrum. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="external-identity-provider-administrator-permissions"></a>Beheerders van externe identiteitsprovider

Identiteitsproviders configureren voor gebruik in directe federatie.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Identiteitsproviders lezen en configureren in Azure Active Directory B2C. |

### <a name="global-reader-permissions"></a>Machtigingen voor globale lezers
Kan alles lezen wat een globale beheerder kan, maar niet om het even wat bewerk.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie [bovenstaande rolbeschrijving](#global-reader) voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.commerce.billing/allEntiteiten/gelezen    | Lees alle aspecten van Office 365-facturering. |
| microsoft.directory/administrativeEenheden/basis/gelezen    | Lees basiseigenschappen op beheerEenheden in Azure Active Directory. |
| microsoft.directory/administrativeEenheden/leden/lezen    | Lees de eigenschap administrativeUnits.members in Azure Active Directory. |
| microsoft.directory/applications/basic/read    | Lees basiseigenschappen voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/owners/read    | Lees de eigenschap applications.owners in Azure Active Directory. |
| microsoft.directory/applications/policies/read    | Lees de eigenschap applications.policies in Azure Active Directory. |
| microsoft.directory/contacts/basic/read    | Lees basiseigenschappen voor contactpersonen in Azure Active Directory. |
| microsoft.directory/contacts/memberOf/read    | Lees de eigenschap contacts.memberOf in Azure Active Directory. |
| microsoft.directory/contracts/basic/read    | Lees basiseigenschappen voor contracten in Azure Active Directory. |
| microsoft.directory/devices/basic/read    | Lees basiseigenschappen op apparaten in Azure Active Directory. |
| microsoft.directory/devices/memberOf/read    | Lees devices.memberOf eigenschap in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/read    | Lees de eigenschap Devices.registeredOwners in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/read    | Lees de eigenschap devices.registeredUsers in Azure Active Directory. |
| microsoft.directory/directoryRoles/basic/read    | Lees basiseigenschappen in directoryRollen in Azure Active Directory. |
| microsoft.directory/directoryRoles/eligibleMembers/read    | Lees de eigenschap directoryRoles.eligibleMembers in Azure Active Directory. |
| microsoft.directory/directoryRoles/members/read    | Lees de eigenschap directoryRoles.members in Azure Active Directory. |
| microsoft.directory/domains/basic/read    | Lees basiseigenschappen op domeinen in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/read    | Lees de eigenschap groups.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/groups/basic/read    | Lees basiseigenschappen in groepen in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read    | Groep.hiddenMembers lezen, eigenschap in Azure Active Directory. |
| microsoft.directory/groups/memberOf/read    | Lees groups.memberOf eigenschap in Azure Active Directory. |
| microsoft.directory/groepen/leden/lezen    | Lees de eigenschap groups.members in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/lezen    | De eigenschap groups.owners lezen in Azure Active Directory. |
| microsoft.directory/groups/settings/read    | De eigenschap groups.settings lezen in Azure Active Directory. |
| microsoft.directory/groupInstellingen/basis/gelezen    | Lees basiseigenschappen in groepInstellingen in Azure Active Directory. |
| microsoft.directory/groupSettingTemplates/basic/read    | Lees basiseigenschappen op groepsinstellingSjablonen in Azure Active Directory. |
| microsoft.directory/oAuth2PermissionGrants/basic/read    | Lees basiseigenschappen op oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/organization/basic/read    | Lees basiseigenschappen op organisatie in Azure Active Directory. |
| microsoft.directory/organization/trustedCAsForPasswordlessAuth/read    | Lees de eigenschap organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.directory/policies/standard/read    | Lees standaardbeleidsregels in Azure Active Directory. |
| microsoft.directory/roleOpdrachten/basic/read    | Lees basiseigenschappen op rollenToewijzingen in Azure Active Directory. |
| microsoft.directory/roleDefinities/basis/gelezen    | Lees basiseigenschappen op rolDefinities in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/read    | Lees de eigenschap ServicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.directory/servicePrincipals/appRoleAssignments/read    | Lees de eigenschap ServicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/read    | Lees basiseigenschappen op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/memberOf/read    | Lees de eigenschap ServicePrincipals.memberOf in Azure Active Directory. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/basic/read    | Lees de eigenschap ServicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/servicePrincipals/ownedObjects/read    | Lees de eigenschap ServicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/read    | Lees de eigenschap servicePrincipals.owners in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/read    | Lees de eigenschap servicePrincipals.policies in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read    | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.directory/subscribedSkus/basic/read    | Lees basiseigenschappen op geabonneerdSkus in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/read    | Lees de eigenschap user.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read    | Lees basiseigenschappen voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/directReports/read    | Lees de eigenschap users.directReports in Azure Active Directory. |
| microsoft.directory/users/manager/read    | Lees de eigenschap users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read    | Lees de eigenschap users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read    | Lees de eigenschap users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read    | Lees de eigenschap users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read    | Lees de eigenschap users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read    | Lees de eigenschap users.registeredDevices in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/read    | Lees sterke verificatie-eigenschappen zoals MFA-referentiegegevens. |
| microsoft.office365.exchange/allEntiteiten/lezen    | Lees alle aspecten van Exchange Online. |
| microsoft.office365.messageCenter/berichten/gelezen    | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityBerichten/gelezen    | Lees securityBerichten in microsoft.office365.messageCenter. |
| microsoft.office365.network/performance/allProperties/read | Lees netwerkprestatiepagina's in het M365-beheercentrum. |
| microsoft.office365.protectionCenter/allEntities/read    | Lees alle aspecten van Office 365 Protection Center. |
| microsoft.office365.securityComplianceCenter/allEntiteiten/gelezen    | Lees alle standaardeigenschappen in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.usageReports/allEntiteiten/gelezen    | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.webPortal/allEntiteiten/standaard/gelezen    | Lees standaardeigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="groups-administrator-permissions"></a>Beheerdersmachtigingen groepen
Kan alle aspecten van groepen en groepsinstellingen beheren, zoals naamgevings- en expiratiebeleid.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/groups/basic/read | Lees standaardeigenschappen in Groepen in Azure Active Directory.  |
| microsoft.directory/groups/basic/update | Basiseigenschappen bijwerken op groepen in Azure Active Directory. |
| microsoft.directory/groups/create | Groepen maken in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Groepen maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/groups/delete | Groepen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Groep.hiddenMembers lezen, eigenschap in Azure Active Directory. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/update | Eigenaren van groepen bijwerken in Azure Active Directory. |
| microsoft.directory/groups/restore | Groepen herstellen in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Groep.settings bijwerken in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="guest-inviter-permissions"></a>Machtigingen voor gastgenodigden
Kan gastgebruikers uitnodigen die onafhankelijk zijn van de instelling 'leden kunnen gasten uitnodigen'.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/users/appRoleAssignments/read | Lees de eigenschap user.appRoleAssignments in Azure Active Directory. |
| microsoft.directory/users/basic/read | Lees basiseigenschappen voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/directReports/read | Lees de eigenschap users.directReports in Azure Active Directory. |
| microsoft.directory/users/inviteGuest | Nodig gastgebruikers uit in Azure Active Directory. |
| microsoft.directory/users/manager/read | Lees de eigenschap users.manager in Azure Active Directory. |
| microsoft.directory/users/memberOf/read | Lees de eigenschap users.memberOf in Azure Active Directory. |
| microsoft.directory/users/oAuth2PermissionGrants/basic/read | Lees de eigenschap users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.directory/users/ownedDevices/read | Lees de eigenschap users.ownedDevices in Azure Active Directory. |
| microsoft.directory/users/ownedObjects/read | Lees de eigenschap users.ownedObjects in Azure Active Directory. |
| microsoft.directory/users/registeredDevices/read | Lees de eigenschap users.registeredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator-permissions"></a>Machtigingen voor helpdeskbeheerder

Kan wachtwoorden opnieuw instellen voor niet-beheerders en helpdeskbeheerders.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in Azure Active Directory. Zie online documentatie voor meer details. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="hybrid-identity-administrator-permissions"></a>Machtigingen voor hybride identiteitsbeheerder

Cloudprovisioning- en authenticatieservices inschakelen, implementeren, configureren, beheren, bewaken en oplossen. 

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.directory/applications/audience/update  | Eigenschap applications.audience bijwerken in Azure Active Directory. |
| microsoft.directory/applications/authentication/update | Eigenschap applications.authentication bijwerken in Azure Active Directory.  |
| microsoft.directory/applications/basic/update | Basiseigenschappen bijwerken voor toepassingen in Azure Active Directory. |
| microsoft.directory/applications/create | Toepassingen maken in Azure Active Directory. |
| microsoft.directory/applications/credentials/update | Eigenschap applications.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/applications/delete | Toepassingen verwijderen in Azure Active Directory. |
| microsoft.directory/applications/owners/update | Eigenschap applications.owners bijwerken in Azure Active Directory. |
| microsoft.directory/applications/permissions/update | Eigenschap applications.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/applications/policies/update | Eigenschap applications.policies bijwerken in Azure Active Directory. |
| microsoft.directory/applicationSjablonen/instantiate | Instantiate galerijtoepassingen van toepassingssjablonen. |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/cloudProvisioning/allProperties/allTasks | Lees en configureer alle eigenschappen van de Azure AD Cloud Provisioning-service. |
| microsoft.directory/federatedAuthentication/allProperties/allTasks | Beheer alle aspecten van Active Directory Federated Services (ADFS) of federatieprovider van derden in Azure AD. |
| microsoft.directory/organisatie/dirSync/update | Organization.dirSync bijwerken, eigenschap in Azure Active Directory. |
| microsoft.directory/passwordHashSync/allProperties/allTasks | Beheer alle aspecten van Password Hash Sync (PHS) in Azure AD. |
| microsoft.directory/passThroughAuthentication/allProperties/allTasks | Beheer alle aspecten van Pass-through Authentication (PTA) in Azure AD. |
| microsoft.directory/seamlessSSO/allProperties/allTasks | Beheer alle aspecten van naadloze single sign-on (SSO) in Azure AD. |
| microsoft.directory/servicePrincipals/audience/update | Eigenschap servicePrincipals.audience bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/authentication/update | Eigenschap servicePrincipals.authentication bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/basic/update | Basiseigenschappen bijwerken op servicePrincipals in Azure Active Directory. |
| microsoft.directory/servicePrincipals/create | ServicePrincipals maken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/credentials/update | Eigenschap servicePrincipals.credentials bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/delete | ServicePrincipals verwijderen in Azure Active Directory. |
| microsoft.directory/servicePrincipals/owners/update | Eigenschap servicePrincipals.owners bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/permissions/update | Eigenschap servicePrincipals.permissions bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Eigenschap servicePrincipals.policies bijwerken in Azure Active Directory. |
| microsoft.directory/servicePrincipals/synchronizationJobs/manage | Beheer alle aspecten van synchronisatietaken in Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationSchema/manage | Beheer alle aspecten van het synchronisatieschema in Azure AD. |
| microsoft.directory/servicePrincipals/synchronizationCredentials/manage | Beheer alle aspecten van synchronisatiereferenties in Azure AD. |
| microsoft.directory/servicePrincipals/tag/update | Eigenschap servicePrincipals.tag bijwerken in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |


### <a name="intune-service-administrator-permissions"></a>Machtigingen voor intune-servicebeheerders

Kan alle aspecten van het Intune-product beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen in Azure Active Directory bijwerken. |
| microsoft.directory/contacts/maken | Contactpersonen maken in Azure Active Directory. |
| microsoft.directory/contacts/delete | Contactpersonen verwijderen in Azure Active Directory. |
| microsoft.directory/devices/basic/update | Basiseigenschappen bijwerken op apparaten in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/devices/create | Apparaten maken in Azure Active Directory. |
| microsoft.directory/devices/delete | Apparaten verwijderen in Azure Active Directory. |
| microsoft.directory/devices/registeredOwners/update | Devices.registered-eigenaren bijwerken in Azure Active Directory. |
| microsoft.directory/devices/registeredUsers/update | Devices.registered-apparaten bijwerkenDe eigenschap van Gebruikers in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Groepen.appRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Basiseigenschappen bijwerken op groepen in Azure Active Directory. |
| microsoft.directory/groups/create | Groepen maken in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Groepen maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/groups/delete | Groepen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Groep.hiddenMembers lezen, eigenschap in Azure Active Directory. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/update | Eigenaren van groepen bijwerken in Azure Active Directory. |
| microsoft.directory/groups/restore | Groepen herstellen in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Groep.settings bijwerken in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Gebruikers.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/users/basic/update | Basiseigenschappen bijwerken voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/manager/update | Eigenschap users.manager bijwerken in Azure Active Directory. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.intune/allEntiteiten/alleTaken | Beheer alle aspecten van Intune. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="kaizala-administrator-permissions"></a>Machtigingen voor Kaizala-beheerders

Kan instellingen voor Microsoft Kaizala beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees Office 365-beheercentrum. |

### <a name="license-administrator-permissions"></a>Machtigingen voor licentiebeheerder

Kan productlicenties beheren voor gebruikers en groepen.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/users/assignLicense | Licenties beheren voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/usageLocatie/update | Gebruikers.usage bijwerkenLocatieeigenschap in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |

### <a name="lync-service-administrator-permissions"></a>Machtigingen voor Lync-servicebeheerder

Kan alle aspecten van het Skype voor Bedrijven-product beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Beheer alle aspecten van Skype voor Bedrijven Online. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen    | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |


### <a name="message-center-privacy-reader-permissions"></a>Machtigingen voor privacylezer message center

Kan Berichten van Het Berichtencentrum, gegevensprivacyberichten, groepen, domeinen en abonnementen lezen.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityBerichten/gelezen | Lees securityBerichten in microsoft.office365.messageCenter. |

### <a name="message-center-reader-permissions"></a>Machtigingen voor Message Center Reader
Kan alleen berichten en updates voor hun organisatie lezen in Office 365 Message Center. 

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |

### <a name="network-administrator-permissions"></a>Machtigingen voor netwerkbeheerder
Kan netwerklocaties beheren en de inzichten in het ontwerp van bedrijfsnetwerken voor Microsoft 365 Software as a Service-toepassingen bekijken.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.network/performance/allProperties/read | Lees netwerkprestatiepagina's in het M365-beheercentrum.  |
| microsoft.office365.network/locaties/allProperties/allTasks | Lees en configureer netwerklocaties eigenschappen voor elke locatie. |

### <a name="office-apps-administrator-permissions"></a>Beheerdersmachtigingen voor Office Apps
Kan de cloudservices van Office-apps beheren, inclusief beheer van beleid en instellingen, en de mogelijkheid beheren om 'wat is er nieuw' functie-inhoud te selecteren, de selectie uit te voeren en te publiceren op apparaten van de eindgebruiker.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.userCommunicatie/alleEntiteiten/alleTaken | Lees en werk de zichtbaarheid van nieuwe berichten uit. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="partner-tier1-support-permissions"></a>Machtigingen voor Ondersteuning voor PartnerTier1

Niet gebruiken - niet bedoeld voor algemeen gebruik.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen in Azure Active Directory bijwerken. |
| microsoft.directory/contacts/maken | Contactpersonen maken in Azure Active Directory. |
| microsoft.directory/contacts/delete | Contactpersonen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/create | Groepen maken in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Groepen maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/update | Eigenaren van groepen bijwerken in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Gebruikers.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Licenties beheren voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/basic/update | Basiseigenschappen bijwerken voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/delete | Gebruikers verwijderen in Azure Active Directory. |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/manager/update | Eigenschap users.manager bijwerken in Azure Active Directory. |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in Azure Active Directory. Zie online documentatie voor meer details. |
| microsoft.directory/users/restore | Verwijderde gebruikers herstellen in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | De eigenschap userPrincipalName van users.userIn bijwerken in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="partner-tier2-support-permissions"></a>Machtigingen voor Partner Tier2-ondersteuning

Niet gebruiken - niet bedoeld voor algemeen gebruik.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen in Azure Active Directory bijwerken. |
| microsoft.directory/contacts/maken | Contactpersonen maken in Azure Active Directory. |
| microsoft.directory/contacts/delete | Contactpersonen verwijderen in Azure Active Directory. |
| microsoft.directory/domains/allTasks | Domeinen maken en verwijderen en standaardeigenschappen lezen en bijwerken in Azure Active Directory. |
| microsoft.directory/groups/create | Groepen maken in Azure Active Directory. |
| microsoft.directory/groups/delete | Groepen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groups/restore | Groepen herstellen in Azure Active Directory. |
| microsoft.directory/organization/basic/update | Basiseigenschappen op organisatie bijwerken in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Gebruikers.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Licenties beheren voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/basic/update | Basiseigenschappen bijwerken voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/delete | Gebruikers verwijderen in Azure Active Directory. |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/manager/update | Eigenschap users.manager bijwerken in Azure Active Directory. |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in Azure Active Directory. Zie online documentatie voor meer details. |
| microsoft.directory/users/restore | Verwijderde gebruikers herstellen in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | De eigenschap userPrincipalName van users.userIn bijwerken in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="password-administrator-permissions"></a>Machtigingen voor wachtwoordbeheerder

Kan wachtwoorden opnieuw instellen voor niet-beheerders en wachtwoordbeheerders.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in Azure Active Directory. Zie online documentatie voor meer details. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="power-bi-service-administrator-permissions"></a>Machtigingen voor Power BI-servicebeheerder

Kan alle aspecten van het Power BI-product beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.powerApps.powerBI/allEntiteiten/allTasks | Beheer alle aspecten van Power BI. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |


### <a name="power-platform-administrator-permissions"></a>Machtigingen voor Power Platform Administrator

Kan alle aspecten van Microsoft Dynamics 365, PowerApps en Microsoft Flow maken en beheren. 

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.dynamics365/allEntiteiten/allTasks | Beheer alle aspecten van Dynamics 365. |
| microsoft.flow/allEntiteiten/alleTaken | Beheer alle aspecten van Microsoft Flow. |
| microsoft.powerApps/allEntiteiten/allTasks | Beheer alle aspecten van PowerApps. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="printer-administrator-permissions"></a>Machtigingen voor printerbeheerder

Kan alle aspecten van printers en printerconnectoren beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.print/allEntiteiten/allProperties/allTasks | Maak en verwijder printers en connectoren en lees en werk alle eigenschappen in Microsoft Print. |

### <a name="printer-technician-permissions"></a>Machtigingen printertechnicus

Kan printers registreren en uitschrijven en de status van de printer bijwerken.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>
| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.print/connectors/allProperties/read | Lees alle eigenschappen van connectors in Microsoft Print. |
| microsoft.azure.print/printers/allProperties/read | Lees alle eigenschappen van printers in Microsoft Print. |
| microsoft.azure.print/printers/basic/update | Basiseigenschappen van printers in Microsoft Print bijwerken. |
| microsoft.azure.print/printers/register | Printers registreren in Microsoft Print. |
| microsoft.azure.print/printers/uitschrijven | Het registreren van printers in Microsoft Print. |

### <a name="privileged-authentication-administrator-permissions"></a>Machtigingen voor bevoegde verificatiebeheerders

Toegestaan om verificatiemethodegegevens voor elke gebruiker (beheerder of niet-beheerder) te bekijken, in te stellen en opnieuw in te stellen.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/strongAuthentication/update | Sterke verificatie-eigenschappen zoals MFA-referentiegegevens bijwerken. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in de Office 365-organisatie. Zie online documentatie voor meer details. |

### <a name="privileged-role-administrator-permissions"></a>Machtigingen voor bevoegde rolbeheerders

Kan roltoewijzingen beheren in Azure AD en alle aspecten van Privileged Identity Management.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntiteiten/allTasks | Maak en verwijder alle bronnen en lees en werk standaardeigenschappen in microsoft.aad.privilegedIdentityManagement. |
| microsoft.directory/servicePrincipals/appRoleAssignedTo/allTasks | De eigenschap ServicePrincipals.appRoleAssignedTo in Azure Active Directory lezen en configureren. |
| microsoft.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | De eigenschap ServicePrincipals.oAuth2PermissionGrants lezen en configureren in Azure Active Directory. |
| microsoft.directory/administrativeEenheden/allProperties/allTasks | Administratieve eenheden maken en beheren (inclusief leden) |
| microsoft.directory/roleOpdrachten/allProperties/allTasks | Roltoewijzingen maken en beheren. |
| microsoft.directory/roleDefinities/allProperties/allTasks | Roldefinities maken en beheren. |

### <a name="reports-reader-permissions"></a>Rapporten Reader machtigingen

U aanmeldings- en controlerapporten lezen.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen | Lees de gebruiksrapporten van Office 365. |

### <a name="search-administrator-permissions"></a>Machtigingen voor zoekbeheerders

Kan alle aspecten van Microsoft Search-instellingen maken en beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntiteiten/allProperties/allTasks | Alle bronnen maken en verwijderen en alle eigenschappen in microsoft.office365.search lezen en bijwerken. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="search-editor-permissions"></a>Machtigingen voor zoekeditor

Kan de redactionele inhoud maken en beheren, zoals bladwijzers, Q en As, locaties, plattegrond.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.office365.messageCenter/berichten/gelezen | Lees berichten in microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Maak en verwijder inhoud en lees en werk alle eigenschappen in microsoft.office365.search. |

### <a name="security-administrator-permissions"></a>Machtigingen voor beveiligingsbeheerders

Kan beveiligingsinformatie en -rapporten lezen en de configuratie beheren in Azure AD en Office 365.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/applications/policies/update | Eigenschap applications.policies bijwerken in Azure Active Directory. |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/policies/basic/update | Basiseigenschappen bijwerken voor beleidsregels in Azure Active Directory. |
| microsoft.directory/policies/create | Beleid maken in Azure Active Directory. |
| microsoft.directory/policies/delete | Beleid verwijderen in Azure Active Directory. |
| microsoft.directory/policies/owners/update | Eigenschap van updatepolicies.owners in Azure Active Directory. |
| microsoft.directory/policies/tenantDefault/update | Update policies.tenantStandaardeigenschap in Azure Active Directory. |
| microsoft.directory/servicePrincipals/policies/update | Eigenschap servicePrincipals.policies bijwerken in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntiteiten/lezen | Lees alle bronnen in microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntiteiten/update | Werk alle bronnen bij in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntiteiten/gelezen | Lees alle bronnen in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Lees alle aspecten van Office 365 Protection Center. |
| microsoft.office365.protectionCenter/allEntiteiten/update | Werk alle bronnen bij in microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |

### <a name="security-operator-permissions"></a>Machtigingen voor beveiligingsoperatoren

Hiermee maakt en beheert u beveiligingsgebeurtenissen.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntiteiten/allTasks | Microsoft Cloud App Security lezen en configureren. |
| microsoft.aad.identityProtection/allEntiteiten/lezen | Lees alle bronnen in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntiteiten/gelezen | Lees alle bronnen in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntiteiten/lezen | Lees en configureer Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntiteiten/alleTaken | Beheer alle aspecten van Intune. |
| microsoft.office365.securityComplianceCenter/allEntiteiten/allTasks | Lees en configureer beveiliging & Compliance Center. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntiteiten/lezen | Lees en configureer Windows Defender Advanced Threat Protection. |

### <a name="security-reader-permissions"></a>Machtigingen voor beveiligingslezers

Kan beveiligingsinformatie en -rapporten lezen in Azure AD en Office 365.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/auditLogs/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op auditLogs in Azure Active Directory. |
| microsoft.directory/devices/bitLockerRecoveryKeys/read | Lees de eigenschap devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.directory/policies/conditionalAccess/basic/read | Lees de eigenschap policies.conditionalAccess in Azure Active Directory. |
| microsoft.directory/signInReports/allProperties/read | Lees alle eigenschappen (inclusief bevoorrechte eigenschappen) op signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntiteiten/lezen | Lees alle bronnen in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntiteiten/gelezen | Lees alle bronnen in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Lees alle aspecten van Office 365 Protection Center. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |

### <a name="service-support-administrator-permissions"></a>Machtigingen voor beheerders van serviceondersteuning

Kan informatie over de gezondheid van de service lezen en ondersteuningstickets beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

### <a name="sharepoint-service-administrator-permissions"></a>Machtigingen voor SharePoint-servicebeheerder

Kan alle aspecten van de SharePoint-service beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Eigenschap groepen.unified bijwerken in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Basiseigenschappen van Office 365-groepen bijwerken. |
| microsoft.directory/groups/unified/create | Office 365-groepen maken. |
| microsoft.directory/groups/unified/delete | Office 365-groepen verwijderen. |
| microsoft.directory/groups/unified/members/update | Het lidmaatschap van Office 365-groepen bijwerken. |
| microsoft.directory/groups/unified/owners/update | Het eigendom van Office 365-groepen bijwerken. |
| microsoft.office365.network/performance/allProperties/read | Lees netwerkprestatiepagina's in het M365-beheercentrum. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.sharepoint/allEntiteiten/allTasks | Alle resources maken en verwijderen en standaardeigenschappen lezen en bijwerken in microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen    | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="teams-communications-administrator-permissions"></a>Machtigingen voor communicatiebeheerders van Teams

Kan de functies voor bellen en vergaderen beheren binnen de Microsoft Teams-service.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen | Lees de gebruiksrapporten van Office 365. |

### <a name="teams-communications-support-engineer-permissions"></a>Machtigingen voor Teams Communications Support Engineer

Kan communicatieproblemen binnen Teams oplossen met geavanceerde tools.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |

### <a name="teams-communications-support-specialist-permissions"></a>Teams Communications Support Specialist machtigingen

Kan communicatieproblemen binnen Teams oplossen met basistools.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |

### <a name="teams-service-administrator-permissions"></a>Machtigingen voor Teams Service Administrator

Kan de Microsoft Teams-service beheren.

> [!NOTE]
> Deze rol heeft extra machtigingen buiten Azure Active Directory. Zie bovenstaande rolbeschrijving voor meer informatie.
>
>

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.directory/groups/hiddenMembers/read | Groep.hiddenMembers lezen, eigenschap in Azure Active Directory. |
| microsoft.directory/groups/unified/appRoleAssignments/update | Eigenschap groepen.unified bijwerken in Azure Active Directory. |
| microsoft.directory/groups/unified/basic/update | Basiseigenschappen van Office 365-groepen bijwerken. |
| microsoft.directory/groups/unified/create | Office 365-groepen maken. |
| microsoft.directory/groups/unified/delete | Office 365-groepen verwijderen. |
| microsoft.directory/groups/unified/members/update | Het lidmaatschap van Office 365-groepen bijwerken. |
| microsoft.directory/groups/unified/owners/update | Het eigendom van Office 365-groepen bijwerken. |
| microsoft.office365.network/performance/allProperties/read | Lees netwerkprestatiepagina's in het M365-beheercentrum. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |
| microsoft.office365.usageReports/allEntiteiten/gelezen | Lees de gebruiksrapporten van Office 365. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |

### <a name="user-administrator-permissions"></a>Machtigingen voor gebruikersbeheerders
Kan alle aspecten van gebruikers en groepen beheren, inclusief het opnieuw instellen van wachtwoorden voor beperkte beheerders.

| **Acties** | **Beschrijving** |
| --- | --- |
| microsoft.directory/appRoleAssignments/create | Maak appRoleAssignments in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/delete | AppRoleAssignments verwijderen in Azure Active Directory. |
| microsoft.directory/appRoleAssignments/update | AppRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/contacts/basic/update | Basiseigenschappen voor contactpersonen in Azure Active Directory bijwerken. |
| microsoft.directory/contacts/maken | Contactpersonen maken in Azure Active Directory. |
| microsoft.directory/contacts/delete | Contactpersonen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/appRoleAssignments/update | Groepen.appRoleAssignments bijwerken in Azure Active Directory. |
| microsoft.directory/groups/basic/update | Basiseigenschappen bijwerken op groepen in Azure Active Directory. |
| microsoft.directory/groups/create | Groepen maken in Azure Active Directory. |
| microsoft.directory/groups/createAsOwner | Groepen maken in Azure Active Directory. Creator wordt toegevoegd als de eerste eigenaar en het gemaakte object telt mee voor het quotum van 250 gemaakte objecten van de maker. |
| microsoft.directory/groups/delete | Groepen verwijderen in Azure Active Directory. |
| microsoft.directory/groups/hiddenMembers/read | Groep.hiddenMembers lezen, eigenschap in Azure Active Directory. |
| microsoft.directory/groups/members/update | Groep.members bijwerken in Azure Active Directory. |
| microsoft.directory/groepen/eigenaren/update | Eigenaren van groepen bijwerken in Azure Active Directory. |
| microsoft.directory/groups/restore | Groepen herstellen in Azure Active Directory. |
| microsoft.directory/groups/settings/update | Groep.settings bijwerken in Azure Active Directory. |
| microsoft.directory/users/appRoleAssignments/update | Gebruikers.appRoleAssignments eigenschap bijwerken in Azure Active Directory. |
| microsoft.directory/users/assignLicense | Licenties beheren voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/basic/update | Basiseigenschappen bijwerken voor gebruikers in Azure Active Directory. |
| microsoft.directory/users/create | Maak gebruikers in Azure Active Directory. |
| microsoft.directory/users/delete | Gebruikers verwijderen in Azure Active Directory. |
| microsoft.directory/users/ongeldig allvalidateAllRefreshTokens | Alle vernieuwingstokens van gebruikers ongeldig maken in Azure Active Directory. |
| microsoft.directory/users/manager/update | Eigenschap users.manager bijwerken in Azure Active Directory. |
| microsoft.directory/users/password/update | Wachtwoorden bijwerken voor alle gebruikers in Azure Active Directory. Zie online documentatie voor meer details. |
| microsoft.directory/users/restore | Verwijderde gebruikers herstellen in Azure Active Directory. |
| microsoft.directory/users/userPrincipalName/update | De eigenschap userPrincipalName van users.userIn bijwerken in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntiteiten/allTasks | Azure Service Status lezen en configureren. |
| microsoft.azure.supportTickets/allEntiteiten/allTasks | Azure-ondersteuningstickets maken en beheren. |
| microsoft.office365.webPortal/allEntiteiten/basic/read | Lees basiseigenschappen op alle bronnen in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntiteiten/allTasks | Office 365-servicestatus lezen en configureren. |
| microsoft.office365.supportTickets/allEntiteiten/allTasks | Office 365-ondersteuningstickets maken en beheren. |

## <a name="role-template-ids"></a>Rolsjabloon-iD's

Rolsjabloon-id's worden voornamelijk gebruikt door de Microsoft Graph API- of PowerShell-gebruikers.

GrafiekweergaveNaam | Weergavenaam azure-portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Toepassingsbeheerder | Toepassingsbeheerder | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Toepassingsontwikkelaar | Toepassingsontwikkelaar | CF1C38E5-3621-4004-A7CB-879624DCED7C
Verificatiebeheerder | Verificatiebeheerder | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure DevOps-beheerder | Azure DevOps-beheerder | e3973bdf-4987-49ae-837a-ba8e231c7286
Azure-beheerder voor gegevensbeveiliging | Azure Information Protection-beheerder | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C-gebruikersstroombeheerder | B2C-gebruikersstroombeheerder | 6e591065-9bad-43ed-90f3-e9424366d2f0
Beheerder van b2c-gebruikersstroomkenmerk | Beheerder van b2c-gebruikersstroomkenmerk | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF Keyset Administrator | B2C IEF Keyset Administrator | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF-beleidsbeheerder | B2C IEF-beleidsbeheerder | 3edaf663-341e-4475-9f94-5c398ef6c070
Factureringsbeheerder | Factureringsbeheerder | b0f54661-2d74-4c50-afa3-1ec803f12efe
Beheerder van de cloudtoepassing | Beheerder van cloudtoepassingen | 158c047a-c907-4556-b7ef-446551a6b5f7
Beheerder van cloudapparaten | Beheerder van cloudapparaten | 7698a772-787b-4ac8-901f-60d6b08affd2
Company Administratoristrain opr | Globale beheerder | 62e90394-69f5-4237-9190-012177145e10
Beheerder voor naleving | Compliancebeheerder | 17315797-102d-40b4-93e0-432062caca18
Beheerder voor nalevingsgegevens | Beheerder van nalevingsgegevens | e6d1a23a-da11-4be4-9570-befc86d067a7
Beheerder van voorwaardelijke toegang | Beheerder van voorwaardelijke toegang | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM-servicebeheerder | Dynamics 365-beheerder | 44367163-eba1-44c3-98af-f5787879f96a
Goedkeuringsvoor klantlockboxtoegang | Klant Lockbox-toegangsgoedkeuringr | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Beheerder van bureaubladanalyse | Beheerder van bureaubladanalyse | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Apparaatbeheerders | Apparaatbeheerders | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Apparaat lid worden | Apparaatlid | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Apparaatbeheerders | Apparaatbeheerders | 2b499bcd-da44-4968-8aec-78e1674fa64d
Apparaatgebruikers | Apparaatgebruikers | d405c6df-0af8-4e3b-95e4-4d06e542189e
Directorylezers | Directorylezers | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Adreslijstsynchronisatieaccounts | Adreslijstsynchronisatie-accounts | d29b2b05-8046-44ba-8758-1e26182fcf32
Directory Schrijvers | Directory schrijvers | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange-servicebeheerder | Exchange-beheerder | 29232cdf-9323-42fd-ade2-1d097af3e4de
Beheerder externe identiteitsprovider | Beheerder externe identiteitsprovider | be2f45a1-457d-42af-a067-6ec1fa63bc45
Algemene lezer | Globale lezer | f2ef992c-3afb-46b9-b7cf-a126ee74c451
Groepsbeheerder | Beheerders van groepen | fdd7a751-b60b-444a-984c-02652fe8fa1c 
Gast genodigden | Gast genodigden | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Helpdeskbeheerder | Helpdeskbeheerder | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Hybride identiteitsbeheerder | Hybride identiteitsbeheerder | 8ac3fc64-6eca-42ea-9e69-59f4c7b60eb2
Intune-servicebeheerder | Intune-beheerder | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala-beheerder | Kaizala beheerder | 74ef975b-6605-40af-a5d2-b9539d836353
Licentiebeheerder | Licentiebeheerder | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-servicebeheerder | Skype voor Bedrijven-beheerder | 75941009-915a-4869-abe7-691bff18279e
Privacylezer van het Berichtencentrum | Privacylezer van het Berichtencentrum | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Berichtcentrumlezer | Berichtcentrumlezer | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Netwerkbeheerder | Netwerkbeheerder | d37c8bed-0711-4417-ba38-b4abe66ce4c2
Beheerder van Office Apps | Beheerder van Office-apps | 2b745bdf-0803-4d80-aa65-822c4493daac
Ondersteuning voor Partner Tier1 | Ondersteuning voor partnertier1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Ondersteuning voor Partner Tier2 | Ondersteuning voor partnertier2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Wachtwoordbeheerder | Wachtwoordbeheerder | 966707d0-3269-4727-9be2-8c3a10f19b9d
Power BI-servicebeheerder | Power BI-beheerder | a9ea8996-122f-4c74-9520-8edcd192826c
Beheerder van het Power-platform | Beheerder van het Energieplatform | 11648597-926c-4cf3-9c36-bcebb0ba8dcc
Printerbeheerder | Printerbeheerder | 644ef478-e28f-4e28-b9dc-3fdde9aa0b1f
Printertechnicus | Printertechnicus | e8cef6f1-e4bd-4ea8-bc07-4b8d950f4477
Beheerder van geprivilegieerde verificatie | Bevoegde verificatiebeheerder | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Beheerder van een bevoorrechte rol | Bevoegde rolbeheerder | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapporten Reader | Rapporten lezer | 4a5d8f65-41da-4de4-8968-e035b65339cf
Zoekbeheerder | Zoekbeheerder | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Zoekeditor | Zoekeditor | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Beveiligingsbeheer | Beveiligingsbeheerder | 194ae4cb-b126-40b2-bd5b-6091b380977d
Beveiligingsoperator | Beveiligingsoperator | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Beveiligingslezer | Beveiligingslezer | 5d6b6bb7-de71-4623-b4af-96380a352509
Beheerder van serviceondersteuning | Serviceondersteuningsbeheerder | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint-servicebeheerder | SharePoint-beheerder | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams Communicatiebeheerder | Teams Communicatiebeheerder | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams Communications Support Engineer | Teams Communications Support Engineer | f70938a0-fc10-4177-9e90-2178f8765737
Teams Communications Support Specialist | Teams Communications Support Specialist | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Teams Service Beheerder | Teams Service Beheerder | 69091246-20e8-4a56-aa4d-066075b2a7a8
Gebruiker | Gebruiker | a0b1b346-4d3e-4e8b-98f8-753987be4970
Beheerder van gebruikersaccount | Gebruikersbeheerder | fe930be7-5e62-47db-91af-98c3a49a38b1
Deelnemen aan werkplekapparaten | Deelnemen aan werkplekapparaten | c34f683f-4d5a-4403-affd-6615e00e3a7f

## <a name="deprecated-roles"></a>Afgeschafte rollen

De volgende rollen mogen niet worden gebruikt. Ze zijn afgeschaft en worden in de toekomst uit Azure AD verwijderd.

* AdHoc-licentiebeheerder
* Apparaat lid worden
* Apparaatbeheerders
* Apparaatgebruikers
* Verificatie van de gebruiker e-mail
* Postvakbeheerder
* Deelnemen aan werkplekapparaten

## <a name="roles-not-shown-in-the-portal"></a>Rollen die niet in de portal worden weergegeven

Niet elke rol die door PowerShell of MS Graph API wordt geretourneerd, is zichtbaar in azure-portal. In de volgende tabel worden deze verschillen georganiseerd.

API-naam | Azure-portalnaam | Opmerkingen
-------- | ------------------- | -------------
Company Administratoristrain opr | Globale beheerder | [Naam veranderd voor een betere duidelijkheid](directory-assign-admin-roles.md#role-template-ids)
CRM-servicebeheerder | Dynamics 365-beheerder | [Weerspiegelt de huidige productbranding](directory-assign-admin-roles.md#role-template-ids)
Apparaat lid worden | Afgeschaft | [Documentatie van afgeschafte rollen](directory-assign-admin-roles.md#deprecated-roles)
Apparaatbeheerders | Afgeschaft | [Documentatie van afgeschafte rollen](directory-assign-admin-roles.md#deprecated-roles)
Apparaatgebruikers | Afgeschaft | [Documentatie van afgeschafte rollen](directory-assign-admin-roles.md#deprecated-roles)
Adreslijstsynchronisatieaccounts | Niet getoond omdat het niet mag worden gebruikt | [Documentatie over adreslijstsynchronisatieaccounts](directory-assign-admin-roles.md#directory-synchronization-accounts)
Directory Schrijvers | Niet getoond omdat het niet mag worden gebruikt | [Documentatie van Directory Writers](directory-assign-admin-roles.md#directory-writers)
Gastgebruiker | Niet weergegeven omdat het niet kan worden gebruikt  | N.v.t.
Lync-servicebeheerder | Skype voor Bedrijven-beheerder | [Weerspiegelt de huidige productbranding](directory-assign-admin-roles.md#role-template-ids)
Ondersteuning voor Partner Tier 1 | Niet getoond omdat het niet mag worden gebruikt | [Documentatie van Partner Tier1-ondersteuning](directory-assign-admin-roles.md#partner-tier1-support)
Ondersteuning voor Partner Tier 2 | Niet getoond omdat het niet mag worden gebruikt | [Partner Tier2-ondersteuningsdocumentatie](directory-assign-admin-roles.md#partner-tier2-support)
Printerbeheerder | Work in progress | Work in progress
Printertechnicus | Work in progress | Work in progress
Beperkte gastgebruiker | Niet weergegeven omdat het niet kan worden gebruikt | N.v.t.
Gebruiker | Niet weergegeven omdat het niet kan worden gebruikt | N.v.t.
Deelnemen aan werkplekapparaten | Afgeschaft | [Documentatie van afgeschafte rollen](directory-assign-admin-roles.md#deprecated-roles)

## <a name="next-steps"></a>Volgende stappen

* Zie Toegang beheren [met Azure-rollen (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) voor meer informatie over het toewijzen van een gebruiker als beheerder van een Azure-abonnement.
* Zie [De verschillende rollen begrijpen](../../role-based-access-control/rbac-and-directory-admin-roles.md) voor meer informatie over hoe toegang tot resources wordt beheerd in Microsoft Azure
* Zie [Hoe Azure-abonnementen worden gekoppeld aan Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md) voor meer informatie over hoe Azure Active Directory aan uw Azure-abonnement wordt gekoppeld
