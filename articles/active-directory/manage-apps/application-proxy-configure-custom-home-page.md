---
title: Aangepaste start pagina voor gepubliceerde apps-Azure AD-toepassingsproxy
description: Bevat informatie over de basisbeginselen van Azure AD Application Proxy connectors
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1621b273f617955a374ed46d9c215ba99e5b2913
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275601"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Een aangepaste startpagina voor gepubliceerde apps instellen met behulp van Azure AD-toepassingsproxy

In dit artikel wordt beschreven hoe u een app kunt configureren om een gebruiker naar een aangepaste start pagina te sturen. Wanneer u een app met toepassings proxy publiceert, stelt u een interne URL in, maar soms is dat niet de pagina die een gebruiker eerst moet zien. Stel een aangepaste start pagina zo in dat een gebruiker de juiste pagina krijgt wanneer hij of zij toegang tot de app heeft. Een gebruiker ziet de aangepaste start pagina die u hebt ingesteld, ongeacht of ze toegang hebben tot de app vanuit het Azure Active Directory toegangs venster of het start programma voor de Office 365-app.

Wanneer een gebruiker de app start, wordt deze standaard omgeleid naar de hoofd domein-URL voor de gepubliceerde app. De startpagina is standaard ingesteld als de URL van startpagina. Gebruik de Azure AD Power shell-module om een aangepaste URL voor de start pagina te definiëren wanneer u wilt dat een app-gebruiker op een specifieke pagina in de app terechtkomt.

Hier volgt een scenario waarin wordt uitgelegd waarom uw bedrijf een aangepaste start pagina zou instellen:

- In het bedrijfs netwerk gaat een gebruiker naar `https://ExpenseApp/login/login.aspx` om zich aan te melden en toegang te krijgen tot uw app.
- Omdat u andere assets (zoals installatie kopieën) hebt die door de toepassings proxy moeten worden geopend op het hoogste niveau van de mappen structuur, publiceert u de app met `https://ExpenseApp` als de interne URL.
- De standaard externe URL is `https://ExpenseApp-contoso.msappproxy.net`, die geen externe gebruiker naar de aanmeldings pagina gaat.
- U wilt in plaats daarvan `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` instellen als de URL van de start pagina, zodat een externe gebruiker de aanmeldings pagina eerst ziet.

> [!NOTE]
> Wanneer u gebruikers toegang geeft tot gepubliceerde apps, worden de apps weer gegeven in het [Azure AD-toegangs venster](../user-help/my-apps-portal-end-user-access.md) en het [Start programma voor de Office 365-app](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/).

## <a name="before-you-start"></a>Voordat u begint

Voordat u de URL van startpagina, houd rekening met de volgende vereisten:

- Het pad dat u opgeeft, moet een subdomein zijn van de URL van het hoofd domein.

  Als de URL van het hoofd domein bijvoorbeeld is `https://apps.contoso.com/app1/`, moet de URL van de start pagina die u configureert, beginnen met `https://apps.contoso.com/app1/`.

- Als u een wijziging in de gepubliceerde app aanbrengt, kan de wijziging opnieuw instellen van de waarde van de URL van startpagina. Wanneer u de app in de toekomst bijwerkt, moet u controleren en, indien nodig werkt u de URL van startpagina.

U kunt de URL van de start pagina instellen via de Azure Portal of met behulp van Power shell.

## <a name="change-the-home-page-in-the-azure-portal"></a>Wijzigen van de startpagina in de Azure-portal

Als u de URL van de start pagina van uw app wilt wijzigen via de Azure AD-Portal, voert u de volgende stappen uit:

