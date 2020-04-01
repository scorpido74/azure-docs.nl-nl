---
title: Verouderde verificatie blokkeren - Azure Active Directory
description: Meer informatie over het verbeteren van uw beveiligingshouding door verouderde verificatie te blokkeren met Azure AD Conditional Access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957aa77e18ea8f910f258d1dc59de0d093b0eab6
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476643"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>How to: Verouderde verificatie blokkeren naar Azure AD met voorwaardelijke toegang   

Azure Active Directory (Azure AD) ondersteunt een breed scala aan verificatieprotocollen, waaronder verouderde verificatie, om uw gebruikers eenvoudig toegang te geven tot uw cloud-apps. Oudere protocollen ondersteunen echter geen multi-factor authenticatie (MFA). MFA is in veel omgevingen een veelvoorkomende vereiste om identiteitsdiefstal aan te pakken. 

Alex Weinert, Director of Identity Security bij Microsoft, in zijn blogpost van 12 maart 2020 [Nieuwe tools om verouderde verificatie in uw organisatie te blokkeren,](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302#) benadrukken waarom organisaties verouderde verificatie moeten blokkeren en welke extra tools Microsoft biedt om deze taak uit te voeren:

> Om MFA effectief te laten zijn, moet u ook verouderde verificatie blokkeren. Dit komt omdat verouderde verificatieprotocollen zoals POP, SMTP, IMAP en MAPI MFA niet kunnen afdwingen, waardoor ze de voorkeur hebben aan toegangspunten voor tegenstanders die uw organisatie aanvallen...
> 
>... De cijfers over oudere verificatie uit een analyse van Azure Active Directory-verkeer (Azure AD) zijn grimmig:
> 
> - Meer dan 99 procent van de wachtwoordsprayaanvallen gebruikt verouderde authenticatieprotocollen
> - Meer dan 97 procent van de credential stuffing-aanvallen gebruiken verouderde verificatie
> - Azure AD-accounts in organisaties die verouderde verificatie hebben uitgeschakeld, ervaren 67 procent minder compromissen dan die waarin verouderde verificatie is ingeschakeld
>

Als uw omgeving klaar is om verouderde verificatie te blokkeren om de beveiliging van uw tenant te verbeteren, u dit doel bereiken met Voorwaardelijke toegang. In dit artikel wordt uitgelegd hoe u beleid voor voorwaardelijke toegang configureren waarmee verouderde verificatie voor uw tenant wordt geblokkeerd.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met: 

- De [basisconcepten](overview.md) van Azure AD Conditional Access 
- De [aanbevolen procedures](best-practices.md) voor het configureren van beleid voor voorwaardelijke toegang in de Azure-portal

## <a name="scenario-description"></a>Scenariobeschrijving

Azure AD ondersteunt een aantal van de meest gebruikte verificatie- en autorisatieprotocollen, waaronder verouderde verificatie. Legacy-verificatie verwijst naar protocollen die basisverificatie gebruiken. Deze protocollen kunnen doorgaans geen enkele vorm van tweedefactorverificatie afdwingen. Voorbeelden voor apps die zijn gebaseerd op verouderde verificatie zijn:

- Oudere Microsoft Office-apps
- Apps met behulp van e-mailprotocollen zoals POP, IMAP en SMTP

Single factor authenticatie (bijvoorbeeld gebruikersnaam en wachtwoord) is niet genoeg deze dagen. Wachtwoorden zijn slecht omdat ze gemakkelijk te raden zijn en wij (mensen) slecht zijn in het kiezen van goede wachtwoorden. Wachtwoorden zijn ook kwetsbaar voor een verscheidenheid aan aanvallen zoals phishing en wachtwoord spray. Een van de gemakkelijkste dingen die u doen om te beschermen tegen wachtwoordbedreigingen is het implementeren van MFA. Met MFA is het wachtwoord alleen niet voldoende om de gegevens te verifiëren en toegang te krijgen tot de gegevens, zelfs als een aanvaller in het bezit komt van het wachtwoord van een gebruiker.

Hoe u voorkomen dat apps met behulp van verouderde verificatie toegang krijgen tot de bronnen van uw tenant? De aanbeveling is om ze gewoon te blokkeren met een beleid voor voorwaardelijke toegang. Indien nodig staat u alleen bepaalde gebruikers en specifieke netwerklocaties toe om apps te gebruiken die zijn gebaseerd op verouderde verificatie.

Beleid voor voorwaardelijke toegang wordt afgedwongen nadat de eerste-factorverificatie is voltooid. Voorwaardelijke toegang is daarom niet bedoeld als een eerste lijnverdediging voor scenario's zoals dos-aanvallen (denial-of-service), maar kan signalen van deze gebeurtenissen (bijvoorbeeld het aanmeldingsrisiconiveau, locatie van het verzoek, enzovoort) gebruiken om de toegang te bepalen.

## <a name="implementation"></a>Implementatie

In dit gedeelte wordt uitgelegd hoe u een beleid voor voorwaardelijke toegang configureert om verouderde verificatie te blokkeren. 

### <a name="legacy-authentication-protocols"></a>Verouderde verificatieprotocollen

De volgende opties worden beschouwd als verouderde verificatieprotocollen

- Geauthenticeerde SMTP - Gebruikt door POP en IMAP client's om e-mailberichten te verzenden.
- Automatisch ontdekken - Gebruikt door Outlook- en EAS-clients om postvakken in Exchange Online te vinden en te verbinden.
- Exchange Online PowerShell - Wordt gebruikt om verbinding te maken met Exchange Online met externe PowerShell. Als u basisverificatie voor Exchange Online PowerShell blokkeert, moet u de Exchange Online PowerShell-module gebruiken om verbinding te maken. Zie Verbinding [maken met Exchange Online PowerShell met behulp van meervoudige verificatie](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)voor instructies.
- Exchange Web Services (EWS) - een programmeerinterface die wordt gebruikt door Outlook, Outlook voor Mac en apps van derden.
- IMAP4 - Gebruikt door IMAP e-mailclients.
- MAPI over HTTP (MAPI/HTTP) - Gebruikt door Outlook 2010 en hoger.
- Offline adresboek (OAB) - een kopie van adreslijstverzamelingen die worden gedownload en gebruikt door Outlook.
- Outlook Anywhere (RPC via HTTP) - Gebruikt door Outlook 2016 en eerder.
- Outlook-service - Gebruikt door de mail- en agenda-app voor Windows 10.
- POP3 - Gebruikt door POP e-mailclients.
- Webservices rapporteren - Wordt gebruikt om rapportgegevens op te halen in Exchange Online.
- Andere clients - Andere protocollen die zijn geïdentificeerd als gebruikmakend van verouderde verificatie.

Zie [Aanmeldingsactiviteitsrapporten in de Azure Active Directory-portal](../reports-monitoring/concept-sign-ins.md#filter-sign-in-activities)voor meer informatie over deze verificatieprotocollen en -services.

### <a name="identify-legacy-authentication-use"></a>Identificeer het gebruik van verouderde verificatie

Voordat u oudere verificatie in uw directory blokkeren, moet u eerst begrijpen of uw gebruikers apps hebben die verouderde verificatie gebruiken en hoe deze van invloed is op uw algemene directory. Azure AD-aanmeldingslogboeken kunnen worden gebruikt om te begrijpen of u verouderde verificatie gebruikt.

1. Navigeer naar de **Azure portal** > Azure Active**Directory-aanmeldingen****Azure Active Directory** > .
1. Voeg de kolom Client-app toe als deze niet wordt weergegeven door op de kolom > **Kolommenclientapp**te klikken . **Columns**
1. **Filters toevoegen****Client App** > alle verouderde verificatieprotocollen selecteren en op **Toepassen klikken.** > 

Als u alleen aanmeldingspogingen doen die zijn uitgevoerd door verouderde verificatieprotocollen, wordt alleen weergegeven. Als u op elke afzonderlijke aanmeldingspoging klikt, ziet u meer details. In het veld **Client-app** onder het tabblad **Basisgegevens** wordt aangegeven welk verouderd verificatieprotocol is gebruikt.

Deze logboeken geven aan welke gebruikers nog afhankelijk zijn van verouderde verificatie en welke toepassingen verouderde protocollen gebruiken om verificatieverzoeken in te dienen. Voor gebruikers die niet in deze logboeken worden weergegeven en waarvan is bevestigd dat ze geen verouderde verificatie gebruiken, implementeert u alleen een beleid voor voorwaardelijke toegang voor deze gebruikers.

### <a name="block-legacy-authentication"></a>Verouderde verificatie blokkeren 

In een beleid voor voorwaardelijke toegang u een voorwaarde instellen die is gekoppeld aan de client-apps die worden gebruikt om toegang te krijgen tot uw bronnen. Met de voorwaarde client-apps u het bereik beperken tot apps met behulp van verouderde verificatie door **Exchange ActiveSync-clients** en **Andere clients** te selecteren onder mobiele apps **en desktopclients.**

![Andere clients](./media/block-legacy-authentication/01.png)

Als u de toegang voor deze apps wilt blokkeren, moet u **Toegang blokkeren**selecteren.

![Toegang blokkeren](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Gebruikers en cloud-apps selecteren

Als u oudere verificatie voor uw organisatie wilt blokkeren, denkt u waarschijnlijk dat u dit bereiken door het selecteren van:

- Alle gebruikers
- Alle cloud-apps
- Toegang blokkeren

![Toewijzingen](./media/block-legacy-authentication/03.png)

Azure heeft een veiligheidsfunctie die voorkomt dat u een dergelijk beleid maakt, omdat deze configuratie in strijd is met de [aanbevolen procedures](best-practices.md) voor beleid voor voorwaardelijke toegang.
 
![Beleidsconfiguratie wordt niet ondersteund](./media/block-legacy-authentication/04.png)

De veiligheidsfunctie is noodzakelijk omdat *alle gebruikers en alle cloud-apps* het potentieel hebben om uw hele organisatie te blokkeren om zich aan te melden bij uw tenant. U moet ten minste één gebruiker uitsluiten om te voldoen aan de minimale vereiste voor beste praktijken. U ook een maprol uitsluiten.

![Beleidsconfiguratie wordt niet ondersteund](./media/block-legacy-authentication/05.png)

U aan deze veiligheidsfunctie voldoen door één gebruiker uit te sluiten van uw beleid. Idealiter moet u een aantal [beheeraccounts voor noodgevallen definiëren in Azure AD](../users-groups-roles/directory-emergency-access.md) en deze uitsluiten van uw beleid.

Het gebruik [van de modus Alleen-gegevens gebruiken](concept-conditional-access-report-only.md) wanneer u uw beleid inschakelt om verouderde verificatie te blokkeren, biedt uw organisatie de mogelijkheid om te controleren wat de impact van het beleid zou zijn.

## <a name="policy-deployment"></a>Implementatie van beleid

Voordat u uw beleid in productie neemt, moet u zorgen voor:
 
- **Serviceaccounts** : identificeer gebruikersaccounts die worden gebruikt als serviceaccounts of apparaten, zoals telefooncellen in vergaderruimtes. Zorg ervoor dat deze accounts sterke wachtwoorden hebben en voeg ze toe aan een uitgesloten groep.
- **Aanmeldingsrapporten** - Bekijk het aanmeldingsrapport en zoek naar **ander klantverkeer.** Identificeer het topgebruik en onderzoek waarom het in gebruik is. Meestal wordt het verkeer gegenereerd door oudere Office-clients die geen moderne verificatie gebruiken, of sommige e-mailapps van derden. Maak een plan om het gebruik van deze apps te verplaatsen of als de impact laag is, laat uw gebruikers weten dat ze deze apps niet meer kunnen gebruiken.
 
Zie Hoe moet u een nieuw beleid implementeren voor meer [informatie?](best-practices.md#how-should-you-deploy-a-new-policy)

## <a name="what-you-should-know"></a>Wat u moet weten

Toegang blokkeren met **andere clients** blokkeert ook Exchange Online PowerShell en Dynamics 365 met behulp van basic auth.

Het configureren van een beleid voor **andere clients** blokkeert de hele organisatie van bepaalde clients zoals SPConnect. Dit blok gebeurt omdat oudere clients op onverwachte manieren verifiëren. Het probleem is niet van toepassing op grote Office-toepassingen zoals de oudere Office-clients.

Het kan tot 24 uur duren voordat het beleid in werking treedt.

U alle beschikbare subsidiebesturingselementen voor de voorwaarde **Overige clients** selecteren. de ervaring van de eindgebruiker is echter altijd hetzelfde - geblokkeerde toegang.

Als u verouderde verificatie blokkeert met de voorwaarde **Andere clients,** u ook de voorwaarde voor het apparaatplatform en de locatie instellen. Als u bijvoorbeeld alleen verouderde verificatie voor mobiele apparaten wilt blokkeren, stelt u de voorwaarde **van apparaatplatforms** in door het gewenste aantal te selecteren:

- Android
- iOS
- Windows Phone

![Beleidsconfiguratie wordt niet ondersteund](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Volgende stappen

- [Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)
- Als u nog niet bekend bent met het configureren van beleid voor voorwaardelijke toegang, [raadpleegt u MFA vereisen voor specifieke apps met Voorwaardelijke toegang voor Azure Active Directory](app-based-mfa.md) voor een voorbeeld.
- Zie [Hoe moderne verificatie werkt voor Office 2013- en Office 2016-clientapps voor](/office365/enterprise/modern-auth-for-office-2013-and-2016) meer informatie over moderne verificatieondersteuning 
