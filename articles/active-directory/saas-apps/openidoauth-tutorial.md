---
title: Een OpenID/OAuth-toepassing configureren vanuit de Azure AD-app-galerie | Microsoft Docs
description: Stappen voor het configureren van een OpenID/OAuth-toepassing uit de Azure AD-app-galerie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: eedebb76-e78c-428f-9cf0-5891852e79fb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f8a2c962c69ead28c4e79b663010eab77a499f5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048420"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Een OpenID/OAuth-toepassing uit de Azure AD-app-galerie configureren

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Proces voor het toevoegen van een OpenID-toepassing vanuit de galerie

1. In de [Azure-portal](https://portal.azure.com), selecteert u in het linkerdeelvenster **Azure Active Directory**. 

    ![De knop Azure Active Directory](common/select-azuread.png))

2. Ga naar **Enterprise-toepassingen** > **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer **Nieuwe toepassing** boven in het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak de naam van de toepassing. Selecteer de gewenste toepassing in het deelvenster met resultaten en registreer u bij de toepassing.

    ![Openid in de lijst met resultaten](common/search-new-app.png)

    > [!NOTE]
    > Voor OpenID Connect- en OAuth-apps is de knop **Toevoegen** standaard uitgeschakeld. De tenantbeheerder moet hier de registratieknop selecteren en toestemming geven voor de toepassing. De toepassing wordt vervolgens toegevoegd aan de klanttenant, waar u configuraties kunt uitvoeren. Het is niet nodig om de toepassing expliciet toe te voegen.

    ![Knop Toevoegen](./media/openidoauth-tutorial/addbutton.png)

5. Wanneer u de registratiekoppeling selecteert, wordt u naar de Azure Active Directory-pagina (Azure AD) geleid voor aanmeldingsreferenties.

6. Wanneer de verificatie is voltooid, accepteert u toestemming op de toestemmingspagina. Hierna wordt de startpagina van de toepassing weergegeven.

    > [!NOTE]
    > U kunt slechts één exemplaar van de toepassing toevoegen. Als u er al een hebt toegevoegd en u opnieuw hebt geprobeerd om toestemming te geven, wordt deze niet opnieuw toegevoegd aan de tenant. Logisch gezien kunt u dus maar één app-exemplaar gebruiken in de tenant.

## <a name="authentication-flow-using-openid-connect"></a>Verificatiestroom waarbij OpenID Connect wordt gebruikt

De meest eenvoudige aanmeldingsstroom bestaat uit de volgende stappen:

![Verificatiestroom waarbij OpenID Connect wordt gebruikt](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Toepassing met meerdere tenants 
Toepassingen met meerdere tenants kunnen in vele organisatie worden gebruikt en niet slechts in één organisatie. Dit zijn normaliter Software-as-a-Service-toepassingen (SaaS), geschreven door een onafhankelijke softwareleverancier (ISV). 

Toepassingen met meerdere tenants moeten in elke map waarin ze zullen worden gebruikt, worden ingericht. Er toestemming van een gebruiker of beheerder nodig om ze te kunnen registreren. Het toestemmingsproces begint wanneer een toepassing in de map is geregistreerd en toegang verkrijgt tot de Graph-API of wellicht zelfs een andere web-API. Wanneer een gebruiker of beheerder van een andere organisatie zich registreert voor gebruik van de toepassing, wordt er een dialoogvenster weergegeven met de machtigingen die de toepassing nodig heeft. 

De gebruiker of beheerder geeft dan toestemming voor de toepassing. Met deze toestemming krijgt de toepassing toegang tot de opgegeven gegevens én wordt de toepassing in de map geregistreerd.

> [!NOTE]
> Als u uw toepassing beschikbaar stelt aan gebruikers in meerdere mappen, hebt u een mechanisme nodig om te bepalen in welke tenant ze zich bevindt. Toepassingen met één tenant hoeven alleen in hun eigen map op zoek te gaan naar een gebruiker. Toepassingen met meerdere tenants moeten specifieke gebruikers identificeren vanuit álle mappen in Azure AD.
> 
> Om deze taak uit te voeren, biedt Azure AD een algemeen verificatie-eindpunt waar toepassingen met meerdere tenants aanmeldingsaanvragen naartoe kunnen sturen (in plaats van een tenantspecifiek eindpunt). Dit eindpunt is `https://login.microsoftonline.com/common` voor alle mappen in Azure AD. Een tenantspecifiek eindpunt kan bijvoorbeeld `https://login.microsoftonline.com/contoso.onmicrosoft.com` zijn. 
>
> Het algemene eindpunt is belangrijk om in overweging te nemen bij het ontwikkelen van uw toepassing. U hebt de nodige logica nodig om meerdere tenants te verwerken tijdens het aanmelden, afmelden en tokenvalidatie.

Azure AD promoot standaard toepassingen met meerdere tenants. Ze zijn eenvoudig toegankelijk in meerdere organisaties en zijn eenvoudig te gebruiken nadat de toestemming is geaccepteerd.

## <a name="consent-framework"></a>Toestemmingsframework

U kunt u het Azure AD-toestemmingsframework gebruiken om clienttoepassingen met meerdere tenants (op internet en systeemeigen) te maken. Bij deze toepassingen kunnen gebruikersaccounts zich aanmelden uit Azure AD-tenants die verschillen van de tenant waarin de toepassing is geregistreerd. Mogelijk hebben ze ook toegang nodig tot web-API's zoals:
- De Microsoft Graph-API, voor toegang tot Azure AD, Intune en services in Office 365. 
- De API's van andere Microsoft-services.
- Uw eigen web-API's. 

Het framework is gebaseerd op een gebruiker of beheerder die toestemming geeft aan een toepassing die vraagt in de map te worden geregistreerd. De registratie kan toegang tot mapgegevens omvatten. Wanneer er toestemming is gegeven, kan de clienttoepassing de Microsoft Graph-API namens de gebruiker aanroepen en op basis van behoefte gebruikmaken van gegevens.

De [Microsoft Graph-API](https://developer.microsoft.com/graph/) biedt toegang tot gegevens in Office 365, zoals:

- Agenda's en berichten uit Exchange.
- Sites en lijsten uit SharePoint.
- Documenten uit OneDrive.
- Notebooks uit OneNote.
- Taken uit Planner.
- Werkmappen uit Excel.

De Graph-API biedt ook toegang tot gebruikers en groepen uit Azure AD en andere gegevensobjecten uit andere Microsoft-cloudservices.

In de volgende stappen ontdekt u hoe de toestemmingservaring werkt voor de toepassingsontwikkelaar en -gebruiker:

1. Stel dat u een webclienttoepassing hebt die specifieke machtigingen moet aanvragen voor toegang tot een resource of API. De Azure-portal wordt gebruikt om tijdens de configuratie machtigingsaanvragen te declareren. Net als andere configuratie-instellingen worden ze onderdeel van de Azure AD-registraties van de toepassing. Voor het pad Met toestemmingsaanvraag hebt u de onderstaande stappen nodig:

    a. Klik op de **app-registraties** aan de linkerkant van het menu en open uw toepassing door de toepassingsnaam in het zoekvak te typen.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Klik **op API-machtigingen weergeven**.

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Klik op **Een machtiging toevoegen**.

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Klik op **Microsoft Graph**.

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Selecteer vereiste opties in **gedelegeerde machtigingen** en **toepassingsmachtigingen**.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Houd er rekening mee dat de machtigingen van uw toepassing zijn bijgewerkt. De toepassing is actief en een gebruiker staat op het punt deze voor het eerst te gebruiken. Om te beginnen moet de toepassing een verificatiecode ophalen bij het Azure AD-eindpunt /authorize. De autorisatiecode kan vervolgens worden gebruikt bij het verkrijgen van een nieuw token voor toegang en vernieuwen.

3. Als de gebruiker nog niet is geverifieerd, vraagt het Azure AD-eindpunt /authorize om aanmelding.

    ![Verificatie](./media/openidoauth-tutorial/authentication.png)

4. Wanneer de gebruiker zich heeft aangemeld, bepaalt Azure AD of de gebruiker een toestemmingspagina te zien krijgt. Deze bepaling is gebaseerd op de vraag of de gebruiker (of de beheerder van hun organisatie) de aanvraagal toestemming heeft verleend.

   Als er nog geen toestemming is gegeven, vraagt Azure AD de gebruiker om toestemming en wordt weergegeven welke machtigingen nodig zijn om de app goed te laten werken. De machtigingen die in het dialoogvenster voor toestemming worden weergegeven, komen overeen met de gedelegeerde toestemmingen die in de Azure-portal zijn geselecteerd.

    ![Toestemmingspagina](./media/openidoauth-tutorial/consentpage.png)

Gewone gebruikers kunnen toestemming geven voor verschillende machtigingen. Voor andere machtigingen is toestemming van een tenantbeheerder vereist.

## <a name="difference-between-admin-consent-and-user-consent"></a>Het verschil tussen de toestemming van een beheerder en de toestemming van een gebruiker

Beheerders kunnen toestemming geven voor de gedelegeerde machtigingen van een toepassing voor alle gebruikers in de tenant. Met beheerderstoestemming wordt het dialoogvenster voor toestemming niet meer weergegeven aan alle gebruikers in de tenant. Gebruikers die beschikken over de beheerdersrol kunnen in de Azure-portal toestemming geven. Selecteer **op** de pagina Instellingen voor uw toepassing de optie **Vereiste machtigingen** > **Verlenen admin toestemming**.

![De knop Machtigingen verlenen](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Het verlenen van expliciete toestemming door gebruik te maken van de **toestemmingsknop Grant admin** is nu vereist voor toepassingen met één pagina (SB's) die ADAL.js gebruiken. Als dit niet gebeurt, treedt er een fout op in de toepassing wanneer het toegangstoken wordt aangevraagd.

Voor app-only machtigingen is altijd toestemming van een tenantbeheerder vereist. Als uw toepassing een app-specifieke machtiging aanvraagt en een gebruiker zich bij de toepassing probeert aan te melden, wordt er een foutmelding weergegeven. In het bericht staat dat de gebruiker geen toestemming kan geven.

Als voor uw toepassing gebruik wordt gemaakt van machtigingen waarvoor beheerderstoestemming nodig is, moet u een knop of een koppeling toevoegen waarmee de beheerder toestemming kan geven. De aanvraag die uw toepassing met deze actie verzendt, is de gebruikelijke OAuth2/OpenID Connect-verificatieaanvraag. De aanvraag bevat de queryreeksparameter *prompt=admin_consent*. 

Nadat de beheerder heeft ingestemd en de serviceprincipal is gemaakt in de tenant van de klant, hebben latere aanmeldingsverzoeken de parameter *prompt=admin_consent* niet nodig. Omdat de beheerder heeft besloten dat de aangevraagde machtigingen worden geaccepteerd, wordt andere gebruikers in de tenant niet meer om toestemming gevraagd.

Tenantbeheerders kunnen uitschakelen dat normale gebruikers toestemming kunnen geven voor toepassingen. Als dit wordt uitgeschakeld, is er altijd beheerderstoestemming nodig om een toepassing in een tenant te kunnen gebruiken. Als u uw toepassing wilt testen terwijl toestemming voor eindgebruikers is uitgeschakeld, kunt u toestemming uitschakelen in de [Azure-portal](https://portal.azure.com/). Ga hiervoor naar het gedeelte [Gebruikersinstellingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) onder **Bedrijfstoepassingen**.

De parameter *prompt=admin_consent* kan ook worden gebruikt door toepassingen die machtigingen aanvragen waarvoor geen beheerderstoestemming nodig is. Een voorbeeld is een toepassing die een ervaring vereist waarbij de tenantbeheerder zich één keer "aanmeldt" en dat er vanaf dat moment geen andere gebruikers om toestemming worden gevraagd.

Stel dat een toepassing beheerderstoestemming vereist en een beheerder zich aanmeldt zonder dat de parameter *prompt=admin_consent* wordt verzonden. Als de beheerder toestemming geeft voor de toepassing, is dit alleen van toepassing op zijn of haar eigen gebruikersaccount. Gewone gebruikers kunnen zich nog altijd niet aanmelden en geen toestemming geven voor de toepassing. Deze functie is handig als u de tenantbeheerder de mogelijkheid wilt bieden om uw toepassing te bekijken voordat andere gebruikers toegang wordt geboden.
