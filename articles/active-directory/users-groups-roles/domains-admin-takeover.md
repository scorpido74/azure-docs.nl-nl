---
title: Beheerde overname van een niet-beheerde map - Azure AD | Microsoft Documenten
description: Een DNS-domeinnaam overnemen in een onbeheerde Azure AD-organisatie (schaduwtenant).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09012d93a1f9fd24427cb8b3937b3a36cf75d9e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834172"
---
# <a name="take-over-an-unmanaged-directory-as-administrator-in-azure-active-directory"></a>Een niet-beheerde map overnemen als beheerder in Azure Active Directory

In dit artikel worden twee manieren beschreven om een DNS-domeinnaam over te nemen in een niet-beheerde map in Azure Active Directory (Azure AD). Wanneer een selfservice-gebruiker zich registreert voor een cloudservice die gebruikmaakt van Azure AD, wordt deze toegevoegd aan een niet-beheerde Azure AD-adreslijst op basis van het e-maildomein. Zie [Wat is selfservice-aanmelding voor Azure Active Directory voor](directory-self-service-signup.md) meer informatie over selfservice of 'virale' aanmelding voor een service?

## <a name="decide-how-you-want-to-take-over-an-unmanaged-directory"></a>Bepalen hoe u een niet-beheerde map wilt overnemen
Tijdens het proces van overname door een beheerder kunt u eigendom bewijzen, zoals beschreven in [Een aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md). In de volgende secties wordt de ervaring voor de beheerder gedetailleerder uitgelegd, maar hier volgt een samenvatting:

* Wanneer u een ['interne' beheerdersovername](#internal-admin-takeover) van een niet-beheerde Azure-adreslijst uitvoert, wordt u als globale beheerder van de niet-beheerde adreslijst toegevoegd. Er worden geen gebruikers, domeinen of serviceabonnementen gemigreerd naar een andere adreslijst die u beheert.

* Wanneer u een ['externe' beheerdersovername](#external-admin-takeover) uitvoert van een niet-beheerde Azure-adreslijst, voegt u de naam van het DNS-domein van de niet-beheerde adreslijst toe aan uw beheerde Azure-adreslijst. Wanneer u de domeinnaam toevoegt, wordt een toewijzing van gebruikers aan bronnen gemaakt in uw beheerde Azure-adreslijst, zodat gebruikers zonder onderbreking toegang houden tot services. 

## <a name="internal-admin-takeover"></a>Interne admin overname

Sommige producten, waaronder SharePoint en OneDrive, zoals Office 365, ondersteunen geen externe overname. Als dat uw scenario is, of als u een beheerder bent en een onbeheerde of schaduw-tenant wilt overnemen die wordt gemaakt door gebruikers die selfservice-aanmelding hebben gebruikt, u dit doen met een interne beheerdersovername.

1. Maak een gebruikerscontext in de onbeheerde tenant door u aan te melden voor Power BI. Voor het gemak van bijvoorbeeld, deze stappen nemen dat pad.

2. Open de [Power BI-site](https://powerbi.com) en selecteer **Startvrij**. Voer een gebruikersaccount in dat de domeinnaam voor de organisatie gebruikt. bijvoorbeeld . `admin@fourthcoffee.xyz` Nadat u de verificatiecode hebt ingevoerd, controleert u uw e-mail op de bevestigingscode.

3. Selecteer **ja, dat ben ik in**de bevestigingse-mail van Power BI.

4. Meld u aan bij het [Microsoft 365-beheercentrum](https://portal.office.com/admintakeover) met het Power BI-gebruikersaccount. U ontvangt een bericht waarin u wordt geïnstrueerd **om beheerder te worden** van de domeinnaam die al is geverifieerd in de niet-beheerde tenant. selecteer **Ja, ik wil de beheerder zijn.**
  
   ![eerste screenshot voor Word de beheerder](./media/domains-admin-takeover/become-admin-first.png)
  
5. Voeg de TXT-record toe om te bewijzen dat u eigenaar bent van de domeinnaam **fourthcoffee.xyz** bij uw domeinnaamregistrar. In dit voorbeeld is het GoDaddy.com.
  
   ![Een txt-record voor de domeinnaam toevoegen](./media/domains-admin-takeover/become-admin-txt-record.png)

Wanneer de DNS-TXT-records worden geverifieerd bij uw domeinnaamregistrar, u de Azure AD-tenant beheren.

Wanneer u de voorgaande stappen hebt voltooid, bent u nu de globale beheerder van de tenant Vierde koffie in Office 365. Als u de domeinnaam wilt integreren met uw andere Azure-services, u deze verwijderen uit Office 365 en deze toevoegen aan een andere beheerde tenant in Azure.

### <a name="adding-the-domain-name-to-a-managed-tenant-in-azure-ad"></a>De domeinnaam toevoegen aan een beheerde tenant in Azure AD

1. Open het [Microsoft 365-beheercentrum](https://admin.microsoft.com).
2. Selecteer het tabblad **Gebruikers** en maak een nieuw gebruikersaccount met een naam zoals *gebruikersfourthcoffeexyz.onmicrosoft.com\@* die de aangepaste domeinnaam niet gebruikt. 
3. Controleer of het nieuwe gebruikersaccount algemene beheerdersrechten heeft voor de Azure AD-tenant.
4. Open het tabblad **Domeinen** in het Microsoft 365-beheercentrum, selecteer de domeinnaam en selecteer **Verwijderen**. 
  
   ![de domeinnaam uit Office 365 verwijderen](./media/domains-admin-takeover/remove-domain-from-o365.png)
  
5. Als u gebruikers of groepen in Office 365 hebt die verwijzen naar de verwijderde domeinnaam, moeten deze worden hernoemd naar het onmicrosoft.com-domein. Als u de domeinnaam wilt verwijderen, worden alle gebruikers automatisch hernoemd, in dit voorbeeld naar *\@gebruiker fourthcoffeexyz.onmicrosoft.com.*
  
6. Meld u aan bij het [Azure AD-beheercentrum](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) met een account dat de globale beheerder is voor de Azure AD-tenant.
  
7. Selecteer **Aangepaste domeinnamen**en voeg vervolgens de domeinnaam toe. U moet de DNS TXT-records invoeren om het eigendom van de domeinnaam te verifiëren. 
  
   ![domein geverifieerd als toegevoegd aan Azure AD](./media/domains-admin-takeover/add-domain-to-azure-ad.png)
  
> [!NOTE]
> Gebruikers van de Power BI- of Azure Rights Management-service die licenties hebben toegewezen in de Office 365-tenant, moeten hun dashboards opslaan als de domeinnaam wordt verwijderd. Ze moeten zich aanmelden met een gebruikersnaam zoals *gebruiker\@fourthcoffeexyz.onmicrosoft.com* in plaats van gebruiker *\@fourthcoffee.xyz*.

## <a name="external-admin-takeover"></a>Overname externe beheerder

Als u al een tenant beheert met Azure-services of Office 365, u geen aangepaste domeinnaam toevoegen als deze al is geverifieerd in een andere Azure AD-tenant. Vanuit uw beheerde tenant in Azure AD u echter een onbeheerde tenant overnemen als een externe beheerde beheerderovername. De algemene procedure volgt het artikel [Een aangepast domein toevoegen aan Azure AD](../fundamentals/add-custom-domain.md).

Wanneer u het eigendom van de domeinnaam verifieert, verwijdert Azure AD de domeinnaam uit de niet-beheerde tenant en verplaatst deze naar uw bestaande tenant. Voor de overname van een niet-beheerde map voor externe beheerders is hetzelfde DNS-TXT-validatieproces vereist als de overname van interne beheerders. Het verschil is dat het volgende ook worden verplaatst met de domeinnaam:

- Gebruikers
- Abonnementen
- Licentietoewijzingen

### <a name="support-for-external-admin-takeover"></a>Ondersteuning voor externe beheerdersovername
Externe admin overname wordt ondersteund door de volgende online diensten:

- Azure Rights Management
- Exchange Online

De ondersteunde serviceplannen omvatten:

- PowerApps Gratis
- PowerFlow Vrij
- RMS voor personen
- Microsoft Stream
- Dynamics 365 gratis proefversie

Externe beheerdersovername wordt niet ondersteund voor serviceplannen met SharePoint, OneDrive of Skype Voor Bedrijven. bijvoorbeeld via een Office-gratis abonnement. 

U optioneel de [ **optie ForceTakeover** ](#azure-ad-powershell-cmdlets-for-the-forcetakeover-option) gebruiken voor het verwijderen van de domeinnaam uit de niet-beheerde tenant en het verifiëren ervan op de gewenste tenant. 

#### <a name="more-information-about-rms-for-individuals"></a>Meer informatie over RMS voor particulieren

Voor [RMS voor personen](/azure/information-protection/rms-for-individuals), wanneer de niet-beheerde tenant zich in dezelfde regio bevindt als de tenant die u bezit, worden de automatisch gemaakte azure information [protection-tenantsleutel](/azure/information-protection/plan-implement-tenant-key) en [standaardbeveiligingssjablonen](/azure/information-protection/configure-usage-rights#rights-included-in-the-default-templates) bovendien verplaatst met de domeinnaam.

De sleutel en sjablonen worden niet verplaatst wanneer de onbeheerde tenant zich in een andere regio bevindt. Als de onbeheerde huurder zich bijvoorbeeld in Europa bevindt en de organisatie die u bezit zich in Noord-Amerika bevindt.

Hoewel RMS voor particulieren is ontworpen om Azure AD-verificatie te ondersteunen voor het openen van beveiligde inhoud, voorkomt het niet dat gebruikers ook inhoud beveiligen. Als gebruikers de inhoud met het RMS for individuals-abonnement hebben beschermd en de sleutel en sjablonen niet zijn verplaatst, is die inhoud niet toegankelijk na de overname van het domein.

### <a name="azure-ad-powershell-cmdlets-for-the-forcetakeover-option"></a>Azure AD PowerShell-cmdlets voor de optie ForceTakeover
U deze cmdlets zien die worden gebruikt in [powershell-voorbeeld.](#powershell-example)

cmdlet | Gebruik
------- | -------
`connect-msolservice` | Meld je op wanneer je wordt gevraagd bij je beheerde tenant.
`get-msoldomain` | Toont uw domeinnamen die zijn gekoppeld aan de huidige tenant.
`new-msoldomain –name <domainname>` | Hiermee voegt u de domeinnaam toe aan tenant als Niet-geverifieerd (er is nog geen DNS-verificatie uitgevoerd).
`get-msoldomain` | De domeinnaam is nu opgenomen in de lijst met domeinnamen die zijn gekoppeld aan uw beheerde tenant, maar wordt vermeld als **Niet-geverifieerd.**
`get-msoldomainverificationdns –Domainname <domainname> –Mode DnsTxtRecord` | Biedt de informatie om in nieuwe DNS TXT-record voor het domein (MS=xxxxx) te plaatsen. Verificatie kan niet onmiddellijk gebeuren, omdat het enige tijd duurt voordat de TXT-record zich voortplant, dus wacht een paar minuten voordat u de optie **-ForceTakeover** overweegt. 
`confirm-msoldomain –Domainname <domainname> –ForceTakeover Force` | <li>Als uw domeinnaam nog steeds niet is geverifieerd, u doorgaan met de optie **-ForceTakeover.** Het controleert of de TXT-record is gemaakt en start het overnameproces.<li>De optie **-ForceTakeover** mag alleen aan de cmdlet worden toegevoegd wanneer een externe beheerdersovername wordt geforceerd, bijvoorbeeld wanneer de onbeheerde tenant Office 365-services heeft die de overname blokkeren.
`get-msoldomain` | In de domeinlijst wordt nu de domeinnaam weergegeven **als Geverifieerd.**

> [!NOTE]
> De niet-beheerde Azure AD-organisatie wordt 10 dagen nadat u de externe overnameforceoptie hebt uitgeoefend, verwijderd.

### <a name="powershell-example"></a>PowerShell-voorbeeld

1. Maak verbinding met Azure AD met behulp van de referenties die zijn gebruikt om te reageren op het selfservice-aanbod:
   ```powershell
   Install-Module -Name MSOnline
   $msolcred = get-credential
    
   connect-msolservice -credential $msolcred
   ```
2. Een lijst met domeinen:
  
   ```powershell
   Get-MsolDomain
   ```
3. Voer de cmdlet Get-MsolDomainVerificationDns uit om een uitdaging te creëren:
   ```powershell
   Get-MsolDomainVerificationDns –DomainName *your_domain_name* –Mode DnsTxtRecord
   ```
    Bijvoorbeeld:
   ```
   Get-MsolDomainVerificationDns –DomainName contoso.com –Mode DnsTxtRecord
   ```

4. Kopieer de waarde (de uitdaging) die wordt geretourneerd vanuit deze opdracht. Bijvoorbeeld:
   ```powershell
   MS=32DD01B82C05D27151EA9AE93C5890787F0E65D9
   ```
5. Maak in uw openbare DNS-naamruimte een DNS-txt-record met de waarde die u in de vorige stap hebt gekopieerd. De naam voor deze record is de naam van het bovenliggende domein, dus als u deze bronrecord maakt met de DNS-rol van Windows Server, laat u de naam Record leeg en plakt u de waarde gewoon in het vak Tekst.
6. Voer de cmdlet Confirm-MsolDomain uit om de uitdaging te verifiëren:
  
   ```powershell
   Confirm-MsolDomain –DomainName *your_domain_name* –ForceTakeover Force
   ```
  
   Bijvoorbeeld:
  
   ```powershell
   Confirm-MsolDomain –DomainName contoso.com –ForceTakeover Force
   ```

Een geslaagde uitdaging brengt u zonder een fout terug naar de prompt.

## <a name="next-steps"></a>Volgende stappen

* [Een aangepaste domeinnaam toevoegen aan Azure AD](../fundamentals/add-custom-domain.md)
* [Azure PowerShell installeren en configureren](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure-cmdlet-naslaginformatie](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
