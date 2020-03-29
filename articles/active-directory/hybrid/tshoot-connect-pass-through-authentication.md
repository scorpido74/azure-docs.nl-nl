---
title: 'Azure AD Connect: pass-through-verificatie oplossen | Microsoft Documenten'
description: In dit artikel wordt beschreven hoe u problemen oplossen met Azure Active Directory (Azure AD) Pass-through-verificatie.
services: active-directory
keywords: Problemen met Azure AD Connect Pass-Through Authentication oplossen, Active Directory installeren, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae83cea866367fa6a6596caa683d0287bea96c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456124"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Problemen met Pass Through-verificatie in Azure Active Directory oplossen

In dit artikel vindt u informatie over het oplossen van problemen met veelvoorkomende problemen met azure AD-pass-through-verificatie.

>[!IMPORTANT]
>Als u problemen ondervindt met aanmeldingsproblemen van gebruikers met Pass-through Authentication, schakelt u de functie niet uit of verwijdert u Pass-through Authentication Agents niet zonder dat u een global administrator-account in de cloud hebt om op terug te vallen. Meer informatie over [het toevoegen van een global administrator-account met alleen cloud](../active-directory-users-create-azure-portal.md). Het doen van deze stap is van cruciaal belang en zorgt ervoor dat u niet buitengesloten van uw huurder.

## <a name="general-issues"></a>Algemene problemen

### <a name="check-status-of-the-feature-and-authentication-agents"></a>Status van de functie en verificatieagents controleren

Controleer of de functie Verificatie vooraf nog steeds is **ingeschakeld** op uw tenant en dat de status van verificatieagents **Actief**en niet **Inactief wordt weergegeven.** U de status controleren door naar het **Azure AD Connect-blad** te gaan in het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com/).

