---
title: Eenmalige aanmelding met application proxy | Microsoft Documenten
description: Heeft betrekking op het bieden van eenmalige aanmelding met Azure AD-toepassingsproxy.
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
ms.date: 08/13/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017, it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5948fba67d3f071d77192f9ad89bc696fdc0c3cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253453"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Kerberos Constrained Delegation voor één aanmelding bij uw apps met application proxy

U één aanmelding bieden voor on-premises toepassingen die zijn gepubliceerd via Application Proxy en die zijn beveiligd met geïntegreerde Windows-verificatie. Voor deze toepassingen is een Kerberos-ticket nodig voor toegang. Application Proxy gebruikt Kerberos Constrained Delegation (KCD) om deze toepassingen te ondersteunen. 

U eenmalige aanmelding bij uw toepassingen inschakelen met geïntegreerde Windows-verificatie (IWA) door toepassingsproxyconnectors toestemming te geven in Active Directory om zich voor te doen als gebruikers. De connectors gebruiken deze toestemming om namens hen tokens te verzenden en te ontvangen.

## <a name="how-single-sign-on-with-kcd-works"></a>Hoe single sign-on met KCD werkt
In dit diagram wordt de stroom uitgelegd wanneer een gebruiker probeert toegang te krijgen tot een on-premises toepassing die IWA gebruikt.

