---
title: Problemen met Enterprise State Roaming in Azure Active Directory oplossen
description: Geeft antwoorden op een aantal vragen die IT-beheerders kunnen hebben over instellingen en het synchroniseren van app-gegevens.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae8ce24aeb665a7f99326e83dbe18d020e1b6196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672354"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Problemen met de roaming-instellingen voor bedrijfsstatus in Azure Active Directory oplossen

In dit onderwerp vindt u informatie over het oplossen en diagnosticeren van problemen met Enterprise State Roaming en wordt een lijst met bekende problemen weergegeven.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Dit artikel is van toepassing op de Microsoft Edge Legacy HTML-gebaseerde browser die in juli 2015 met Windows 10 is gelanceerd. Het artikel is niet van toepassing op de nieuwe Microsoft Edge Chromium-gebaseerde browser uitgebracht op 15 januari 2020. Zie het artikel Microsoft Edge Sync voor meer informatie over het gedrag Synchroniseren voor de nieuwe Microsoft [Edge.](/deployedge/microsoft-edge-enterprise-sync)

## <a name="preliminary-steps-for-troubleshooting"></a>Voorbereidende stappen voor het oplossen van problemen 

Voordat u begint met het oplossen van problemen, controleert u of de gebruiker en het apparaat correct zijn geconfigureerd en of aan alle vereisten van Enterprise State Roaming is voldaan door het apparaat en de gebruiker. 

1. Windows 10, met de nieuwste updates, en een minimum versie 1511 (OS Build 10586 of hoger) is geïnstalleerd op het apparaat. 
1. Het apparaat is Azure AD lid geworden of hybride Azure AD toegetreden. Zie voor meer informatie [hoe u een apparaat onder de controle van Azure AD krijgt.](overview.md)
1. Controleer of **Enterprise State Roaming** is ingeschakeld voor de tenant in Azure AD, zoals beschreven in Enterprise State [Roaming](enterprise-state-roaming-enable.md)inschakelen . U roaming inschakelen voor alle gebruikers of voor slechts een geselecteerde groep gebruikers.
1. De gebruiker krijgt een Azure Active Directory Premium-licentie toegewezen.  
1. Het apparaat moet opnieuw worden opgestart en de gebruiker moet zich opnieuw aanmelden om toegang te krijgen tot enterprise state roaming-functies.

## <a name="information-to-include-when-you-need-help"></a>Benodigde informatie om snel te worden geholpen
Als u uw probleem niet oplossen met de onderstaande richtlijnen, u contact opnemen met onze ondersteuningstechnici. Wanneer u contact met hen opneemt, moet u de volgende informatie opnemen:

* **Algemene beschrijving van de fout:** Zijn er foutmeldingen gezien door de gebruiker? Als er geen foutmelding is geweest, beschrijf dan het onverwachte gedrag dat u opmerkte, in detail. Welke functies zijn ingeschakeld voor synchronisatie en wat verwacht de gebruiker te synchroniseren? Zijn meerdere functies niet synchroniseren of is het geïsoleerd om een?
* **Getroffen gebruikers** : werkt/mislukt de synchronisatie voor één gebruiker of meerdere gebruikers? Om hoeveel apparaten gaat het per gebruiker? Zijn ze allemaal niet synchroniseren of zijn sommige van hen synchroniseren en sommige niet synchroniseren?
* **Informatie over de gebruiker** : welke identiteit gebruikt de gebruiker om zich aan te melden bij het apparaat? Hoe meldt de gebruiker zich bij het apparaat? Maken ze deel uit van een geselecteerde beveiligingsgroep die mag synchroniseren? 
* **Informatie over het apparaat** : is dit apparaat dat is aangesloten bij Azure AD of met een domein verbonden? Op welke build staat het apparaat? Wat zijn de meest recente updates?
* **Datum / Tijd / Tijdzone** - Wat was de precieze datum en tijd zag je de fout (inclusief de tijdzone)?

Het opnemen van deze informatie helpt ons om uw probleem zo snel mogelijk op te lossen.

## <a name="troubleshooting-and-diagnosing-issues"></a>Het oplossen en diagnosticeren van problemen