![Azure Active Directory-beheercentrum - Azure AD Connect-blad](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory-beheercentrum - Verificatieblad voor doorgeefverificatie](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Aanmeldingsfoutberichten op basis van gebruikers

Als de gebruiker zich niet kan aanmelden bij pass-through-verificatie, ziet hij mogelijk een van de volgende gebruikersgerichte fouten op het aanmeldingsscherm van Azure AD: 

|Fout|Beschrijving|Oplossing
| --- | --- | ---
|AADSTS80001|Kan geen verbinding maken met Active Directory|Controleer of agentservers lid zijn van hetzelfde AD-forest als de gebruikers van wie de wachtwoorden moeten worden gevalideerd en dat ze verbinding kunnen maken met Active Directory.  
|AADSTS8002|Er is een time-out opgetreden bij het maken van verbinding met Active Directory|Controleer of Active Directory beschikbaar is en reageert op verzoeken van de agents.
|AADSTS80004|De gebruikersnaam die aan de agent is doorgegeven, was niet geldig|Zorg ervoor dat de gebruiker probeert in te loggen met de juiste gebruikersnaam.
|AADSTS80005|Validatie ondervonden onvoorspelbare WebException|Een tijdelijke fout. Probeer het verzoek opnieuw. Als dit mislukt, neemt u contact op met microsoft-ondersteuning.
|AADSTS80007|Er is een fout opgetreden bij het communiceren met Active Directory|Controleer de agentlogboeken voor meer informatie en controleer of Active Directory werkt zoals verwacht.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Redenen voor aanmeldingsfout in het Azure Active Directory-beheercentrum (heeft Premium-licentie nodig)

Als aan uw tenant een Azure AD Premium-licentie is gekoppeld, u ook het [aanmeldingsactiviteitsrapport](../reports-monitoring/concept-sign-ins.md) bekijken in het [Azure Active Directory-beheercentrum](https://aad.portal.azure.com/).

![Azure Active Directory-beheercentrum - Rapport Aanmeldingen](./media/tshoot-connect-pass-through-authentication/pta4.png)

Navigeer naar Azure Active -> **Directory-aanmeldingen in** het Azure Active [Directory-beheercentrum](https://aad.portal.azure.com/) en klik op de aanmeldingsactiviteit van een specifieke gebruiker. **Azure Active Directory** Zoek naar het veld **AANMELDINGsFOUTCODE.** De waarde van dat veld toewijzen aan een foutreden en -oplossing met behulp van de volgende tabel:

|Aanmeldingsfoutcode|Reden voor aanmeldingsfout|Oplossing
| --- | --- | ---
| 50144 | Het Active Directory-wachtwoord van de gebruiker is verlopen. | Het wachtwoord van de gebruiker opnieuw instellen in uw on-premises Active Directory.
| 80001 | Er is geen verificatieagent beschikbaar. | Een verificatieagent installeren en registreren.
| 80002 | Er is een time-out opgetreden bij de wachtwoordvalidatie voor de verificatieagent. | Controleer of uw Active Directory bereikbaar is via de verificatieagent.
| 80003 | Ongeldig antwoord ontvangen door de verificatieagent. | Als het probleem consistent reproduceerbaar is voor meerdere gebruikers, controleert u de Active Directory-configuratie.
| 80004 | Onjuiste UPN (user principal name) gebruikt voor aanmeldingsaanvraag. | Vraag de gebruiker om in te loggen met de juiste gebruikersnaam.
| 80005 | Verificatieagent: er is een fout opgetreden. | Tijdelijke fout. Probeer het later opnieuw.
| 80007 | Verificatieagent kan geen verbinding maken met Active Directory. | Controleer of uw Active Directory bereikbaar is via de verificatieagent.
| 80010 | Verificatieagent kan wachtwoord niet ontsleutelen. | Als het probleem consistent reproduceerbaar is, installeert en registreert u een nieuwe verificatieagent. En verwijder de huidige. 
| 80011 | Verificatieagent kan ontsleutelingssleutel hier ophalen. | Als het probleem consistent reproduceerbaar is, installeert en registreert u een nieuwe verificatieagent. En verwijder de huidige.

>[!IMPORTANT]
>Doorgeefverificatie-agenten verifiëren Azure AD-gebruikers door hun gebruikersnamen en wachtwoorden te valideren van Active Directory door de [Win32 LogonUser API aan](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)te roepen. Als u de instelling 'Aanmelden bij' in Active Directory hebt ingesteld om de aanmeldingstoegang voor werkstations te beperken, moet u ook servers die pass-through-verificatieagents hosten, toevoegen aan de lijst met 'Aanmeldingsservers'. Als u dit niet doet, wordt uw gebruikers geblokkeerd om zich aan te melden bij Azure AD.

## <a name="authentication-agent-installation-issues"></a>Installatieproblemen met verificatieagent

### <a name="an-unexpected-error-occurred"></a>Er is een onverwachte fout opgetreden

[Verzamel agentlogboeken](#collecting-pass-through-authentication-agent-logs) van de server en neem contact op met Microsoft Support met uw probleem.

## <a name="authentication-agent-registration-issues"></a>Registratieproblemen van verificatieagent

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Registratie van de verificatieagent is mislukt vanwege geblokkeerde poorten

Controleer of de server waarop de verificatieagent is geïnstalleerd, kan communiceren met onze service-URL's en -poorten die [hier](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)worden vermeld.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Registratie van de verificatieagent is mislukt vanwege token- of accountautorisatiefouten

Controleer of u een global administrator-account in de cloud gebruikt voor alle installatie- en registratiebewerkingen van Azure AD Connect of zelfstandige verificatieagent. Er is een bekend probleem met global administrator-accounts met MFA-ingeschakeld; MFA tijdelijk uitschakelen (alleen om de bewerkingen te voltooien) als tijdelijke oplossing.

### <a name="an-unexpected-error-occurred"></a>Er is een onverwachte fout opgetreden

[Verzamel agentlogboeken](#collecting-pass-through-authentication-agent-logs) van de server en neem contact op met Microsoft Support met uw probleem.

## <a name="authentication-agent-uninstallation-issues"></a>Problemen met de installatie van verificatieagent verwijderen

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Waarschuwingsbericht bij het verwijderen van Azure AD Connect

Als u Verificatie vooraf hebt ingeschakeld op uw tenant en u Azure AD Connect probeert te verwijderen, ziet u het volgende waarschuwingsbericht: "Gebruikers kunnen zich niet aanmelden bij Azure AD, tenzij u andere pass-through-verificatiemedewerkers hebt geïnstalleerd op andere servers."

Controleer of uw installatie [zeer beschikbaar](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) is voordat u Azure AD Connect verwijdert om te voorkomen dat gebruikersaanmelden wordt afgebroken.

## <a name="issues-with-enabling-the-feature"></a>Problemen met het inschakelen van de functie

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>De functie inschakelen is mislukt omdat er geen verificatieagents beschikbaar waren

U moet ten minste één actieve verificatieagent hebben om verificatie vooraf te kunnen doorschakelen op uw tenant. U een verificatieagent installeren door Azure AD Connect of een zelfstandige verificatieagent te installeren.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>De functie inschakelen is mislukt vanwege geblokkeerde poorten

Controleer of de server waarop Azure AD Connect is geïnstalleerd, kan communiceren met onze service-URL's en -poorten die [hier](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)worden vermeld.

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>De functie inschakelen is mislukt vanwege token- of accountautorisatiefouten

Zorg ervoor dat u een global administrator-account in de cloud gebruikt wanneer u de functie inschakelt. Er is een bekend probleem met multi-factor authenticatie (MFA)-enabled Global Administrator accounts; MFA tijdelijk uitschakelen (alleen om de bewerking te voltooien) als tijdelijke oplossing.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Logboeken van doorgeefverificaties verzamelen

Afhankelijk van het type probleem dat u hebt, moet u op verschillende plaatsen zoeken naar logboeken van de doorpasverificatieagent.

### <a name="azure-ad-connect-logs"></a>Azure AD Connect-logboeken

Controleer de Azure AD Connect-logboeken bij **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Gebeurtenislogboeken van verificatieagent

Open de toepassing Logboeken op de server en controleer onder **Toepassings- en servicelogboeken\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**voor fouten met betrekking tot de verificatieagent.

Schakel voor gedetailleerde analyses het logboek 'Sessie' in (klik met de rechtermuisknop in de toepassing Logboeken om deze optie te vinden). Voer de verificatieagent niet uit met dit logboek ingeschakeld tijdens normale bewerkingen. alleen gebruiken voor het oplossen van problemen. De inhoud van de logboekis pas zichtbaar nadat het logboek opnieuw is uitgeschakeld.

### <a name="detailed-trace-logs"></a>Gedetailleerde traceerlogboeken

Als u aanmeldingsfouten van gebruikers wilt oplossen, zoekt u naar traceerlogboeken bij **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\\**. Deze logboeken bevatten redenen waarom een specifieke aanmelding van gebruikers is mislukt met de functie Doorgeefverificatie. Deze fouten worden ook toegewezen aan de aanmeldingsfoutredenen die worden weergegeven in de vorige tabel met aanmeldingsfoutredenen. Hieronder volgt een voorbeeld logboekvermelding:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

U beschrijvende details van de fout ('1328' in het voorgaande voorbeeld) krijgen door de opdrachtprompt te openen en de volgende opdracht uit te voeren (Opmerking: Vervang '1328' door het werkelijke foutnummer dat u in uw logboeken ziet):

`Net helpmsg 1328`

![Pass-through-verificatie](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Logboeken domeincontroller

Als controlelogboekregistratie is ingeschakeld, kan aanvullende informatie worden gevonden in de beveiligingslogboeken van uw domeincontrollers. Een eenvoudige manier om aanmeldingsverzoeken op te vragen die door Pass-through Authentication Agents worden verzonden, is als volgt:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Prestatiemetertellers

Een andere manier om verificatieagents te controleren, is door specifieke prestatiemetertellers bij te houden op elke server waar de verificatieagent is geïnstalleerd. Gebruik de volgende globale tellers (**# PTA-verificaties,** **#PTA mislukte verificaties** en **#PTA geslaagde verificaties**) en fouttellers (**# PTA-verificatiefouten):**

![Frequentiemetertellers voor verificatieverificatie doorgeven](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Pass-through Authentication biedt hoge beschikbaarheid met behulp van meerdere verificatieagents en _niet_ met taakverdeling. Afhankelijk van uw configuratie ontvangen _niet_ al uw verificatiemedewerkers ongeveer _hetzelfde_ aantal aanvragen. Het is mogelijk dat een specifieke verificatieagent helemaal geen verkeer ontvangt.