![Microsoft AAD-verificatiestroomdiagram](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. De gebruiker voert de URL in om toegang te krijgen tot de on-premises applicatie via Application Proxy.
2. Application Proxy leidt het verzoek door naar Azure AD-verificatieservices naar preauthenticeren. Op dit moment past Azure AD alle toepasselijke verificatie- en autorisatiebeleidsregels toe, zoals multifactorauthenticatie. Als de gebruiker is gevalideerd, maakt Azure AD een token en stuurt het naar de gebruiker.
3. De gebruiker geeft het token door aan Application Proxy.
4. Application Proxy valideert het token en haalt de User Principal Name (UPN) eruit, waarna de Connector de UPN en de Service Principal Name (SPN) door een dubbel geverifieerd beveiligd kanaal haalt.
5. De connector voert KCD-onderhandelingen (Kerberos Constrained Delegation) uit met het on-premises AD, waarbij de gebruiker zich voordoet als de gebruiker om een Kerberos-token voor de toepassing te krijgen.
6. Active Directory stuurt het Kerberos-token voor de toepassing naar de connector.
7. De connector stuurt het oorspronkelijke verzoek naar de toepassingsserver met behulp van het Kerberos-token dat het van AD heeft ontvangen.
8. De toepassing stuurt het antwoord naar de connector, die vervolgens wordt teruggestuurd naar de Application Proxy-service en ten slotte naar de gebruiker.

## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag gaat met eenmalige aanmelding voor IWA-toepassingen, controleert u of uw omgeving klaar is met de volgende instellingen en configuraties:

* Uw apps, zoals SharePoint Web-apps, zijn ingesteld op geïntegreerde Windows-verificatie. Zie [Ondersteuning inschakelen voor Kerberos-verificatie](https://technet.microsoft.com/library/dd759186.aspx)of voor SharePoint zie Verificatie voor [Kerberos in SharePoint 2013 voor](https://technet.microsoft.com/library/ee806870.aspx)meer informatie .
* Al uw apps hebben [Service Principal Names.](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx)
* De server waarop de connector en de server waarop de app wordt uitgevoerd, zijn domeinsamengevoegd en maken deel uit van hetzelfde domein of vertrouwende domeinen. Zie Lid worden van [een computer naar een domein](https://technet.microsoft.com/library/dd807102.aspx)voor meer informatie over het deelnemen aan een domein.
* De server waarop de connector wordt uitgevoerd, heeft toegang tot het kenmerk TokenGroupsGlobalAndUniversal voor gebruikers. Deze standaardinstelling is mogelijk beïnvloed door beveiligingsverharding van de omgeving.

### <a name="configure-active-directory"></a>Active Directory configureren
De Active Directory-configuratie varieert, afhankelijk van of uw toepassingsproxyconnector en de toepassingsserver zich in hetzelfde domein bevinden of niet.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Connector- en toepassingsserver in hetzelfde domein
1. Ga in Active Directory naar **Extragebruikers** > **en computers**.
2. Selecteer de server waarop de connector wordt uitgevoerd.
3. Klik met de rechtermuisknop en selecteer > **Eigenschappendelegatie**. **Properties**
4. Selecteer **Deze computer mag alleen aan opgegeven services delegeren**. 
5. Selecteer **Elk verificatieprotocol gebruiken**.
6. Onder **Services waaraan deze account gedelegeerde referenties kan presenteren,** voegt u de waarde toe voor de SPN-identiteit van de toepassingsserver. Hierdoor kan de Application Proxy Connector zich voordoen als gebruikers in AD tegen de toepassingen die in de lijst zijn gedefinieerd.

   ![Schermafbeelding van het venster Eigenschappen van Connector-SVR](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Connector en toepassingsserver in verschillende domeinen
1. Zie [Kerberos Constrained Delegation voor](https://technet.microsoft.com/library/hh831477.aspx)een lijst met vereisten voor het werken met KCD tussen domeinen.
2. Gebruik `principalsallowedtodelegateto` de eigenschap van het serviceaccount (computer of speciaal domeingebruikersaccount) van de webtoepassing om Kerberos-verificatiedeleger van de toepassingsproxy (connector) in te schakelen. De toepassingsserver wordt uitgevoerd `webserviceaccount` in de context van `connectorcomputeraccount`en de delegerende server is . Voer de onderstaande opdrachten uit op een domeincontroller (met Windows Server 2012 R2 of hoger) in het domein van `webserviceaccount`. Gebruik platte namen (niet UPN) voor beide accounts.

   Als `webserviceaccount` het een computeraccount is, gebruikt u de volgende opdrachten:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Als `webserviceaccount` het een gebruikersaccount is, gebruikt u de volgende opdrachten:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Eenmalige aanmelding configureren 
1. Publiceer uw toepassing volgens de instructies beschreven in [Publicatie toepassingen met Application Proxy](application-proxy-add-on-premises-application.md). Selecteer Azure **Active Directory** als **verificatiemethode**.
2. Nadat uw toepassing in de lijst met bedrijfstoepassingen is weergegeven, selecteert u deze en klikt u op **Eén aanmelding**.
3. Stel de enkele aanmeldingsmodus in op **Geïntegreerde Windows-verificatie**.  
4. Voer de **SPN voor interne toepassing** van de toepassingsserver in. In dit voorbeeld is de SPN voor onze gepubliceerde applicatie http/www.contoso.com. Deze SPN moet in de lijst met services staan waaraan de connector gedelegeerde referenties kan presenteren. 
5. Kies de **gedelegeerde aanmeldingsidentiteit** die de connector namens uw gebruikers moet gebruiken. Zie [Werken met verschillende on-premises en cloudidentiteiten](#working-with-different-on-premises-and-cloud-identities) voor meer informatie

   ![Geavanceerde toepassingsconfiguratie](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO voor niet-Windows-apps

De Kerberos-delegatiestroom in Azure AD-toepassingsproxy wordt gestart wanneer Azure AD de gebruiker in de cloud verifieert. Zodra de aanvraag on-premises binnenkomt, geeft de Azure AD Application Proxy-connector namens de gebruiker een Kerberos-ticket uit door interactie met de lokale Active Directory. Dit proces wordt aangeduid als Kerberos Constrained Delegation (KCD). In de volgende fase wordt een aanvraag naar de backend-applicatie gestuurd met dit Kerberos-ticket. 

Er zijn verschillende protocollen die bepalen hoe dergelijke verzoeken te verzenden. De meeste niet-Windows-servers verwachten te onderhandelen met SPNEGO. Dit protocol wordt ondersteund op Azure AD Application Proxy, maar is standaard uitgeschakeld. Een server kan worden geconfigureerd voor SPNEGO of standaard KCD, maar niet beide.

Als u een connectormachine voor SPNEGO configureert, controleert u of alle andere connectoren in die connectorgroep ook zijn geconfigureerd met SPNEGO. Toepassingen die standaard KCD verwachten, moeten worden doorgestuurd via andere connectors die niet zijn geconfigureerd voor SPNEGO.
 

SpNEGO inschakelen:

1. Open een opdrachtprompt die als beheerder wordt uitgevoerd.
2. Voer in de opdrachtprompt de volgende opdrachten uit op de connectorservers die SPNEGO nodig hebben.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Zie Alles wat u [wilt weten over Kerberos Constrained Delegation (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd)voor meer informatie over Kerberos.

Niet-Windows-apps hebben meestal gebruikersnamen of SAM-accountnamen in plaats van domeine-mailadressen. Als die situatie van toepassing is op uw toepassingen, moet u het veld gedelegeerde inlogidentiteit configureren om uw cloudidentiteiten te verbinden met uw toepassingsidentiteiten. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Werken met verschillende on-premises en cloudidentiteiten
Application Proxy gaat ervan uit dat gebruikers precies dezelfde identiteit hebben in de cloud en on-premises. Maar in sommige omgevingen moeten organisaties vanwege bedrijfsbeleid of toepassingsafhankelijkheden mogelijk alternatieve id's gebruiken voor aanmelding. In dergelijke gevallen u KCD nog steeds gebruiken voor eenmalige aanmelding. Configureer een **gedelegeerde inlogidentiteit** voor elke toepassing om aan te geven welke identiteit moet worden gebruikt bij het uitvoeren van eenmalige aanmelding.  

Met deze mogelijkheid kunnen veel organisaties met verschillende on-premises en cloudidentiteiten SSO hebben van de cloud tot on-premises apps zonder dat de gebruikers verschillende gebruikersnamen en wachtwoorden hoeven in te voeren. Dit geldt ook voor organisaties die:

* Meerdere domeinen internjoe@us.contoso.com( joe@eu.contoso.com, ) en eenjoe@contoso.comenkel domein in de cloud ( ).
* Hebben niet-routable domeinnaam internjoe@contoso.usa( ) en een juridische in de cloud.
* Gebruik geen domeinnamen intern (joe)
* Gebruik verschillende aliassen op locatie en in de cloud. Bijvoorbeeld vs. joe-johns@contoso.comjoej@contoso.com  

Met Application Proxy u selecteren welke identiteit u wilt gebruiken om het Kerberos-ticket te verkrijgen. Deze instelling is per toepassing. Sommige van deze opties zijn geschikt voor systemen die geen e-mailadresformaat accepteren, andere zijn ontworpen voor alternatieve aanmelding.

![Schermafbeelding van de parameter Voor gedelegeerde inlogidentiteit](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Als gedelegeerde aanmeldingsidentiteit wordt gebruikt, is de waarde mogelijk niet uniek voor alle domeinen of forests in uw organisatie. U dit probleem voorkomen door deze toepassingen twee keer te publiceren met twee verschillende connectorgroepen. Omdat elke toepassing een andere gebruikersdoelgroep heeft, u de connectors aansluiten bij een ander domein.

### <a name="configure-sso-for-different-identities"></a>SSO configureren voor verschillende identiteiten
1. Configureer Azure AD Connect-instellingen, zodat de hoofdidentiteit het e-mailadres (e-mail) is. Dit gebeurt als onderdeel van het pasproces door het veld **Gebruikersnaam** in de synchronisatie-instellingen te wijzigen. Deze instellingen bepalen ook hoe gebruikers zich aanmelden bij Office365, Windows10-apparaten en andere toepassingen die Azure AD gebruiken als hun identiteitsarchief.  
   ![Gebruikers schermafbeelding identificeren - Vervolgkeuzelijst gebruikersnaam](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Selecteer in de instellingen voor toepassingsconfiguratie voor de toepassing die u wilt wijzigen de **gedelegeerde aanmeldingsidentiteit** die u wilt gebruiken:

   * Gebruikersnaam (bijvoorbeeld joe@contoso.com)
   * Alternatieve gebruikersnaam (bijvoorbeeld joed@contoso.local)
   * Gebruikersnaam onderdeel van gebruikersnaam (bijvoorbeeld joe)
   * Gebruikersnaam onderdeel van Alternatieve gebruikersnaam (bijvoorbeeld joed)
   * On-premises SAM-accountnaam (afhankelijk van de configuratie van de domeincontroller)

### <a name="troubleshooting-sso-for-different-identities"></a>Problemen met SSO oplossen voor verschillende identiteiten
Als er een fout optreedt in het SSO-proces, wordt deze weergegeven in het gebeurtenislogboek van de connectormachine zoals uitgelegd in [Probleemoplossing](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Maar in sommige gevallen wordt het verzoek met succes verzonden naar de backend-toepassing, terwijl deze toepassing in verschillende andere HTTP-antwoorden antwoordt. Het oplossen van problemen met deze aanvragen moet beginnen met het onderzoeken van gebeurtenisnummer 24029 op de connectormachine in het gebeurtenislogboek van de toepassingsproxysessie. De gebruikersidentiteit die is gebruikt voor overdracht, wordt weergegeven in het veld 'gebruiker' in de gebeurtenisdetails. Als u het sessielogboek wilt inschakelen, selecteert u **Analytische en foutopsporingslogboeken weergeven** in het weergavemenu van de gebeurtenisviewer.

## <a name="next-steps"></a>Volgende stappen

* [Een toepassingsproxytoepassing configureren om de beperkte delegatie van Kerberos te gebruiken](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Problemen met toepassingsproxy oplossen (Engelstalig artikel)](application-proxy-troubleshoot.md)


Ga naar het [blog over toepassingsproxy](https://blogs.technet.com/b/applicationproxyblog/) voor nieuws en updates.
