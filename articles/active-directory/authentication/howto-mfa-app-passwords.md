---
title: App-wacht woorden voor Azure Multi-Factor Authentication-Azure Active Directory configureren
description: Meer informatie over het configureren en gebruiken van app-wacht woorden voor oudere toepassingen in azure Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b247b64d563bc2b12c5bffff6a460d77cb96207
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485600"
---
# <a name="enable-and-use-azure-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Azure Multi-Factor Authentication met verouderde toepassingen inschakelen en gebruiken met behulp van app-wacht woorden

Sommige toepassingen, zoals Office 2010 of eerder en Apple mail vóór iOS 11, bieden geen ondersteuning voor multi-factor Authentication. De apps zijn niet geconfigureerd om een tweede vorm van verificatie of prompt te accepteren. Als u deze toepassingen op een veilige manier wilt gebruiken met Azure Multi-Factor Authentication ingeschakeld voor gebruikers accounts, kunt u app-wacht woorden gebruiken. Met deze app-wacht woorden is het traditionele wacht woord vervangen zodat een app multi-factor Authentication omzeilt en goed werkt.

Moderne verificatie wordt ondersteund voor de Microsoft Office 2013-clients en hoger. Office 2013-clients, waaronder Outlook, ondersteunen moderne verificatie protocollen en kunnen worden ingeschakeld om te werken met verificatie in twee stappen. Nadat de client is ingeschakeld, zijn app-wacht woorden niet vereist voor de client.

In dit artikel wordt beschreven hoe u app-wacht woorden inschakelt en gebruikt voor oudere toepassingen die geen ondersteuning bieden voor multi-factor Authentication-prompts.

>[!NOTE]
> App-wacht woorden werken niet met voorwaardelijke toegang op basis van multi-factor Authentication-beleid en moderne verificatie.

## <a name="overview-and-considerations"></a>Overzicht en overwegingen

Wanneer een gebruikers account is ingeschakeld voor Azure Multi-Factor Authentication, wordt de reguliere aanmeldings prompt onderbroken door een aanvraag voor aanvullende verificatie. Sommige oudere toepassingen begrijpen dit onderbreken niet in het aanmeldings proces, zodat de verificatie mislukt. Als u de beveiliging van gebruikers accounts wilt behouden en Azure Multi-Factor Authentication wilt verlaten, kunnen app-wacht woorden worden gebruikt in plaats van de normale gebruikers naam en het wacht woord van de gebruiker. Wanneer een app-wacht woord tijdens het aanmelden wordt gebruikt, is er geen extra verificatie prompt, dus de verificatie is geslaagd.

App-wacht woorden worden automatisch gegenereerd, niet opgegeven door de gebruiker. Met deze automatisch gegenereerd wacht woord is het moeilijker voor een aanvaller om te raden. Dit is veiliger. Gebruikers hoeven de wacht woorden niet bij te houden of ze elke keer in te voeren wanneer app-wacht woorden slechts eenmaal per toepassing worden ingevoerd.

Wanneer u app-wacht woorden gebruikt, zijn de volgende overwegingen van toepassing:

* Er is een limiet van 40 app-wacht woorden per gebruiker.
* Toepassingen die wacht woorden in de cache opslaan en gebruiken in on-premises scenario's, kunnen mislukken omdat het app-wacht woord niet bekend is buiten het werk-of school account. Een voor beeld van dit scenario is Exchange-e-mail berichten die on-premises zijn, maar de gearchiveerde e-mail bevindt zich in de Cloud. In dit scenario werkt hetzelfde wacht woord niet.
* Nadat Azure Multi-Factor Authentication is ingeschakeld op het account van een gebruiker, kunnen app-wacht woorden worden gebruikt met de meeste niet-browser-clients, zoals Outlook en micro soft Skype voor bedrijven. Beheer acties kunnen echter niet worden uitgevoerd met behulp van app-wacht woorden via niet-browser toepassingen, zoals Windows Power shell. De acties kunnen niet worden uitgevoerd, zelfs niet wanneer de gebruiker een Administrator-account heeft.
    * Als u Power shell-scripts wilt uitvoeren, maakt u een service account met een sterk wacht woord en schakelt u het account niet in voor verificatie in twee stappen.

>[!WARNING]
> App-wacht woorden werken niet in hybride omgevingen waar clients communiceren met zowel lokale als Cloud-eind punten voor automatische detectie. Domein wachtwoorden zijn vereist voor de verificatie van on-premises. App-wacht woorden zijn vereist voor verificatie met de Cloud.

### <a name="app-password-names"></a>Namen van app-wacht woorden

De namen van app-wacht woorden moeten overeenkomen met het apparaat waarop ze worden gebruikt. Als u een laptop hebt met niet-browser toepassingen zoals Outlook, Word en Excel, maakt u één app-wacht woord met de naam **laptop** voor deze apps. Maak een ander app-wacht woord met de naam **bureau blad** voor dezelfde toepassingen die op uw desktop computer worden uitgevoerd.

