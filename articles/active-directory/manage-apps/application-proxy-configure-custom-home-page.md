---
title: Aangepaste startpagina voor gepubliceerde apps - Azure AD-toepassingsproxy
description: Dekt de basisprincipes van Azure AD Application Proxy-connectors
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275601"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-ad-application-proxy"></a>Een aangepaste startpagina instellen voor gepubliceerde apps met Azure AD-toepassingsproxy

In dit artikel wordt besproken hoe u een app configureert om een gebruiker naar een aangepaste startpagina te leiden. Wanneer u een app publiceert met Application Proxy, stelt u een interne URL in, maar soms is dat niet de pagina die een gebruiker eerst moet zien. Stel een aangepaste startpagina in zodat een gebruiker de juiste pagina krijgt wanneer hij de app opent. Een gebruiker ziet de aangepaste startpagina die u instelt, ongeacht of deze toegang heeft tot de app vanuit het Azure Active Directory Access Panel of het startprogramma voor Office 365-apps.

Wanneer een gebruiker de app start, wordt deze standaard doorverwezen naar de URL van het hoofddomein voor de gepubliceerde app. De bestemmingspagina wordt meestal ingesteld als de URL van de startpagina. Gebruik de Azure AD PowerShell-module om een aangepaste URL van de startpagina te definiëren wanneer u wilt dat een app-gebruiker op een specifieke pagina in de app terechtkomt.

Hier is een scenario dat uitlegt waarom uw bedrijf een aangepaste startpagina zou instellen:

- Binnen uw bedrijfsnetwerk gaat `https://ExpenseApp/login/login.aspx` een gebruiker naar om in te loggen en toegang te krijgen tot uw app.
- Omdat u andere elementen (zoals afbeeldingen) hebt die Toepassingsproxy op het hoogste niveau van `https://ExpenseApp` de mapstructuur moet openen, publiceert u de app als interne URL.
- De standaard externe `https://ExpenseApp-contoso.msappproxy.net`URL is , waardoor een externe gebruiker niet naar de aanmeldingspagina wordt geleid.
- U wilt `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx` in plaats daarvan instellen als de URL van de startpagina, zodat een externe gebruiker eerst de aanmeldingspagina ziet.

> [!NOTE]
> Wanneer u gebruikers toegang geeft tot gepubliceerde apps, worden de apps weergegeven in het [Azure AD Access-paneel](../user-help/my-apps-portal-end-user-access.md) en het [startprogramma voor Office 365-apps.](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)

## <a name="before-you-start"></a>Voordat u begint

Voordat u de URL van de startpagina instelt, moet u rekening houden met de volgende vereisten:

- Het pad dat u opgeeft, moet een subdomeinpad van de URL van het hoofddomein zijn.

  Als de URL van het `https://apps.contoso.com/app1/`hoofddomein bijvoorbeeld is, moet de `https://apps.contoso.com/app1/`URL van de startpagina die u configureert, beginnen met .

- Als u een wijziging aanbrengt in de gepubliceerde app, kan de wijziging de waarde van de URL van de startpagina opnieuw instellen. Wanneer u de app in de toekomst bijwerkt, moet u de URL van de startpagina opnieuw controleren en zo nodig bijwerken.

U de URL van de startpagina instellen via de Azure-portal of via PowerShell.

## <a name="change-the-home-page-in-the-azure-portal"></a>De startpagina in de Azure-portal wijzigen

Voer de volgende stappen uit om de URL van de startpagina van uw app te wijzigen via de Azure AD-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) als beheerder.
1. Selecteer **Azure Active Directory**en vervolgens **App-registraties**. De lijst met geregistreerde apps wordt weergegeven.
1. Kies uw app in de lijst. Er verschijnt een pagina met de details van de geregistreerde app.
1. Selecteer **Onder Beheren**de optie **Branding**.
1. Werk de URL van de **startpagina bij** met uw nieuwe pad.

   ![Merkpagina voor een geregistreerde app met het veld URL op de startpagina](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. Selecteer **Opslaan**.

## <a name="change-the-home-page-with-powershell"></a>De startpagina wijzigen met PowerShell

Als u de startpagina van een app wilt configureren met PowerShell, moet u het als:

1. Installeer de Azure AD PowerShell-module.
1. Zoek de ObjectId-waarde van de app.
1. Werk de URL van de startpagina van de app bij met PowerShell-opdrachten.

### <a name="install-the-azure-ad-powershell-module"></a>De Azure AD PowerShell-module installeren

Installeer de Azure AD PowerShell-module voordat u een aangepaste URL van de startpagina definieert met PowerShell.U het pakket downloaden uit de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16), die gebruik maakt van het Graph API-eindpunt.