In dit gedeelte vindt u suggesties voor het oplossen en diagnosticeren van problemen in verband met Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Synchronisatie verifiëren en de instellingenpagina 'Uw instellingen synchroniseren' 

1. Nadat u uw Windows 10-pc hebt aangesloten bij een domein dat is geconfigureerd om Enterprise State Roaming toe te staan, meldt u zich aan met uw werkaccount. Ga naar **Instellingen** > **Accounts** > **Synchroniseer je instellingen** en bevestig dat de synchronisatie en de afzonderlijke instellingen zijn ingeschakeld en dat de bovenkant van de instellingenpagina aangeeft dat je synchroniseert met je werkaccount. Controleer of hetzelfde account ook wordt gebruikt als je aanmeldingsaccount in **Instellingen** > **accounts** > **Je Gegevens**. 
1. Controleer of synchronisatie werkt op meerdere machines door enkele wijzigingen aan te brengen op de oorspronkelijke machine, zoals het verplaatsen van de taakbalk naar de rechter- of bovenzijde van het scherm. Bekijk hoe de wijziging zich binnen vijf minuten voortplant naar de tweede machine. 

   * Het vergrendelen en ontgrendelen van het scherm (Win + L) kan helpen bij het activeren van een synchronisatie.
   * U moet zich aanmelden met hetzelfde account op beide pc's voor synchronisatie naar werk , omdat Enterprise State Roaming is gekoppeld aan het gebruikersaccount en niet aan het machineaccount.

**Mogelijk probleem:** als de besturingselementen op de pagina **Instellingen** niet beschikbaar zijn en u het bericht 'Sommige Windows-functies zijn alleen beschikbaar als u een Microsoft-account of werkaccount gebruikt'. Dit probleem kan zich voordoen voor apparaten die zijn ingesteld om te worden samengevoegd en geregistreerd bij Azure AD, maar het apparaat is nog niet geverifieerd naar Azure AD. Een mogelijke oorzaak is dat het apparaatbeleid moet worden toegepast, maar deze toepassing gebeurt asynchroon en kan met een paar uur worden vertraagd. 

### <a name="verify-the-device-registration-status"></a>De registratiestatus van het apparaat controleren

Enterprise State Roaming vereist dat het apparaat is geregistreerd bij Azure AD. Hoewel niet specifiek voor Enterprise State Roaming, kan het volgen van de onderstaande instructies helpen bevestigen dat de Windows 10-client is geregistreerd en duimafdruk, URL van Azure AD-instellingen, NGC-status en andere informatie bevestigen.

1. Open de opdrachtprompt niet-verhoogd. Open hiervoor de startlauncher (Win + R) en typ 'cmd' om te openen in Windows.
1. Zodra de opdrachtprompt is geopend, typt u "*dsregcmd.exe /status*".
1. Voor de verwachte uitvoer moet de veldwaarde **AzureAdJoined** 'JA' zijn, de veldwaarde **Van WamDefaultSet** moet 'JA' zijn en moet de **veldwaarde WamDefaultGUID** aan het einde een GUID zijn met '(AzureAd)'.

**Mogelijk probleem**: **WamDefaultSet** en **AzureAdJoined** hebben beide 'NEE' in de veldwaarde, het apparaat is verbonden met het domein en geregistreerd bij Azure AD en het apparaat synchroniseert niet. Als dit wordt weergegeven, moet het apparaat mogelijk wachten tot het beleid is toegepast of is de verificatie voor het apparaat mislukt wanneer het verbinding maakt met Azure AD. Het kan zijn dat de gebruiker een paar uur moet wachten voordat het beleid wordt toegepast. Andere stappen voor het oplossen van problemen kunnen bestaan uit het opnieuw proberen van automatische registratie door u af te melden en weer in te sluiten, of de taak te starten in Taakplanner. In sommige gevallen kan het uitvoeren van "*dsregcmd.exe /leave*" in een verhoogd opdrachtpromptvenster, opnieuw opstarten en opnieuw registreren proberen helpen bij dit probleem.

