---
title: Eenmalige aanmelding met Application Proxy | Microsoft Docs
description: Bevat informatie over het bieden van eenmalige aanmelding met Azure AD-toepassingsproxy.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79253453"
---
# <a name="kerberos-constrained-delegation-for-single-sign-on-to-your-apps-with-application-proxy"></a>Beperkte Kerberos-delegering voor single sign-on bij uw apps met Application Proxy

U kunt eenmalige aanmelding bieden voor on-premises toepassingen worden gepubliceerd via toepassingsproxy die zijn beveiligd met geïntegreerde Windows-verificatie. Deze toepassingen vereisen een Kerberos-ticket voor toegang. Application Proxy maakt gebruik van Kerberos-beperkte delegatie (KCD) om deze toepassingen te ondersteunen. 

U kunt eenmalige aanmelding inschakelen voor uw toepassingen met behulp van geïntegreerde Windows-verificatie (IWA) door middel van een Application Proxy connectors machtiging in Active Directory gebruikers te imiteren. De connectors gebruiken deze machtiging om te verzenden en ontvangen van tokens in hun naam.

## <a name="how-single-sign-on-with-kcd-works"></a>Hoe eenmalige aanmelding met KCD werkt
In dit diagram wordt de stroom uitgelegd wanneer een gebruiker toegang probeert te krijgen tot een on-premises toepassing die gebruikmaakt van IWA.