Voer de volgende stappen uit om het pakket te installeren:

1. Open een standaard PowerShell-venster en voer de volgende opdracht uit:

   ```powershell
   Install-Module -Name AzureAD
   ```

    Als u de opdracht uitvoert als niet-beheerder, gebruikt u de `-scope currentuser` optie.

1. Selecteer Tijdens de installatie **Y** om twee pakketten van Nuget.org te installeren. Beide pakketten zijn vereist.

### <a name="find-the-objectid-of-the-app"></a>Zoek de objectid van de app

U krijgt de ObjectId van de app door te zoeken naar de app op de weergavenaam of startpagina.

1. Importeer in hetzelfde PowerShell-venster de Azure AD-module.

   ```powershell
   Import-Module AzureAD
   ```

1. Meld u aan bij de Azure AD-module als tenantbeheerder.

   ```powershell
   Connect-AzureAD
   ```

1. Zoek de app. In dit voorbeeld wordt PowerShell gebruikt om de ObjectId `SharePoint`te vinden door te zoeken naar de app met een weergavenaam van .

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   Je moet een resultaat dat vergelijkbaar is met de hier getoonde te krijgen. Kopieer de ObjectId-GUID om te gebruiken in de volgende sectie.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   U ook gewoon de lijst met alle apps opvragen, in de lijst naar de app zoeken met een specifieke weergavenaam of startpagina en de ObjectId van de app kopiëren zodra de app is gevonden.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>De URL van de startpagina bijwerken

Maak de URL van de startpagina en werk uw app bij met die waarde. Ga door met hetzelfde PowerShell-venster of als u een nieuw PowerShell-venster `Connect-AzureAD`gebruikt, meld u opnieuw aan bij de Azure AD-module met behulp van . Volg nu deze stappen:

1. Maak een variabele om de ObjectId-waarde vast te houden die u in de vorige sectie hebt gekopieerd. (Vervang de objectid-waarde die in dit SharePoint-voorbeeld wordt gebruikt door de objectid-waarde van uw app.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. Controleer of u de juiste app hebt door de volgende opdracht uit te voeren. De uitvoer moet identiek zijn aan de uitvoer die u in de vorige sectie hebt gezien[(Zoek de objectid van de app).](#find-the-objectid-of-the-app)

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. Maak een leeg toepassingsobject om de wijzigingen vast te houden die u wilt aanbrengen.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. Stel de URL van de startpagina in op de gewenste waarde. De waarde moet een subdomeinpad van de gepubliceerde app zijn. Als u bijvoorbeeld de URL van `https://sharepoint-iddemo.msappproxy.net/` `https://sharepoint-iddemo.msappproxy.net/hybrid/`de startpagina wijzigt van naar , gaan app-gebruikers rechtstreeks naar de aangepaste startpagina.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. Maak de update van de startpagina.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. Als u wilt bevestigen dat de wijziging is gelukt, voert u de volgende opdracht vanaf stap 2 opnieuw uit.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   Voor ons voorbeeld moet de uitvoer nu als volgt worden weergegeven:

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. Start de app opnieuw op om te bevestigen dat de startpagina wordt weergegeven als het eerste scherm, zoals verwacht.

> [!NOTE]
> Eventuele wijzigingen die u in de app aanbrengt, kunnen de URL van de startpagina opnieuw instellen. Als de URL van uw startpagina wordt gereset, herhaalt u de stappen in deze sectie om deze terug te zetten.

## <a name="next-steps"></a>Volgende stappen

- [Externe toegang tot SharePoint inschakelen met Azure AD-toepassingsproxy](application-proxy-integrate-with-sharepoint-server.md)
- [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via toepassingsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md)