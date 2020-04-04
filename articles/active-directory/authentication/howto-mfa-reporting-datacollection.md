---
title: Azure MFA-gebruikersgegevensverzameling - Azure Active Directory
description: Welke informatie wordt gebruikt om gebruikers te authenticeren door Azure Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f3b5af972ad6dd15b7c992d5e264ede97bd1dde
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653634"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Azure Multi-Factor Authentication-gebruikersgegevensverzameling

In dit document wordt uitgelegd hoe u gebruikersgegevens vinden die zijn verzameld door Azure Multi-Factor Authentication Server (MFA Server) en Azure MFA (Cloud-based) in de gebeurtenis die u wilt verwijderen.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Verzamelde informatie

MFA Server, de NPS-extensie en de Windows Server 2016 Azure MFA AD FS-adapter verzamelen en opslaan gedurende 90 dagen de volgende informatie.

Verificatiepogingen (gebruikt voor rapportage en probleemoplossing):

- Tijdstempel
- Gebruikersnaam
- Voornaam
- Achternaam
- E-mailadres
- Gebruikersgroep
- Verificatiemethode (telefoongesprek, sms, mobiele app, OATH-token)
- Telefoongespreksmodus (standaard, pincode)
- Richting sms-berichten (eenrichtingsverkeer, tweerichtingsverkeer)
- Tekstberichtmodus (OTP, OTP + PINCODE)
- Modus mobiele app (standaard, pincode)
- OATH-tokenmodus (standaard, pincode)
- Verificatietype
- Naam van de toepassing
- Primaire oproeplandcode
- Telefoonnummer primaire oproep
- Primaire oproepextensie
- Primaire oproep geverifieerd
- Resultaat primaire oproep
- Back-up van oproeplandcode
- Telefoonnummer back-up
- Back-upoproepextensie
- Back-upgesprek geverifieerd
- Resultaat van back-upgesprek
- Algemeen geverifieerd
- Totaalresultaat
- Resultaten
- Geverifieerd
- Resultaat
- IP-adres initiëren
- Apparaten
- Apparaattoken
- Apparaattype
- Versie van mobiele app
- Versie van het besturingssysteem
- Resultaat
- Gebruikte controle op melding

Activeringen (pogingen om een account te activeren in de mobiele Microsoft Authenticator-app):
- Gebruikersnaam
- Accountnaam
- Tijdstempel
- Resultaat activeringscode
- Succes activeren
- Fout activeren
- Resultaat activeringsstatus
- Apparaatnaam
- Apparaattype
- App-versie
- OATH-token ingeschakeld

Blokken (gebruikt om de geblokkeerde status te bepalen en voor rapportage):

- Tijdstempel blokkeren
- Blokkeren op gebruikersnaam
- Gebruikersnaam
- Landnummer
- Telefoonnummer
- Telefoonnummer opgemaakt
- Toestelnummer
- Schone extensie
- Geblokkeerd
- Reden blokkeren
- Tijdstempel voltooiing
- Voltooiing Reden
- Accountvergrendeling
- Fraudewaarschuwing
- Fraude alert niet geblokkeerd
- Taal

Omleidingen (gebruikt voor rapportage):

- Tijdstempel omzeilen
- Seconden omzeilen
- Omzeilen op gebruikersnaam
- Gebruikersnaam
- Landnummer
- Telefoonnummer
- Telefoonnummer opgemaakt
- Toestelnummer
- Schone extensie
- Reden omzeilen
- Tijdstempel voltooiing
- Voltooiing Reden
- Bypass gebruikt

Wijzigingen (gebruikt om wijzigingen van gebruikers in MFA Server of Azure AD te synchroniseren):

- Tijdstempel wijzigen
- Gebruikersnaam
- Nieuwe landcode
- Nieuw telefoonnummer
- Nieuwe extensie
- Nieuwe back-uplandcode
- Nieuw back-uptelefoonnummer
- Nieuwe back-upextensie
- Nieuwe pincode
- Pincodewijziging vereist
- Oud apparaattoken
- Nieuw apparaattoken

## <a name="gather-data-from-mfa-server"></a>Gegevens van MFA-server verzamelen

Met het volgende proces kunnen beheerders met het volgende proces alle gegevens voor gebruikers exporteren:

- Meld u aan bij uw MFA-server, navigeer naar het tabblad **Gebruikers,** selecteer de betreffende gebruiker en klik op de knop **Bewerken.** Maak screenshots (Alt-PrtScn) van elk tabblad om de gebruiker hun huidige MFA-instellingen te bieden.
- Voer vanaf de opdrachtregel van de MFA-server de volgende `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` opdracht uit waarbij het pad volgens uw installatie wordt gewijzigd om een JSON-geformatteerd bestand te produceren.
- Beheerders kunnen de webservice SDK GetUserGdpr-bewerking ook gebruiken als een optie om alle MFA-cloudservicegegevens die voor een bepaalde gebruiker zijn verzameld, te exporteren of op te nemen in een grotere rapportageoplossing.
- Zoek `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` en eventuele back-ups voor "\<gebruikersnaam>" (neem de aanhalingstekens in de zoekopdracht) om alle exemplaren van de gebruikersrecord te vinden die worden toegevoegd of gewijzigd.
   - Deze records kunnen worden beperkt (maar niet geëlimineerd) door het uitvinken van **"Wijzigingen van de gebruiker van het logboek"** in de MFA Server UX, logging sectie, Log Files tabblad.
   - Als syslog is geconfigureerd en **'Wijzigingen van de logboekgebruiker'** is aangevinkt in de MFA Server UX, Logging sectie, Syslog tabblad, dan kunnen de logboekvermeldingen worden verzameld uit syslog plaats.
- Andere gebeurtenissen van de gebruikersnaam in MultiFactorAuthSvc.log en andere MFA Server logbestanden met betrekking tot authenticatie pogingen worden beschouwd als operationeel en duplicatief voor de informatie die met behulp van MultiFactorAuthGdpr.exe export of Web Service SDK GetUserGdpr.

## <a name="delete-data-from-mfa-server"></a>Gegevens verwijderen uit MFA-server

Voer vanaf de opdrachtregel van de MFA-server de volgende `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` opdracht uit om het pad volgens uw installatie te wijzigen om alle MFA-cloudservicegegevens te verwijderen die voor deze gebruiker zijn verzameld.

- Gegevens die in de export zijn opgenomen, worden in realtime verwijderd, maar het kan tot 30 dagen duren voordat operationele of dubbele gegevens volledig zijn verwijderd.
- Beheerders kunnen ook de webservice SDK DeleteUserGdpr-bewerking gebruiken als een optie om alle MFA-cloudservicegegevens die voor een bepaalde gebruiker zijn verzameld, te verwijderen of op te nemen in een grotere rapportageoplossing.

## <a name="gather-data-from-nps-extension"></a>Gegevens verzamelen van NPS-extensie

Gebruik de [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een verzoek tot exporteren in te dienen.

- MFA-informatie is opgenomen in de export, wat uren of dagen kan duren.
- Exemplaren van de gebruikersnaam in de gebeurtenislogboeken AzureMfa/AuthN/AuthNOptCh, AzureMfa/AuthZ/AuthZAdminCh en AzureMfa/AuthZ/AuthZOptCh worden beschouwd als operationeel en dubbelvoorde van de informatie die in de export wordt verstrekt.

## <a name="delete-data-from-nps-extension"></a>Gegevens verwijderen uit NPS-extensie

Gebruik de [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een verzoek aan te vragen voor Account Close om alle MFA-cloudservicegegevens te verwijderen die voor deze gebruiker zijn verzameld.

- Het kan tot 30 dagen duren voordat gegevens volledig zijn verwijderd.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Gegevens verzamelen van Windows Server 2016 Azure MFA AD FS-adapter

Gebruik de [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een verzoek tot exporteren in te dienen. 

- MFA-informatie is opgenomen in de export, wat uren of dagen kan duren.
- Gebeurtenissen van de gebruikersnaam in de ad fs-logboeken voor tracering/foutopsporing (indien ingeschakeld) worden beschouwd als operationeel en dubbelend voor de informatie die in de export wordt verstrekt.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Gegevens verwijderen uit Windows Server 2016 Azure MFA AD FS-adapter

Gebruik de [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een verzoek aan te vragen voor Account Close om alle MFA-cloudservicegegevens te verwijderen die voor deze gebruiker zijn verzameld.

- Het kan tot 30 dagen duren voordat gegevens volledig zijn verwijderd.

## <a name="gather-data-for-azure-mfa"></a>Gegevens verzamelen voor Azure MFA

Gebruik de [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een verzoek tot exporteren in te dienen.

- MFA-informatie is opgenomen in de export, wat uren of dagen kan duren.

## <a name="delete-data-for-azure-mfa"></a>Gegevens voor Azure MFA verwijderen

Gebruik de [Microsoft Privacy Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) om een verzoek aan te vragen voor Account Close om alle MFA-cloudservicegegevens te verwijderen die voor deze gebruiker zijn verzameld.

- Het kan tot 30 dagen duren voordat gegevens volledig zijn verwijderd.

## <a name="next-steps"></a>Volgende stappen

[MFA-serverrapportage](howto-mfa-reporting.md)
