---
title: Een wachtwoordloze verificatie-implementatie plannen met Azure AD
description: Meer informatie over het plannen en implementeren van een Azure Active Directory-wachtwoordloze verificatie-implementatie
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: iainfoulds
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 145439ebedd2ddf7c081339146010c66f37fe1af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136531"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Een wachtwoordloze verificatie-implementatie plannen in Azure Active Directory

> [!NOTE]
> Als u een offlineversie van dit implementatieplan wilt maken, gebruikt u de functionaliteit Afdrukken naar PDF van uw browser.

De meeste cyberaanvallen beginnen met een gecompromitteerde gebruikersnaam en wachtwoord. Organisaties proberen de dreiging tegen te gaan door gebruikers te verplichten een van de volgende benaderingen te gebruiken:

- Lange wachtwoorden
- Complexe wachtwoorden
- Frequente wachtwoordwijzigingen
- Meervoudige verificatie (MFA)

Uit onderzoek van Microsoft [blijkt](https://aka.ms/passwordguidance) dat deze inspanningen gebruikers irriteren en de ondersteuningskosten opdrijven. Voor meer informatie, zie [Uw Pa $ $word maakt niet uit](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

### <a name="benefits-of-passwordless-authentication"></a>Voordelen van wachtwoordloze verificatie

- **Verhoogde beveiliging.** Verminder het risico op phishing- en wachtwoordsprayaanvallen door wachtwoorden te verwijderen als aanvalsoppervlak.
-  **Betere gebruikerservaring.** Geef gebruikers een handige manier om overal toegang te krijgen tot gegevens. Bied eenvoudig toegang tot toepassingen en services zoals Outlook, OneDrive of Office terwijl u mobiel bent.
-  **Robuuste inzichten.** Krijg inzicht in gebruikers die wachtwoordloze activiteiten hebben met robuuste logboekregistratie en controle.

Met wachtwoordloos, het wachtwoord wordt vervangen door iets wat je hebt plus iets wat je bent of iets wat je weet. Windows Hello for Business kan bijvoorbeeld een biometrischgebaar gebruiken, zoals een gezicht of vingerafdruk, of een apparaatspecifieke pincode die niet via een netwerk wordt verzonden.

## <a name="passwordless-authentication-methods"></a>Verificatiemethoden zonder wachtwoord
Microsoft biedt drie wachtwoordloze verificatieopties die betrekking hebben op vele scenario's. Deze methoden kunnen in tandem worden gebruikt:

- [Windows Hello for Business](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) is het beste voor gebruikers op hun speciale Windows-computers.
- Beveiligingssleutelaanmelding met [FIDO2-beveiligingssleutels](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) is vooral handig voor gebruikers die zich aanmelden bij gedeelde machines zoals kiosken, in situaties waarin het gebruik van telefoons beperkt is en voor zeer bevoorrechte identiteiten.
- Aanmelden via de telefoon met de [Microsoft Authenticator-app](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) is handig voor het bieden van een wachtwoordloze optie aan gebruikers met mobiele apparaten. De Authenticator-app verandert elke iOS- of Android-telefoon in een sterke, wachtwoordloze referentie door gebruikers toe te staan zich aan te melden bij elk platform of browser. Gebruikers melden zich aan door een melding op hun telefoon te krijgen, een nummer op het scherm te matchen met het nummer op hun telefoon en vervolgens hun biometrische gegevens of pincode te gebruiken om te bevestigen.

### <a name="passwordless-authentication-scenarios"></a>Verificatiescenario's zonder wachtwoord

De wachtwoordloze verificatiemethoden van Microsoft maken verschillende scenario's mogelijk. Houd rekening met uw organisatorische behoeften, vereisten en de mogelijkheden van elke verificatiemethode om uw verificatiestrategie zonder wachtwoord te selecteren. We raden elke organisatie die Windows 10-apparaten gebruikt aan windows Hello voor Bedrijven te gebruiken. Voeg vervolgens aanmelding via de telefoon toe (met de Microsoft Authenticator-app) of beveiligingssleutels voor extra scenario's.

| Scenario | Telefoonverificatie | Beveiligingssleutels | Windows Hello voor Bedrijven |
| --- | --- | --- | --- |
| **Computerlog aanmelden:** <br> Van toegewezen Windows 10-apparaat | **Nee** | **Ja** <br> Met biometrische, PIN | **Ja**<br>met biometrische herkenning en/of pincode |
| **Computerlog aanmelden:** <br> Van gedeeld Windows 10-apparaat | **Nee** | **Ja** <br> Met biometrische, PIN  | **Nee** |
| **Aanmelding voor web-apps:** <br>vanaf een door de gebruiker bestemde computer | **Ja** | **Ja** <br> Mits eenmalige aanmelding voor apps is ingeschakeld door aanmelding via de computer | **Ja**<br> Mits eenmalige aanmelding voor apps is ingeschakeld door aanmelding via de computer |
| **Aanmelding voor web-apps:** <br> vanaf een mobiel of niet-windows-apparaat | **Ja** | **Nee** | **Nee** |
| **Computerlog aanmelden:** <br> Niet-Windows-computer | **Nee** | **Nee** | **Nee** |

Zie [Een wachtwoordloze methode bepalen](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#deciding-a-passwordless-method)voor informatie over het selecteren van de beste methode voor uw organisatie.

## <a name="prerequisites"></a>Vereisten

Organisaties moeten aan de volgende vereisten voldoen voordat ze met een wachtwoordloze implementatie beginnen:

| Vereiste | Authenticator-app | FIDO2-beveiligingssleutels |
| --- | --- | --- |
| [Gecombineerde registratie voor Azure Multi-factor authenticatie en self-service password reset (SSPR)](howto-registration-mfa-sspr-combined.md) is ingeschakeld (preview-functie) | √ | √ |
| [Gebruikers kunnen Azure Multi-factor-verificatie uitvoeren](howto-mfa-getstarted.md) | √ | √ |
| [Gebruikers hebben zich geregistreerd voor Azure Multi-factor authenticatie en SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Gebruikers hebben hun mobiele apparaten geregistreerd bij Azure Active Directory](../devices/overview.md) | √ |   |
| Windows 10 versie 1809 of hoger met behulp van een ondersteunde browser zoals Microsoft Edge of Mozilla Firefox <br> (versie 67 of hoger). <br> *Microsoft raadt versie 1903 of hoger aan voor native ondersteuning.* |   | √ |
| Compatibele FIDO2-beveiligingssleutels. Zorg ervoor dat u een [door Microsoft getest en geverifieerd](howto-authentication-passwordless-enable.md) FIDO2-beveiligingsapparaat of een ander compatibel FIDO2-beveiligingsapparaat gebruikt. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Voorwaarden voor Windows Hello voor Bedrijven

De vereisten voor Windows Hello zijn sterk afhankelijk van de vraag of u implementeert in een on-premises, hybride of cloudconfiguratie. Zie voor meer informatie de [volledige lijst met vereisten voor Windows Hello for Business.](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Gebruikers registreren hun wachtwoordloze methode als onderdeel van de Azure Multi-factor authenticatieregistratiestroom. Multi-factor authenticatie met een gebruikersnaam en wachtwoord samen met een andere geregistreerde methode kan worden gebruikt als een terugval in het geval ze niet kunnen gebruiken hun telefoon of beveiligingssleutel in sommige scenario's.

### <a name="licensing"></a>Licentieverlening 
Er zijn geen extra kosten voor wachtwoordloze verificatie, hoewel sommige vereisten mogelijk een premium-abonnement vereisen. Voor gedetailleerde functie- en licentiegegevens op de [azure Active Directory-licentiepagina](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Een plan ontwikkelen

Houd rekening met uw bedrijfsbehoeften en de use cases voor elke verificatiemethode. Selecteer vervolgens de methode die het beste bij uw behoeften past.

### <a name="use-cases"></a>Gebruiksvoorbeelden

In de volgende tabel worden de use cases beschreven die tijdens dit project moeten worden geïmplementeerd.

| Onderwerp | Beschrijving |
| --- | --- |
| **Toegang** | Aanmelden zonder wachtwoord is beschikbaar vanaf een zakelijk of persoonlijk apparaat binnen of buiten het bedrijfsnetwerk. |
| **Controleren** | Gebruiksgegevens zijn beschikbaar voor beheerders om in bijna realtime te controleren. <br> Gebruiksgegevens worden minstens om de 29 dagen gedownload naar bedrijfssystemen of siem-tool wordt gebruikt. |
| **Governance** | Levenscyclus van gebruikerstoewijzingen naar de juiste verificatiemethode en bijbehorende groepen wordt gedefinieerd en gecontroleerd. |
| **Beveiliging** | De toegang tot de juiste verificatiemethode wordt beheerd via gebruikers- en groepstoewijzingen. <br> Alleen geautoriseerde gebruikers kunnen zich zonder wachtwoord aanmelden. |
| **Prestaties** | Toegangstoewijzingstijdlijnen worden gedocumenteerd en gecontroleerd. <br> De aanmeldingstijden worden gemeten voor gebruiksgemak. |
| **Gebruikerservaring** | Gebruikers zijn zich bewust van mobiele compatibiliteit. <br> Gebruikers kunnen de Authenticator-app zonder wachtwoord configureren. |
| **Ondersteuning** | Gebruikers zijn zich bewust van hoe ze ondersteuning kunnen vinden voor wachtwoordloze aanmeldingsproblemen. |

### <a name="engage-the-right-stakeholders"></a>Betrek de juiste stakeholders

Wanneer technologieprojecten mislukken, is dit meestal vanwege de oneigenlijke verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden indienst gaat](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) en dat de rollen van belanghebbenden in het project goed worden begrepen.

### <a name="plan-communications"></a>De communicatie plannen

Communicatie is cruciaal voor het succes van elke nieuwe dienst. Communiceer proactief hoe de ervaring van gebruikers zal veranderen, wanneer deze zal veranderen en hoe ondersteuning kan worden opgedaan als ze problemen ervaren.

Uw communicatie met eindgebruikers moet de volgende informatie bevatten:

- [De gecombineerde beveiligingsregistratie-ervaring inschakelen](howto-authentication-passwordless-phone.md)
- [De Microsoft Authenticator-app downloaden](../user-help/user-help-auth-app-download-install.md)
- [Registreren in de Microsoft Authenticator-app](howto-authentication-passwordless-phone.md)
- [Inloggen met uw telefoon](../user-help/user-help-auth-app-sign-in.md)

Microsoft biedt [communicatiesjablonen](https://aka.ms/mfatemplates)voor verificatie via meerdere factoren, SSPR-communicatiesjablonen (Self-Service Password Reset) en [documentatie van eindgebruikers](../user-help/security-info-setup-signin.md) om uw communicatie te helpen opstellen. [communication templates](https://www.microsoft.com/download/details.aspx?id=56768) U gebruikers [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) rechtstreeks naar de registratie sturen door de koppelingen **beveiligingsgegevens** op die pagina te selecteren.

### <a name="plan-to-pilot"></a>Plan om piloot

Wanneer u wachtwoordloze verificatie implementeert, moet u eerst een of meer pilotgroepen inschakelen. U groepen speciaal voor dit doel [maken.](../fundamentals/active-directory-groups-create-azure-portal.md) Voeg de gebruikers die deelnemen aan de pilot toe aan de groepen. Schakel vervolgens nieuwe wachtwoordloze verificatiemethoden in voor de geselecteerde groepen.

Groepen kunnen worden gesynchroniseerd vanuit een on-premises directory of vanuit Azure AD. Zodra u tevreden bent met de resultaten van uw pilot, u de wachtwoordloze verificatie voor alle gebruikers inschakelen.

Zie [Aanbevolen procedures voor een pilot](https://aka.ms/deploymentplans) op de pagina implementatieplannen.

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Wachtwoordloze verificatie plannen met de Microsoft Authenticator-app

De Microsoft Authenticator-app is gratis te downloaden via Google Play of de Apple App Store. [Meer informatie over het downloaden van de Microsoft Authenticator-app](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Laat gebruikers de Microsoft Authenticator-app downloaden. en volg de aanwijzingen om telefoonaanmelden in te schakelen. 

Het verandert elke iOS of Android telefoon in een sterke, wachtwoordloze referentie. Gebruikers melden zich aan bij elk platform of browser door een melding op hun telefoon te krijgen, een nummer op het scherm te matchen met het nummer op hun telefoon en vervolgens biometrie of een pincode te gebruiken om te bevestigen. [Meer informatie over hoe de Microsoft Authenticator-app werkt.](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless#user-using-microsoft-authenticator-for-passwordless-sign-in) 

![inloggen met de Authenticator-app](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technische overwegingen voor de Microsoft Authenticator-app

**AD FS-integratie** - Wanneer een gebruiker de microsoft authenticator-wachtwoordloze referentie inschakelt, wordt de verificatie voor die gebruiker standaard ingesteld op het verzenden van een melding ter goedkeuring. Gebruikers in een hybride tenant worden niet naar ADFS geleid voor aanmelding, tenzij ze 'Uw wachtwoord gebruiken' selecteren. Dit proces omzeilt ook alle on-premises voorwaardelijke toegangsbeleidsregels en doorgeefverificatiestromen. Als er echter een *login_hint* is opgegeven, wordt de gebruiker doorgestuurd naar ADFS en omzeilt de optie om de wachtwoordloze referentie te gebruiken.

**Azure Multi-factor authentication server** - Eindgebruikers die zijn ingeschakeld voor multi-factor authenticatie via de on-premises Azure MFA-server van een organisatie, kunnen één wachtwoordloze aanmeldingsreferentie voor telefoons maken en gebruiken. Als de gebruiker meerdere installaties (5 of meer) van de Microsoft Authenticator probeert te upgraden met de referentie, kan deze wijziging leiden tot een fout.

**Apparaatregistratie** : als u de Authenticator-app wilt gebruiken voor verificatie zonder wachtwoord, moet het apparaat zijn geregistreerd in de Azure AD-tenant en kan het geen gedeeld apparaat zijn. Een apparaat kan slechts in één tenant worden geregistreerd. Deze limiet betekent dat slechts één werk- of schoolaccount wordt ondersteund voor telefonischaanmelden met de Authenticator-app.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Wachtwoordloze verificatie plannen met FIDO2-beveiligingssleutels
Er zijn drie soorten wachtwoordloze aanmeldingsimplementaties beschikbaar met beveiligingssleutels:

-    Azure Active Directory-webapps in een ondersteunde browser
-    Azure Active Directory Joined Windows 10-apparaten
-    Hybride Azure Active Directory Samengevoegde Windows 10-apparaten (voorbeeld)
     -    Biedt toegang tot zowel cloudgebaseerde als on-premises resources. Zie [SSO naar on-premises resources met FIDOP2-sleutels voor](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) meer informatie over toegang tot on-premises bronnen.

U moet **compatibele FIDO2-beveiligingssleutels**inschakelen. Microsoft heeft belangrijke partnerschappen met [FIDO2-sleutelleveranciers](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493)aangekondigd.

**Voor Azure AD-webapps en Azure AD Windows-apparaten:**

-    Windows 10 versie 1809 of hoger met behulp van een ondersteunde browser zoals Microsoft Edge of Mozilla Firefox (versie 67 of hoger). 
-    Windows 10 versie 1809 ondersteunt FIDO2-aanmelding en vereist mogelijk dat software van de FIDO2-sleutelfabrikant moet worden geïmplementeerd. Wij raden u aan versie 1903 of hoger te gebruiken. 

**Voor hybride Azure Active Directory Domain Joined-apparaten:** 
-    Windows 10 Insider build 18945 of hoger
-    Volledig gepatchte domeinservers met Windows Server 2016 of 2019.
-    Nieuwste versie van Azure AD Connect

Zie Aanmelding met [wachtwoordloze beveiligingssleutel inschakelen voor Windows 10-apparaten met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-windows#requirements)voor een volledige lijst met vereisten.


### <a name="security-key-life-cycle"></a>Levenscyclus van beveiligingssleutels

Beveiligingssleutels maken toegang tot uw resources mogelijk en u moet het beheer van deze fysieke apparaten plannen.

1. **Sleutelverdeling:** Plan hoe u sleutels aan uw organisatie indient. U een gecentraliseerd inrichtingsproces hebben of eindgebruikers toestaan fido 2.0-compatibele sleutels aan te schaffen.
1. **Sleutelactivering**: Eindgebruikers moeten de beveiligingssleutel zelf activeren. Eindgebruikers registreren hun beveiligingssleutels bij [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) en schakelen de tweede factor (PIN of biometrische) bij het eerste gebruik in.
1. **Een sleutel uitschakelen:** terwijl de functionaliteit van beveiligingssleutel zich in de voorvertoningsfase bevindt, is er geen manier voor een beheerder om een sleutel uit een gebruikersaccount te verwijderen. De gebruiker moet het verwijderen. Als een sleutel verloren of gestolen is:
   1. Verwijder de gebruiker uit een groep die is ingeschakeld voor verificatie zonder wachtwoord.
   1. Controleer of ze de sleutel als verificatiemethode hebben verwijderd.
   1. Geef een nieuwe sleutel uit. **Sleutelvervanging:** gebruikers kunnen twee beveiligingssleutels tegelijk inschakelen. Controleer bij het vervangen van een beveiligingssleutel of de gebruiker ook de vervangen sleutel heeft verwijderd.

### <a name="enable-windows-10-support"></a>Windows 10-ondersteuning inschakelen

Als u Windows 10-aanmelding inschakelt met FIDO2-beveiligingssleutels, moet de functionaliteit van de referentieprovider in Windows 10 worden inschakelt. Kies een van de volgende opties:

- [Referentieprovider inschakelen met Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Intune-implementatie is de aanbevolen optie.
- [Referentieprovider inschakelen met een inrichtingspakket](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Als Intune-implementatie niet mogelijk is, moeten beheerders op elke machine een pakket implementeren om de functionaliteit van de referentieprovider in te schakelen. De pakketinstallatie kan worden uitgevoerd door een van de volgende opties:
      - Groepsbeleid of configuratiebeheer
      - Lokale installatie op een Windows 10-machine
- [Referentieprovider inschakelen met groepsbeleid](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Alleen ondersteund voor hybride Azure AD-apparaten.

#### <a name="enable-on-premises-integration"></a>On-premises integratie inschakelen

Als u toegang tot on-premises bronnen wilt inschakelen, voert u de stappen uit om [het aanmelden voor een wachtwoordloze beveiligingssleutel in te schakelen bij on-premises bronnen (preview).](howto-authentication-passwordless-security-key-on-premises.md)

> [!IMPORTANT]
> Deze stappen moeten ook worden voltooid voor hybride Azure AD-apparaten om FIDO2-beveiligingssleutels voor Windows 10-aanmelding te gebruiken.

### <a name="register-security-keys"></a>Beveiligingssleutels registreren

Gebruikers moeten hun beveiligingssleutel registreren op elk van hun Azure Active Directory-apparaten met Windows 10.Users must register their security key on each of their Azure Active Directory joined Windows 10 machines.

Zie [Gebruikersregistratie en -beheer van FIDO2-beveiligingssleutels](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)voor meer informatie.


## <a name="plan-auditing-security-and-testing"></a>Controle, beveiliging en testen plannen
Planning voor auditing die voldoet aan uw organisatie- en complianceframeworks is een essentieel onderdeel van uw implementatie.

### <a name="auditing-passwordless"></a>Controleren zonder wachtwoord

Azure AD heeft rapporten die technische en zakelijke inzichten bieden. Laat eigenaren van uw bedrijf en technische toepassing eigenaar worden van deze rapporten en deze gebruiken op basis van de vereisten van uw organisatie.

In de sectie **Verificatiemethoden** in de Azure Active Directory-portal kunnen beheerders instellingen voor wachtwoordloze referenties inschakelen en beheren.

Azure AD voegt vermeldingen toe aan de controlelogboeken wanneer:

- Een beheerder voert wijzigingen aan in de sectie Verificatiemethoden.
- Een gebruiker brengt elke vorm van wijzigingen aan in zijn referenties in Azure Active Directory.

In de volgende tabel vindt u enkele voorbeelden van typische rapportagescenario's:

|   | beheer risico's. | Verhoog de productiviteit | Governance en naleving |
| --- | --- | --- | --- |
| **Rapporttypen** | Authenticatiemethoden- gebruikers die zijn geregistreerd voor gecombineerde beveiligingsregistratie | Verificatiemethoden – gebruikers die zijn geregistreerd voor app-melding | Aanmeldingen: bekijk wie toegang heeft tot de tenant en hoe |
| **Mogelijke acties** | Doelgebruikers nog niet geregistreerd | De adoptie van microsoft authenticator-app of beveiligingssleutels stimuleren | Toegang intrekken of aanvullende beveiligingsbeleidsregels voor beheerders afdwingen |

Azure AD bewaart de **meeste controlegegevens gedurende 30 dagen** en stelt de gegevens beschikbaar via Azure Admin portal of API voor u om te downloaden naar uw analysesystemen. Als u langere retentie, exporteren en consumeren van logboeken in een SIEM-tool zoals [Azure Sentinel,](../../sentinel/connect-azure-active-directory.md)Splunk of Sumo Logic vereist. [Meer informatie over het bekijken van uw toegangs- en gebruiksrapporten](../reports-monitoring/overview-reports.md).

Gebruikers kunnen hun referenties registreren en [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)beheren door te navigeren naar . Deze koppeling leidt gebruikers naar de ervaring voor het beheer van de gebruikersreferenties die is ingeschakeld via de gecombineerde SSPR/Multi-factor authenticatieregistratie-ervaring. Azure AD registreert registratie van FIDO2-beveiligingsapparaten en wijzigingen in verificatiemethoden door gebruikers.

### <a name="plan-security"></a>Beveiliging plannen
Als onderdeel van dit implementatieplan raadt Microsoft aan om wachtwoordloze verificatie in te schakelen voor alle bevoegde beheerdersaccounts.

Wanneer gebruikers het account in- of uitschakelen op een beveiligingssleutel of de tweede factor voor de beveiligingssleutel op hun Windows 10-machines opnieuw instellen, wordt een vermelding toegevoegd aan het beveiligingslogboek en worden ze onder de volgende gebeurtenis-id's toegevoegd: *4670* en *5382*.

### <a name="plan-testing"></a>Testen plannen

In elke fase van uw implementatie terwijl u scenario's en adoptie test, moet u ervoor zorgen dat de resultaten zijn zoals verwacht.

#### <a name="testing-the-microsoft-authenticator-app"></a>De Microsoft Authenticator-app testen

Hieronder volgen voorbeeldtestcases voor wachtwoordloze verificatie met de Microsoft Authenticator-app:

| Scenario | Verwachte resultaten |
| --- | --- |
| Gebruiker kan Microsoft Authenticator-app registreren | Gebruiker kan app registreren vanuit aka.ms/mysecurityinfo |
| Gebruiker kan aanmelden via telefoon inschakelen | Telefoonaanmelding geconfigureerd voor werkaccount |
| Gebruiker heeft toegang tot een app met telefonische aanmelding | Gebruiker gaat door telefoon aanmeldingsstroom en bereikt de toepassing. |
| Registratie van aanmeldingsformulieren voor telefoons testen door microsoft authenticator wachtwoordloze aanmelding uit te schakelen binnen het scherm Verificatiemethoden in de Azure Active Directory-portal | Voorheen ingeschakeldgebruikers die geen wachtwoordloze aanmelding van Microsoft Authenticator kunnen gebruiken. |
| Aanmelding voor telefoon verwijderen uit de Microsoft Authenticator-app | Werkaccount niet meer beschikbaar op Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Beveiligingssleutels testen

Hieronder volgen voorbeeldtestcases voor wachtwoordloze verificatie met beveiligingssleutels.

**Wachtwoordloze FIDO-aanmelding bij Azure Active Directory Joined Windows 10-apparaten**

| Scenario | Verwachte resultaten |
| --- | --- |
| De gebruiker kan FIDO2-apparaat registreren (1809) | Gebruiker kan FIDO2-apparaat registreren via Instellingen > Accounts > aanmeldingsopties > beveiligingssleutel |
| De gebruiker kan fido2-apparaat opnieuw instellen (1809) | Gebruiker kan FIDO2-apparaat resetten met behulp van software van de fabrikant |
| De gebruiker kan zich aanmelden met FIDO2-apparaat (1809) | De gebruiker kan beveiligingssleutel selecteren in het aanmeldingsvenster en zich met succes aanmelden. |
| De gebruiker kan FIDO2-apparaat registreren (1903) | Gebruiker kan FIDO2-apparaat registreren bij Instellingen > Accounts > aanmeldingsopties > beveiligingssleutel |
| De gebruiker kan fido2-apparaat resetten (1903) | Gebruiker kan FIDO2-apparaat opnieuw instellen bij Instellingen > Accounts > aanmeldingsopties > beveiligingssleutel |
| De gebruiker kan zich aanmelden met FIDO2-apparaat (1903) | De gebruiker kan beveiligingssleutel selecteren in het aanmeldingsvenster en zich met succes aanmelden. |

**Wachtwoordloze FIDO-aanmelding bij Azure AD-webapps**

| Scenario | Verwachte resultaten |
| --- | --- |
| De gebruiker kan FIDO2-apparaat registreren op aka.ms/mysecurityinfo met Microsoft Edge | Registratie moet slagen |
| De gebruiker kan FIDO2-apparaat registreren op aka.ms/mysecurityinfo met Firefox | Registratie moet slagen |
| De gebruiker kan zich online aanmelden bij OneDrive met behulp van FIDO2-apparaat met Microsoft Edge | Aanmelden moet lukken |
| De gebruiker kan zich online aanmelden bij OneDrive met behulp van FIDO2-apparaat met Firefox | Aanmelden moet lukken |
| Fido2-apparaatregistratie opnieuw terugtypen testen door FIDO2-beveiligingssleutels uit te schakelen in het venster Verificatiemethode in de Azure Active Directory-portal | Gebruikers wordt gevraagd zich aan te melden met hun beveiligingssleutel. Gebruikers loggen met succes in en er wordt een fout weergegeven: "Uw bedrijfsbeleid vereist dat u een andere methode gebruikt om u aan te melden". Gebruikers moeten dan in staat zijn om een andere methode te selecteren en met succes aanmelden. Sluit het venster en meld u opnieuw aan om te controleren of ze niet hetzelfde foutbericht zien. |

### <a name="plan-for-rollback"></a>Plannen voor terugdraaien

Hoewel wachtwoordloze verificatie een lichtgewicht functie is met minimale impact op eindgebruikers, kan het nodig zijn om terug te draaien.

Als u zich aanmeldt bij de Azure Active Directory-portal, moet de beheerder zich aanmelden bij de Azure Active Directory-portal, de gewenste sterke verificatiemethoden selecteren en de optie inschakelen wijzigen in **Nee**. Dit proces schakelt de wachtwoordloze functionaliteit voor alle gebruikers uit.

Gebruikers die fido2-beveiligingsapparaten al hebben geregistreerd, wordt gevraagd het beveiligingsapparaat te gebruiken bij hun volgende aanmelding en ziet vervolgens de volgende fout:

![kies een andere manier om je aan te melden](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Wachtwoordloze verificatie implementeren en oplossen

Volg hieronder de stappen die zijn uitgelijnd op de door u gekozen methode.

### <a name="required-administrative-roles"></a>Vereiste administratieve rollen

| Azure AD-rol | Beschrijving |
| --- | --- |
| Globale beheerder|Minst bevoorrechte rol in staat om gecombineerde registratie-ervaring te implementeren. |
| Verificatiebeheerder | Minst bevoorrechte rol die verificatiemethoden kan implementeren en beheren. |
| Gebruiker | De minst bevoorrechte rol om de Authenticator-app op het apparaat te configureren of om een beveiligingssleutelapparaat in te schrijven voor de aanmelding op het web of Windows 10. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Aanmelden voor telefoons implementeren met de Microsoft Authenticator-app

Volg de stappen in het artikel, [Wachtwoordloze aanmelding inschakelen met de Microsoft Authenticator-app](howto-authentication-passwordless-phone.md) om de Microsoft Authenticator-app in te schakelen als een wachtwoordloze verificatiemethode in uw organisatie.

### <a name="deploy-fido2-security-key-sign-in"></a>Fido2-aanmeldingscode implementeren

Volg de stappen in het artikel, Schakel het aanmelden voor [de wachtwoordloze beveiligingssleutel voor Azure AD in](howto-authentication-passwordless-security-key.md) om FIDO2-beveiligingssleutels in te schakelen als verificatiemethoden zonder wachtwoord.

### <a name="troubleshoot-phone-sign-in"></a>Problemen met de aanmelding voor telefoon oplossen

| Scenario | Oplossing |
| --- | --- |
| Gebruiker kan geen gecombineerde registratie uitvoeren. | Zorg ervoor dat [gecombineerde registratie](concept-registration-mfa-sspr-combined.md) is ingeschakeld. |
| De gebruiker kan de app voor een aanmelding voor telefoons niet inschakelen. | Zorg ervoor dat de gebruiker in het bereik is voor implementatie. |
| Gebruiker is niet in het bereik voor wachtwoordloze authenticatie, maar wordt gepresenteerd met wachtwoordloze aanmeldingsoptie, die ze niet kunnen voltooien. | Dit scenario treedt op wanneer de gebruiker telefonischaanmelden in de toepassing heeft ingeschakeld voordat het beleid wordt gemaakt. <br> *Aanmelden inschakelen:* Voeg de gebruiker toe aan het bereik van gebruikers die zijn ingeschakeld voor een wachtwoordloze aanmelding. <br> *Als u aanmelding wilt blokkeren:* laat de gebruiker zijn of haar referentie uit die toepassing verwijderen. |

### <a name="troubleshoot-security-key-sign-in"></a>Het oplossen van een probleem met de aanmelding van beveiligingssleutelen

| Scenario | Oplossing |
| --- | --- |
| De gebruiker kan geen gecombineerde registratie uitvoeren. | Zorg ervoor dat [gecombineerde registratie](concept-registration-mfa-sspr-combined.md) is ingeschakeld. |
| De gebruiker kan geen beveiligingssleutel toevoegen aan zijn [beveiligingsinstellingen.](https://aka.ms/mysecurityinfo) | Controleer of [beveiligingssleutels](howto-authentication-passwordless-security-key.md) zijn ingeschakeld. |
| De gebruiker kan geen beveiligingssleutel toevoegen aan aanmeldingsopties voor Windows 10. | [Ervoor zorgen dat beveiligingssleutels voor Windows zich aanmelden](howto-authentication-passwordless-enable.md) |
| **Foutbericht**: We hebben ontdekt dat deze browser of besturingssysteem geen FIDO2-beveiligingssleutels ondersteunt. | Wachtwoordloze FIDO2-beveiligingsapparaten kunnen alleen worden geregistreerd in ondersteunde browsers (Microsoft Edge, Firefox versie 67) op Windows 10 versie 1809 of hoger. |
| **Foutbericht:** voor uw bedrijfsbeleid moet u een andere methode gebruiken om u aan te melden. | Onzeker beveiligingssleutels zijn ingeschakeld in de tenant. |
| Gebruiker kan mijn beveiligingssleutel niet beheren op Windows 10 versie 1809 | Versie 1809 vereist dat u de beveiligingssleutelbeheersoftware gebruikt die wordt geleverd door de FIDO2-sleutelleverancier. Neem contact op met de leverancier voor ondersteuning. |
| Ik denk dat mijn FIDO2-beveiligingssleutel defect kan zijn, hoe kan ik deze testen. | Navigeer [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/)naar, voer referenties in voor een testaccount, sluit **+** de verdachte beveiligingssleutel aan, selecteer de knop rechtsboven in het scherm, klik op maken en doorzoek het creatieproces. Als dit scenario mislukt, kan het zijn dat uw apparaat defect is. |

## <a name="next-steps"></a>Volgende stappen

- [Wachtwoordloze beveiligingssleutels inschakelen voor aanmelden voor Azure AD](howto-authentication-passwordless-security-key.md)
- [Aanmelden zonder wachtwoord inschakelen met de Microsoft Authenticator-app](howto-authentication-passwordless-phone.md)
- [Meer informatie over het gebruik van verificatiemethoden & inzichten](howto-authentication-methods-usage-insights.md)

