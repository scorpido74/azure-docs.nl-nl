---
title: Problemen met toegang tot Azure EA Portal oplossen
description: In dit artikel worden enkele veelvoorkomende problemen beschreven die zich kunnen voordoen met een Azure Enterprise Agreement (EA) in Azure EA Portal.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/04/2020
ms.topic: troubleshooting
ms.service: cost-management-billing
manager: boalcsva
ms.openlocfilehash: 1a8900085bfb447b50c7b777fabc264201a37591
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025721"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Problemen met toegang tot Azure EA Portal oplossen

In dit artikel worden enkele veelvoorkomende problemen beschreven die zich kunnen voordoen met een Azure Enterprise Agreement (EA). Azure EA Portal wordt gebruikt om gebruikers en kosten voor een Enterprise Agreement te beheren. U kunt deze problemen tegenkomen wanneer u de toegang tot Azure EA Portal configureert of bijwerkt.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problemen bij het toevoegen van een beheerder aan een inschrijving

Er zijn verschillende typen verificatieniveaus voor ondernemingsinschrijvingen. Als de verificatieniveaus onjuist worden toegepast, ervaart u mogelijk problemen als u zich probeert aan te melden bij Azure EA Portal.

U kunt Azure EA Portal gebruiken om toegang te verlenen aan gebruikers met verschillende verificatieniveaus. Ondernemingsbeheerders kunnen het verificatieniveau bijwerken om te voldoen aan de beveiligingsvereisten van de organisatie.

### <a name="authentication-level-types"></a>Typen verificatieniveaus

- Alleen Microsoft-accounts: voor organisaties die gebruikers willen gebruiken, maken en beheren via Microsoft-accounts.
- Werk- of schoolaccount: voor organisaties die Active Directory met federatie hebben ingesteld voor de cloud en waarbij alle accounts zich in één tenant bevinden.
- Werk- of schoolaccount (meerdere tenants): voor organisaties die Active Directory met federatie hebben ingesteld voor de cloud en waarbij accounts verdeeld zijn over meerdere tenants.
- Gemengd account: hiermee kunt u gebruikers met een Microsoft-account en/of met een werk- of schoolaccount toevoegen.

Het eerste werk- of schoolaccount dat aan de inschrijving wordt toegevoegd, bepaalt welk domein wordt gebruikt: het _standaard_domein of het _hoofd_domein. Als u een werk- of schoolaccount aan een andere tenant wilt toevoegen, moet u het verificatieniveau van de inschrijving veranderen in verificatie voor meerdere tenants.

Het verificatieniveau bijwerken:

1. Meld u als ondernemingsbeheerder bij Azure EA Portal.
2. Klik op **Beheren** in het linkernavigatievenster.
3. Klik op het tabblad **Inschrijving**.
4. Selecteer **Verificatieniveau** onder **Details van de inschrijving**.
5. Klik op het potloodsymbool.
6. Klik op **Opslaan**.

