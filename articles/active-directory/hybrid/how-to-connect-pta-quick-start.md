---
title: Azure AD-pasverificatie - Snelstart | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u aan de slag met Azure Active Directory (Azure AD) Pass-through Authentication.
services: active-directory
keywords: Azure AD Connect Pass-Through Authentication, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b84e972584562be741919c7dccb6bdfe1bdea628
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312858"
---
# <a name="azure-active-directory-pass-through-authentication-quickstart"></a>Azure Active Directory Pass-Through-verificatie: snel aande slag

## <a name="deploy-azure-ad-pass-through-authentication"></a>Azure AD-pass-through-verificatie implementeren

Azure Active Directory (Azure AD) Pass-through Authentication stelt uw gebruikers in staat om zich aan te melden bij zowel on-premises als cloudtoepassingen met dezelfde wachtwoorden. Pass-through Authentication meldt gebruikers in door hun wachtwoorden rechtstreeks te valideren tegen on-premises Active Directory.

>[!IMPORTANT]
>Als u migreert van AD FS (of andere federatietechnologieën) naar Pass-through Authentication, raden we u ten zeerste aan onze gedetailleerde implementatiehandleiding te volgen die [hier](https://aka.ms/adfstoPTADPDownload)wordt gepubliceerd.

Volg deze instructies om Pass-through Authentication op uw tenant te implementeren:

## <a name="step-1-check-the-prerequisites"></a>Stap 1: Controleer de voorwaarden

Zorg ervoor dat de volgende voorwaarden zijn ingevoerd.

### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheercentrum

1. Maak een algemeen beheerdersaccount voor de cloud op uw Azure AD-tenant. Op deze manier u de configuratie van uw tenant beheren als uw on-premises services mislukken of niet meer beschikbaar zijn. Meer informatie over [het toevoegen van een wereldwijd beheerdersaccount voor alleen cloud](../active-directory-users-create-azure-portal.md). Het voltooien van deze stap is essentieel om ervoor te zorgen dat u niet buitengesloten van uw huurder.
2. Voeg een of meer [aangepaste domeinnamen](../active-directory-domains-add-azure-portal.md) toe aan uw Azure AD-tenant. Uw gebruikers kunnen zich aanmelden met een van deze domeinnamen.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. Identificeer een server met Windows Server 2012 R2 of hoger om Azure AD Connect uit te voeren. Als dit nog niet is ingeschakeld, [schakelt u TLS 1.2 in op de server.](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect) Voeg de server toe aan hetzelfde Active Directory-forest als de gebruikers van wie u de wachtwoorden moet valideren.
2. Installeer de [nieuwste versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) op de server die in de vorige stap is geïdentificeerd. Als Azure AD Connect al actief is, moet u ervoor zorgen dat de versie 1.1.750.0 of hoger is.

    >[!NOTE]
    >Azure AD Connect-versies 1.1.557.0, 1.1.558.0, 1.1.561.0 en 1.1.614.0 hebben een probleem met betrekking tot wachtwoordhashsynchronisatie. Als u _niet van_ plan bent om wachtwoordhashsynchronisatie te gebruiken in combinatie met Pass-through Authentication, leest u de azure [AD Connect-releasenotities](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Identificeer een of meer extra servers (met Windows Server 2012 R2 of hoger, waarbij TLS 1.2 is ingeschakeld) waar u zelfstandige verificatieagents uitvoeren. Deze extra servers zijn nodig om de hoge beschikbaarheid van aanvragen om in te loggen te garanderen. Voeg de servers toe aan hetzelfde Active Directory-forest als de gebruikers van wie u de wachtwoorden moet valideren.

    >[!IMPORTANT]
    >In productieomgevingen raden we u aan minimaal 3 verificatieagents op uw tenant te laten draaien. Er is een systeemlimiet van 40 verificatieagents per tenant. En als aanbevolen, behandel alle servers met verificatieagents als Tier 0-systemen (zie [referentie).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

4. Als er een firewall is tussen uw servers en Azure AD, configureert u de volgende items:
   - Controleer of verificatieagents *uitgaande* aanvragen kunnen indienen bij Azure AD via de volgende poorten:

     | Poortnummer | Hoe dat wordt gebruikt |
     | --- | --- |
     | **80** | Downloadt de certificaatintrekkingslijsten (CRL's) terwijl het TLS/SSL-certificaat wordt valideren |
     | **443** | Verwerkt alle uitgaande communicatie met de service |
     | **8080** (optioneel) | Verificatieagents melden hun status elke tien minuten via poort 8080, als poort 443 niet beschikbaar is. Deze status wordt weergegeven op de Azure AD-portal. Poort 8080 wordt _niet_ gebruikt voor aanmeldingen van gebruikers. |
     
     Als uw firewall regels afdwingt volgens de oorspronkelijke gebruikers, opent u deze poorten voor verkeer van Windows-services die als netwerkservice worden uitgevoerd.
   - Als uw firewall of proxy DNS-whitelisting toestaat, voegt u verbindingen toe aan ** \*.msappproxy.net** en ** \*.servicebus.windows.net**. Zo niet, geef dan toegang tot de [IP-bereiken van Azure-datacenters,](https://www.microsoft.com/download/details.aspx?id=41653)die wekelijks worden bijgewerkt.
   - Uw verificatiemedewerkers hebben toegang nodig tot **login.windows.net** en **login.microsoftonline.com** voor de eerste registratie. Open uw firewall ook voor die URL's.
   - Voor certificaatvalidatie deblokkeren van de volgende URL's: **mscrl.microsoft.com:80,** **crl.microsoft.com:80,** **ocsp.msocsp.com:80**en **\.www microsoft.com:80**. Aangezien deze URL's worden gebruikt voor certificaatvalidatie met andere Microsoft-producten, u deze URL's al laten deblokkeren.

### <a name="azure-government-cloud-prerequisite"></a>Vereiste Azure Government-cloud
Download de nieuwste versie van de PTA-agent van de Azure-portal voordat u pass-through-verificatie via Azure AD Connect met stap 2 inschakelt.  U moet ervoor zorgen dat uw agent versies **x.x.xxx.x** of hoger is.  Als u wilt controleren of uw agent [verificatieagents bijwerken ziet](how-to-connect-pta-upgrade-preview-authentication-agents.md)

Na het downloaden van de nieuwste versie van de agent gaat u verder met de onderstaande instructies om Pass-Through-verificatie te configureren via Azure AD Connect.

## <a name="step-2-enable-the-feature"></a>Stap 2: De functie inschakelen

Pass-through-verificatie inschakelen via [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>U Pass-through Authentication inschakelen op de primaire azure AD Connect-primaire of faseringsserver. Het wordt ten zeerste aanbevolen om het in te schakelen vanaf de primaire server. Als u in de toekomst een Azure AD Connect-faseringsserver instelt, **moet** u doorgaan-verificatie blijven kiezen als aanmeldingsoptie. als u een andere optie **kiest, schakelt u** Pass-through Authentication op de tenant uit en overschrijft u de instelling in de primaire server.

Als u Azure AD Connect voor de eerste keer installeert, kiest u het [aangepaste installatiepad](how-to-connect-install-custom.md). Kies op de **aanmeldingspagina voor gebruiker** **pass-through-verificatie** als **aanmeldingsmethode**. Na een succesvolle voltooiing is een Pass-through Authentication Agent geïnstalleerd op dezelfde server als Azure AD Connect. Daarnaast is de functie Pass-through Authentication ingeschakeld op uw tenant.

![Azure AD Connect: aanmelding voor gebruikers](./media/how-to-connect-pta-quick-start/sso3.png)

Als u Azure AD Connect al hebt geïnstalleerd met behulp van de [expresinstallatie](how-to-connect-install-express.md) of het [aangepaste installatiepad,](how-to-connect-install-custom.md) selecteert u de **aanmeldingstaak van** de gebruiker wijzigen in Azure AD Connect en selecteert u **Volgende**. Selecteer vervolgens **Doorgeefverificatie** als aanmeldingsmethode. Na een succesvolle voltooiing is een Pass-through Authentication Agent geïnstalleerd op dezelfde server als Azure AD Connect en is de functie ingeschakeld op uw tenant.

![Azure AD Connect: aanmelding voor gebruikers wijzigen](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Pass-through Authentication is een functie op tenantniveau. Het inschakelen is van invloed op de aanmelding voor gebruikers in _alle_ beheerde domeinen in uw tenant. Als u overstapt van Active Directory Federation Services (AD FS) naar Verificatie door de doorlaat, moet u ten minste 12 uur wachten voordat u uw AD FS-infrastructuur afsluit. Deze wachttijd is om ervoor te zorgen dat gebruikers zich tijdens de overgang kunnen blijven aanmelden bij Exchange ActiveSync. Voor meer hulp bij het migreren van AD FS naar Pass-through Authentication, bekijk ons gedetailleerde implementatieplan [dat hier](https://aka.ms/adfstoptadpdownload)wordt gepubliceerd.

## <a name="step-3-test-the-feature"></a>Stap 3: Test de functie

Volg deze instructies om te controleren of u Pass-through Authentication correct hebt ingeschakeld:

1. Meld u aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met de algemene beheerdersreferenties voor uw tenant.
2. Selecteer **Azure Active Directory** in het linkerdeelvenster.
3. Selecteer **Azure AD Connect**.
4. Controleer of de **verificatiefunctie doorlaten** wordt weergegeven als **Ingeschakeld**.
5. Selecteer **Doorgeefverificatie**. In **het verificatievenster van pass-through** worden de servers weergegeven waar uw verificatieagents zijn geïnstalleerd.

![Azure Active Directory-beheercentrum: deelvenster Azure AD Connect](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory-beheercentrum: deelvenster Verificatie doorgeven](./media/how-to-connect-pta-quick-start/pta8.png)

In dit stadium kunnen gebruikers van alle beheerde domeinen in uw tenant zich aanmelden met Pass-through Authentication. Gebruikers van federatieve domeinen blijven zich echter aanmelden met AD FS of een andere federatieprovider die u eerder hebt geconfigureerd. Als u een domein converteert van federatief naar beheerd, beginnen alle gebruikers van dat domein automatisch aan te melden met pass-through-verificatie. De functie Pass-through Authentication heeft geen invloed op alleen cloudgebruikers.

## <a name="step-4-ensure-high-availability"></a>Stap 4: Zorg voor hoge beschikbaarheid

Als u pass-through-verificatie wilt implementeren in een productieomgeving, moet u extra zelfstandige verificatieagents installeren. Installeer deze verificatieagent(s) op _andere_ server(s) dan die met Azure AD Connect. Deze instelling biedt u een hoge beschikbaarheid voor aanmeldingsverzoeken van gebruikers.

>[!IMPORTANT]
>In productieomgevingen raden we u aan minimaal 3 verificatieagents op uw tenant te laten draaien. Er is een systeemlimiet van 40 verificatieagents per tenant. En als aanbevolen, behandel alle servers met verificatieagents als Tier 0-systemen (zie [referentie).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)

Het installeren van meerdere Pass-through Authentication Agents zorgt voor een hoge beschikbaarheid, maar niet voor deterministische load balancing tussen de Verificatieagents. Als u wilt bepalen hoeveel verificatieagents u nodig hebt voor uw tenant, moet u rekening houden met de piek en de gemiddelde belasting van aanmeldingsaanvragen die u op uw tenant verwacht te zien. Als benchmark kan één verificatieagent 300 tot 400 verificaties per seconde verwerken op een standaard 4-core CPU, 16 GB RAM-server.

Als u het netwerkverkeer wilt schatten, gebruikt u de volgende richtlijnen voor het formaat:
- Elk verzoek heeft een payload-grootte van (0,5 K + 1K * num_of_agents) bytes, dat wil zeggen gegevens van Azure AD naar de verificatieagent. Hier geeft 'num_of_agents' het aantal verificatieagents aan dat op uw tenant is geregistreerd.
- Elk antwoord heeft een payloadgrootte van 1K bytes, dat wil zeggen gegevens van de verificatieagent naar Azure AD.

Voor de meeste klanten zijn drie verificatiemedewerkers in totaal voldoende voor hoge beschikbaarheid en capaciteit. Installeer verificatieagents dicht bij uw domeincontrollers om de aanmeldingslatentie te verbeteren.

Volg om te beginnen de volgende instructies om de verificatieagentsoftware te downloaden:

1. Als u de nieuwste versie van de verificatieagent (versie 1.5.193.0 of hoger) wilt downloaden, meldt u zich aan bij het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com) met de globale beheerdersreferenties van uw tenant.
2. Selecteer **Azure Active Directory** in het linkerdeelvenster.
3. Selecteer **Azure AD Connect,** selecteer **Doorgeefverificatie**en selecteer **Vervolgens Agent downloaden**.
4. Selecteer de knop **Voorwaarden accepteren & download.**

![Azure Active Directory-beheercentrum: knop Verificatieagent downloaden](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory-beheercentrum: deelvenster Agent downloaden](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>U ook direct [de Authentication Agent-software downloaden.](https://aka.ms/getauthagent) Bekijk en accepteer de [servicevoorwaarden van](https://aka.ms/authagenteula) de verificatieagent _voordat u_ deze installeert.

Er zijn twee manieren om een zelfstandige verificatieagent te implementeren:

Ten eerste u dit interactief doen door de gedownloade verificatieagent uitvoerbaar te maken en de algemene beheerdersreferenties van uw tenant op te geven wanneer daarom wordt gevraagd.

Ten tweede u een script voor onbeheerde implementatie maken en uitvoeren. Dit is handig wanneer u meerdere verificatieagents tegelijk wilt implementeren of verificatieagents wilt installeren op Windows-servers die geen gebruikersinterface hebben ingeschakeld of die u niet openen met Extern bureaublad. Hier zijn de instructies over het gebruik van deze aanpak:

1. Voer de volgende opdracht uit `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`om een verificatieagent te installeren: .
2. U de verificatieagent registreren bij onze service met Windows PowerShell. Maak een PowerShell-object `$cred` met een algemene gebruikersnaam en wachtwoord voor uw tenant. Voer de volgende opdracht * \<\> * uit en vervang gebruikersnaam en * \<wachtwoord:\>*

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Ga naar **C:\Program Files\Microsoft Azure AD Connect Authentication** `$cred` Agent en voer het volgende script uit met het object dat u hebt gemaakt:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Als een verificatieagent op een virtuele machine is geïnstalleerd, u de virtuele machine niet klonen om een andere verificatieagent in te stellen. Deze methode wordt **niet ondersteund**.

## <a name="step-5-configure-smart-lockout-capability"></a>Stap 5: Smart Lockout-mogelijkheden configureren

Smart Lockout helpt bij het vergrendelen van slechte acteurs die proberen om wachtwoorden van uw gebruikers raden of met behulp van brute-force methoden om binnen te komen. Door Smart Lockout-instellingen in Azure AD te configureren en/of de juiste vergrendelingsinstellingen in on-premises Active Directory, kunnen aanvallen worden uitgefilterd voordat ze Active Directory bereiken. Lees [dit artikel](../authentication/howto-password-smart-lockout.md) voor meer informatie over het configureren van Smart Lockout-instellingen op uw tenant om uw gebruikersaccounts te beschermen.

## <a name="next-steps"></a>Volgende stappen
- [Migreren van AD FS naar Pass-through Authentication](https://aka.ms/adfstoptadp) - Een gedetailleerde handleiding om te migreren van AD FS (of andere federatietechnologieën) naar Pass-through Authentication.
- [Smart Lockout:](../authentication/howto-password-smart-lockout.md)meer informatie over het configureren van de Smart Lockout-mogelijkheid op uw tenant om gebruikersaccounts te beschermen.
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): Ontdek welke scenario's worden ondersteund met de pass-through-verificatie en welke niet.
- [Technische diepe duik:](how-to-connect-pta-how-it-works.md)Begrijp hoe de Pass-through Authentication-functie werkt.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Vind antwoorden op veelgestelde vragen.
- [Problemen oplossen:](tshoot-connect-pass-through-authentication.md)meer informatie over het oplossen van veelvoorkomende problemen met de functie Doorgeefverificatie.
- [Security deep dive](how-to-connect-pta-security-deep-dive.md): Krijg technische informatie over de pass-through authentication-functie.
- [Azure AD Seamless SSO:](how-to-connect-sso.md)Meer informatie over deze aanvullende functie.
- [UserVoice:](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)gebruik het Azure Active Directory Forum om nieuwe functieaanvragen in te dienen.
