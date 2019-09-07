---
title: Implementatie plan selfservice wachtwoord herstel-Azure Active Directory
description: Strategie voor een succes volle implementatie van de selfservice voor wachtwoord herstel van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 330b02e3db6af90fcfeb962e78b043b04090116e
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743243"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Self-service voor wachtwoordherstel van Azure AD implementeren

Self-service voor wachtwoord herstel (SSPR) is een Azure Active Directory functie waarmee werk nemers hun wacht woord opnieuw kunnen instellen zonder contact op te nemen met IT-personeel. Werk nemers moeten zich registreren voor de selfservice voor het opnieuw instellen van het wacht woord voordat ze de service kunnen gebruiken. Tijdens de registratie kiest de werk nemer een of meer authenticatie methoden die worden ingeschakeld door hun organisatie.

Met SSPR kunnen werk nemers snel de blok kering opheffen en blijven werken, ongeacht waar ze zijn of op het tijdstip van de dag. Door gebruikers toe te staan zichzelf te deblokkeren, kan uw organisatie de niet-productieve tijd en hoge ondersteunings kosten voor de meest voorkomende problemen met betrekking tot het wacht woord beperken.

Help gebruikers om snel te worden geregistreerd door SSPR te implementeren naast een andere toepassing of service in uw organisatie. Met deze actie wordt een groot aantal aanmeldingen gegenereerd en wordt de registratie van het apparaat uitgevoerd.

Voor het implementeren van SSPR kunnen organisaties bepalen hoeveel wacht woorden voor de Help Desk moeten worden ingesteld na verloop van tijd en de gemiddelde kosten van elke oproep. Ze kunnen deze gegevens na de implementatie gebruiken om weer te geven dat de waarde SSPR naar uw organisatie gaat.  

## <a name="how-sspr-works"></a>Hoe SSPR werkt

