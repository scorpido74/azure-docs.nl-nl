---
title: Een tenant maken in Windows Virtual Desktop - Azure
description: Beschrijft hoe u Windows Virtual Desktop-tenants in Azure Active Directory instelt.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9f9f7f709d31967e892900ccb25657a5963c0379
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370216"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Zelfstudie: Een tenant maken in Windows Virtueel bureaublad

Het maken van een tenant in Windows Virtual Desktop is de eerste stap in de richting van het bouwen van uw desktopvirtualisatieoplossing. Een tenant is een groep van een of meer hostpools. Elke hostgroep bestaat uit meerdere sessiehosts, die worden uitgevoerd als virtuele machines in Azure en zijn geregistreerd bij de Windows Virtual Desktop-service. Elke hostgroep bestaat ook uit een of meer appgroepen die worden gebruikt om externe bureaublad- en externe toepassingsbronnen te publiceren aan gebruikers. Met een tenant u hostgroepen maken, app-groepen maken, gebruikers toewijzen en verbindingen maken via de service.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Azure Active Directory-machtigingen verlenen aan de Windows Virtual Desktop-service.
> * Wijs de toepassingsrol TenantCreator toe aan een gebruiker in uw Azure Active Directory-tenant.
> * Een Windows Virtual Desktop-tenant maken.

## <a name="what-you-need-to-set-up-a-tenant"></a>Wat u nodig hebt om een tenant in te stellen

Voordat u begint met het instellen van uw Windows Virtual Desktop-tenant, controleert u of u deze dingen hebt:

* De Azure Active Directory-tenant-id voor Windows Virtual Desktop-gebruikers. [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)
* Een algemeen beheerdersaccount binnen de Azure Active Directory-tenant.
   * Dit geldt ook voor CSP-organisaties (Cloud Solution Provider) die een Windows Virtual Desktop-tenant voor hun klanten maken. Als u deel maakt van een CSP-organisatie, moet u zich kunnen aanmelden als globale beheerder van het Azure Active Directory-exemplaar van de klant.
   * Het beheerdersaccount moet afkomstig zijn van de Azure Active Directory-tenant waarin u de Windows Virtual Desktop-tenant probeert te maken. Dit proces biedt geen ondersteuning voor Azure Active Directory B2B-accounts (gastaccounts).
   * Het beheerdersaccount moet een werk- of schoolaccount zijn.
* Een Azure-abonnement.

U moet de tenant-id, het globale beheerdersaccount en het Azure-abonnement klaar hebben staan, zodat het proces dat in deze zelfstudie wordt beschreven, goed kan werken.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Machtigingen verlenen aan Windows Virtual Desktop

Als u al machtigingen hebt verleend aan Windows Virtual Desktop voor dit Azure Active Directory-exemplaar, slaat u deze sectie over.

Door machtigingen toe te kennen aan de Windows Virtual Desktop-service kan azure Active Directory worden opgevraagd voor beheer- en eindgebruikerstaken.

Ga als u de servicemachtigingen verleent:

1. Open een browser en begin de beheerderstoestemmingsstroom naar de [Windows Virtual Desktop-server-app](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Als u een klant beheert en beheerderstoestemming moet verlenen voor de map van de klant, voert u de volgende URL in de browser in en vervangt u {tenant} door de Azure AD-domeinnaam van de klant. Als de organisatie van de klant bijvoorbeeld de Azure AD-domeinnaam van contoso.onmicrosoft.com heeft geregistreerd, vervangt u {tenant} door contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Meld u aan bij de toestemmingspagina van Windows Virtual Desktop met een algemeen beheerdersaccount. Als u bijvoorbeeld bij de Contoso-organisatie bent, kan uw account zijn admin@contoso.com of admin@contoso.onmicrosoft.com.
3. Selecteer **Accepteren**.
4. Wacht een minuut, zodat Azure AD toestemming kan opnemen.
5. Open een browser en begin de beheerderstoestemmingsstroom naar de [Windows Virtual Desktop-client-app](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Als u een klant beheert en beheerderstoestemming moet verlenen voor de map van de klant, voert u de volgende URL in de browser in en vervangt u {tenant} door de Azure AD-domeinnaam van de klant. Als de organisatie van de klant bijvoorbeeld de Azure AD-domeinnaam van contoso.onmicrosoft.com heeft geregistreerd, vervangt u {tenant} door contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Meld u aan bij de toestemmingspagina van Windows Virtual Desktop als globale beheerder, zoals u in stap 2 hebt gedaan.
7. Selecteer **Accepteren**.

## <a name="assign-the-tenantcreator-application-role"></a>De toepassingsrol TenantCreator toewijzen

Als u een Azure Active Directory-gebruiker toewijs, kan de tenant TenantCreator een Windows Virtual Desktop-tenant maken die is gekoppeld aan het Azure Active Directory-exemplaar. U moet uw globale beheerdersaccount gebruiken om de rol TenantCreator toe te wijzen.

Ga als u de toepassingsrol TenantCreator toewijst:

1. Ga naar de [Azure-portal](https://portal.azure.com) om de toepassingsrol TenantCreator te beheren. Zoeken naar en selecteer **Enterprise-toepassingen**. Als u met meerdere Azure Active Directory-tenants werkt, is het een goede gewoonte om een privébrowsersessie te openen en de URL's in de adresbalk te kopiëren en te plakken.

   ![Schermafbeelding van zoeken naar Enterprise-toepassingen in de Azure-portal](media/azure-portal-enterprise-applications.png)
2. Zoek in **Enterprise-toepassingen**naar **Windows Virtual Desktop.** U ziet de twee toepassingen waarvoor u toestemming hebt gegeven in de vorige sectie. Selecteer Windows Virtual **Desktop**in deze twee apps .
   
   ![Een schermafbeelding van de zoekresultaten bij het zoeken naar 'Windows Virtual Desktop' in 'Enterprise-toepassingen'. De app met de naam "Windows Virtual Desktop" wordt gemarkeerd.](media/tenant-enterprise-app.png)
3. Selecteer **Gebruikers en groepen**. Mogelijk ziet u dat de beheerder die toestemming heeft gegeven voor de toepassing al is weergegeven met de **standaardtoegangsrol** toegewezen. Dit is niet genoeg om een Windows Virtual Desktop-tenant te maken. Blijf deze instructies volgen om de rol **TenantCreator** aan een gebruiker toe te voegen.
   
   ![Een schermafbeelding van de gebruikers en groepen die zijn toegewezen aan het beheer van de bedrijfstoepassing 'Windows Virtual Desktop'. De schermafbeelding toont slechts één toewijzing, namelijk voor 'Standaardtoegang'.](media/tenant-default-access.png)
4. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** op het tabblad **Toewijzing toevoegen.**
5. Zoek naar een gebruikersaccount waarmee uw Windows Virtual Desktop-tenant wordt gemaakt. Voor de eenvoud kan dit het wereldwijde beheerdersaccount zijn.
   - Als u een Microsoft Identity contosoadmin@live.com Provider contosoadmin@outlook.comleuk of, u zich mogelijk niet aanmelden bij Windows Virtual Desktop. We raden u aan een admin@contoso.com admin@contoso.onmicrosoft.com domeinspecifiek account te gebruiken, zoals of in plaats daarvan.

   ![Een schermafbeelding van het selecteren van een gebruiker die moet worden toegevoegd als 'TenantCreator'.](media/tenant-assign-user.png)
   > [!NOTE]
   > U moet een gebruiker (of een groep die een gebruiker bevat) selecteren die afkomstig is van dit Azure Active Directory-exemplaar. U geen gastgebruiker (B2B) of een serviceprincipal kiezen.

6. Selecteer het gebruikersaccount, kies de knop **Selecteren** en selecteer **Vervolgens Toewijzen**.
7. Controleer op de pagina **Windows Virtueel bureaublad - Gebruikers en groepen** of u een nieuw item ziet met de **TenantCreator-rol** die is toegewezen aan de gebruiker die de Windows Virtual Desktop-tenant maakt.

   ![Een schermafbeelding van de gebruikers en groepen die zijn toegewezen aan het beheer van de bedrijfstoepassing 'Windows Virtual Desktop'. De schermafbeelding bevat nu een tweede vermelding van een gebruiker die is toegewezen aan de rol 'TenantCreator'.](media/tenant-tenant-creator-added.png)

Voordat u verder gaat met het maken van uw Windows Virtual Desktop-tenant, hebt u twee stukjes informatie nodig:

   - Uw Azure Active Directory-tenant-id (of **Directory-id)**
   - Uw Azure-abonnements-id

Uw Azure Active Directory-tenant-id (of **Directory-id)** zoeken:
1. Zoek in dezelfde [Azure-portalsessie](https://portal.azure.com) naar en selecteer **Azure Active Directory**.

   ![Een schermafbeelding van de zoekresultaten voor 'Azure Active Directory' in de Azure-portal. Het zoekresultaat onder 'Services' wordt gemarkeerd.](media/tenant-search-azure-active-directory.png)
2. Schuif omlaag totdat u **Eigenschappen**vindt en selecteer deze.
3. Zoek naar **Directory-ID**en selecteer vervolgens het klembordpictogram. Plak het op een handige locatie zodat u het later gebruiken als de **AadTenantId-waarde.**

   ![Een schermafbeelding van de Azure Active Directory-eigenschappen. De muis zweeft boven het klembordpictogram voor 'Directory ID' om te kopiëren en plakken.](media/tenant-directory-id.png)

Ga als een opzoekgaan naar uw Azure-abonnements-id:
1. Zoek en selecteer **abonnementen**in dezelfde [Azure-portalsessie](https://portal.azure.com) .
   
   ![Een schermafbeelding van de zoekresultaten voor 'Azure Active Directory' in de Azure-portal. Het zoekresultaat onder 'Services' wordt gemarkeerd.](media/tenant-search-subscription.png)
2. Selecteer het Azure-abonnement dat u wilt gebruiken om meldingen van de Windows Virtual Desktop-service te ontvangen.
3. Zoek naar **abonnements-id**en plaats de plaats vervolgens boven de waarde totdat er een klembordpictogram wordt weergegeven. Selecteer het klembordpictogram en plak het op een handige locatie, zodat u het later gebruiken als de **AzureSubscriptionId-waarde.**
   
   ![Een schermafbeelding van de eigenschappen van het Azure-abonnement. De muis zweeft boven het klembordpictogram voor 'Abonnement-id' om te kopiëren en plakken.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Een Windows Virtual Desktop-tenant maken

Nu u de Windows Virtual Desktop-servicemachtigingen hebt verleend voor query Azure Active Directory en de rol TenantCreator aan een gebruikersaccount hebt toegewezen, u een Windows Virtual Desktop-tenant maken.

Download en importeer eerst [de Windows Virtual Desktop-module](/powershell/windows-virtual-desktop/overview/) die u in uw PowerShell-sessie gebruiken als u dat nog niet hebt gedaan.

Meld u aan bij Windows Virtual Desktop met het tenantcreator-gebruikersaccount met deze cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Maak daarna een nieuwe Windows Virtual Desktop-tenant die is gekoppeld aan de Azure Active Directory-tenant:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Vervang de waarden tussen haakjes door waarden die relevant zijn voor uw organisatie en tenant. De naam die u kiest voor uw nieuwe Windows Virtual Desktop-tenant moet wereldwijd uniek zijn. Stel dat u de Windows Virtual Desktop TenantCreator bent voor de Contoso-organisatie. De cmdlet die je zou lopen zou er zo uitzien:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Het is een goed idee om administratieve toegang toe te wijzen aan een tweede gebruiker voor het geval u ooit merkt dat u buitengesloten van uw account, of je gaat op vakantie en iemand nodig om op te treden als de huurder admin in uw afwezigheid. Als u beheerderstoegang wilt toewijzen aan een tweede `<TenantName>` `<Upn>` gebruiker, voert u de volgende cmdlet uit met en vervangt u de naam van uw tenant en de UPN van de tweede gebruiker.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Volgende stappen

Nadat u uw tenant hebt gemaakt, moet u een serviceprincipal maken in Azure Active Directory en deze een rol toewijzen binnen Windows Virtual Desktop. Met de serviceprincipal u het Windows Virtual Desktop Azure Marketplace-aanbod met succes implementeren om een hostgroep te maken. Ga voor meer informatie over hostpools verder naar de zelfstudie voor het maken van een hostpool in Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Service-principals en roltoewijzingen maken met PowerShell](./create-service-principal-role-powershell.md)
