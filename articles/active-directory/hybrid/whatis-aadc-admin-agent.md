---
title: 'Wat is de Azure AD Connect-beheer agent: Azure AD Connect | Microsoft Docs'
description: Hier worden de hulpprogramma's beschreven die worden gebruikt om uw on-premises omgeving met Azure AD te synchroniseren en bewaken.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 222dab87ee71870e564e426d7466555893cc565b
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305198"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Wat is de Azure AD Connect-beheerderagent? 
De Azure AD Connect beheer agent is een nieuw onderdeel van Azure Active Directory Connect dat kan worden geïnstalleerd op een Azure Active Directory Connect server. Het wordt gebruikt voor het verzamelen van specifieke gegevens uit uw Active Directory omgeving die een ondersteunings technicus van micro soft helpt bij het oplossen van problemen bij het openen van een ondersteunings aanvraag. 

>[!NOTE]
>De beheer agent is niet standaard geïnstalleerd en ingeschakeld.  U moet de agent installeren om gegevens te kunnen verzamelen om te helpen bij de ondersteunings cases.

Na installatie wacht de Azure AD Connect beheer agent op specifieke aanvragen voor gegevens van Azure Active Directory, haalt de aangevraagde gegevens op uit de synchronisatie omgeving en verzendt deze naar Azure Active Directory, waar deze wordt aangeboden aan de micro soft-ondersteuning ingenieur. 

De informatie die door de Azure AD Connect beheer agent van uw omgeving wordt opgehaald, wordt niet op een wille keurige manier opgeslagen. deze wordt alleen weer gegeven aan de ondersteunings technicus van micro soft om hen te helpen bij het onderzoeken en oplossen van problemen met de Azure Active Directory Connect gerelateerde ondersteunings cases die u hebt Azure AD Connect geopend, is standaard niet geïnstalleerd op de Azure AD Connect-server. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installeer de Azure AD Connect-beheer agent op de Azure AD Connect-server 

Vereisten:
1.  Azure AD Connect is geïnstalleerd op de server
2.  Azure AD Connect Health is geïnstalleerd op de server

![beheer agent](media/whatis-aadc-admin-agent/adminagent0.png)

De binaire bestanden van de Azure AD Connect-beheer agent worden geplaatst op de AAD Connect-server. Ga als volgt te werk om de agent te installeren:

1.  Power shell openen in de beheer modus
2.  Navigeer naar de map waar de toepassing zich bevindt CD "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.  Voer ConfigureAdminAgent. ps1 uit

Voer de referenties voor de globale beheerder van Azure AD in wanneer dit wordt gevraagd. Dit moet hetzelfde zijn als de referenties die zijn ingevoerd tijdens de installatie van Azure AD Connect.

Nadat de agent is geïnstalleerd, ziet u de volgende twee nieuwe Program ma's in de lijst Program Ma's toevoegen/verwijderen in het configuratie scherm van uw server: 

![beheer agent](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Welke gegevens in de synchronisatie service worden weer gegeven voor de micro soft-service-engineer? 
Wanneer u een ondersteunings Case opent, kan de Microsoft Ondersteuning Engineer voor een bepaalde gebruiker, de relevante gegevens in Active Directory, de Active Directory Connector ruimte op de Azure Active Directory Connect-server, de Azure Active Directory-Connector ruimte in azure bekijken Active Directory verbinding maken met server en de tekst in de Azure Active Directory Connect-server. 

De Microsoft Ondersteuning-Engineer kan geen gegevens in uw systeem wijzigen en kan geen wacht woorden zien. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Wat moet ik doen als ik niet wil dat de ondersteunings technicus van micro soft toegang heeft tot mijn gegevens? 
Als u de agent hebt geïnstalleerd en u niet wilt dat de micro soft-service-engineer toegang krijgt tot uw gegevens voor een ondersteunings oproep, kunt u de functionaliteit uitschakelen door het service configuratie bestand te wijzigen, zoals hieronder wordt beschreven: 

1.  Open **C:\Program Files\Microsoft Azure AD Connect beheer Agent\AzureADConnectAdministrationAgentService.exe.config** in Klad blok.
2.  Schakel de instelling **UserDataEnabled** uit zoals hieronder wordt weer gegeven. Als de instelling **UserDataEnabled** bestaat en is ingesteld op True, stelt u deze in op false. Als de instelling niet bestaat, voegt u de instelling toe, zoals hieronder wordt weer gegeven.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  Sla het configuratie bestand op.
4.  Start Azure AD Connect Administration Agent-service opnieuw zoals hieronder wordt weer gegeven

![beheer agent](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
