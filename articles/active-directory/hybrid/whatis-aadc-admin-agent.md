---
title: Wat is de Azure AD Connect-beheeragent - Azure AD Connect | Microsoft Documenten
description: Hier worden de hulpprogramma's beschreven die worden gebruikt om uw on-premises omgeving met Azure AD te synchroniseren en bewaken.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f68635820125161ed4f5777e27a20de9e6fbe8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049384"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Wat is de Azure AD Connect-beheerderagent? 
De Azure AD Connect-beheeragent is een nieuw onderdeel van Azure Active Directory Connect dat kan worden geïnstalleerd op een Azure Active Directory Connect-server. Het wordt gebruikt om specifieke gegevens uit uw Active Directory-omgeving te verzamelen waarmee een Microsoft-ondersteuningstechnicus problemen oplossen wanneer u een ondersteuningsaanvraag opent. 

>[!NOTE]
>De beheerdersagent is standaard niet geïnstalleerd en ingeschakeld.  U moet de agent installeren om gegevens te verzamelen om te helpen bij ondersteuningsaanvragen.

Wanneer de Azure AD Connect-beheeragent is geïnstalleerd, wacht deze op specifieke aanvragen voor gegevens uit Azure Active Directory, worden de gevraagde gegevens uit de synchronisatieomgeving opgehaald en naar Azure Active Directory, waar deze wordt aangeboden aan de Microsoft-ondersteuning Ingenieur. 

De informatie die de Azure AD Connect-beheeragent uit uw omgeving ophaalt, wordt op geen enkele manier opgeslagen- deze wordt alleen weergegeven aan de Microsoft-ondersteuningstechnicus om hen te helpen bij het onderzoeken en oplossen van problemen met de Azure Active Directory Connect gerelateerde ondersteuningscase die u hebt geopend De Azure AD Connect-beheeragent is niet standaard geïnstalleerd op de Azure AD Connect Server. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>De Azure AD Connect-beheeragent installeren op de Azure AD Connect-server 

Vereisten:
1.    Azure AD Connect is geïnstalleerd op de server
2.    Azure AD Connect-status is geïnstalleerd op de server

![admin agent](media/whatis-aadc-admin-agent/adminagent0.png)

De binaire bestanden van de Azure AD Connect-beheeragent worden in de AAD Connect-server geplaatst. Ga als volgt te werk om de agent te installeren:

1.    Powershell openen in de beheermodus
2.    Navigeer naar de map waar de toepassing zich bevindt cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"
3.    ConfigureAdminAgent.ps1 uitvoeren

Voer desgevraagd uw globale ad-beheerdersreferenties van Azure AD in. Dit moeten dezelfde referenties zijn die zijn ingevoerd tijdens de installatie van Azure AD Connect.

Nadat de agent is geïnstalleerd, ziet u de volgende twee nieuwe programma's in de lijst Programma's toevoegen/verwijderen in het Configuratiescherm van uw server: 

![admin agent](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Welke gegevens in mijn Sync-service worden weergegeven aan de Microsoft-serviceengineer? 
Wanneer u een ondersteuningscase opent, kan de Microsoft Support Engineer voor een bepaalde gebruiker de relevante gegevens in Active Directory, de Active Directory-verbindingsruimte in de Azure Active Directory Connect-server, de Azure Active Directory-connectorruimte in Azure zien Active Directory Connect-server en de metaverse in de Azure Active Directory Connect-server. 

De Microsoft Support Engineer kan geen gegevens in uw systeem wijzigen en kan geen wachtwoorden zien. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Wat moet ik doen als ik niet wil dat de Microsoft-ondersteuningstechnicus toegang krijgt tot mijn gegevens? 
Zodra de agent is geïnstalleerd, als u niet wilt dat de Microsoft-serviceengineer toegang heeft tot uw gegevens voor een ondersteuningsgesprek, u de functionaliteit uitschakelen door het bestand van de serviceconfig zoals hieronder beschreven te wijzigen: 

1.    Open **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** in kladblok.
2.    De instelling **UserDataEnabled uitschakelen,** zoals hieronder wordt weergegeven. Als de instelling **UserDataEnabled** bestaat en is ingesteld op true, stelt u deze in op false. Als de instelling niet bestaat, voegt u de instelling zoals hieronder weergegeven toe.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.    Sla het config-bestand op.
4.    Azure AD Connect-beheeragentservice opnieuw starten zoals hieronder wordt weergegeven

![admin agent](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