**Mogelijk probleem:** het veld voor **SettingsUrl** is leeg en het apparaat synchroniseert niet. Mogelijk is de gebruiker voor het laatst aangemeld bij het apparaat voordat Enterprise State Roaming is ingeschakeld in de Azure Active Directory Portal. Start het apparaat opnieuw op en laat de gebruiker inloggen. Probeer de IT-beheerder in de portal te laten navigeren naar **Azure Active Directory** > **Devices** > **Enterprise State Roaming** en schakel instellingen **en app-gegevens**op verschillende apparaten opnieuw in. Zodra het apparaat opnieuw is ingeschakeld, start u het apparaat opnieuw op en laat u de gebruiker inloggen. Als dit het probleem niet oplost, kan **SettingsUrl** leeg zijn als er een slecht apparaatcertificaat is. In dit geval kan het uitvoeren van "*dsregcmd.exe /leave*" in een verhoogd opdrachtpromptvenster, opnieuw opstarten en opnieuw registreren proberen helpen bij dit probleem.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming voor ondernemingsstatus en multifactorauthenticatie 

Onder bepaalde voorwaarden kan Enterprise State Roaming gegevens niet synchroniseren als Azure Multi-Factor Authentication is geconfigureerd. Zie het ondersteuningsdocument [KB3193683](https://support.microsoft.com/kb/3193683)voor meer informatie over deze symptomen. 

**Mogelijk probleem:** als uw apparaat is geconfigureerd om multifactorauthenticatie op de Azure Active Directory-portal te vereisen, u de instellingen mogelijk niet synchroniseren terwijl u zich aanmeldt bij een Windows 10-apparaat met een wachtwoord. Dit type multi-factor authenticatie configuratie is bedoeld om een Azure-beheerdersaccount te beschermen. Beheerders gebruikers kunnen nog steeds synchroniseren door zich aan te melden bij hun Windows 10-apparaten met hun Microsoft Passport for Work-pincode of door multi-factorverificatie in te vullen terwijl ze toegang krijgen tot andere Azure-services zoals Office 365.

**Mogelijk probleem:** Synchronisatie kan mislukken als de beheerder het beleid voor voorwaardelijke toegangsbeleid voor multifactorauthenticatie van Active Directory Federation Services configureert en het toegangstoken op het apparaat verloopt. Zorg ervoor dat u zich afmeldt en afmeldt met de pincode voor Microsoft Passport for Work of multifactorauthenticatie voltooien terwijl u toegang hebt tot andere Azure-services zoals Office 365.

### <a name="event-viewer"></a>Logboeken

Voor geavanceerde probleemoplossing kan Logboeken worden gebruikt om specifieke fouten te vinden. Deze zijn gedocumenteerd in de onderstaande tabel. De gebeurtenissen zijn te vinden onder Logboeken >-logboeken voor toepassingen en services > **Microsoft** > **Windows** > **SettingSync-Azure** en voor identiteitsgerelateerde problemen met synchronisatie van **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Bekende problemen

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchroniseren werkt niet op apparaten waarop apps aan de zijkant zijn geladen met MDM-software

Is van invloed op apparaten waarop de Windows 10 Anniversary Update wordt uitgevoerd (versie 1607). In Logboeken onder de logboeken InstellingSync-Azure wordt de gebeurtenis-id 6013 met fout 80070259 vaak gezien.

**Aanbevolen actie**  
Zorg ervoor dat de Windows 10 v1607-client de cumulatieve update van 23 augustus 2016 heeft ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Internet Explorer-favorieten worden niet gesynchroniseerd

Dit is van invloed op apparaten waarop de Windows 10 November Update wordt uitgevoerd (versie 1511).

**Aanbevolen actie**  
Zorg ervoor dat de Windows 10 v1511-client de cumulatieve update van juli 2016 heeft[(KB3172985](https://support.microsoft.com/kb/3172985) OS Build 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Thema wordt niet gesynchroniseerd, evenals gegevens die worden beschermd met Windows Information Protection 

Om gegevenslekken te voorkomen, worden gegevens die zijn beveiligd met [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) niet gesynchroniseerd via Enterprise State Roaming voor apparaten die de Windows 10 Anniversary Update gebruiken.

**Aanbevolen actie**  
Geen. Toekomstige updates voor Windows kunnen dit probleem oplossen.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Datum-, tijd- en regio-instellingen worden niet gesynchroniseerd op het apparaat dat is verbonden met een domein 
  
Apparaten die zijn verbonden met een domein, ervaren geen synchronisatie voor de instelling Datum, Tijd en Regio: automatische tijd. Het gebruik van automatische tijd kan de andere instellingen voor datum, tijd en regio overschrijven en ervoor zorgen dat deze instellingen niet worden gesynchroniseerd. 

**Aanbevolen actie**  
Geen. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>UAC-vragen bij het synchroniseren van wachtwoorden

Is van invloed op apparaten waarop de Windows 10 November Update (versie 1511) wordt uitgevoerd, met een draadloze NIC die is geconfigureerd om wachtwoorden te synchroniseren.

**Aanbevolen actie**  
Zorg ervoor dat de Windows 10 v1511-client de cumulatieve update heeft[(KB3140743](https://support.microsoft.com/kb/3140743) OS Build 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchroniseren werkt niet op apparaten die smartcard gebruiken om in te loggen

Als u zich met een smartcard of virtuele smartcard bij uw Windows-apparaat probeert aan te melden, werkt de synchronisatie van de instellingen niet meer.     

**Aanbevolen actie**  
Geen. Toekomstige updates voor Windows kunnen dit probleem oplossen.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Apparaat dat is verbonden met een domein synchroniseert niet nadat het bedrijfsnetwerk is verlaten     

Domeingebonden apparaten die zijn geregistreerd bij Azure AD kunnen synchronisatiefouten ondervinden als het apparaat langere tijd niet op locatie is en domeinverificatie niet kan worden voltooid.

**Aanbevolen actie**  
Sluit het apparaat aan op een bedrijfsnetwerk, zodat de synchronisatie kan worden hervat.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD Joined-apparaat wordt niet gesynchroniseerd en de gebruiker heeft een gebruikersnaam voor gemengde letters.

Als de gebruiker een upn met gemengde behuizing heeft (bijvoorbeeld UserName in plaats van gebruikersnaam) en de gebruiker zich op een Azure AD Joined-apparaat bevindt, dat is geüpgraded van Windows 10 Build 10586 naar 14393, kan het apparaat van de gebruiker niet worden gesynchroniseerd. 

**Aanbevolen actie**  
De gebruiker moet de join losmaken en opnieuw aansluiten bij het apparaat in de cloud. Meld u hiervoor aan als gebruiker van de lokale beheerder en schakelt de verbinding met het apparaat uit door naar > **Instellingensysteem** > **Over** te gaan en selecteer 'Beheren of loskoppelen van werk of school'. **Settings** Ruim de onderstaande bestanden op en vervolgens Word lid van Azure AD Opnieuw lid van het apparaat in > **Instellingensysteem** > **Over** en selecteer 'Verbinding maken met werk of school'. **Settings** Blijf het apparaat aansluiten bij Azure Active Directory en voltooi de stroom.

Ruim in de opruimstap de volgende bestanden op:
- Instellingen.dat in`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Alle bestanden onder de map`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>Gebeurtenis-id 6065: 80070533 Deze gebruiker kan zich niet aanmelden omdat dit account momenteel is uitgeschakeld  

In Logboeken onder de logboeken InstellingSync/Foutopsporing kan deze fout worden waargenomen wanneer de referenties van de gebruiker zijn verlopen. Bovendien kan dit gebeuren wanneer de tenant niet automatisch AzureRMS heeft ingericht. 

**Aanbevolen actie**  
Laat de gebruiker in het eerste geval zijn referenties bijwerken en inloggen op het apparaat met de nieuwe referenties. Als u het AzureRMS-probleem wilt oplossen, gaat u verder met de stappen in [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>Gebeurtenis-id 1098: fout: 0xCAA5001C Token-brokerbewerking is mislukt  

In Logboeken onder de AAD/Operationele logboeken kan deze fout worden weergegeven met gebeurtenis 1104: AAD Cloud AP-plug-ingesprek Token geretourneerde fout: 0xC000005F. Dit probleem treedt op als er geen machtigingen of eigendomskenmerken zijn.  

**Aanbevolen actie**  
Ga verder met de stappen die [KB3196528](https://support.microsoft.com/kb/3196528)worden vermeld .  

## <a name="next-steps"></a>Volgende stappen

Zie het [roamingoverzicht enterprise state voor](enterprise-state-roaming-overview.md)een overzicht van de bedrijfsstatus .