![Voorbeeld met verificatieniveaus ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Voor Microsoft-accounts moet er een bijbehorende id worden gemaakt via [https://signup.live.com](https://signup.live.com/).

Werk- en schoolaccounts zijn beschikbaar voor organisaties die Active Directory met federatie hebben ingesteld en waarbij alle accounts zich in één tenant bevinden. Gebruikers kunnen worden toegevoegd met federatieve gebruikersverificatie voor op het werk of op school, als de interne Active Directory van het bedrijf federatief is.

Als uw organisatie niet gebruikmaakt van Active Directory-federatie kunt u het e-mailadres voor werk of school niet gebruiken. U kunt in plaats daarvan een nieuw e-mailadres registreren of maken en dit registreren als Microsoft-account.

## <a name="unable-to-access-the-azure-ea-portal"></a>Er kan geen toegang worden verkregen tot Azure EA Portal

Als er een foutmelding wordt weergegeven wanneer u zich probeert aan te melden bij Azure EA Portal, gebruikt u de volgende stappen voor het oplossen van problemen:

- Zorg ervoor dat u de juiste Azure EA Portal-URL gebruikt: https://ea.azure.com.
- Bepaal of met een werk- of schoolaccount of met een Microsoft-account toegang hebt verkregen tot Azure EA Portal.
  - Als u uw werkaccount gebruikt, voert u uw zakelijke e-mailadres en het bijbehorende wachtwoord in. Het wachtwoord dat u nodig hebt, wordt door uw organisatie aangeleverd. Neem contact op met de IT-afdeling voor informatie over het opnieuw instellen van het wachtwoord als u problemen ervaart.
  - Als u een Microsoft-account gebruikt, voert u het e-mailadres en wachtwoord van uw Microsoft-account in. Als u het wachtwoord van uw Microsoft-account bent vergeten, kunt u het opnieuw instellen via [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Open de browser privé of incognito om u aan te melden. Er worden dan geen cookies of informatie uit de cache bewaard van eerdere of huidige sessies. Wis de cache van uw browser en gebruik een privé- of incognito venster om https://ea.azure.com te openen.
- Als de foutmelding _Ongeldige gebruiker_ wordt weergegeven bij het gebruik van een Microsoft-account, kan dit zijn omdat u meerdere Microsoft-accounts hebt. Het account waarmee u zich probeert aan te melden, is niet die met het primaire e-mailadres.
Als u een foutmelding ziet over een _ongeldige gebruiker_, kan dat zijn omdat het onjuiste accounttype is gebruikt bij het toevoegen van de gebruiker aan de inschrijving. Het kan bijvoorbeeld zijn dat er een werk- of schoolaccount is gebruikt in plaats van een Microsoft-account. In dit voorbeeld laat u een andere EA-beheerder het juiste account toevoegen, of neemt u contact op met de [ondersteuning](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Als u de primaire alias wilt controleren, gaat u naar [https://account.live.com](https://account.live.com). Klik vervolgens op **Uw gegevens** en klik vervolgens op **Beheren hoe u zich aanmeldt bij Microsoft**. Volg de aanwijzingen voor het verifiëren van een alternatief e-mailadres en het verkrijgen van een code voor toegang tot gevoelige informatie. Voer de beveiligingscode in. Selecteer **Later instellen** als u de tweeledige verificatiemethode niet wilt instellen.
  - U ziet de pagina **Aanmelden bij Microsoft beheren** waar u uw accountaliassen kunt bekijken. Controleer of u de primaire alias gebruikt voor het aanmelden bij Azure EA Portal. Als dat niet het geval is, kunt u van uw huidige alias de primaire alias maken. U kunt in plaats daarvan ook de primaire alias voor Azure EA Portal gebruiken.

## <a name="no-activation-email-received"></a>Er is geen activeringse-mail ontvangen

Er wordt een activeringse-mail van Azure EA Portal verzonden vanaf *waep@microsoft.com* . Als u geen activeringse-mail hebt ontvangen, controleert u de map met ongewenste e-mail of de prullenbak. Deze wordt verzonden met de omschrijving: _Invitation to View/Manage the Microsoft Azure service_subject_. De e-mail wordt verzonden naar alle nieuw toegevoegde EA-beheerders.

Als u zeker weet dat u bent ingesteld als EA-beheerder, hoeft u niet te wachten op het ontvangen van de activeringse-mail om u aan te melden bij Azure EA Portal. U kunt ook naar https://ea.azure.com gaan en u aanmelden met uw e-mailadres (werk-, school- of Microsoft-account) en uw wachtwoord.

## <a name="azure-ea-activation-faq"></a>Veelgestelde vragen over activering van Azure EA

In deze sectie van het artikel vindt u een overzicht van oplossingen voor veelvoorkomende problemen rond de activering van Azure EA.

### <a name="i-would-like-to-add-a-new-ea-administrator-to-my-enrollment"></a>Ik wil een nieuwe EA-beheerder toevoegen aan mijn inschrijving

Een nieuwe Enterprise-beheerder kan worden toegevoegd door bestaande Enterprise-beheerders. Als u de EA-beheerder bent, meldt u zich aan bij de EA-Portal en klikt u in de rechter bovenhoek op **Beheren** en vervolgens op **+ beheerders toevoegen** om een nieuwe EA-beheerder toe te voegen. Zorg ervoor dat u een e-mailadres en een voorkeursmethode voor aanmelden hebt, zoals verificatie via werk of school of een Microsoft Live ID om de gebruikers toe te voegen.

Als u geen EA-beheerder bent, neem dan contact op met de EA-beheerders in uw bedrijf om te vragen of ze u kunnen toevoegen aan de inschrijving. Zodra ze u aan de inschrijving hebben toegevoegd, ontvangt u een activeringsmail.

Als EA-beheerders u hierbij echter niet kunnen helpen, kunnen we u namens hen laten toevoegen als u ons het volgende kunt aangeven:
- het inschrijvingsnummer.
- e-mailadres dat moet worden toegevoegd en het verificatietype (werk/school/MS).
- een e-mailgoedkeuring van de EA-beheerder.

Zodra u alle vereiste gegevens hebt, moet u een aanvraag indienen bij [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

### <a name="i-would-like-to-update-the-first-ea-admin-on-the-enrollment"></a>Ik wil de eerste EA-beheerder bij de inschrijving bijwerken

De eerste EA-beheerder kan worden bijgewerkt in het servicecentrum voor volumelicenties door de contactpersoon en de online beheerder in de portal bij te werken. Het duurt ongeveer 24 uur voordat de EA Portal de update heeft doorgevoerd. Zodra deze is bijgewerkt, ontvangt de nieuwe EA-beheerder een activeringsmail.

Als u geen toegang hebt tot de portal van het servicecentrum voor volumelicenties of als uw eerste EA-beheerder de inschrijving niet meer kan beheren en geen toegang heeft tot de EA-Portal, moet u een aanvraag indienen bij [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) om een update aan te vragen en de volgende informatie op te geven:
- Inschrijvingsnummer
- E-mailadres dat moet worden toegevoegd en het verificatietype (werk/school/MS)
- Reden voor het wijzigen van de eerste EA-beheerder
- E-mailgoedkeuring van de eerste EA-beheerder

### <a name="my-current-ea-admin-is-no-longer-with-the-company"></a>Mijn huidige EA-beheerder werkt niet langer bij het bedrijf

Een EA-inschrijving kan meerdere EA-beheerders hebben, u kunt een andere EA-beheerder bereiken om een nieuwe EA-beheerder/accounteigenaar/afdelingsbeheerder toe te voegen. Als u echter niet weet wie de EA-beheerder is in uw bedrijf of als er geen andere beschikbare EA-beheerder is voor de inschrijving, kunt u contact met ons opnemen met de volgende informatie:
- Inschrijvingsnummer
- E-mailadres dat moet worden toegevoegd en het verificatietype (werk/school/MS)
- Informatie over het feit dat de huidige EA-beheerder niet langer bij het bedrijf werkt

Als er sprake is van andere EA-beheerders bij de inschrijving, zullen we contact met de EA-beheerders opnemen om goedkeuring te vragen bij administratieve wijzigingen voor de inschrijving.

### <a name="my-enrollment-is-showing-in-pending-status-how-do-i-activate-my-enrollment"></a>De status van mijn inschrijving geeft weer dat deze in behandeling is. Hoe kan ik mijn inschrijving activeren?

Inschrijvingen hebben de status ‘in behandeling’ als de eerste EA-beheerder niet eerder is aangemeld bij de registratie. Als u de EA-beheerder bent, moet u zich aanmelden bij de Azure EA Portal. Op de landingspagina met al uw inschrijvingsnummers ziet u mogelijk niet de inschrijving die in behandeling is. Schakel het selectie vakje 'actief' in de rechter bovenhoek van de EA Portal uit. met deze actie wordt de inschrijving die in behandeling is weergegeven. Klik op de inschrijving om de informatie te zien. Zodra u de pagina Beheren van de inschrijving hebt bereikt, wordt de status bijgewerkt van in behandeling naar actief.

### <a name="why-is-my-account-stuck-in-pending-status"></a>Waarom blijft mijn account vaststaan op de status in behandeling?

Wanneer nieuwe accounteigenaren voor de eerste keer worden toegevoegd aan de inschrijving, krijgen ze altijd de status 'In behandeling'. De accounteigenaar kan zich aanmelden om het account te activeren zodra hij de welkomstmail voor de activering ontvangt. Met deze aanmelding wordt de accountstatus bijgewerkt van 'In behandeling' naar 'Actief'.

### <a name="i-received-an-error-when-signing-in-to-azure-ea-portal"></a>Ik krijg een foutmelding bij het aanmelden bij de Azure EA Portal

Er zijn een aantal mogelijke redenen voor een foutmelding in de Azure EA Portal tijdens het aanmelden. Volg deze stappen om het probleem op te lossen:

 1. Controleer of u de juiste URL voor de EA Portal op [https://ea.azure.com](https://ea.azure.com) gebruikt.
 1. Bepaal of u met een werk- of schoolaccount of met een Microsoft Live ID toegang hebt tot Azure EA Portal. Als u uw werkaccount gebruikt, voert u uw zakelijke e-mailadres en het bijbehorende wachtwoord in. Als u Microsoft Live ID gebruikt, voert u uw e-mailadres is van Live ID en wachtwoord voor Microsoft Live ID in. Als u uw wachtwoord voor Microsoft Live ID bent vergeten, moet u het opnieuw instellen op [https://account.live.com/password/reset](https://account.live.com/password/reset).
 1. We raden u aan om een privé-browser te gebruiken om u aan te melden, zodat er geen cookies of cache van eerdere/bestaande sessies worden bewaard. Wis cache en gebruik de modus voor privé browsen/incognitomodus om [https://ea.azure.com](https://ea.azure.com) te openen.
 1. Als u een foutmelding voor ongeldige gebruiker krijgt wanneer u een Microsoft-account gebruikt, kan het zijn dat u meerdere Microsoft-accounts hebt en het account waarmee u zich probeert aan te melden, niet de primaire alias is. Om de primaire alias te controleren, gaat u naar account.live.com:
    - Ga naar 'Uw gegevens' > 'Uw e-mailadres of telefoonnummer voor aanmelden beheren'.
    - Volg de aanwijzingen op het scherm voor het verifiëren van een alternatief e-mailadres en het verkrijgen van een code voor toegang tot gevoelige informatie.
    - Voer de beveiligingscode in.
    - Als u later tweestapsverificatie wilt instellen, selecteert u 'Later instellen'.
    - U belandt op de pagina 'Uw accountaliassen beheren', waar u de accountaliassen ziet die u hebt. Controleer of u de primaire alias gebruikt om u aan te melden bij de Azure EA Portal. Als dat niet het geval is, kunt u dit instellen als uw primaire alias of kunt u in plaats daarvan de primaire alias voor de EA Portal gebruiken.

Als de bovenstaande stappen niet helpen om de probleem op te lossen, moet u een aanvraag indienen bij [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) met informatie zoals:
- De gebruikte browsers en versie.
- Schermopname van de foutmelding.
- URL van de pagina met de fout.  
- De datum, tijd en tijdzone van het tijdstip waarop de fout optreedt.
- Daarnaast kunt u hiermee een logboekbestand ophalen. Hier volgen de stappen voor het vastleggen van een netwerktracering met behulp van de volgende gegevens:
  1. Open Internet Explorer.
  1. Druk op F12 om een vak onder in Internet Explorer te openen.
  1. Selecteer het tabblad **Netwerk**.
  1. Klik op **Beginnen met vastleggen**.
  1. Voer de actie uit die de fout veroorzaakt.
  1. Wanneer de fout verschijnt, klikt u op **Stoppen met vastleggen**.
  1. Sla het bestand op en voeg de informatie bij de ondersteuningsaanvraag.
  1. Zorg ervoor dat u uw inschrijvingsnummer en e-mailadres bij de ondersteuningsaanvraag opgeeft.

### <a name="what-is-the-difference-between-a-workschool-account-and-microsoft-account"></a>Wat is het verschil tussen een werk-/schoolaccount en een Microsoft-account?

**Microsoft-account:** Accounts die zijn gekoppeld aan Live ID op [https://signup.live.com](https://signup.live.com).

**Werk-/schoolaccount:** Alleen beschikbaar voor bedrijven die Active Directory met federatie hebben ingesteld voor de cloud en waarbij alle accounts zich in één tenant bevinden. Gebruikers kunnen worden toegevoegd met het verificatietype voor op het werk of op school, als de interne Active Directory van het bedrijf federatief is in de cloud.

  Vanaf september 2016 kan Microsoft e-mails voor werk of school niet meer registreren als Microsoft-account. Raadpleeg voor meer informatie de volgende bronnen: [https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/](https://blogs.technet.microsoft.com/enterprisemobility/2016/09/15/cleaning-up-the-azure-ad-and-microsoft-account-overlap/).

  Als uw organisatie niet federatief is voor de cloud, kunt u uw e-mailadres voor werk of school niet gebruiken. U kunt in plaats daarvan een nieuw e-mailadres registreren of maken en dit als een Microsoft-account registreren.

### <a name="i-forgot-my-password-to-azure-ea-portal"></a>Ik ben mijn wachtwoord voor Azure EA Portal vergeten

Als u uw wachtwoord voor Microsoft Live ID bent vergeten, moet u het opnieuw instellen op [https://account.live.com/password/reset](https://account.live.com/password/reset).

Neem contact op met de IT-beheerder van uw bedrijf als u uw wachtwoord bent vergeten.

### <a name="i-have-a-valid-work-or-school-account-but-i-cant-add-it-to-the-ea-portal"></a>Ik heb een geldig werk- of schoolaccount, maar ik kan het niet toevoegen aan de EA Portal

Als u een werk -of schoolaccount hebt onder een andere tenant, wijzigt u het autorisatieniveau onder de pagina Inschrijvingsdetails in een werk -of schoolaccount voor verschillende tenants, zodat u het account ook kunt toevoegen.

## <a name="next-steps"></a>Volgende stappen

- Azure EA Portal-beheerders moeten [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