1. Wanneer een gebruiker probeert een wacht woord opnieuw in te stellen, moeten ze hun eerder geregistreerde verificatie methode of methoden controleren om hun identiteit te bewijzen.
1. Vervolgens voert de gebruiker een nieuw wacht woord in.
   1. Voor Cloud gebruikers wordt het nieuwe wacht woord opgeslagen in Azure Active Directory. Zie het artikel [How SSPR Works](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work)(Engelstalig) voor meer informatie.
   1. Voor hybride gebruikers wordt het wacht woord teruggeschreven naar de on-premises Active Directory via de Azure AD Connect-service. Zie het artikel [Wat is wacht woord terugschrijven](concept-sspr-writeback.md#how-password-writeback-works)voor meer informatie.

## <a name="licensing-considerations"></a>Licentie overwegingen

Azure Active Directory is gelicentieerd per gebruiker, wat betekent dat elke gebruiker over een juiste licentie beschikt voor de functies die ze gebruiken.

Meer informatie over licentie verlening vindt u op de [pagina met Azure Active Directory prijzen](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Gecombineerde registratie inschakelen voor SSPR en MFA

Micro soft raadt organisaties aan de gecombineerde registratie-ervaring voor SSPR en multi-factor Authentication in te scha kelen. Wanneer u deze gecombineerde registratie-ervaring inschakelt, hoeven gebruikers slechts eenmaal hun registratie gegevens te selecteren om beide functies in te scha kelen.

![Registratie van gecombineerde beveiligings gegevens](./media/howto-sspr-deployment/combined-security-info.png)

De gecombineerde registratie-ervaring vereist niet dat organisaties zowel SSPR als Azure Multi-Factor Authentication kunnen gebruiken. De gecombineerde registratie-ervaring biedt organisaties een betere gebruikers ervaring in vergelijking met de traditionele afzonderlijke onderdelen. Meer informatie over gecombineerde registratie en hoe u kunt inschakelen, vindt u in het artikel [registratie van gecombineerde beveiligings gegevens (preview)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>De configuratie plannen

De volgende instellingen zijn vereist om SSPR samen met aanbevolen waarden in te scha kelen.

| Onderwerp | Instelling | Value |
| --- | --- | --- |
| **SSPR-eigenschappen** | Self-service voor wacht woord opnieuw instellen is ingeschakeld | **Geselecteerde** groep voor pilot/ **all** voor productie |
| **Verificatiemethoden** | Verificatie methoden die nodig zijn om te registreren | Altijd 1 meer dan vereist voor opnieuw instellen |
|   | Verificatie methoden die nodig zijn om opnieuw in te stellen | Een of twee |
| **Registratie** | Vereisen dat gebruiker zich bij aanmelding registreren | Ja |
|   | Aantal dagen waarna gebruikers wordt gevraagd om de verificatie gegevens opnieuw te bevestigen | 90 – 180 dagen |
| **Meldingen** | Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord | Ja |
|   | Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen | Ja |
| **Aanpassing** | Helpdeskkoppeling aanpassen | Ja |
|   | Aangepast e-mailadres of aangepaste URL van de helpdesk | Ondersteunings site of e-mail adres |
| **On-premises integratie** | Wacht woorden terugschrijven naar on-premises AD | Ja |
|   | Gebruikers toestaan om het account te ontgrendelen zonder het wacht woord opnieuw in te stellen | Ja |

### <a name="sspr-properties-recommendations"></a>Aanbevelingen voor SSPR-eigenschappen

Als selfservice voor wacht woord opnieuw instellen is ingeschakeld, kiest u een beveiligings groep die u tijdens de pilot wilt gebruiken.

Wanneer u van plan bent om de service vaker te starten, kunt u het beste de optie alles gebruiken om SSPR af te dwingen voor iedereen in de organisatie. Als u niet kunt instellen op alle, selecteert u de juiste Azure AD-beveiligings groep of AD-groep die is gesynchroniseerd met Azure AD.

### <a name="authentication-methods"></a>Verificatiemethoden

Stel de verificatie methoden in die vereist zijn om te registreren bij ten minste één waarde voor het aantal dat moet worden ingesteld. Het toestaan van meerdere gebruikers flexibiliteit wanneer ze opnieuw moeten worden ingesteld.

Stel het **aantal methoden in** dat moet worden ingesteld op een niveau dat geschikt is voor uw organisatie. Voor één is de minste wrijving vereist, terwijl twee de beveiligings postuur kan verg Roten.

Zie [Wat zijn verificatie methoden](concept-authentication-methods.md) voor gedetailleerde informatie over de verificatie methoden die beschikbaar zijn voor SSPR, vooraf gedefinieerde beveiligings vragen en het maken van aangepaste beveiligings vragen.

### <a name="registration-settings"></a>Registratie-instellingen

Stel **in dat gebruikers zich moeten registreren wanneer ze zich aanmelden** bij **Ja**. Met deze instelling worden de gebruikers gedwongen zich bij het aanmelden te registreren, zodat alle gebruikers worden beveiligd.

Stel het **aantal dagen in voordat gebruikers wordt gevraagd hun verificatie gegevens te bevestigen** tussen **90** en **180** dagen, tenzij uw organisatie voor een kortere periode een bedrijfs behoefte heeft.

### <a name="notifications-settings"></a>Meldingsinstellingen

Configureer de **gebruikers op de hoogte van het opnieuw instellen van wacht woorden** en stel **alle beheerders op de hoogte wanneer andere beheerders hun wacht woord opnieuw instellen** op **Ja**. Als u **Ja** selecteert, wordt de beveiliging verhoogd door ervoor te zorgen dat gebruikers op de hoogte zijn van het opnieuw instellen van het wacht woord en dat alle beheerders op de hoogte zijn wanneer een beheerder een wacht woord wijzigt. Als gebruikers of beheerders een dergelijke melding ontvangen en de wijziging niet hebben geïnitieerd, kunnen ze onmiddellijk een mogelijke schending van de beveiliging melden.

### <a name="customization"></a>Aanpassing

Het is essentieel om de **e-mail adres of URL van de Help Desk** aan te passen om ervoor te zorgen dat gebruikers die problemen ondervinden snel hulp kunnen krijgen. Stel deze optie in op een gemeen schappelijke e-mail adres van de Help Desk of de webpagina waarmee uw gebruikers bekend zijn.

### <a name="on-premises-integration"></a>On-premises integratie

Als u een hybride omgeving hebt, moet u ervoor zorgen dat het **terugschrijven van wacht woorden naar on-premises AD** is ingesteld op **Ja**. Stel ook de optie gebruikers toestaan om account te ontgrendelen zonder wacht woord opnieuw in op Ja, omdat ze meer flexibiliteit bieden.

### <a name="changingresetting-passwords-of-administrators"></a>Wacht woorden van beheerders wijzigen/opnieuw instellen

Beheerders accounts zijn speciale accounts met verhoogde machtigingen. Om ze te beveiligen, gelden de volgende beperkingen voor het wijzigen van wacht woorden van beheerders:

- On-premises ondernemings Administrators of domein Administrators kunnen hun wacht woord niet opnieuw instellen via SSPR. Ze kunnen alleen hun wacht woord wijzigen in hun on-premises omgeving. Daarom raden wij aan om on-premises AD-beheerders accounts te synchroniseren met Azure AD.
- Een beheerder kan geen geheime vragen & antwoorden gebruiken als een methode om het wacht woord opnieuw in te stellen.

### <a name="environments-with-multiple-identity-management-systems"></a>Omgevingen met meerdere systemen voor identiteits beheer

Als er meerdere systemen voor identiteits beheer binnen een omgeving zijn, zoals on-premises identiteits managers zoals Oracle AM, SiteMinder of andere systemen, moeten wacht woorden die zijn geschreven naar Active Directory mogelijk worden gesynchroniseerd met de andere systemen met een hulp programma zoals de meldings service voor wachtwoord wijzigingen (PCNS) met Microsoft Identity Manager (MIM). Zie het artikel [de MIM-meldings service voor wachtwoord wijzigingen implementeren op een domein controller](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)voor meer informatie over dit complexe scenario.

## <a name="plan-deployment-and-support-for-sspr"></a>Implementatie en ondersteuning plannen voor SSPR

### <a name="engage-the-right-stakeholders"></a>De juiste belanghebbenden benaderen

Wanneer technologie projecten mislukken, worden ze doorgaans als gevolg van niet-overeenkomende verwachtingen voor impact, resultaten en verantwoordelijkheden. Als u deze problemen wilt voor komen, moet u ervoor zorgen dat u de juiste belanghebbenden ophoudt en dat de rol van belanghebbenden in het project goed worden begrepen door de betrokken partijen en hun project invoer en-verantwoording te documenteren.

### <a name="communications-plan"></a>Communicatie plan

Communicatie is van cruciaal belang voor het slagen van een nieuwe service. Communiceer proactief met uw gebruikers om de service te gebruiken en wat ze kunnen doen om hulp te krijgen als er iets anders is dan verwacht. Lees de [self-service voor het opnieuw instellen van wacht woorden op het micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=56768) voor ideeën over het plannen van de communicatie strategie van uw eind gebruikers.

### <a name="testing-plan"></a>Test plan

Om ervoor te zorgen dat uw implementatie werkt zoals verwacht, moet u een set test cases plannen die u wilt gebruiken om de implementatie te valideren. De volgende tabel bevat een aantal nuttige test scenario's die u kunt gebruiken om de verwachte resultaten van uw organisatie op basis van uw beleid te documenteren.

| Businesscase | Verwacht resultaat |
| --- | --- |
| SSPR Portal is toegankelijk vanuit het bedrijfs netwerk | Bepaald door uw organisatie |
| SSPR Portal is toegankelijk vanaf buiten het bedrijfs netwerk | Bepaald door uw organisatie |
| Het gebruikers wachtwoord opnieuw instellen vanuit de browser wanneer de gebruiker niet is ingeschakeld voor het opnieuw instellen van het wacht woord | De gebruiker heeft geen toegang tot de stroom voor het opnieuw instellen van het wacht woord |
| Het gebruikers wachtwoord opnieuw instellen vanuit de browser wanneer de gebruiker niet is geregistreerd voor het opnieuw instellen van het wacht woord | De gebruiker heeft geen toegang tot de stroom voor het opnieuw instellen van het wacht woord |
| Gebruiker meldt zich aan als registratie van wacht woord opnieuw instellen wordt afgedwongen | De gebruiker wordt gevraagd om beveiligings gegevens te registreren |
| De gebruiker meldt zich aan wanneer de registratie voor het opnieuw instellen van het wacht woord is voltooid | De gebruiker wordt niet gevraagd om beveiligings gegevens te registreren |
| De SSPR-Portal is toegankelijk als de gebruiker geen licentie heeft | Is toegankelijk |
| Gebruikers wachtwoord opnieuw instellen vanuit Windows 10 AADJ of H + AADJ apparaat-vergrendelings scherm nadat de gebruiker zich heeft geregistreerd | Gebruiker kan wacht woord opnieuw instellen |
| SSPR-registratie en gebruiks gegevens zijn bijna in real time beschikbaar voor beheerders | Is beschikbaar via audit logboeken |

### <a name="support-plan"></a>Ondersteuningsplan

Hoewel SSPR doorgaans geen gebruikers problemen maakt, is het belang rijk dat u ondersteunings personeel hebt voor het oplossen van problemen die zich kunnen voordoen.

Hoewel een beheerder het wacht woord voor eind gebruikers via de Azure AD-Portal kan wijzigen of opnieuw instellen, is het beter om het probleem op te lossen via een self-service-ondersteunings proces.

Maak in het gedeelte operationele hand leiding van dit document een lijst met ondersteunings cases en de mogelijke oorzaken daarvan en maak een hulp lijn voor oplossing.

### <a name="auditing-and-reporting"></a>Controle en rapportage

Na de implementatie willen veel organisaties weten hoe of als self-service voor wachtwoord herstel (SSPR) werkelijk wordt gebruikt. Met de rapportage functie die Azure Active Directory (Azure AD) kunt u vragen beantwoorden met behulp van vooraf ontwikkelde rapporten.

Audit logboeken voor registratie en het opnieuw instellen van wacht woorden zijn 30 dagen beschikbaar. Als de beveiligings controle binnen een onderneming een langere retentie vereist, moeten de logboeken daarom worden geëxporteerd en gebruikt in een SIEM-hulp programma zoals [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk of ArcSight.

Documenteer in een tabel, zoals hieronder, het back-upschema, het systeem en de verantwoordelijke partijen. U hebt mogelijk geen afzonderlijke back-ups van controles en rapporten nodig, maar u moet een afzonderlijke back-up hebben van waaruit u een probleem kunt herstellen.

|   | Download frequentie | Doel systeem | Verantwoordelijke partij |
| --- | --- | --- | --- |
| Back-up controleren |   |   |   |
| Rapportage back-up |   |   |   |
| Back-ups voor herstel na nood gevallen |   |   |   |

## <a name="implementation"></a>Implementatie

Implementatie vindt plaats in drie fasen:

- Gebruikers en licenties configureren
- Azure AD SSPR configureren voor registratie en self-service
- Azure AD Connect voor het terugschrijven van wacht woorden configureren

### <a name="communicate-the-change"></a>De wijziging door geven

Begin implementatie van het communicatie abonnement dat u in de plannings fase hebt ontwikkeld.

### <a name="ensure-groups-are-created-and-populated"></a>Controleren of groepen zijn gemaakt en ingevuld

Raadpleeg de sectie plannings wachtwoord verificatie methoden en zorg ervoor dat de groep (en) voor de test-of productie-implementatie beschikbaar zijn en dat alle relevante gebruikers worden toegevoegd aan de groepen.

### <a name="apply-licenses"></a>Licenties Toep assen

Aan de groepen die u gaat implementeren, moet de Azure AD Premium-licentie zijn toegewezen. U kunt licenties rechtstreeks aan de groep toewijzen of u kunt bestaande licentie beleidsregels gebruiken, zoals via Power shell of op groep gebaseerde licentie verlening.

Informatie over het toewijzen van licenties aan groepen gebruikers vindt u in het artikel, het [toewijzen van licenties aan gebruikers per groepslid maatschap in azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>SSPR configureren

#### <a name="enable-groups-for-sspr"></a>Groepen inschakelen voor SSPR

1. Toegang tot de Azure Portal met een Administrator-account.
1. Selecteer alle services, typ Azure Active Directory in het vak Filter en selecteer vervolgens Azure Active Directory.
1. Selecteer wacht woord opnieuw instellen op de Blade Active Directory.
1. Selecteer geselecteerd in het deel venster Eigenschappen. Als u alle gebruikers wilt inschakelen, selecteert u alle.
1. Typ op de Blade standaard beleid voor wachtwoord herstel de naam van de eerste groep, Selecteer deze en klik vervolgens op selecteren onder aan het scherm en selecteer opslaan boven aan het scherm.
1. Herhaal dit proces voor elke groep.

#### <a name="configure-the-authentication-methods"></a>De verificatie methoden configureren

Raadpleeg uw planning vanuit de sectie plannings wachtwoord verificatie methoden van dit document.

1. Selecteer registratie, onder gebruikers moeten zich registreren bij het aanmelden, selecteer Ja, en stel vervolgens het aantal dagen vóór verval datum in en selecteer vervolgens opslaan.
1. Selecteer melding, Configureer uw abonnement en selecteer vervolgens opslaan.
1. Selecteer aanpassing, en configureer uw abonnement en selecteer vervolgens opslaan.
1. Selecteer on-premises integratie, Configureer uw abonnement en selecteer vervolgens opslaan.

### <a name="enable-sspr-in-windows"></a>SSPR in Windows inschakelen

Windows 10-apparaten met versie 1803 of hoger die zijn toegevoegd aan Azure AD of hybride Azure AD, kunnen hun wacht woord opnieuw instellen in het Windows-aanmeldings scherm. Informatie en stappen voor het configureren van deze mogelijkheid vindt u in het artikel [Azure AD-wacht woord opnieuw instellen vanuit het aanmeldings scherm](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Wacht woord terugschrijven configureren

De stappen voor het terugschrijven van wacht woorden voor uw organisatie vindt u [in het artikel How to: Wacht woord terugschrijven](howto-sspr-writeback.md)configureren.

## <a name="manage-sspr"></a>SSPR beheren

Vereiste rollen voor het beheren van functies die zijn gekoppeld aan selfservice voor het opnieuw instellen van wacht woorden.

| Zakelijke rol/persoona | Azure AD-rol (indien nodig) |
| :---: | :---: |
| Help Desk van niveau 1 | Wachtwoordbeheerder |
| Help Desk van niveau 2 | Gebruikersbeheerder |
| SSPR-beheerder | Globale beheerder |

### <a name="support-scenarios"></a>Ondersteunings scenario's

Als u het ondersteunings team wilt inschakelen, kunt u een veelgestelde vragen maken op basis van vragen die u van uw gebruikers ontvangt. De volgende tabel bevat algemene ondersteunings scenario's.

| Scenario's | Description |
| --- | --- |
| De gebruiker heeft geen geregistreerde verificatie methoden beschikbaar | Een gebruiker probeert het wacht woord opnieuw in te stellen, maar heeft geen van de authenticatie methoden die ze beschikbaar hebben (voor beeld: ze hebben hun mobiele telefoon thuis verlaten en kunnen geen toegang krijgen tot e-mail) |
| De gebruiker ontvangt geen tekst of oproep op hun kantoor of mobiele telefoon | Een gebruiker probeert zijn identiteit te verifiëren via tekst of oproep, maar ontvangt geen tekst/oproep. |
| Gebruiker heeft geen toegang tot de portal voor het opnieuw instellen van wacht woorden | Een gebruiker wil het wacht woord opnieuw instellen, maar is niet ingeschakeld voor wachtwoord herstel en heeft daarom geen toegang tot de pagina om wacht woorden bij te werken. |
| Gebruiker kan geen nieuw wacht woord instellen | Een gebruiker heeft de verificatie voltooid tijdens de stroom voor het opnieuw instellen van het wacht woord, maar kan geen nieuw wacht woord instellen. |
| De gebruiker ziet een koppeling voor het opnieuw instellen van het wacht woord op een Windows 10-apparaat niet | Een gebruiker probeert het wacht woord opnieuw in te stellen vanaf het vergrendelings scherm van Windows 10, maar het apparaat is niet gekoppeld aan Azure AD of het intune-apparaatbeleid is niet ingeschakeld |

Het is ook mogelijk dat u informatie wilt toevoegen zoals het volgende voor extra probleem oplossing.

- Welke groepen zijn ingeschakeld voor SSPR.
- Welke verificatie methoden worden geconfigureerd.
- Het toegangs beleid dat betrekking heeft op of van het bedrijfs netwerk.
- Stappen voor probleem oplossing voor algemene scenario's.

U kunt ook de online documentatie raadplegen voor het oplossen van problemen met het opnieuw instellen van het wacht woord voor een self-service voor het oplossen van algemene probleemoplossings stappen voor de meest voorkomende SSPR-scenario's.

## <a name="next-steps"></a>Volgende stappen

- [Implementatie van Azure AD-wachtwoord beveiliging overwegen](concept-password-ban-bad.md)

- [Overweeg het implementeren van Azure AD Smart-vergren deling](howto-password-smart-lockout.md)
