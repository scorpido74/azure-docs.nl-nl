---
title: Externe toegang tot Power BI inschakelen met Azure AD-toepassingsproxy
description: Behandelt de basisprincipes over het integreren van een on-premises Power BI met Azure AD Application Proxy.
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
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc96c94152b39cc70cfc4553690faaa5b9cb8d20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111584"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Externe toegang tot Power BI - Mobiel met Azure AD-toepassingsproxy inschakelen

In dit artikel wordt besproken hoe u Azure AD Application Proxy gebruiken om de mobiele Power BI-app in staat te stellen verbinding te maken met Power BI Report Server (PBIRS) en SQL Server Reporting Services (SSRS) 2016 en hoger. Via deze integratie kunnen gebruikers die zich niet uit het bedrijfsnetwerk bevinden, toegang krijgen tot hun Power BI-rapporten via de mobiele Power BI-app en worden beschermd door Azure AD-verificatie. Deze beveiliging omvat [beveiligingsvoordelen](application-proxy-security.md#security-benefits) zoals voorwaardelijke toegang en meervoudige verificatie.  

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u Rapportservices al hebt geïmplementeerd en Application Proxy hebt [ingeschakeld.](application-proxy-add-on-premises-application.md)

- Voor het inschakelen van toepassingsproxy moet u een connector op een Windows-server installeren en de [vereisten](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) invullen, zodat de connector kan communiceren met Azure AD-services.  
- Bij het publiceren van Power BI raden we u aan dezelfde interne en externe domeinen te gebruiken. Zie Werken met aangepaste [domeinen in Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)voor meer informatie over aangepaste domeinen.
- Deze integratie is beschikbaar voor de **Power BI Mobile iOS- en Android-applicatie.**

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Stap 1: Kerberos Constrained Delegation (KCD) configureren

Voor on-premises toepassingen die Windows-verificatie gebruiken, u eenmalige aanmelding (SSO) bereiken met het Kerberos-verificatieprotocol en een functie genaamd Kerberos constrained delegation (KCD). Wanneer kcd is geconfigureerd, kan de Toepassingsproxy-connector een Windows-token voor een gebruiker verkrijgen, zelfs als de gebruiker zich niet rechtstreeks bij Windows heeft aangemeld. Zie [Kerberos Constrained Delegation Overview](https://technet.microsoft.com/library/jj553400.aspx) en [Kerberos Constrained Delegation voor eenmalige aanmelding bij uw apps met Application Proxy voor](application-proxy-configure-single-sign-on-with-kcd.md)meer informatie over KCD.

Wat Reporting Services betreft, hoeft er niet veel te worden geconfigureerd. Zorg ervoor dat u een geldige Service Principal Name (SPN) hebt om de juiste Kerberos-verificatie mogelijk te maken. Zorg er ook voor dat de server Van Reporting Services is ingeschakeld voor Verificatie onderhandelen.

Als u KCD voor rapportageservices wilt instellen, gaat u verder met de volgende stappen.

### <a name="configure-the-service-principal-name-spn"></a>De spn -naam van de serviceprincipal configureren

De SPN is een unieke id voor een service die Kerberos-verificatie gebruikt. U moet ervoor zorgen dat u een goede HTTP SPN-aanwezig hebt voor uw rapportserver. Zie [Een Service Principal Name (SPN) voor een rapportserver registreren](https://msdn.microsoft.com/library/cc281382.aspx) voor meer informatie over het configureren van de juiste Service Principal Name (SPN) voor de rapportserver.
U controleren of de SPN is toegevoegd door de opdracht Setspn uit te voeren met de optie -L. Zie [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx)voor meer informatie over deze opdracht.

### <a name="enable-negotiate-authentication"></a>Verificatie onderhandelen inschakelen

Als u wilt dat een rapportserver Kerberos-verificatie gebruikt, configureert u het verificatietype van de rapportserver als RSWindowsNegotiate. Configureer deze instelling met het rsreportserver.config-bestand.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Zie [Een Reporting Services-configuratiebestand wijzigen](https://msdn.microsoft.com/library/bb630448.aspx) en [Windows-verificatie op een rapportserver configureren](https://msdn.microsoft.com/library/cc281253.aspx) voor meer informatie.

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Ervoor zorgen dat de connector wordt vertrouwd voor de overdracht naar het SPN dat is toegevoegd aan het account voor de groep van de toepassingsgroep rapportageservices
Configureer KCD zodat de Azure AD Application Proxy-service gebruikersidentiteiten kan delegeren aan het account van de groep van de rapportageservices-groep. Configureer KCD door de toepassingsproxyconnector in te schakelen om Kerberos-tickets op te halen voor uw gebruikers die zijn geverifieerd in Azure AD. Vervolgens geeft die server de context door aan de doeltoepassing of rapportageservices in dit geval.

Als u KCD wilt configureren, herhaalt u de volgende stappen voor elke connectormachine:

1. Meld u aan bij een domeincontroller als domeinbeheerder en open **Active Directory: gebruikers en computers**.
2. Zoek de computer waarop de connector wordt uitgevoerd.  
3. Dubbelklik op de computer en selecteer het tabblad **Delegatie.**
4. Stel de delegatieinstellingen in **op Deze computer vertrouwen voor alleen de overdracht naar de opgegeven services**. Selecteer vervolgens **Een verificatieprotocol gebruiken**.
5. Selecteer **Toevoegen**en selecteer **vervolgens Gebruikers of computers**.
6. Voer het serviceaccount in dat u gebruikt voor rapportageservices. Dit is het account waaraan u de SPN hebt toegevoegd in de Reporting Services-configuratie.
7. Klik op **OK**. Als u de wijzigingen wilt opslaan, klikt u nogmaals op **OK.**

Zie [Kerberos Constrained Delegation voor eenmalige aanmelding bij uw apps met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)voor meer informatie.

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Stap 2: Rapportservices publiceren via Azure AD-toepassingsproxy

Nu u Azure AD-toepassingsproxy configureren.

1. Publiceer Rapportservices via Application Proxy met de volgende instellingen. Zie Publicatie van [toepassingen met Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)Application Proxy voor stapsgewijze instructies over het publiceren van een toepassing via Application Proxy.
   - **Interne URL**: Voer de URL in naar de rapportserver die de connector in het bedrijfsnetwerk kan bereiken. Controleer of deze URL bereikbaar is vanaf de server waarop de connector is geïnstalleerd. Een aanbevolen procedure is het gebruik `https://servername/` van een top-level domein, zoals om problemen met subpaden gepubliceerd via Application Proxy te voorkomen. Bijvoorbeeld, gebruik `https://servername/` en `https://servername/reports/` `https://servername/reportserver/`niet of .
     > [!NOTE]
     > We raden u aan een beveiligde HTTPS-verbinding met de rapportserver te gebruiken. Zie [SSL-verbindingen configureren op een rapportserver in de native modus](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) voor informatie over hoe.
   - **Externe URL**: Voer de openbare URL in waarmee de mobiele Power BI-app verbinding maakt. Het kan er bijvoorbeeld `https://reports.contoso.com` uitzien alsof een aangepast domein wordt gebruikt. Als u een aangepast domein wilt gebruiken, uploadt u een certificaat voor het domein en wijst u een DNS-record naar het standaarddomein msappproxy.net voor uw toepassing. Zie [Werken met aangepaste domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md)voor gedetailleerde stappen.

   - **Verificatiemethode:** Azure Active Directory

2. Zodra uw app is gepubliceerd, configureert u de instellingen voor één aanmelding met de volgende stappen:

   a. Selecteer op de toepassingspagina in de portal De optie **Eén aanmelding**.

   b. Selecteer **Geïntegreerde Windows-verificatie** **voor de modus**Eenmalig aanmelden .

   c. Stel **interne toepassing SPN** in op de waarde die u eerder instelt.  

   d. Kies de **gedelegeerde aanmeldingsidentiteit** die de connector namens uw gebruikers moet gebruiken. Zie [Werken met verschillende on-premises en cloudidentiteiten](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)voor meer informatie.

   e. Klik **op Opslaan** om de wijzigingen op te slaan.

Als u het instellen van uw toepassing wilt voltooien, gaat u naar de sectie **Gebruikers en groepen** en wijst u gebruikers toe om toegang te krijgen tot deze toepassing.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Stap 3: De Reply URI's voor de toepassing wijzigen

Voordat de mobiele Power BI-app verbinding kan maken met en toegang heeft tot Report Services, moet u de toepassingsregistratie configureren die automatisch voor u is gemaakt in stap 2. 

1. Selecteer **app-registraties**op de pagina Azure Active Directory **Overview** .
2. Zoek onder het tabblad **Alle toepassingen** naar de toepassing die u in stap 2 hebt gemaakt.
3. Selecteer de toepassing en selecteer **Vervolgens Verificatie**.
4. Voeg de volgende Redirect URI's toe op basis van welk platform u gebruikt.

   Voeg bij het configureren van de app voor Power BI Mobile **iOS**de volgende OMLEIDINGs-URI's van het type Public Client (Mobile & Desktop) toe:
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Voeg bij het configureren van de app voor Power BI Mobile **Android**de volgende OMLEIDINGs-URI's van het type Openbare client (Mobile & Desktop) toe:
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D` 
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > De OMLEIDINGs-URI's moeten worden toegevoegd om de toepassing correct te laten werken. Als u de app configureert voor zowel Power BI Mobile iOS als Android, voegt u de volgende Omleidinguri van het `urn:ietf:wg:oauth:2.0:oob`type Public Client (Mobile & Desktop) toe aan de lijst met omleidings-URI's die zijn geconfigureerd voor iOS: .

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Stap 4: Verbinding maken vanuit de mobiele Power BI-app

1. Maak in de mobiele Power BI-app verbinding met het exemplaar Reporting Services. Voer hiervoor de **externe URL** in voor de toepassing die u hebt gepubliceerd via Application Proxy.

   ![Mobiele Power BI-app met externe URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Selecteer **Verbinden**. U wordt doorverwezen naar de aanmeldingspagina van Azure Active Directory.

3. Voer geldige referenties in voor uw gebruiker en selecteer **Aanmelden**. U ziet de elementen van uw server van Rapportageservices.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Stap 5: Intune-beleid configureren voor beheerde apparaten (optioneel)

U Microsoft Intune gebruiken om de client-apps te beheren die het personeel van uw bedrijf gebruikt. Met Intune u mogelijkheden gebruiken, zoals gegevensversleuteling en aanvullende toegangsvereisten. Zie Intune App Management voor meer informatie over app-beheer via Intune. Als u de mobiele Power BI-toepassing wilt laten werken met het Intune-beleid, voert u de volgende stappen uit.

1. Ga naar **Azure Active Directory** en vervolgens naar **App-registraties**.
2. Selecteer de toepassing die is geconfigureerd in stap 3 bij het registreren van uw native clienttoepassing.
3. Selecteer **API-machtigingen**op de pagina van de toepassing.
4. Klik **op Een machtiging toevoegen**. 
5. Zoek onder **API's naar**"Microsoft Mobile Application Management" en selecteer deze.
6. De machtiging **DeviceManagementManagedApps.ReadWrite** toevoegen aan de toepassing
7. Klik **op Toestemming verlenen van** beheerders om de toestemming toegang tot de toepassing te verlenen.
8. Configureer het gewenste Intune-beleid door te verwijzen naar [Het maken en toewijzen van app-beveiligingsbeleid](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>Problemen oplossen

Als de toepassing een foutpagina retourneert nadat u een rapport langer dan een paar minuten hebt geladen, moet u mogelijk de time-outinstelling wijzigen. Standaard ondersteunt Application Proxy toepassingen die tot 85 seconden duren om op een verzoek te reageren. Als u deze instelling wilt verlengen tot 180 seconden, selecteert u de back-endtime-out naar **Lang** op de pagina App-proxy-instellingen voor de toepassing. Zie Aanbevolen procedures voor [Power BI-rapporten](https://docs.microsoft.com/power-bi/power-bi-reports-performance)voor tips over het maken van snelle en betrouwbare rapporten.

## <a name="next-steps"></a>Volgende stappen

- [Native clienttoepassingen inschakelen om te communiceren met proxytoepassingen](application-proxy-configure-native-client-application.md)
- [On-premises rapportserverrapporten en KPI's weergeven in de mobiele Power BI-apps](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
