---
title: Een implementatie zonder wacht woord authenticatie uitvoeren met Azure AD
description: Een Azure Active Directory implementatie met een wacht woordloze verificatie volt ooien
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f07e1dd8e9252d2b6e00b85a47ba2e19f8bd8
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73603456"
---
# <a name="complete-a-passwordless-authentication-deployment"></a>Een authenticatie-implementatie met een wacht woord volt ooien

Een meerderheid van Cyber aanvallen begint met een gebruikers naam en wacht woord die worden gestolen door iemand in een organisatie. 

Organisaties proberen de dreiging te telleren door te vereisen dat gebruikers het volgende gebruiken:

- Lange wacht woorden
- Complexe wacht woorden
- Frequente wachtwoord wijzigingen
- Meervoudige verificatie (MFA)

Het onderzoek van micro soft [laat zien](https://aka.ms/passwordguidance) dat deze inspanningen gebruikers ergeren en de ondersteunings kosten ondersteunen. Zie voor meer informatie [uw PA $ $Word niet van belang](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)is.

Implementatie van wacht woordloze verificatie biedt de volgende voor delen:

- Verbeterde beveiliging. Verminder het risico op phishing-en wacht woord-aanvallen door wacht woorden te verwijderen als een kwets baarheid.
- Betere gebruikers ervaring. Geef gebruikers een handige manier om vanaf elke locatie toegang te krijgen tot gegevens en biedt eenvoudige toegang tot Outlook, OneDrive, Office en meer terwijl mobiel.
- Robuuste inzichten. Krijg inzicht in de activiteit van gebruikers met een wacht woord zonder robuuste logboek registratie en controle.

Het wacht woord wordt vervangen door iets wat u hebt en wat u al weet. Een voor beeld: in Windows hello voor bedrijven wordt een biometrische penbeweging, zoals een gezicht of vinger afdruk, of een apparaatspecifieke pincode die niet via een netwerk wordt verzonden.

Micro soft biedt drie verificatie opties met een wacht woord voor veel scenario's. Deze methoden kunnen samen worden gebruikt. 

- [Windows hello voor bedrijven](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) is het meest geschikt voor gebruikers op hun specifieke Windows-computers.
- De beveiligings sleutel aanmelden met [FIDO2-beveiligings sleutels](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) is vooral nuttig voor gebruikers die zich aanmelden bij gedeelde computers als kiosken, situaties waar het gebruik van telefoons is beperkt en voor identiteiten met een hoge bevoegdheid.
- Aanmelden met de Microsoft Authenticator- [app](https://docs.microsoft.com/azure/security/fundamentals/ad-passwordless) is handig voor het bieden van een wacht woordloze optie voor gebruikers met mobiele apparaten. Hiermee wordt een wille keurige iOS-of Android-telefoon omgezet in een sterke, wacht woordloze referentie door gebruikers toe te staan zich aan te melden bij een platform of browser. Gebruikers melden zich aan met een melding op hun telefoon, die overeenkomt met een nummer dat op het scherm wordt weer gegeven op de telefoon en vervolgens met behulp van de biometrische gegevens of de pincode.

## <a name="compare-passwordless-authentication-methods"></a>Authenticatie methoden met een wacht woord vergelijken

Met de verificatie methoden met een wacht woord van micro soft kunnen verschillende scenario's worden ingeschakeld. Houd rekening met de behoeften van uw organisatie, vereisten en de mogelijkheden van elke verificatie methode om uw verificatie strategie met een wacht woord te selecteren. U wordt aangeraden om voor elke organisatie met Windows 10-apparaten Windows hello voor bedrijven te gebruiken. Voeg vervolgens het aanmelden van de telefoon toe (met de Microsoft Authenticator-app) of de beveiligings sleutels voor aanvullende scenario's.

### <a name="passwordless-authentication-scenarios"></a>Verificatie scenario's met een wacht woord

| Scenario | Telefoon verificatie | Beveiligings sleutels | Windows Hello voor Bedrijven |
| --- | --- | --- | --- |
| **Aanmelden bij computer**: <br> Van toegewezen Windows 10-apparaat | **Nee** | **Ja** <br> Met biometrische, pincode | **Ja**<br>met biometrische herkenning en pincode |
| **Aanmelden bij computer**: <br> Van gedeeld Windows 10-apparaat | **Nee** | **Ja** <br> Met biometrische, pincode  | **Nee** |
| **Aanmelden voor web-app**: <br>van een door de gebruiker toegewezen computer | **Ja** | **Ja** <br> Eenmalige aanmelding bij apps is ingeschakeld door aanmelden bij de computer | **Ja**<br> Eenmalige aanmelding bij apps is ingeschakeld door aanmelden bij de computer |
| **Aanmelden voor web-app**: <br> vanaf een mobiel of niet-Windows-apparaat | **Ja** | **Nee** | **Nee** |
| **Aanmelden bij computer**: <br> Niet-Windows-computer | **Nee** | **Nee** | **Nee** |

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technische overwegingen voor de Microsoft Authenticator-app

**Integratie van AD FS** : wanneer een gebruiker de Microsoft Authenticator wacht woord-referentie voor de gebruiker inschakelt, wordt de verificatie van de gebruikers standaard ingesteld op het verzenden van een melding voor goed keuring. Gebruikers in een Hybrid-Tenant kunnen niet worden doorgestuurd naar ADFS om zich aan te melden, tenzij u in plaats daarvan uw wacht woord gebruiken selecteert. Dit proces omzeilt ook alle on-premises beleids regels voor voorwaardelijke toegang en Pass Through-verificatie stromen. Als er echter een login_hint is opgegeven, wordt de gebruiker doorgestuurd naar ADFS en wordt de optie voor het gebruik van de referenties zonder wacht woord omzeild.

**Azure MFA-server** : eind gebruikers die zijn ingeschakeld voor MFA via een on-premises Azure MFA-server van een organisatie kunnen nog steeds een eenmalige aanmeldings referentie voor een wacht woord zonder gebruikers maken en gebruiken. Als de gebruiker meerdere installaties (5 +) van de Microsoft Authenticator met de referentie probeert bij te werken, kan deze wijziging een fout veroorzaken.

**Apparaatregistratie** : voor het gebruik van de verificator-app voor verificatie met een wacht woord moet het apparaat zijn geregistreerd in de Azure AD-Tenant en kan het geen gedeeld apparaat zijn. Een apparaat kan alleen in één Tenant worden geregistreerd. Deze limiet betekent dat er slechts één werk-of school account wordt ondersteund voor telefonisch aanmelden met behulp van de verificator-app.

## <a name="prerequisites"></a>Vereisten

Organisaties moeten voldoen aan de volgende vereisten voordat een implementatie met een wacht woord wordt gestart.

| Vereiste | Authenticator-app | FIDO2-beveiligings sleutels |
| --- | --- | --- |
| [Gecombineerde registratie voor Azure MFA en self-service voor wachtwoord herstel (SSPR)](howto-registration-mfa-sspr-combined.md) is ingeschakeld (preview-functie) | √ | √ |
| [Gebruikers kunnen Azure MFA uitvoeren](howto-mfa-getstarted.md) | √ | √ |
| [Gebruikers zijn geregistreerd voor Azure MFA en SSPR](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Gebruikers hebben hun mobiele apparaten bij Azure Active Directory geregistreerd](../devices/overview.md) | √ |   |
| Windows 10 versie 1809 of hoger met een ondersteunde browser zoals micro soft Edge of Mozilla Firefox <br> (versie 67 of hoger). <br> *Micro soft raadt versie 1903 of hoger aan voor systeem eigen ondersteuning*. |   | √ |
| Compatibele FIDO2-beveiligings sleutels. Zorg ervoor dat u een door [micro soft getest en gecontroleerd](howto-authentication-passwordless-enable.md) FIDO2 Security-apparaat of een ander compatibel FIDO2-beveiligings apparaat gebruikt. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Vereisten voor Windows hello voor bedrijven

De vereisten voor Windows hello zijn zeer afhankelijk van of u implementeert in een on-premises, hybride of Cloud configuratie. Zie voor meer informatie de [volledige lijst met vereisten voor Windows hello voor bedrijven](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Gebruikers registreren hun eigen methode als onderdeel van de Azure MFA-registratie stroom. Multi-factor Authentication met een gebruikers naam en wacht woord samen met een andere geregistreerde methode kan worden gebruikt als terugval voor het geval ze hun telefoon of beveiligings sleutel in sommige scenario's niet kunnen gebruiken.

### <a name="security-key-lifecycle"></a>Levens cyclus van beveiligings sleutels

Met beveiligings sleutels kunt u toegang tot uw resources geven en moet u het beheer van deze fysieke apparaten plannen.

1. Sleutel distributie: plan hoe u sleutels inricht in uw organisatie. Mogelijk hebt u een gecentraliseerd inrichtings proces of toestaan dat eind gebruikers FIDO 2,0-compatibele sleutels aanschaffen.
1. Sleutel activering: eind gebruikers moeten de beveiligings sleutel zelf activeren. Eind gebruikers registreren hun beveiligings sleutels op [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) en scha kelen de tweede factor (pincode of biometrische) in voor het eerste gebruik.
1. Uitschakelen van een sleutel: Hoewel de functionaliteit van de beveiligings sleutel zich in de preview-fase bevindt, is er geen manier om een sleutel van een gebruikers account te verwijderen. De gebruiker moet het verwijderen. Als een sleutel verloren of gestolen is:
   1. Verwijder de gebruiker uit een groep die is ingeschakeld voor verificatie zonder wacht woord.
   1. Controleer of de sleutel is verwijderd als verificatie methode.
   1. Geef een nieuwe sleutel op.
1. Sleutel vervanging: gebruikers kunnen twee beveiligings sleutels tegelijk inschakelen. Wanneer u een beveiligings sleutel vervangt, moet u ervoor zorgen dat de gebruiker ook de sleutel heeft verwijderd die wordt vervangen.

### <a name="enable-windows-10-support"></a>Ondersteuning voor Windows 10 inschakelen

Voor het inschakelen van Windows 10 sign in met behulp van FIDO2-beveiligings sleutels moet de functionaliteit van de referentie provider in Windows 10 worden ingeschakeld. Schakel deze op een van de volgende twee manieren in:

- [Referentie provider inschakelen met intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - InTune-implementatie is de aanbevolen optie.
- [Referentie provider met een inrichtings pakket inschakelen](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Als de intune-implementatie niet mogelijk is, moeten beheerders een pakket op elke computer implementeren om de functionaliteit van de referentie provider in te scha kelen. De installatie van het pakket kan worden uitgevoerd met een van de volgende opties:
      - Groepsbeleid of System Center Configuration Manager (SCCM)
      - Lokale installatie op een Windows 10-computer

### <a name="register-security-keys"></a>Beveiligings sleutels registreren

Gebruikers moeten hun beveiligings sleutel registreren op elk van hun Azure Active Directory gekoppelde Windows 10-computers.

Zie [gebruikers registratie en het beheer van FIDO2-beveiligings sleutels](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)voor meer informatie.

### <a name="licensing-for-passwordless-authentication"></a>Licenties voor verificatie met een wacht woord

Er zijn geen extra kosten verbonden aan authenticatie zonder wacht woord, hoewel voor sommige vereisten mogelijk een Premium-abonnement nodig is. Zie de gedetailleerde informatie over functies en licenties op de [pagina Azure Active Directory licentie verlening](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="develop-a-plan"></a>Een plan ontwikkelen

Houd rekening met de behoeften van uw bedrijf en de use cases voor elke verificatie methode. Selecteer vervolgens de methode die het beste bij uw behoeften past.

### <a name="use-cases"></a>Gebruiksvoorbeelden

De volgende tabel bevat een overzicht van de use cases die tijdens dit project moeten worden geïmplementeerd.

| Onderwerp | Beschrijving |
| --- | --- |
| **Toegang** | Aanmelding zonder wacht woord is beschikbaar vanaf een zakelijk of persoonlijk apparaat binnen of buiten het bedrijfs netwerk. |
| **Controle** | Gebruiks gegevens zijn beschikbaar voor beheerders die bijna in realtime kunnen worden gecontroleerd. <br> Gebruiks gegevens worden minstens elke 29 dagen naar bedrijfs systemen gedownload, of het hulp programma SIEM wordt gebruikt. |
| **Beheer** | De levens cyclus van gebruikers toewijzingen aan de juiste verificatie methode en de bijbehorende groepen wordt gedefinieerd en bewaakt. |
| **Beveiliging** | Toegang tot de juiste verificatie methode wordt geregeld via gebruikers-en groeps toewijzingen. <br> Alleen geautoriseerde gebruikers kunnen aanmelden zonder wacht woord gebruiken. |
| **Prestaties** | De tijd lijnen voor het door geven van toegangs toewijzingen worden gedocumenteerd en gecontroleerd. <br> Aanmeldings tijden worden gemeten voor gebruiks gemak. |
| **Gebruikers ervaring** | Gebruikers zijn op de hoogte van Mobile-compatibiliteit. <br> Gebruikers kunnen het aanmelden met een wacht woord voor de verificator-app configureren. |
| **Ondersteuning** | Gebruikers weten hoe ze ondersteuning kunnen vinden voor aanmeld problemen met een wacht woord. |

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, is dit doorgaans het gevolg van niet-overeenkomende verwachtingen wat betreft impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet [u ervoor zorgen dat u de juiste belanghebbenden gebruikt](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) en dat de rol van belanghebbenden in het project goed zijn begrepen.

### <a name="organization-communications"></a>Organisatie communicatie

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Proactief communiceren hoe de ervaring van gebruikers wordt gewijzigd, wanneer deze wordt gewijzigd en hoe u ondersteuning krijgt als er problemen optreden.

Uw mede delingen aan eind gebruikers moeten de volgende informatie bevatten:

- [De ervaring voor het registreren van gecombineerde beveiliging inschakelen](howto-authentication-passwordless-phone.md)
- [De Microsoft Authenticator-app downloaden](../user-help/user-help-auth-app-download-install.md)
- [Registreren in de Microsoft Authenticator-app](howto-authentication-passwordless-phone.md)
- [Aanmelden met uw telefoon](../user-help/user-help-auth-app-sign-in.md)

Micro soft biedt MFA- [communicatie sjablonen](https://aka.ms/mfatemplates), self-service voor het opnieuw instellen van wacht woorden ( [SSPR) en](https://www.microsoft.com/download/details.aspx?id=56768) [documentatie voor eind gebruikers](../user-help/security-info-setup-signin.md) om uw communicatie te ontwerpen. U kunt gebruikers naar [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) verzenden om rechtstreeks te registreren door de koppelingen voor beveiligings gegevens op die pagina te selecteren.

### <a name="testing-passwordless"></a>Wacht woord wordt getest

Controleer in elke fase van de implementatie of u de resultaten wilt testen.

#### <a name="testing-the-microsoft-authenticator-app"></a>De Microsoft Authenticator-app testen

Hieronder ziet u voor beelden van test cases voor verificatie zonder wacht woord met de Microsoft Authenticator-app

| Scenario | Verwachte resultaten |
| --- | --- |
| Gebruiker kan Microsoft Authenticator-app registreren | Gebruiker kan de app registreren bij aka.ms/mysecurityinfo |
| Gebruiker kan aanmelden via telefoon inschakelen | Telefoon aanmelding geconfigureerd voor werk account |
| Gebruiker heeft toegang tot een app met aanmelden op telefoon | De gebruiker verloopt via de aanmeldings stroom van de telefoon en bereikt de aangewezen toepassing. |
| Test het terugdraaien van de registratie van de telefoon en schakel Microsoft Authenticator aanmelding zonder wacht woord in het scherm verificatie methoden in de Azure Active Directory Portal | Eerder ingeschakelde gebruikers kunnen zich niet aanmelden met een wacht woord zonder tussen Microsoft Authenticator. |
| Aanmelding voor telefoon verwijderen uit Microsoft Authenticator-app | Het werk account is niet meer beschikbaar op Microsoft Authenticator |

#### <a name="testing-security-keys"></a>Beveiligings sleutels testen

Hieronder ziet u voor beelden van test cases voor verificatie zonder wacht woord met beveiligings sleutels.

**Wacht woord FIDO Meld u aan bij Azure Active Directory gekoppelde Windows 10-apparaten**

| Scenario | Verwachte resultaten |
| --- | --- |
| De gebruiker kan een FIDO2-apparaat registreren (1809) | De gebruiker kan een FIDO2-apparaat registreren met behulp van bij instellingen > Accounts > aanmeldings opties > beveiligings sleutel |
| De gebruiker kan het FIDO2-apparaat opnieuw instellen (1809) | Gebruiker kan FIDO2-apparaat opnieuw instellen met behulp van software van de fabrikant |
| De gebruiker kan zich aanmelden met FIDO2-apparaat (1809) | De gebruiker kan de beveiligings sleutel selecteren in het aanmeld venster en zich aanmelden. |
| De gebruiker kan een FIDO2-apparaat registreren (1903) | Gebruiker kan FIDO2-apparaat registreren bij instellingen > Accounts > aanmeldings opties > beveiligings sleutel |
| De gebruiker kan het FIDO2-apparaat opnieuw instellen (1903) | Gebruiker kan FIDO2-apparaat opnieuw instellen bij instellingen > Accounts > aanmeldings opties > beveiligings sleutel |
| De gebruiker kan zich aanmelden met FIDO2-apparaat (1903) | De gebruiker kan de beveiligings sleutel selecteren in het aanmeld venster en zich aanmelden. |

**Wacht woord-FIDO aanmelden bij Azure AD web apps**

| Scenario | Verwachte resultaten |
| --- | --- |
| De gebruiker kan FIDO2-apparaat registreren bij aka.ms/mysecurityinfo met behulp van micro soft Edge | Registratie moet slagen |
| De gebruiker kan FIDO2-apparaat registreren bij aka.ms/mysecurityinfo met behulp van Firefox | Registratie moet slagen |
| De gebruiker kan zich met behulp van micro soft Edge aanmelden bij OneDrive online met behulp van een FIDO2-apparaat | Aanmelden moet slagen |
| De gebruiker kan zich met behulp van Firefox aanmelden bij OneDrive online met FIDO2-apparaat | Aanmelden moet slagen |
| Het terugdraaien van FIDO2-apparaatregistratie testen door FIDO2-beveiligings sleutels uit te scha kelen op de Blade authenticatie methoden in de Azure Active Directory Portal | Gebruikers wordt gevraagd zich aan te melden met behulp van hun beveiligings sleutel, ze worden geregistreerd bij en er wordt een fout weer gegeven: ' uw bedrijfs beleid vereist dat u een andere methode gebruikt om u aan te melden '. Gebruikers moeten dan een andere methode kunnen selecteren en zich aanmelden. Sluit het venster en meld u opnieuw aan om te controleren of deze niet hetzelfde fout bericht weer geven. |

### <a name="auditing-passwordless"></a>Wacht woorden controleren

Azure AD bevat rapporten die technische en zakelijke inzichten bieden. Laat uw eigen aren van zakelijke en technische toepassingen uitgaan van de eigendom van en deze rapporten gebruiken op basis van de vereisten van uw organisatie.

In de sectie verificatie methoden in de Azure Active Directory-portal kunnen beheerders instellingen voor wacht woordloze referenties inschakelen en beheren.

Azure AD voegt vermeldingen aan de audit Logboeken toe wanneer:

- Een beheerder brengt wijzigingen aan in de sectie verificatie methoden.
- Een gebruiker brengt een wijziging aan in hun referenties in Azure Active Directory.

De onderstaande tabel bevat enkele voor beelden van typische rapportage scenario's.

|   | beheer risico's. | Productiviteit verhogen | Governance en naleving |
| --- | --- | --- | --- |
| **Rapport typen** | Verificatie methoden: gebruikers die zijn geregistreerd voor de gecombineerde beveiligings registratie | Verificatie methoden – gebruikers die zijn geregistreerd voor app-melding | Aanmeldingen: controleren wie toegang heeft tot de Tenant en hoe |
| **Mogelijke acties** | De doel gebruikers zijn nog niet geregistreerd | Acceptatie van Microsoft Authenticator-app of-beveiligings sleutels | Toegang intrekken of extra beveiligings beleid afdwingen voor beheerders |

**Azure AD behoudt de meeste controle gegevens gedurende 30 dagen** en maakt de gegevens beschikbaar via de Azure-beheer portal of-API die u kunt downloaden naar uw analyse systemen. Als uw organisatie een langere retentie vereist, moeten de logboeken worden geëxporteerd en gebruikt in een SIEM-hulp programma zoals [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk of Sumo Logic. [Meer informatie over het weer geven van uw toegangs-en gebruiks rapporten](../reports-monitoring/overview-reports.md).

Gebruikers kunnen hun referenties registreren en beheren door te navigeren naar [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). Met deze koppeling worden gebruikers omgeleid naar de beheer ervaring voor eind gebruikers die is ingeschakeld via de gecombineerde registratie-ervaring voor SSPR/MFA. Alle registraties van FIDO2-beveiligings apparaten of wijzigingen van de verificatie methoden door een gebruiker worden vastgelegd in de Azure Active Directory audit Logboeken.

Wanneer gebruikers het account in-of uitschakelt op een beveiligings sleutel of de tweede factor voor de beveiligings sleutel op hun Windows 10-computers opnieuw instellen, wordt een vermelding toegevoegd aan het beveiligings logboek en staan de volgende gebeurtenis-Id's: 4670, 5382.

### <a name="plan-for-rollback"></a>Ongedaan maken plannen

Hoewel verificatie zonder wacht woord een licht gewicht functie met minimale gevolgen voor eind gebruikers is, kan het nodig zijn om terug te draaien.

Als u terugkeert, moet de beheerder zich aanmelden bij de Azure Active Directory Portal, de gewenste sterke verificatie methoden selecteren en de optie inschakelen wijzigen in Nee. Met dit proces wordt de functionaliteit voor wacht woorden uitgeschakeld voor alle gebruikers.

Gebruikers die al FIDO2-beveiligings apparaten hebben geregistreerd, wordt gevraagd om het beveiligings apparaat bij de volgende aanmelding te gebruiken. vervolgens wordt de volgende fout weer gegeven:

![Kies een andere manier om u aan te melden](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

### <a name="plan-to-pilot"></a>Een pilot plannen

Wanneer u een wacht woordloze verificatie implementeert, moet u eerst een of meer test groepen inschakelen. U kunt speciaal voor dit doel [groepen maken](../fundamentals/active-directory-groups-create-azure-portal.md) . Voeg de gebruikers toe die deel nemen aan de pilot aan de groepen. Schakel vervolgens nieuwe authenticatie methoden met een wacht woord in voor de geselecteerde groepen.

Groepen kunnen worden gesynchroniseerd vanuit een on-premises adres lijst of vanuit Azure AD. Zodra u tevreden bent met de resultaten van uw pilot, kunt u scha kelen tussen de authenticatie zonder wacht woord voor alle gebruikers.

Zie [Aanbevolen procedures voor een pilot](https://aka.ms/deploymentplans) op de pagina implementatie plannen.

## <a name="deploy-passwordless-authentication"></a>Authenticatie met een wacht woord implementeren

Volg de stappen die zijn afgestemd op de gekozen methode.

### <a name="required-administrative-roles"></a>Vereiste beheerders rollen

| Azure AD-rol | Beschrijving |
| --- | --- |
| Verificatie beheerder | De rol van de minste bevoegdheden waarmee verificatie methoden kunnen worden geïmplementeerd en beheerd |
| Gebruiker | De minst privileged Role voor het configureren van de verificator-app op het apparaat of voor het inschrijven van een apparaat voor beveiligings sleutels voor web-of Windows 10-aanmelding. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Een telefoon aanmelding met de app voor Microsoft Authenticator implementeren

Volg de stappen in het artikel, [Schakel aanmelden zonder wacht woord in met de app Microsoft Authenticator](howto-authentication-passwordless-phone.md) om de Microsoft Authenticator-app in te scha kelen als een verificatie methode zonder wacht woord in uw organisatie.

### <a name="deploy-fido2-security-key-sign-in"></a>Aanmelden met FIDO2-beveiligings sleutel implementeren

Volg de stappen in het artikel, [Schakel wacht woordloze beveiligings sleutel aanmelden in voor Azure AD](howto-authentication-passwordless-security-key.md) om FIDO2-beveiligings sleutels in te scha kelen als verificatie methoden met een wacht woord in uw organisatie.

### <a name="troubleshoot-phone-sign-in"></a>Problemen met aanmelden via telefoon oplossen

| Scenario | Oplossing |
| --- | --- |
| Gebruiker kan geen gecombineerde registratie uitvoeren | Zorg ervoor dat [gecombineerde registratie](concept-registration-mfa-sspr-combined.md) is ingeschakeld. |
| Gebruiker kan de verificator-app voor aanmelden bij de telefoon niet inschakelen | Controleren of de gebruiker binnen het bereik is voor implementatie |
| De gebruiker is niet in het bereik voor verificatie zonder wacht woord, maar wordt weer gegeven met een aanmeldings optie zonder wacht woord, die niet kan worden voltooid. | Dit scenario treedt op wanneer de gebruiker aanmelding via de telefoon heeft ingeschakeld voordat het beleid wordt gemaakt. <br> Aanmelden inschakelen: Hiermee voegt u de gebruiker toe aan het bereik van gebruikers die zijn ingeschakeld voor aanmelden zonder wacht woord. <br> Aanmelden blok keren: laat de gebruiker de referentie formulieren van de toepassing verwijderen. |

### <a name="troubleshoot-security-key-sign-in"></a>Problemen met aanmelden bij een beveiligings sleutel oplossen

| Scenario | Oplossing |
| --- | --- |
| Gebruiker kan geen gecombineerde registratie uitvoeren | Zorg ervoor dat [gecombineerde registratie](concept-registration-mfa-sspr-combined.md) is ingeschakeld. |
| Gebruiker kan geen beveiligings sleutel toevoegen in de [beveiligings instellingen](https://aka.ms/mysecurityinfo) | Zorg ervoor dat de [beveiligings sleutels](howto-authentication-passwordless-security-key.md) zijn ingeschakeld. |
| Gebruiker kan beveiligings sleutel niet toevoegen aan aanmeldings opties voor Windows 10 | [Zorg ervoor dat de beveiligings sleutels voor Windows-aanmelding](howto-authentication-passwordless-enable.md) |
| **Fout bericht**: er is vastgesteld dat deze browser of dit besturings systeem geen FIDO2-beveiligings sleutels ondersteunt. | Wacht woord-FIDO2 beveiligings apparaten kunnen alleen worden geregistreerd in ondersteunde browsers (micro soft Edge, Firefox versie 67) op Windows 10 versie 1809 of hoger. |
| **Fout bericht**: voor uw bedrijfs beleid moet u een andere methode gebruiken om u aan te melden. | Controleren of beveiligings sleutels zijn ingeschakeld in de Tenant. |
| Gebruiker kan mijn beveiligings sleutel niet beheren in Windows 10 versie 1809 | Versie 1809 vereist dat u de software voor beveiligings sleutel beheer gebruikt die wordt verschaft door de leverancier van de FIDO2-sleutel. Neem contact op met de leverancier voor ondersteuning. |
| Ik denk dat mijn FIDO2-beveiligings sleutel defect is, hoe kan ik deze testen | Ga naar [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), voer de referenties voor een test account in, sluit de verdachte beveiligings sleutel aan, Klik rechtsboven in het scherm op maken en ga door het aanmaak proces. Als dit scenario mislukt, is uw apparaat mogelijk defect. |

## <a name="next-steps"></a>Volgende stappen

- [Wachtwoordloze beveiligings sleutels inschakelen voor aanmelding bij Azure AD](howto-authentication-passwordless-security-key.md)
- [Aanmelden zonder wacht woord inschakelen met de app Microsoft Authenticator](howto-authentication-passwordless-phone.md)
- [Meer informatie over het gebruik van verificatie methoden & Insights](howto-authentication-methods-usage-insights.md)
