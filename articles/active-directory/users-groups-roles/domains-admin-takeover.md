---
title: Beheerders overname van een onbeheerde map-Azure Active Directory | Microsoft Docs
description: Het overnemen van een DNS-domein naam in een niet-beheerde map (Shadow Tenant) in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 08/01/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00753ee8d7d128222e9fb773f00a23720813a237
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71146467"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Een niet-beheerde Directory als beheerder overnemen in Azure Active Directory

In dit artikel worden twee manieren beschreven om een DNS-domein naam te nemen in een onbeheerde map in Azure Active Directory (Azure AD). Wanneer een selfservice-gebruiker zich registreert voor een cloudservice die gebruikmaakt van Azure AD, wordt deze toegevoegd aan een niet-beheerde Azure AD-adreslijst op basis van het e-maildomein. Zie [Wat is self-service-aanmelding voor Azure Active Directory?](directory-self-service-signup.md) voor meer informatie over selfservice of virus registratie voor een service.

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bepaal hoe u wilt overnemen van een niet-beheerde map
Tijdens het proces van overname door een beheerder kunt u eigendom bewijzen, zoals beschreven in [Een aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md). In de volgende secties wordt de ervaring voor de beheerder gedetailleerder uitgelegd, maar hier volgt een samenvatting:

* Wanneer u een ['interne' beheerdersovername](#internal-admin-takeover) van een niet-beheerde Azure-adreslijst uitvoert, wordt u als globale beheerder van de niet-beheerde adreslijst toegevoegd. Er worden geen gebruikers, domeinen of serviceabonnementen gemigreerd naar een andere adreslijst die u beheert.

* Wanneer u een ['externe' beheerdersovername](#external-admin-takeover) uitvoert van een niet-beheerde Azure-adreslijst, voegt u de naam van het DNS-domein van de niet-beheerde adreslijst toe aan uw beheerde Azure-adreslijst. Wanneer u de domeinnaam toevoegt, wordt een toewijzing van gebruikers aan bronnen gemaakt in uw beheerde Azure-adreslijst, zodat gebruikers zonder onderbreking toegang houden tot services. 

## <a name="internal-admin-takeover"></a>Interne beheerder overname

Sommige producten die share point en OneDrive bevatten, zoals Office 365, bieden geen ondersteuning voor externe overname. Als dat het geval is, of als u een beheerder bent en u wilt een niet-beheerde of ' Shadow '-Tenant maken op basis van gebruikers die gebruikmaken van self-service registratie, kunt u dit doen met een interne beheerder.

1. Maak een gebruikers context in de onbeheerde Tenant door u aan te melden voor Power BI. Voor het gemak van deze stappen wordt ervan uitgegaan dat het pad.

2. Open de [Power bi-site](https://powerbi.com) en selecteer **gratis starten**. Voer een gebruikers account in dat gebruikmaakt van de domein naam voor de organisatie. bijvoorbeeld `admin@fourthcoffee.xyz`. Nadat u de verificatie code hebt ingevoerd, controleert u uw e-mail op de bevestigings code.

3. Selecteer **Ja**in het bevestigings bericht van Power bi.

4. Meld u aan bij het [Microsoft 365-beheer centrum](https://portal.office.com/admintakeover) met de Power bi gebruikers account. U ontvangt een bericht waarin wordt aangegeven dat u **de beheerder** bent van de domein naam die al is geverifieerd in de niet-beheerde Tenant. Selecteer **Ja, ik wil de beheerder zijn**.
  
   ![eerste scherm afbeelding van de beheerder](./media/domains-admin-takeover/become-admin-first.png)
  
5. Voeg de TXT-record toe om te bewijzen dat u eigenaar bent van de domein naam **fourthcoffee. xyz** in uw domein naam registratie service. In dit voor beeld is het GoDaddy.com.
  
   ![Een TXT-record voor de domein naam toevoegen](./media/domains-admin-takeover/become-admin-txt-record.png)

Wanneer de DNS TXT-records worden geverifieerd bij uw domein naam registratie, kunt u de Azure AD-Tenant beheren.

Wanneer u de voor gaande stappen hebt voltooid, bent u nu de globale beheerder van de Fourth Coffee-Tenant in Office 365. U kunt de domein naam met uw andere Azure-Services integreren door deze uit Office 365 te verwijderen en toe te voegen aan een andere beheerde Tenant in Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>De domein naam toevoegen aan een beheerde Tenant in azure AD

1. Open het [Microsoft 365-beheer centrum](https://admin.microsoft.com).
2. Selecteer **gebruikers** tabblad en maak een nieuw gebruikers account met een naam als *gebruiker\@fourthcoffeexyz.onmicrosoft.com* die geen gebruik maakt van de aangepaste domein naam. 
3. Zorg ervoor dat het nieuwe gebruikers account globale beheerders rechten heeft voor de Azure AD-Tenant.
4. Open het tabblad **domeinen** in het Microsoft 365-beheer centrum, selecteer de domein naam en selecteer **verwijderen**. 
  
   ![de domein naam verwijderen uit Office 365](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Als u gebruikers of groepen in Office 365 hebt die verwijzen naar de verwijderde domein naam, moet u de naam ervan wijzigen in het domein. onmicrosoft.com. Als u de domein naam geforceerd verwijdert, worden alle gebruikers automatisch hernoemd, in dit voor beeld *naar\@gebruikers fourthcoffeexyz.onmicrosoft.com*.
  
6. Meld u aan bij het [Azure AD-beheer centrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat de globale beheerder is voor de Azure AD-Tenant.
  
7. Selecteer **aangepaste domein namen**en voeg vervolgens de domein naam toe. U moet de DNS TXT-records invoeren om het eigendom van de domein naam te verifiëren. 
  
   ![het domein is geverifieerd als toegevoegd aan Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Gebruikers van Power BI-of Azure Rights Management-service met licenties die zijn toegewezen in de Office 365-Tenant, moeten hun Dash boards opslaan als de domein naam wordt verwijderd. Ze moeten zich aanmelden met een gebruikers naam zoals *de\@gebruiker fourthcoffeexyz.onmicrosoft.com* in plaats van de *gebruiker\@fourthcoffee. xyz*.

## <a name="external-admin-takeover"></a>Externe beheerder overname

Als u al een Tenant beheert met Azure-Services of Office 365, kunt u geen aangepaste domein naam toevoegen als deze al is geverifieerd in een andere Azure AD-Tenant. Vanuit uw beheerde Tenant in azure AD kunt u echter een niet-beheerde Tenant nemen als een externe beheerder. De algemene procedure volgt het artikel [een aangepast domein toevoegen aan Azure AD](../fundamentals/add-custom-domain.md).

Wanneer u het eigendom van de domein naam controleert, wordt de domein naam van de onbeheerde Tenant door Azure AD verwijderd en verplaatst naar uw bestaande Tenant. Externe beheerder-overname van een onbeheerde map vereist hetzelfde DNS-TXT-validatie proces als de interne beheerder voor overname. Het verschil is dat het volgende ook wordt verplaatst met de domein naam:

- Gebruikers
- Abonnementen
- Licentietoewijzingen

### <a name="support-for-external-admin-takeover"></a>Ondersteuning voor externe beheerders overname
Externe beheerders overname wordt ondersteund door de volgende onlineservices:

- Azure Rights Management
- Exchange Online

De ondersteunde service plannen zijn onder andere:

- PowerApps gratis
- PowerFlow gratis
- RMS voor personen
- Microsoft Stream
- Dynamics 365 gratis proef versie

Externe beheerder overname wordt niet ondersteund voor een service met service plannen die share point, OneDrive of Skype voor bedrijven bevatten. bijvoorbeeld via een gratis Office-abonnement. 

U kunt eventueel de [optie **ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) gebruiken om de domein naam uit de niet-beheerde Tenant te verwijderen en deze te verifiëren op de gewenste Tenant. **De optie ForceTakeover gaat niet over gebruikers, of behoudt de toegang tot het abonnement. Met deze optie wordt alleen de domein naam verplaatst.**

#### <a name="more-information-about-rms-for-individuals"></a>Meer informatie over RMS voor personen

Wanneer de onbeheerde Tenant zich in dezelfde regio bevindt als de Tenant waarvan u de eigenaar bent, worden de automatisch gemaakte [Azure Information Protection-Tenant sleutel](/azure/information-protection/plan-implement-tenant-key) en de [standaard beveiligings sjablonen](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) ook verplaatst voor [RMS voor personen](/azure/information-protection/rms-for-individuals)met de domein naam.

De sleutel en sjablonen worden niet verplaatst wanneer de onbeheerde Tenant zich in een andere regio bevindt. Als de onbeheerde Tenant zich bijvoorbeeld in Europa bevindt en de organisatie waarvan u de eigenaar bent, bevindt zich in Noord-Amerika.

Hoewel RMS voor individuen is ontworpen ter ondersteuning van Azure AD-verificatie om beveiligde inhoud te openen, voor komt u dat gebruikers ook inhoud beveiligen. Als gebruikers inhoud met het abonnement voor RMS voor personen hebben beveiligd en de sleutel en sjablonen niet zijn verplaatst, is deze inhoud niet toegankelijk na de domein overname.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD Power shell-cmdlets voor de ForceTakeover-optie
U kunt deze cmdlets zien die in [Power shell-voor beeld](#powershell-example)worden gebruikt.

cmdlet | Gebruik
------- | -------
`connect-msolservice` | Meld u aan bij uw beheerde Tenant wanneer u hierom wordt gevraagd.
`get-msoldomain` | Hier worden uw domein namen weer gegeven die zijn gekoppeld aan de huidige Tenant.
`new-msoldomain –name <domainname>` | Voegt de domein naam aan de Tenant toe als niet-geverifieerd (er is nog geen DNS-verificatie uitgevoerd).
`get-msoldomain` | De domein naam is nu opgenomen in de lijst met domein namen die zijn gekoppeld aan uw beheerde Tenant, maar wordt weer gegeven als niet- **geverifieerd**.
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Bevat informatie die u kunt opnemen in een nieuwe DNS TXT-record voor het domein (MS = xxxxx). Verificatie vindt mogelijk niet onmiddellijk plaats omdat het even duurt voordat de TXT-record is door gegeven. wacht daarom een paar minuten voordat u de optie **-ForceTakeover** overweegt. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Als uw domein naam nog niet is geverifieerd, kunt u door gaan met de optie **-ForceTakeover** . Er wordt gecontroleerd of de TXT-record is gemaakt en het overname proces is afgebroken.<li>De optie **-ForceTakeover** moet alleen worden toegevoegd aan de cmdlet wanneer een externe beheerder een overname afdwingt, bijvoorbeeld wanneer de onbeheerde tenant Office 365-Services heeft die de overname blokkeert.
`get-msoldomain` | In de lijst domein wordt nu de domein naam weer gegeven als **geverifieerd**.

> [!NOTE]
> De onbeheerde Azure AD-organisatie wordt 10 dagen verwijderd nadat u de optie voor externe overname forceren hebt uitgeoefend.

### <a name="powershell-example"></a>PowerShell-voorbeeld

1. Maak verbinding met Azure AD met behulp van de referenties die zijn gebruikt om te reageren op de self-service aanbieding:
   ```powershell
    Install-Module -Name MSOnline
    $msolcred = get-credential
    
    connect-msolservice -credential $msolcred
   ```
2. Een lijst met domeinen ophalen:
  
   ```powershell
    Get-MsolDomain
   ```
3. Voer de cmdlet Get-MsolDomainVerificationDns uit om een uitdaging te maken:
   ```powershell
    Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
  
    For example:
  
    Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopieer de waarde (de uitdaging) die wordt geretourneerd met deze opdracht. Bijvoorbeeld:
   ```powershell
    MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Maak in uw open bare DNS-naam ruimte een DNS TXT-record die de waarde bevat die u in de vorige stap hebt gekopieerd. De naam voor deze record is de naam van het bovenliggende domein, dus als u deze bron record maakt met behulp van de DNS-functie van Windows Server, laat u de record naam leeg en plakt u alleen de waarde in het tekstvak.
6. Voer de cmdlet confirm-MsolDomain uit om de uitdaging te controleren:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName *your_domain_name*
   ```
  
   Bijvoorbeeld:
  
   ```powershell
    Confirm-MsolEmailVerifiedDomain -DomainName contoso.com
   ```

Bij een geslaagde poging keert u terug naar de prompt zonder fout.

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste domein naam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