1. Meld u als beheerder aan bij [Azure Portal](https://portal.azure.com/).
1. Selecteer **Azure Active Directory**en klik vervolgens **app-registraties**. De lijst met geregistreerde apps wordt weer gegeven.
1. Kies uw app in de lijst. Er wordt een pagina weer gegeven met de details van de geregistreerde app.
1. Selecteer onder **beheren**de optie **huis stijl**.
1. Werk de **URL van de start pagina** bij met het nieuwe pad.

   ![Huisstijl pagina voor een geregistreerde app waarin het URL-veld van de start pagina wordt weer gegeven](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecteer **Opslaan**.

## <a name="change-the-home-page-with-powershell"></a>Wijzigen van de startpagina met PowerShell

Als u de start pagina van een app wilt configureren met behulp van Power shell, moet u het volgende doen:

1. Installeer de Azure AD Power shell-module.
1. Zoek de ObjectId-waarde van de app.
1. De URL van de start pagina van de app bijwerken met behulp van Power shell-opdrachten.

### <a name="install-the-azure-ad-powershell-module"></a>De Azure AD PowerShell-module installeren

Voordat u de URL van een aangepaste startpagina definiëren met behulp van PowerShell, installeert u de Azure AD PowerShell-module. U kunt het pakket downloaden via de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), dat gebruikmaakt van het Graph API-eind punt.

Volg deze stappen voor het installeren van het pakket:

1. Open een standaard PowerShell-venster en voer de volgende opdracht:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Als u de opdracht als niet-beheerder uitvoert, gebruikt u de optie `-scope currentuser`.

1. Selecteer bij de installatie de optie **Y** om twee pakketten van Nuget.org te installeren. Beide pakketten zijn vereist.

### <a name="find-the-objectid-of-the-app"></a>De ObjectId van de app zoeken

U krijgt de ObjectId van de app door te zoeken naar de app op de weer gegeven naam of de start pagina.

1. In de dezelfde PowerShell-venster, de Azure AD-module te importeren.

   ```powershell
   Import-Module AzureAD
   ```

1. Meld u aan de module Azure AD als de tenantbeheerder.

   ```powershell
   Connect-AzureAD
   ```

1. Zoek de app. In dit voor beeld wordt Power shell gebruikt om de ObjectId te vinden door te zoeken naar de app met een weergave naam van `SharePoint`.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   U moet een resultaat die vergelijkbaar is met hieronder. Kopieer de ObjectId-GUID voor gebruik in de volgende sectie.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   U kunt ook gewoon de lijst met alle apps ophalen, in de lijst zoeken naar de app met een specifieke weergave naam of start pagina en de ObjectId van de app kopiëren nadat de app is gevonden.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>Bijwerken van de URL van startpagina

Maak de URL van de start pagina en werk uw app bij met die waarde. Ga verder met hetzelfde Power shell-venster, of Meld u opnieuw aan bij de Azure AD-module met `Connect-AzureAD`als u een nieuw Power shell-venster gebruikt. Voer vervolgens de volgende stappen uit:

1. Maak een variabele voor de ObjectId-waarde die u in de vorige sectie hebt gekopieerd. (Vervang de ObjectId-waarde voor in dit share point-voor beeld met de waarde van de ObjectId van uw app.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Controleer of u de juiste app hebt door de volgende opdracht uit te voeren. De uitvoer moet identiek zijn aan de uitvoer die u in de vorige sectie hebt gezien ([Zoek de ObjectId van de app](#find-the-objectid-of-the-app)).

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Maak een lege toepassingsobject voor het opslaan van de wijzigingen die u wilt maken.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. De URL van startpagina ingesteld op de waarde die u wilt. De waarde moet een pad van het subdomein van de gepubliceerde app. Als u bijvoorbeeld de URL van de start pagina wijzigt van `https://sharepoint-iddemo.msappproxy.net/` naar `https://sharepoint-iddemo.msappproxy.net/hybrid/`, gaan app-gebruikers rechtstreeks naar de aangepaste start pagina.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. De start pagina bijwerken.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Als u wilt bevestigen dat de wijziging is geslaagd, voert u de volgende opdracht uit stap 2 opnieuw uit.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   In ons voor beeld wordt de uitvoer nu als volgt weer gegeven:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Start de app opnieuw om te bevestigen dat de start pagina als eerste scherm wordt weer gegeven, zoals verwacht.

> [!NOTE]
> Alle wijzigingen die u in de app aanbrengt mogelijk opnieuw instellen van de URL van startpagina. Als de URL van uw startpagina wordt opnieuw ingesteld, herhaalt u de stappen in deze sectie om opnieuw ingesteld.

## <a name="next-steps"></a>Volgende stappen

- [Externe toegang tot share point inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Zelf studie: een on-premises toepassing toevoegen voor externe toegang via toepassings proxy in Azure Active Directory](application-proxy-add-on-premises-application.md)