![Stroomdiagram van Microsoft AAD-verificatie](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. De gebruiker voert de URL in voor toegang tot de on-premises toepassing via de toepassings proxy.
2. Application Proxy stuurt de aanvraag naar Azure AD authentication-services kunnen worden. Azure AD van toepassing op dit moment voor alle van toepassing verificatie en autorisatiebeleid, zoals meervoudige verificatie. Als de gebruiker is gevalideerd, wordt Azure AD wordt een token gemaakt en verzendt ze naar de gebruiker.
3. De gebruiker wordt het token doorgegeven aan Application Proxy.
4. De toepassings proxy valideert het token en haalt de UPN (User Principal Name) van de toepassing op, waarna de connector de UPN en de SPN (Service Principal Name) ophaalt via een dubbel geverifieerd beveiligd kanaal.
5. De connector voert onderhandelingen met Kerberos-beperkte delegering (KCD) uit met de on-premises AD en imiteert de gebruiker om een Kerberos-token aan de toepassing te krijgen.
6. Active Directory, verzendt de Kerberos-token voor de toepassing naar de Connector.
7. De Connector verzendt de oorspronkelijke aanvraag naar de application server, met behulp van het Kerberos-token dat het ontvangen van AD.
8. De toepassing stuurt het antwoord op de Connector, die vervolgens wordt geretourneerd naar de Application Proxy-service en ten slotte naar de gebruiker.

## <a name="prerequisites"></a>Vereisten
Voordat u aan de slag met eenmalige aanmelding voor geïntegreerde Windows-toepassingen, zorg er dan voor dat uw omgeving klaar is met de volgende instellingen en configuraties is:

* Uw apps, zoals SharePoint Web-apps, zijn ingesteld op het gebruik van geïntegreerde Windows-verificatie. Zie [Enable support for Kerberos Authentication](https://technet.microsoft.com/library/dd759186.aspx)(Engelstalig) voor meer informatie, of voor share point Zie [Kerberos-verificatie plannen in share point 2013](https://technet.microsoft.com/library/ee806870.aspx).
* Al uw apps hebben [service-principal-namen](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spns-setspn-syntax-setspn-exe.aspx).
* De server waarop de Connector wordt uitgevoerd en de server met de app zijn toegevoegd aan een domein en deel uitmaken van hetzelfde domein of vertrouwde domeinen. Zie [een computer toevoegen aan een domein](https://technet.microsoft.com/library/dd807102.aspx)voor meer informatie over het toevoegen van domeinen aan een domein.
* De server waarop de Connector wordt uitgevoerd heeft toegang tot het lezen van het kenmerk TokenGroupsGlobalAndUniversal voor gebruikers. Deze standaardinstelling is mogelijk beïnvloed door het beperken van de omgeving.

### <a name="configure-active-directory"></a>Active Directory configureren
De Active Directory-configuratie varieert, afhankelijk van of de toepassingsserver en de Application Proxy-connector zich in hetzelfde domein of niet.

#### <a name="connector-and-application-server-in-the-same-domain"></a>Connector en toepassingsserver in hetzelfde domein
1. Ga in Active Directory naar **Hulpprogram ma's** > **gebruikers en computers**.
2. Selecteer de server waarop de connector wordt uitgevoerd.
3. Klik met de rechter muisknop en selecteer **eigenschappen** > **delegering**.
4. Selecteer **deze computer mag alleen delegeren aan de opgegeven services**. 
5. Selecteer **elk verificatie protocol gebruiken**.
6. Onder **Services waaraan dit account gedelegeerde referenties kan geven** , voegt u de waarde voor de SPN-identiteit van de toepassings server toe. Hierdoor kunnen de Connector voor toepassingsproxy te imiteren van gebruikers in AD op basis van de toepassingen die zijn gedefinieerd in de lijst.

   ![Schermafbeelding van de eigenschappen van de connector-Server-venster](./media/application-proxy-configure-single-sign-on-with-kcd/Properties.jpg)

#### <a name="connector-and-application-server-in-different-domains"></a>Connector en toepassingsserver in verschillende domeinen bevinden
1. Zie [Kerberos-beperkte delegering over domeinen](https://technet.microsoft.com/library/hh831477.aspx)voor een lijst met vereisten voor het werken met KCD in verschillende domeinen.
2. Gebruik de eigenschap `principalsallowedtodelegateto` van het service account (computer of toegewezen domein gebruikers account) van de webtoepassing om Kerberos-verificatie overdracht van de toepassings proxy (connector) mogelijk te maken. De toepassings server wordt uitgevoerd in de context van `webserviceaccount` en de server wordt overdraagt `connectorcomputeraccount`. Voer de onderstaande opdrachten uit op een domein controller (waarop Windows Server 2012 R2 of hoger wordt uitgevoerd) in het domein van `webserviceaccount`. Gebruik platte namen (niet-UPN) voor beide accounts.

   Als de `webserviceaccount` een computer account is, gebruikt u deze opdrachten:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADComputer -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADComputer webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

   Als de `webserviceaccount` een gebruikers account is, gebruikt u deze opdrachten:

   ```powershell
   $connector= Get-ADComputer -Identity connectorcomputeraccount -server dc.connectordomain.com

   Set-ADUser -Identity webserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

   Get-ADUser webserviceaccount -Properties PrincipalsAllowedToDelegateToAccount
   ```

## <a name="configure-single-sign-on"></a>Eenmalige aanmelding configureren 
1. Publiceer uw toepassing volgens de instructies in [toepassingen publiceren met toepassings proxy](application-proxy-add-on-premises-application.md). Zorg ervoor dat u **Azure Active Directory** selecteert als **methode voor verificatie**vooraf.
2. Als uw toepassing wordt weer gegeven in de lijst met bedrijfs toepassingen, selecteert u deze en klikt u op **eenmalige aanmelding**.
3. Stel de modus voor eenmalige aanmelding in op **geïntegreerde Windows-verificatie**.  
4. Voer de **interne toepassings-SPN** van de toepassings server in. In dit voorbeeld is de SPN voor de gepubliceerde toepassing http/www.contoso.com. Deze SPN moet zich in de lijst met services die de connector gedelegeerde referenties kan presenteren. 
5. Kies de **gedelegeerde aanmeldings-id** voor de connector om namens uw gebruikers te gebruiken. Zie [werken met verschillende on-premises en Cloud-identiteiten](#working-with-different-on-premises-and-cloud-identities) voor meer informatie

   ![Geavanceerde configuratie](./media/application-proxy-configure-single-sign-on-with-kcd/cwap_auth2.png)  


## <a name="sso-for-non-windows-apps"></a>SSO voor niet-Windows-apps

De stroom van de Kerberos-delegering in Azure AD-toepassingsproxy wordt gestart wanneer Azure AD de gebruiker in de cloud verifieert. Zodra de aanvraag is ontvangen on-premises, wordt in de Azure AD Application Proxy-connector een Kerberos-ticket namens de gebruiker door interactie met de lokale Active Directory uitgeeft. Dit proces wordt aangeduid als Kerberos-beperkte delegatie (KCD). In de volgende fase wordt een aanvraag verzonden naar de back-endtoepassing met dit Kerberos-ticket. 

Er zijn verschillende protocollen die definiëren hoe om dergelijke aanvragen te verzenden. De meeste niet-Windows-servers verwacht om te onderhandelen over met SPNEGO. Dit protocol wordt ondersteund op Azure AD-toepassingsproxy, maar is standaard uitgeschakeld. Een server kan worden geconfigureerd voor het SPNEGO- of standard KCD, maar niet allebei.

Als u een connector machine voor SPNEGO configureert, zorg ervoor dat alle andere connectors in die groep Connector ook worden geconfigureerd met SPNEGO. Standard KCD verwacht toepassingen moeten worden gerouteerd via andere connectors die niet zijn geconfigureerd voor SPNEGO.
 

SPNEGO inschakelen:

1. Open een opdrachtprompt die wordt uitgevoerd als beheerder.
2. Voer de volgende opdrachten op de Connectorservers waarvoor SPNEGO vanaf de opdrachtprompt.

    ```
    REG ADD "HKLM\SOFTWARE\Microsoft\Microsoft AAD App Proxy Connector" /v UseSpnegoAuthentication /t REG_DWORD /d 1
    net stop WAPCSvc & net start WAPCSvc
    ```

Meer informatie over Kerberos vindt [u in alles wat u wilt weten over Kerberos-beperkte delegering (KCD)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/09/21/all-you-want-to-know-about-kerberos-constrained-delegation-kcd).

Niet-Windows-apps doorgaans gebruiker gebruikersnamen of namen van de SAM-account in plaats van dat e-mailadressen. Als deze situatie is van toepassing op uw toepassingen, moet u de gedelegeerde login ID-veld voor de verbinding van uw cloud-identiteiten met de identiteiten van uw toepassing configureren. 

## <a name="working-with-different-on-premises-and-cloud-identities"></a>Werken met verschillende on-premises en cloud-identiteiten
De toepassingsproxy wordt ervan uitgegaan dat gebruikers dezelfde identiteit in de cloud en on-premises. Maar in sommige omgevingen, vanwege bedrijfs beleid of toepassings afhankelijkheden, moeten organisaties wellicht alternatieve Id's gebruiken om zich aan te melden. In dergelijke gevallen kunt u KCD nog steeds gebruiken voor eenmalige aanmelding. Configureer een **gedelegeerde aanmeldings-id** voor elke toepassing om aan te geven welke identiteit moet worden gebruikt bij het uitvoeren van eenmalige aanmelding.  

Op deze manier kunt veel organisaties die beschikken over verschillende on-premises en cloud-identiteiten voor eenmalige aanmelding vanuit de cloud naar on-premises toepassingen zonder de gebruikers verschillende gebruikersnamen en wachtwoorden in te voeren. Dit omvat organisaties die:

* Meerdere domeinen intern hebben (joe@us.contoso.com, joe@eu.contoso.com) en één domein in de Cloud (joe@contoso.com).
* Een niet-Routeer bare domein naam hebben intern (joe@contoso.usa) en één Legal in de Cloud.
* Domeinnamen niet intern gebruiken (Jaap)
* Gebruik verschillende aliassen on-premises en in de Cloud. Bijvoorbeeld joe-johns@contoso.com versus joej@contoso.com  

Met Application Proxy, kunt u selecteren welke identiteit gebruiken om op te halen van het Kerberos-ticket. Deze instelling is per toepassing. Sommige van deze opties zijn geschikt voor systemen die e-mailindeling niet accepteert, anderen zijn ontworpen voor de alternatieve aanmelding.

![Schermafbeelding van de parameter gedelegeerde aanmelding identiteit](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_upn.png)

Als gedelegeerde Aanmeldingsidentiteit wordt gebruikt, kan de waarde niet uniek zijn in alle domeinen of forests in uw organisatie. U kunt dit probleem voorkomen door het publiceren van deze twee keer met behulp van twee verschillende connectorgroepen toepassingen. Omdat elke toepassing een andere gebruiker publiek heeft, kunt u de Connectors toevoegen aan een ander domein.

### <a name="configure-sso-for-different-identities"></a>Eenmalige aanmelding configureren voor verschillende identiteiten
1. Azure AD Connect-instellingen configureren, zodat de identiteit van de belangrijkste het e-mailadres (e-mail is). Dit wordt gedaan als onderdeel van het aanpassings proces door het veld UPN ( **User Principal name** ) in de synchronisatie-instellingen te wijzigen. Deze instellingen ook bepalen hoe gebruikers aanmelden bij Office 365, Windows10 apparaten, en andere toepassingen die gebruikmaken van Azure AD als hun identiteitenarchief.  
   ![de scherm afbeelding van gebruikers identificeren-vervolg keuzelijst gebruikers-principal-naam](./media/application-proxy-configure-single-sign-on-with-kcd/app_proxy_sso_diff_id_connect_settings.png)  
2. Selecteer in de configuratie-instellingen van de toepassing die u wilt wijzigen de **gedelegeerde aanmeldings-id** die moet worden gebruikt:

   * Principal-naam van gebruiker (bijvoorbeeld joe@contoso.com)
   * Alternatieve Principal-naam van gebruiker (bijvoorbeeld joed@contoso.local)
   * Deel van de User Principal Name (bijvoorbeeld: Jaap) gebruikersnaam
   * Onderdeel van de gebruikersnaam van de alternatieve User Principal Name (bijvoorbeeld joed)
   * On-premises SAM-accountnaam is (afhankelijk van de configuratie van de domeincontroller)

### <a name="troubleshooting-sso-for-different-identities"></a>Oplossen van problemen met eenmalige aanmelding voor verschillende identiteiten
Als er een fout optreedt in het SSO-proces, wordt dit weer gegeven in het gebeurtenis logboek van de connector computer, zoals wordt uitgelegd in [probleem oplossing](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).
Maar in sommige gevallen kan de aanvraag is verzonden naar de back-endtoepassing terwijl antwoorden op deze toepassing in verschillende HTTP-antwoorden. Het oplossen van dergelijke gevallen moet beginnen door te controleren gebeurtenisnummer 24029 op de connector-machine in het gebeurtenislogboek van de Application Proxy-sessie. De identiteit van de gebruiker die is gebruikt voor overdracht wordt weergegeven in het veld 'gebruiker' in de details van de gebeurtenis. Als u het sessie logboek wilt inschakelen, selecteert u **analyse logboeken en fout opsporing weer geven** in het menu Beeld van Logboeken.

## <a name="next-steps"></a>Volgende stappen

* [Een toepassings proxy toepassing configureren voor het gebruik van beperkte Kerberos-delegering](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [Problemen met toepassings proxy oplossen](application-proxy-troubleshoot.md)


Ga naar het [blog over toepassingsproxy](https://blogs.technet.com/b/applicationproxyblog/) voor nieuws en updates.
