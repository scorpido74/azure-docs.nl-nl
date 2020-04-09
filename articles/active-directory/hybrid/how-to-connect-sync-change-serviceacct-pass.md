---
title: 'Azure AD Connect-synchronisatie: het ADSync-serviceaccount wijzigen | Microsoft Documenten'
description: In dit onderwerpdocument wordt de versleutelingssleutel beschreven en hoe u deze verlaten nadat het wachtwoord is gewijzigd.
services: active-directory
keywords: Azure AD-synchronisatieserviceaccount, wachtwoord
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b2a0d0b77b6db481b13785907a1359d2bbe3e9b
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984501"
---
# <a name="changing-the-adsync-service-account-password"></a>Het wachtwoord van het ADSync-serviceaccount wijzigen
Als u het wachtwoord van het ADSync-serviceaccount wijzigt, kan de synchronisatieservice niet correct worden gestart totdat u de versleutelingssleutel hebt verlaten en het wachtwoord van het ADSync-serviceaccount opnieuw hebt geïnitialiseerd. 

Azure AD Connect gebruikt als onderdeel van de synchronisatieservices een versleutelingssleutel om de wachtwoorden van het AD DS-connector-account en het ADSync-serviceaccount op te slaan.  Deze accounts worden versleuteld voordat ze in de database worden opgeslagen. 

