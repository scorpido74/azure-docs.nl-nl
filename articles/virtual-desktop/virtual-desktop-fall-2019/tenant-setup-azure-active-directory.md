---
title: Een Tenant maken in virtueel bureau blad van Windows-Azure
description: Hierin wordt beschreven hoe u Windows Virtual Desktop-tenants instelt in Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 94128c69f227ceff51968354048ec6610e3d7c4c
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614401"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Zelf studie: een Tenant maken in Windows Virtual Desktop

>[!IMPORTANT]
>Deze inhoud is van toepassing op de najaar 2019-release die geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows.

Het maken van een Tenant in Windows Virtual Desktop is de eerste stap bij het bouwen van uw Desktop Virtualization-oplossing. Een Tenant is een groep van een of meer hostgroepen. Elke hostgroep bestaat uit meerdere sessie-hosts die worden uitgevoerd als virtuele machines in Azure en die zijn geregistreerd bij de virtueel bureau blad-service van Windows. Elke hostgroep bestaat ook uit een of meer app-groepen die worden gebruikt voor het publiceren van extern bureau blad-en externe toepassings bronnen voor gebruikers. Met een Tenant kunt u hostgroepen maken, app-groepen maken, gebruikers toewijzen en verbindingen via de service maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Ken Azure Active Directory machtigingen toe aan de virtueel-bureaublad service van Windows.
> * Wijs de toepassingsrol TenantCreator toe aan een gebruiker in uw Azure Active Directory-Tenant.
> * Maak een virtuele bureau blad-Tenant voor Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>Wat u nodig hebt om een Tenant in te stellen

Voordat u begint met het instellen van uw Windows Virtual Desktop-Tenant, moet u het volgende doen:

* De [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) Tenant-id voor Windows-gebruikers met virtueel bureau blad.
* Een globaal beheerders account binnen de Azure Active Directory Tenant.
   * Dit geldt ook voor organisaties van de Cloud Solution Provider (CSP) die een Windows-Tenant voor virtueel bureau blad maken voor hun klanten. Als u zich in een CSP-organisatie bevindt, moet u zich kunnen aanmelden als globale beheerder van het Azure Active Directory-exemplaar van de klant.
   * Het beheerders account moet worden bron van de Azure Active Directory Tenant waarin u de virtuele bureau blad-Tenant van Windows probeert te maken. Dit proces biedt geen ondersteuning voor Azure Active Directory B2B-accounts.
   * Het account van de beheerder moet een werk-of school account zijn.
* Een Azure-abonnement.

U moet beschikken over de Tenant-ID, het globale Administrator-account en het Azure-abonnement, zodat het proces dat in deze zelf studie wordt beschreven, goed kan werken.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Machtigingen verlenen voor virtuele Windows-bureau blad

Als u al machtigingen hebt gekregen voor het virtuele bureau blad van Windows voor dit exemplaar van Azure Active Directory, kunt u deze sectie overs Laan.

Het verlenen van machtigingen aan de virtueel-bureaublad service van Windows stelt IT in staat om Azure Active Directory voor administratieve en eind gebruikers taken.

De service machtigingen verlenen:

1. Open een browser en start de beheerder toestemming Flow naar de [Windows Virtual Desktop server-app](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Als u een klant beheert en beheerders toestemming wilt verlenen voor de directory van de klant, voert u de volgende URL in de browser in en vervangt u {Tenant} door de naam van het Azure AD-domein van de klant. Als de organisatie van de klant bijvoorbeeld de Azure AD-domein naam contoso.onmicrosoft.com heeft geregistreerd, vervangt u {Tenant} door contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Meld u aan bij de Windows-pagina toestemming voor virtueel bureau blad met een algemeen beheerders account. Als u bijvoorbeeld met de contoso-organisatie zou doen, is uw account mogelijk admin@contoso.com of admin@contoso.onmicrosoft.com.
3. Selecteer **Accepteren**.
4. Wacht één minuut zodat Azure AD toestemming kan geven.
5. Open een browser en start de beheerder toestemming Flow naar de [Windows-client-app voor virtueel bureau blad](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Als u een klant beheert en beheerders toestemming wilt verlenen voor de directory van de klant, voert u de volgende URL in de browser in en vervangt u {Tenant} door de naam van het Azure AD-domein van de klant. Als de organisatie van de klant bijvoorbeeld de Azure AD-domein naam contoso.onmicrosoft.com heeft geregistreerd, vervangt u {Tenant} door contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Meld u aan bij de Windows-toestemming pagina voor virtueel bureau blad als globale beheerder, zoals u in stap 2 hebt gedaan.
7. Selecteer **Accepteren**.

## <a name="assign-the-tenantcreator-application-role"></a>De toepassingsrol van de TenantCreator toewijzen

Als u een Azure Active Directory gebruiker toewijst, kan die gebruiker met de toepassingsrol TenantCreator een virtueel bureau blad-Tenant maken dat is gekoppeld aan het Azure Active Directory exemplaar. U moet uw globale beheerders account gebruiken om de TenantCreator-rol toe te wijzen.

De toepassingsrol van de TenantCreator toewijzen:

1. Ga naar de [Azure Portal](https://portal.azure.com) om de TenantCreator te beheren. Zoek en selecteer **bedrijfs toepassingen**. Als u met meerdere Azure Active Directory tenants werkt, is het een best practice om een persoonlijke browser sessie te openen en de Url's te kopiëren en in de adres balk te plakken.

   ![Scherm opname van het zoeken naar zakelijke toepassingen in de Azure Portal](../media/azure-portal-enterprise-applications.png)
2. Zoek in **bedrijfs toepassingen**naar **virtueel bureau blad van Windows**. In de vorige sectie ziet u de twee toepassingen waarvoor u toestemming hebt gegeven. Van deze twee apps selecteert u **virtueel bureau blad van Windows**.
   
   ![Een scherm opname van de zoek resultaten bij het zoeken naar ' Windows virtueel bureau blad ' in ' bedrijfs toepassingen '. De app met de naam ' Windows virtueel bureau blad ' is gemarkeerd.](../media/tenant-enterprise-app.png)
3. Selecteer **gebruikers en groepen**. U ziet mogelijk dat de beheerder die toestemming heeft verleend voor de toepassing, al wordt vermeld met de **standaard Access** -rol toegewezen. Dit is niet voldoende voor het maken van een virtuele bureau blad-Tenant van Windows. Ga door met de volgende instructies om de rol **TenantCreator** toe te voegen aan een gebruiker.
   
   ![Een scherm opname van de gebruikers en groepen die zijn toegewezen voor het beheren van de bedrijfs toepassing Windows Virtual Desktop. In de scherm opname ziet u slechts één toewijzing, die is ingesteld op standaard toegang.](../media/tenant-default-access.png)
4. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** op het tabblad **toewijzing toevoegen** .
5. Zoek naar een gebruikers account dat uw Windows Virtual Desktop-Tenant maakt. Voor de eenvoud kan dit het account van de globale beheerder zijn.
   - Als u een micro soft-identiteits provider contosoadmin@live.com gebruikt contosoadmin@outlook.com, zoals of, kunt u zich mogelijk niet aanmelden bij Windows virtueel bureau blad. U kunt het beste een domein-specifiek account admin@contoso.com gebruiken admin@contoso.onmicrosoft.com , zoals in de plaats.

   ![Een scherm opname van het selecteren van een gebruiker om toe te voegen als ' TenantCreator '.](../media/tenant-assign-user.png)
   > [!NOTE]
   > U moet een gebruiker (of een groep die een gebruiker bevat) selecteren die is afgeleid van deze Azure Active Directory-instantie. U kunt geen gast gebruiker (B2B) of een Service-Principal kiezen.

6. Selecteer het gebruikers account, kies de knop **selecteren** en selecteer vervolgens **toewijzen**.
7. Controleer op de pagina **virtueel bureau blad van Windows-gebruikers en groepen** of er een nieuw item wordt weer gegeven met de **TenantCreator** -rol die is toegewezen aan de gebruiker die de virtueel bureau blad-Tenant van Windows gaat maken.

   ![Een scherm opname van de gebruikers en groepen die zijn toegewezen voor het beheren van de bedrijfs toepassing Windows Virtual Desktop. De scherm opname bevat nu een tweede vermelding van een gebruiker die is toegewezen aan de rol ' TenantCreator '.](../media/tenant-tenant-creator-added.png)

Voordat u doorgaat met het maken van uw virtuele bureau blad-Tenant voor Windows, hebt u twee soorten informatie nodig:

   - Uw Azure Active Directory Tenant-ID (of **Directory-id**)
   - Uw Azure-abonnements-ID

Uw Azure Active Directory Tenant-ID (of **Directory-id**) zoeken:
1. In dezelfde [Azure Portal](https://portal.azure.com) -sessie zoekt en selecteert u **Azure Active Directory**.

   ![Een scherm opname van de zoek resultaten voor ' Azure Active Directory ' in de Azure Portal. Het Zoek resultaat onder ' Services ' is gemarkeerd.](../media/tenant-search-azure-active-directory.png)
2. Schuif omlaag totdat u **Eigenschappen**hebt gevonden en selecteer deze.
3. Zoek naar **Directory-id**en selecteer vervolgens het pictogram van het klem bord. Plak het op een handige locatie, zodat u deze later kunt gebruiken als de **AadTenantId** waarde.

   ![Een scherm opname van de Azure Active Directory eigenschappen. De muis aanwijzer boven het pictogram van het klem bord voor map-ID om te kopiëren en plakken.](../media/tenant-directory-id.png)

Uw Azure-abonnements-ID zoeken:
1. In dezelfde [Azure Portal](https://portal.azure.com) -sessie zoekt en selecteert u **abonnementen**.
   
   ![Een scherm opname van de zoek resultaten voor ' Azure Active Directory ' in de Azure Portal. Het Zoek resultaat onder ' Services ' is gemarkeerd.](../media/tenant-search-subscription.png)
2. Selecteer het Azure-abonnement dat u wilt gebruiken voor het ontvangen van meldingen van de Windows-service voor virtuele Bureau bladen.
3. Zoek naar **abonnements-id**en beweeg de muis aanwijzer over de waarde totdat een pictogram van het klem bord wordt weer gegeven. Selecteer het pictogram van het klem bord en plak het op een handige locatie, zodat u het later kunt gebruiken als de **AzureSubscriptionId** waarde.
   
   ![Een scherm afbeelding van de eigenschappen van het Azure-abonnement. De muis aanwijzer boven het pictogram van het klem bord voor ' abonnements-ID ' om te kopiëren en te plakken.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Een virtueel bureau blad-Tenant voor Windows maken

Nu u de machtigingen voor de Windows Virtual Desktop-service hebt gekregen om Azure Active Directory te zoeken en de rol TenantCreator aan een gebruikers account hebt toegewezen, kunt u een virtuele Windows-bureau blad-Tenant maken.

[Down load en Importeer eerst de module virtueel bureau blad van Windows](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan.

Meld u aan bij Windows virtueel bureau blad met behulp van het TenantCreator-gebruikers account met deze cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Daarna maakt u een nieuwe virtuele bureau blad-Tenant voor Windows die is gekoppeld aan de Azure Active Directory Tenant:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Vervang de waarden met tussen haakjes door de waarden die relevant zijn voor uw organisatie en Tenant. De naam die u voor uw nieuwe virtuele bureau blad-Tenant voor Windows hebt gekozen, moet wereld wijd uniek zijn. Stel bijvoorbeeld dat u de virtuele bureau blad-TenantCreator van Windows bent voor de contoso-organisatie. De cmdlet die u zou uitvoeren zou er als volgt uitzien:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Het is een goed idee om beheerders toegang toe te wijzen aan een tweede gebruiker voor het geval u u ooit hebt geblokkeerd, of u nu op vakantie bent, en iemand moet als Tenant beheerder in uw afwezigheid optreden. Als u beheerders toegang wilt toewijzen aan een tweede gebruiker, voert u de `<TenantName>` volgende `<Upn>` cmdlet uit met en vervangen door de naam van uw Tenant en de UPN van de tweede gebruiker.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw Tenant hebt gemaakt, moet u een service-principal maken in Azure Active Directory en een rol toewijzen in het virtuele bureau blad van Windows. Met de Service-Principal kunt u de Azure Marketplace-aanbieding voor Windows Virtual Desktop implementeren om een hostgroep te maken. Voor meer informatie over hostgroepen gaat u verder met de zelf studie voor het maken van een hostgroep in het virtuele bureau blad van Windows.

> [!div class="nextstepaction"]
> [Service-principals en roltoewijzingen maken met PowerShell](create-service-principal-role-powershell.md)
