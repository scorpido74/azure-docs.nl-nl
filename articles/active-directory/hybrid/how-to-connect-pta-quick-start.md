---
title: Pass-Through-verificatie van Azure AD-Quick Start | Microsoft Docs
description: In dit artikel wordt beschreven hoe u aan de slag gaat met de Pass-Through-verificatie van Azure Active Directory (Azure AD).
services: active-directory
keywords: Azure AD Connect Pass-Through-verificatie, installeren van Active Directory, vereiste onderdelen voor Azure AD, SSO, eenmalige aanmelding
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: be6a6e9231b13c47d1421543464c720f6283b5f9
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376173"
---
# <a name="azure-active-directory-pass-through-authentication-quick-start"></a>Pass-Through-verificatie Azure Active Directory: Quick Start

## <a name="deploy-azure-ad-pass-through-authentication"></a>Azure AD Pass-Through-verificatie implementeren

Met Pass-Through-verificatie van Azure Active Directory (Azure AD) kunnen uw gebruikers zich aanmelden bij zowel lokale als Cloud toepassingen met behulp van dezelfde wacht woorden. Pass-Through-verificatie ondertekent gebruikers in door hun wacht woorden rechtstreeks te valideren ten opzichte van on-premises Active Directory.

>[!IMPORTANT]
>Als u migreert van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie, raden we u ten zeerste aan de gedetailleerde implementatie handleiding te volgen die u [hier](https://aka.ms/adfstoPTADPDownload)hebt gepubliceerd.

Volg deze instructies voor het implementeren van Pass-Through-verificatie voor uw Tenant:

## <a name="step-1-check-the-prerequisites"></a>Stap 1: Controleer de vereisten

Zorg ervoor dat aan de volgende vereisten is voldaan.

### <a name="in-the-azure-active-directory-admin-center"></a>In het Azure Active Directory-beheer centrum

1. Maak een alleen-Cloud account voor globale beheerders op uw Azure AD-Tenant. Op deze manier kunt u de configuratie van uw Tenant beheren als uw on-premises Services mislukken of niet meer beschikbaar zijn. Meer informatie over [het toevoegen van een globale beheerders account voor de Cloud](../active-directory-users-create-azure-portal.md). Het volt ooien van deze stap is van cruciaal belang om ervoor te zorgen dat de Tenant niet wordt vergrendeld.
2. Voeg een of meer [aangepaste domein namen](../active-directory-domains-add-azure-portal.md) toe aan uw Azure AD-Tenant. Uw gebruikers kunnen zich aanmelden met een van deze domein namen.

### <a name="in-your-on-premises-environment"></a>In uw on-premises omgeving

1. Een server met Windows Server 2012 R2 of hoger identificeren om Azure AD Connect uit te voeren. Als dit nog niet is ingeschakeld, [schakelt u TLS 1,2 in op de-server](./how-to-connect-install-prerequisites.md#enable-tls-12-for-azure-ad-connect). Voeg de-server toe aan hetzelfde Active Directory forest als de gebruikers waarvan u de wacht woorden die u wilt valideren.
2. Installeer de [meest recente versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) op de server die in de voor gaande stap is geïdentificeerd. Als u Azure AD Connect al hebt uitgevoerd, controleert u of de versie 1.1.750.0 of hoger is.

    >[!NOTE]
    >Azure AD Connect versies 1.1.557.0, 1.1.558.0, 1.1.561.0 en 1.1.614.0 hebben een probleem met de synchronisatie van wacht woord-hashes. Als u _niet_ van plan bent om wachtwoord hash synchronisatie te gebruiken in combi natie met Pass-Through-verificatie, lees dan de opmerkingen bij de [Azure AD Connect-release](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#116470).

3. Een of meer extra servers identificeren (waarop Windows Server 2012 R2 of hoger wordt uitgevoerd, met TLS 1,2 ingeschakeld), waar u zelfstandige verificatie agenten kunt uitvoeren. Deze extra servers zijn nodig om te zorgen voor een hoge Beschik baarheid van aanvragen om u aan te melden. Voeg de servers toe aan hetzelfde Active Directory forest als de gebruikers waarvan u de wacht woorden die u wilt valideren.

    >[!IMPORTANT]
    >In productie omgevingen wordt aangeraden dat er mini maal drie verificatie agenten worden uitgevoerd op uw Tenant. Er is een systeem limiet van 40-verificatie agenten per Tenant. En als best practice alle servers met verificatie agenten behandelen als laag 0-systemen (Zie [naslag](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

4. Als er een firewall is tussen uw servers en Azure AD, moet u de volgende items configureren:
   - Zorg ervoor dat verificatie agenten *uitgaande* aanvragen kunnen indienen bij Azure AD via de volgende poorten:

     | Poortnummer | Hoe dat wordt gebruikt |
     | --- | --- |
     | **80** | Hiermee worden de certificaatintrekkingslijsten (Crl's) gedownload tijdens het valideren van het SSL-certificaat |
     | **443** | Hiermee wordt alle uitgaande communicatie met de service verwerkt |
     | **8080** (optioneel) | Verificatie agenten rapporteren hun status elke tien minuten via poort 8080 als poort 443 niet beschikbaar is. Deze status wordt weer gegeven in de Azure AD-Portal. Poort 8080 wordt _niet_ gebruikt voor gebruikers aanmeldingen. |
     
     Als uw firewall regels afdwingt op basis van de oorspronkelijke gebruikers, opent u deze poorten voor verkeer van Windows-services die als een netwerk service worden uitgevoerd.
   - Als uw firewall of Proxy DNS-White List, white list-verbindingen met **\*. msappproxy.net** en **\*. servicebus.Windows.net**. Als dat niet het geval is, verleent u toegang tot de [IP-bereiken van het Azure-Data Center](https://www.microsoft.com/download/details.aspx?id=41653), die wekelijks worden bijgewerkt.
   - Uw verificatie agenten hebben toegang tot **login.Windows.net** en **login.microsoftonline.com** nodig voor de eerste registratie. Open ook uw firewall voor deze Url's.
   - Deblokkeren van de certificaat validatie de volgende Url's: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80**en **www\.Microsoft.com:80**. Omdat deze Url's worden gebruikt voor certificaat validatie met andere micro soft-producten, is het mogelijk dat deze Url's al worden gedeblokkeerd.

## <a name="step-2-enable-the-feature"></a>Stap 2: de functie inschakelen

Schakel Pass-Through-verificatie in via [Azure AD Connect](whatis-hybrid-identity.md).

>[!IMPORTANT]
>U kunt Pass-Through-verificatie inschakelen op de Azure AD Connect primaire of faserings server. Het wordt ten zeerste aanbevolen dat u deze functie inschakelt op de primaire server. Als u in de toekomst een Azure AD Connect staging-server instelt, **moet** u Pass-Through-verificatie blijven kiezen als aanmeldings optie. Als u een andere optie kiest, wordt Pass-Through-verificatie op de Tenant **uitgeschakeld** en wordt de instelling op de primaire server overschreven.

Als u Azure AD Connect voor de eerste keer installeert, kiest u het [aangepaste installatiepad](how-to-connect-install-custom.md). Op de **aanmeldings** pagina van de gebruiker kiest u **Pass-Through-verificatie** als de **methode sign on**. Als de bewerking is voltooid, wordt een Pass-Through-verificatie agent geïnstalleerd op dezelfde server als Azure AD Connect. Daarnaast is de functie voor Pass-Through-verificatie ingeschakeld voor uw Tenant.

![Azure AD Connect: aanmelding van gebruiker](./media/how-to-connect-pta-quick-start/sso3.png)

Als u Azure AD Connect al hebt geïnstalleerd met behulp van de [snelle installatie](how-to-connect-install-express.md) of het [aangepaste installatiepad](how-to-connect-install-custom.md) , selecteert u de taak **gebruiker aanmelden wijzigen** op Azure AD Connect en selecteert u **volgende**. Selecteer vervolgens **Pass-Through-verificatie** als de aanmeldings methode. Als de bewerking is voltooid, wordt een Pass-Through-verificatie agent geïnstalleerd op dezelfde server als Azure AD Connect en wordt de functie ingeschakeld op uw Tenant.

![Azure AD Connect: aanmelding van gebruiker wijzigen](./media/how-to-connect-pta-quick-start/changeusersignin.png)

>[!IMPORTANT]
>Pass-Through-verificatie is een functie op Tenant niveau. Als u deze functie inschakelt, is dit van invloed op de aanmelding voor gebruikers in _alle_ beheerde domeinen in uw Tenant. Als u overschakelt van Active Directory Federation Services (AD FS) naar Pass-Through-verificatie, moet u ten minste 12 uur wachten voordat u de AD FS-infra structuur afsluit. Deze wacht tijd is om ervoor te zorgen dat gebruikers zich tijdens de overgang kunnen aanmelden bij Exchange ActiveSync. Bekijk het gedetailleerde implementatie plan dat [hier](https://aka.ms/adfstoptadpdownload)wordt gepubliceerd voor meer informatie over het migreren van AD FS naar Pass-Through-verificatie.

## <a name="step-3-test-the-feature"></a>Stap 3: de functie testen

Volg deze instructies om te controleren of u Pass-Through-verificatie op de juiste manier hebt ingeschakeld:

1. Meld u aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met de referenties van de globale beheerder voor uw Tenant.
2. Selecteer **Azure Active Directory** in het linkerdeel venster.
3. Selecteer **Azure AD Connect**.
4. Controleer of de functie **Pass-Through-verificatie** wordt weer gegeven als **ingeschakeld**.
5. Selecteer **Pass-Through-verificatie**. In het deel venster **Pass-Through-verificatie** worden de servers weer gegeven waarop uw verificatie agenten zijn geïnstalleerd.

![Azure Active Directory-beheer centrum: Azure AD Connect deel venster](./media/how-to-connect-pta-quick-start/pta7.png)

![Azure Active Directory beheer centrum: Pass Through-verificatie venster](./media/how-to-connect-pta-quick-start/pta8.png)

In deze fase kunnen gebruikers van alle beheerde domeinen in uw Tenant zich aanmelden met behulp van Pass-Through-verificatie. Gebruikers van federatieve domeinen blijven echter wel aanmelden met behulp van AD FS of een andere Federation-provider die u eerder hebt geconfigureerd. Als u een domein converteert van federatieve naar beheerd, starten alle gebruikers van dat domein automatisch aanmelden met behulp van Pass-Through-verificatie. De functie Pass-Through-verificatie is niet van invloed op Cloud gebruikers.

## <a name="step-4-ensure-high-availability"></a>Stap 4: een hoge Beschik baarheid garanderen

Als u Pass-Through-verificatie wilt implementeren in een productie omgeving, moet u extra zelfstandige verificatie agenten installeren. Installeer deze verificatie-agent (s) op server (s) _die niet worden_ uitgevoerd Azure AD Connect. Deze configuratie biedt een hoge Beschik baarheid voor aanmeldings aanvragen van gebruikers.

>[!IMPORTANT]
>In productie omgevingen wordt aangeraden dat er mini maal drie verificatie agenten worden uitgevoerd op uw Tenant. Er is een systeem limiet van 40-verificatie agenten per Tenant. En als best practice alle servers met verificatie agenten behandelen als laag 0-systemen (Zie [naslag](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).

Het installeren van meerdere Pass-Through-verificatie agenten garandeert een hoge Beschik baarheid, maar geen deterministische taak verdeling tussen de verificatie agenten. Als u wilt weten hoeveel verificatie agenten u nodig hebt voor uw Tenant, moet u rekening houden met de piek en de gemiddelde belasting van aanmeldings aanvragen die u verwacht te zien in uw Tenant. Als referentie kan één verificatie agent 300 tot 400 authenticaties per seconde afhandelen op een standaard 4-core CPU, 16 GB RAM-server.

Als u het netwerk verkeer wilt schatten, gebruikt u de volgende richt lijnen:
- Elke aanvraag heeft een Payload grootte van (0,5 K + 1K * num_of_agents) bytes; gegevens van Azure AD naar de verificatie agent. Num_of_agents geeft hier het aantal verificatie agenten aan dat is geregistreerd op uw Tenant.
- Elk antwoord heeft een Payload-grootte van 1 KB aan bytes; gegevens van de verificatie-agent naar Azure AD.

Voor de meeste klanten zijn drie verificatie agenten in totaal voldoende voor hoge Beschik baarheid en capaciteit. U moet verificatie agenten voor uw domein controllers sluiten om de aanmeldings latentie te verbeteren.

Volg deze instructies om de verificatie-agent software te downloaden:

1. Als u de meest recente versie van de verificatie agent (versie 1.5.193.0 of hoger) wilt downloaden, meldt u zich aan bij het [Azure Active Directory-beheer centrum](https://aad.portal.azure.com) met de referenties van de globale beheerder van uw Tenant.
2. Selecteer **Azure Active Directory** in het linkerdeel venster.
3. Selecteer **Azure AD Connect**, selecteer **Pass-Through-verificatie**en selecteer vervolgens **agent downloaden**.
4. Selecteer de knop **voor waarden accepteren & downloaden** .

![Azure Active Directory-beheer centrum: knop Verificatie agent downloaden](./media/how-to-connect-pta-quick-start/pta9.png)

![Azure Active Directory-beheer centrum: deel venster agent downloaden](./media/how-to-connect-pta-quick-start/pta10.png)

>[!NOTE]
>U kunt ook rechtstreeks [de verificatie agent software downloaden](https://aka.ms/getauthagent). Bekijk en accepteer de [Service voorwaarden](https://aka.ms/authagenteula) van de verificatie agent _voordat_ u deze installeert.

Er zijn twee manieren om een zelfstandige verificatie agent te implementeren:

U kunt het eerst interactief doen door gewoon het uitvoer bare bestand van de gedownloade verificatie agent uit te voeren en de globale beheerders referenties van uw Tenant op te geven wanneer u hierom wordt gevraagd.

Ten tweede kunt u een script voor installatie zonder toezicht maken en uitvoeren. Dit is handig wanneer u meerdere verificatie agenten tegelijk wilt implementeren of als u verificatie agenten wilt installeren op Windows-servers waarop geen gebruikers interface is ingeschakeld, of als u geen toegang hebt tot Extern bureaublad. Hier volgen de instructies voor het gebruik van deze methode:

1. Voer de volgende opdracht uit om een verificatie-agent te installeren: `AADConnectAuthAgentSetup.exe REGISTERCONNECTOR="false" /q`.
2. U kunt de verificatie agent met onze service registreren met behulp van Windows Power shell. Maak een Power shell-referentie object `$cred` dat de gebruikers naam en het wacht woord van de globale beheerder voor uw Tenant bevat. Voer de volgende opdracht uit en vervang *\<gebruikers naam\>* en *\<wacht woord\>* :

        $User = "<username>"
        $PlainPassword = '<password>'
        $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $User, $SecurePassword
3. Ga naar **C:\Program Files\Microsoft Azure AD Connect authentication agent** en voer het volgende script uit met behulp van het `$cred`-object dat u hebt gemaakt:

        RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft Azure AD Connect Authentication Agent\Modules\" -moduleName "PassthroughAuthPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature PassthroughAuthentication

>[!IMPORTANT]
>Als er een verificatie agent is geïnstalleerd op een virtuele machine, kunt u de virtuele machine niet klonen om een andere verificatie agent te installeren. Deze methode wordt **niet ondersteund**.

## <a name="step-5-configure-smart-lockout-capability"></a>Stap 5: slimme vergrendelings mogelijkheden configureren

Slimme vergren deling helpt bij het vergren delen van ongeldige Actors die proberen de wacht woorden van uw gebruikers te raden of door middel van brute force-methoden om in te komen. Door slimme vergrendelings instellingen te configureren in azure AD en/of passende vergrendelings instellingen in on-premises Active Directory, kunnen aanvallen worden gefilterd voordat ze Active Directory bereiken. Lees [dit artikel](../authentication/howto-password-smart-lockout.md) voor meer informatie over het configureren van Smart-vergrendelings instellingen voor uw Tenant voor het beveiligen van uw gebruikers accounts.

## <a name="next-steps"></a>Volgende stappen
- [Migratie van AD FS naar Pass-](https://aka.ms/adfstoptadp) through-verificatie: een gedetailleerde hand leiding voor het migreren van AD FS (of andere Federatie technologieën) naar Pass-Through-verificatie.
- [Slimme vergren deling](../authentication/howto-password-smart-lockout.md): informatie over het configureren van de mogelijkheden voor slim vergren delen van uw Tenant voor het beveiligen van gebruikers accounts.
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): informatie over welke scenario's worden ondersteund met de Pass-Through-verificatie en wat niet.
- [Technisch diep gaande](how-to-connect-pta-how-it-works.md): begrijpen hoe de functie Pass-Through-verificatie werkt.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Hier vindt u antwoorden op veelgestelde vragen.
- [Problemen oplossen](tshoot-connect-pass-through-authentication.md): informatie over het oplossen van veelvoorkomende problemen met de functie Pass-Through-verificatie.
- [Grondige beveiliging](how-to-connect-pta-security-deep-dive.md): krijg technische informatie over de functie Pass-Through-verificatie.
- [Naadloze SSO van Azure AD](how-to-connect-sso.md): meer informatie over deze complementaire functie.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): gebruik het Azure Active Directory-forum om nieuwe functie aanvragen te verwerken.