De gebruikte versleutelingssleutel wordt beveiligd met [Windows Data Protection (DPAPI).](https://msdn.microsoft.com/library/ms995355.aspx) DPAPI beschermt de versleutelingssleutel met behulp van het **ADSync-serviceaccount.** 

Als u het wachtwoord van het serviceaccount moet wijzigen, u de procedures gebruiken bij [Het verlaten van de ADSync-serviceaccountversleutelingssleutel](#abandoning-the-adsync-service-account-encryption-key) om dit te bereiken.  Deze procedures moeten ook worden gebruikt als u de encryptiesleutel om welke reden dan ook moet verlaten.

## <a name="issues-that-arise-from-changing-the-password"></a>Problemen die voortvloeien uit het wijzigen van het wachtwoord
Er zijn twee dingen die moeten worden gedaan wanneer u het wachtwoord van het serviceaccount wijzigt.

Eerst moet u het wachtwoord wijzigen onder Windows Service Control Manager.  Totdat dit probleem is opgelost, ziet u de volgende fouten:


- Als u de synchronisatieservice probeert te starten in Windows Service Control Manager, ontvangt u de foutmelding "**Windows kan de Microsoft Azure AD Sync-service niet starten op lokale computer**". **Fout 1069: De service is niet gestart vanwege een aanmeldingsfout.**"
- Onder Windows Event Viewer bevat het systeemgebeurtenislogboek een fout met **gebeurtenis-id 7038** en bericht "**De ADSync-service kan zich niet aanmelden zoals bij het momenteel geconfigureerde wachtwoord vanwege de volgende fout: de gebruikersnaam of het wachtwoord is onjuist.**"

Ten tweede kan de synchronisatieservice, onder specifieke voorwaarden, als het wachtwoord wordt bijgewerkt, de versleutelingssleutel niet meer ophalen via DPAPI. Zonder de versleutelingssleutel kan de synchronisatieservice de wachtwoorden die nodig zijn om te synchroniseren naar/van on-premises AD en Azure AD niet decoderen.
U ziet fouten zoals:

- Als u onder Windows Service Control Manager de synchronisatieservice probeert te starten en de versleutelingssleutel niet ophalen, mislukt deze met fout<strong>Windows kan de Microsoft Azure AD Sync niet starten op lokale computer. Bekijk het logboek systeemgebeurtenis voor meer informatie. Als dit een niet-Microsoft-service is, neemt u contact op met de serviceleverancier en raadpleegt u servicespecifieke foutcode -21451857952</strong>."
- Onder Windows Event Viewer bevat het gebeurtenislogboek van de toepassing een fout met **gebeurtenis-id 6028** en foutbericht *'De serverversleutelingssleutel kan niet worden geopend'.*

Als u wilt voorkomen dat u deze fouten ontvangt, volgt u de procedures in [Het verlaten van de adsync-serviceaccountversleutelingssleutel](#abandoning-the-adsync-service-account-encryption-key) bij het wijzigen van het wachtwoord.
 
## <a name="abandoning-the-adsync-service-account-encryption-key"></a>De versleutelingssleutel van de ADSync-serviceaccount verlaten
>[!IMPORTANT]
>De volgende procedures zijn alleen van toepassing op Azure AD Connect build 1.1.443.0 of ouder.

Gebruik de volgende procedures om de versleutelingssleutel te verlaten.

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>Wat te doen als u de versleutelingssleutel moet verlaten

Als u de versleutelingssleutel moet verlaten, gebruikt u de volgende procedures om dit te bereiken.

1. [De synchronisatieservice stoppen](#stop-the-synchronization-service)

1. [De bestaande versleutelingssleutel verlaten](#abandon-the-existing-encryption-key)

2. [Het wachtwoord van het AD DS-connector-account opgeven](#provide-the-password-of-the-ad-ds-connector-account)

3. [Het wachtwoord van het ADSync-serviceaccount opnieuw initialiseren](#reinitialize-the-password-of-the-adsync-service-account)

4. [De synchronisatieservice starten](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>De synchronisatieservice stoppen
Eerst u de service stoppen in Windows Service Control Manager.  Zorg ervoor dat de service niet wordt uitgevoerd wanneer u deze probeert te stoppen.  Als het is, wachten tot het is voltooid en dan stoppen.


1. Ga naar Windows Service Control Manager (START → Services).
2. Selecteer **Microsoft Azure AD Sync** en klik op Stoppen.

#### <a name="abandon-the-existing-encryption-key"></a>De bestaande versleutelingssleutel verlaten
Verlaat de bestaande versleutelingssleutel zodat er nieuwe versleutelingssleutel kan worden gemaakt:

1. Meld u aan bij uw Azure AD Connect Server als beheerder.

2. Start een nieuwe PowerShell-sessie.

3. Navigeer naar map:`'$env:ProgramFiles\Microsoft Azure AD Sync\bin\'`

4. Voer de opdracht uit:`./miiskmu.exe /a`

![Hulpprogramma voor synchronisatiesleutel azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-connector-account"></a>Het wachtwoord van het AD DS-connector-account opgeven
Omdat de bestaande wachtwoorden die in de database zijn opgeslagen niet meer kunnen worden gedecodeerd, moet u de synchronisatieservice het wachtwoord van het AD DS-connector-account opgeven. De synchronisatieservice versleutelt de wachtwoorden met de nieuwe versleutelingssleutel:

1. Start de Synchronisatieservicebeheer (START → Synchronisatieservice).
</br>![Onderhoudsbeheer synchroniseren](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. Ga naar het tabblad **Connectors.**
3. Selecteer de **AD-connector** die overeenkomt met uw on-premises AD. Als u meer dan één AD-connector hebt, herhaalt u de volgende stappen voor elk van deze elementen.
4. Selecteer **Eigenschappen**onder **Acties**.
5. Selecteer verbinding maken met **Active Directory Forest**in het pop-updialoogvenster:
6. Voer het wachtwoord van het AD DS-account in het tekstvak **Wachtwoord** in. Als u het wachtwoord niet kent, moet u het instellen op een bekende waarde voordat u deze stap uitvoert.
7. Klik op **OK** om het nieuwe wachtwoord op te slaan en het pop-updialoogvenster te sluiten.
![Hulpprogramma voor synchronisatiesleutel azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-adsync-service-account"></a>Het wachtwoord van het ADSync-serviceaccount opnieuw initialiseren
U het wachtwoord van het Azure AD-serviceaccount niet rechtstreeks aan de synchronisatieservice verstrekken. In plaats daarvan moet u de cmdlet **Add-ADSyncAADServiceAccount** gebruiken om het Azure AD-serviceaccount opnieuw te initialiseren. De cmdlet reset het accountwachtwoord en stelt het beschikbaar voor de synchronisatieservice:

1. Start een nieuwe PowerShell-sessie op de Azure AD Connect-server.
2. Run cmdlet `Add-ADSyncAADServiceAccount`.
3. Geef in het pop-updialoogvenster de azure AD Global-beheerdersreferenties op voor uw Azure AD-tenant.
![Hulpprogramma voor synchronisatiesleutel azure AD Connect](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. Als dit lukt, ziet u de powershell-opdrachtprompt.

#### <a name="start-the-synchronization-service"></a>De synchronisatieservice starten
Nu de synchronisatieservice toegang heeft tot de versleutelingssleutel en alle wachtwoorden die deze nodig heeft, u de service opnieuw starten in Windows Service Control Manager:


1. Ga naar Windows Service Control Manager (START → Services).
2. Selecteer **Microsoft Azure AD Sync** en klik op Opnieuw starten.

## <a name="next-steps"></a>Volgende stappen
**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)

* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)