Het is raadzaam om één app-wacht woord per apparaat te maken, in plaats van een app-wacht woord per toepassing.

## <a name="federated-or-single-sign-on-app-passwords"></a>App-wacht woorden voor federatieve of eenmalige aanmelding

Azure AD ondersteunt Federatie of eenmalige aanmelding (SSO) met on-premises Active Directory Domain Services (AD DS). Als uw organisatie Federated is met Azure AD en u Azure Multi-Factor Authentication gebruikt, zijn de volgende overwegingen voor het app-wacht woord van toepassing:

>[!NOTE]
> De volgende punten zijn alleen van toepassing op federatieve klanten (SSO).

* App-wacht woorden worden geverifieerd door Azure AD, waardoor de Federatie niet kan worden omzeild. Federatie wordt actief alleen gebruikt bij het instellen van app-wacht woorden.
* Er wordt geen contact gemaakt met de ID-provider (IdP) voor federatieve gebruikers (SSO), in tegens telling tot de passieve stroom. De app-wacht woorden worden opgeslagen in het werk-of school account. Als een gebruiker het bedrijf verlaat, wordt de informatie van de gebruiker naar het werk-of school account stromen met behulp van **DirSync** in realtime. Het uitschakelen of verwijderen van het account kan tot drie uur duren, waardoor het uitschakelen/verwijderen van het app-wacht woord in azure AD kan worden vertraagd.
* On-premises Client Access Control instellingen worden niet gehonoreerd door de functie voor app-wacht woorden.
* Er is geen logboek registratie voor on-premises verificatie of controle beschikbaar met de functie voor app-wacht woorden.

Voor sommige geavanceerde architecturen is een combi natie van referenties vereist voor multi-factor Authentication met clients. Deze referenties kunnen een gebruikers naam en wacht woord voor een werk-of school account bevatten en app-wacht woorden. De vereisten zijn afhankelijk van de manier waarop de verificatie wordt uitgevoerd. Voor clients die worden geverifieerd aan de hand van een on-premises infra structuur, wordt de gebruikers naam en het wacht woord van een werk-of school account vereist. Voor clients die met Azure AD worden geverifieerd, is een app-wacht woord vereist.

Stel bijvoorbeeld dat u de volgende architectuur hebt:

* Uw on-premises exemplaar van Active Directory is federatief met Azure AD.
* U gebruikt Exchange Online.
* U gebruikt Skype voor bedrijven on-premises.
* U gebruikt Azure Multi-Factor Authentication.

In dit scenario gebruikt u de volgende referenties:

* Als u zich wilt aanmelden bij Skype voor bedrijven, gebruikt u de gebruikers naam en het wacht woord van uw werk-of school account.
* Gebruik een app-wacht woord om toegang te krijgen tot het adres boek van een Outlook-client die verbinding maakt met Exchange Online.

## <a name="allow-users-to-create-app-passwords"></a>Gebruikers toestaan app-wacht woorden te maken

Standaard kunnen gebruikers geen app-wacht woorden maken. De functie voor het maken van app-wacht woorden moet zijn ingeschakeld voordat gebruikers deze kunnen gebruiken. Voer de volgende stappen uit om gebruikers de mogelijkheid te geven om app-wacht woorden te maken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek en selecteer **Azure Active Directory**en kies vervolgens **gebruikers**.
3. Selecteer **multi-factor Authentication** in de navigatie balk aan de bovenkant van het venster *gebruikers* .
4. Onder Multi-Factor Authentication selecteert u **Service-instellingen**.
5. Selecteer op de pagina **Service-instellingen** de optie **gebruikers toestaan app-wacht woorden te maken om zich aan te melden bij niet-browser-apps** .

    ![Scherm afbeelding van de Azure Portal waarin de service-instellingen voor multi-factor Authentication worden weer gegeven om de gebruiker van app-wacht woorden toe te staan](media/concept-authentication-methods/app-password-authentication-method.png)

## <a name="create-an-app-password"></a>Een app-wacht woord maken

Wanneer gebruikers hun initiële registratie voor Azure Multi-Factor Authentication volt ooien, is er een optie voor het maken van app-wacht woorden aan het einde van het registratie proces.

Gebruikers kunnen ook app-wacht woorden maken na de registratie. Zie [Wat zijn app-wacht woorden in Azure multi-factor Authentication?](../user-help/multi-factor-authentication-end-user-app-passwords.md) voor meer informatie en gedetailleerde stappen voor uw gebruikers.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het toestaan van gebruikers om snel te registreren voor Azure Multi-Factor Authentication, [overzicht van registratie van gecombineerde beveiligings gegevens](concept-registration-mfa-sspr-combined.md).
