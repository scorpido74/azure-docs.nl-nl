---
title: De MARS-agent (Microsoft Azure Recovery Services) upgraden
description: Meer informatie over het upgraden van de MARS-agent (Microsoft Azure Recovery Services).
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: 49daf438b855d19961519d93b6c3ec535de4756f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672922"
---
# <a name="upgrade-the-microsoft-azure-recovery-services-mars-agent"></a>De MARS-agent (Microsoft Azure Recovery Services) upgraden

In dit artikel leer je hoe je:

* Servers identificeren met eerdere versies van de MARS-agent
* MARS-installaties op die servers bijwerken

## <a name="identify-servers-with-earlier-versions-of-the-mars-agent"></a>Servers identificeren met eerdere versies van de MARS-agent

Voor installaties van Azure Backup-agent en Azure-back-upserver:

1. Navigeer naar de Vault voor Herstelservices, waar u servers hebt geregistreerd waarvan mogelijk een back-up wordt ondersteund door oudere versies van de agent. U vindt een representatieve lijst met kluizen met oudere Azure Backup-agents in de Azure Backup Update-waarschuwingen van Azure.
1. Selecteer **back-upinfrastructuur** onder de sectie Beheren in het gedeelte **Instellingen** aan de **linkerkant** van de Vault voor herstelservices.
1. Als u Azure-back-upagents wilt detecteren die zijn geïnstalleerd als onderdeel van Azure Backup-serverinstallaties, gaat u naar **Back-upbeheerservers** onder **Beheerservers**. Hier worden de servers vermeld die Azure Backup-serverinstallaties hebben, samen met het versienummer voor de bijbehorende Azure Backup-agent.

    ![Lijst met MARS-agents die zijn geïnstalleerd als onderdeel van Azure Backup-serverinstallaties](./media/upgrade-mars-agent/backup-management-servers.png)

1. Als u agentversies wilt controleren voor MARS-agentinstallaties (Microsoft Azure Recovery Services) of de Azure Backup-agent, gaat u naar **Beveiligde servers** onder **Beheerservers**. Selecteer vervolgens **Azure Backup agent** onder Type Back-upbeheer. Hier worden de servers vermeld die Azure Backup agent installaties samen met het versienummer voor de installatie hebben.

    ![Lijst met servers met de MARS-agent geïnstalleerd](./media/upgrade-mars-agent/protected-servers.png)

1. Sorteer de kolom Azure Backup Agent-versie door te klikken op de kolom **Agent-versie** voor MARS Agent-installaties of de kolom **Azure Backup Agent Version** voor Azure-back-upserverinstallaties.

1. In de vorige stap krijgt u de lijst met servers met Azure Backup-agents met versies lager dan 2.0.9083.0 of agentversies die als spaties worden vermeld. Dit zijn de servers waar Azure Backup agents moeten worden bijgewerkt.

## <a name="update-the-mars-agent-installation-on-the-server"></a>De installatie van de MARS-agent op de server bijwerken

Zodra u de servers hebt geïdentificeerd die een Azure Backup Agent-update nodig hebben, voert u de volgende stappen uit voor elke geïdentificeerde server (met Azure-back-upserver of de MARS-agent). [Download de nieuwste versie van de Azure-back-upagent](https://aka.ms/azurebackup_agent) voordat u de onderstaande stappen volgt.

1. Klik op een rij met Azure Backup-agent lager dan 2.0.9083.0 of leeg. Hiermee wordt het scherm met serverdetails geopend.

    ![Beveiligde servers met verouderde agentversies](./media/upgrade-mars-agent/old-agent-version.png)

    ![Azure-back-upservers met verouderde agentversies](./media/upgrade-mars-agent/backup-management-servers-old-versions.png)

1. Klik op **Verbinding maken** om een extern bureaublad-verbindingsbestand te ontvangen om verbinding te maken met de server of rechtstreeks verbinding te maken met de server via de Verbinding met extern bureaublad op uw server.

    ![Verbinding maken met server via externe desktopverbinding](./media/upgrade-mars-agent/connect-to-server.png)

    >[!NOTE]
    > Als de vermelde server niet bestaat of buiten gebruik is gesteld, u de resterende stappen hieronder negeren en naar de volgende server gaan.

1. Voer uw administratieve inloggegevens in en meld u aan.

1. Als de proxy van uw server of server beperkte toegang tot internet heeft, controleert u of firewall-instellingen op de server/proxy zijn geconfigureerd om de URL toe te staan die geschikt is voor de Azure-cloud die u gebruikt:

    Azure Cloud | URL
    -- | ---
    Azure Cloud (Openbaar) |   `https://login.windows.net`
    Azure China 21Vianet Cloud   | `https://login.chinacloudapi.cn`
    Azure US Government Cloud |   `https://login.microsoftonline.us`
    Azure Duitse cloud  |  `https://login.microsoftonline.de`

1. Kopieer het installatieprogramma voor azure-back-upagentupdate naar de server.

    ![Installatieprogramma voor azure-back-upagentupdate kopiëren naar server](./media/upgrade-mars-agent/copy-agent-installer.png)

1. Voer het installatieprogramma uit. De wizard Microsoft Azure Recovery Services Agent Upgrade wordt geopend.

    ![De wizard Upgrade van Microsoft Azure Recovery Services Agent](./media/upgrade-mars-agent/agent-upgrade-wizard.png)

1. Klik op **Volgende**.

1. Klik op **Upgrade**.

    ![Installatie van de Microsoft Azure Recovery Services Agent](./media/upgrade-mars-agent/upgrade-installation.png)

1. Het laatste bevestigingsscherm geeft aan dat de Azure Backup-agent is bijgewerkt.

## <a name="for-system-center-data-protection-manager-sc-dpm-customers"></a>Voor klanten van System Center Data Protection Manager (SC DPM)

Als u Azure-back-upagents hebt geïnstalleerd op de SC DPM-servers (System Center Data Protection Manager), moet u de onderstaande stappen volgen om te bepalen of uw DPM-servers een Azure-back-upagent-update nodig hebben:

1. Meld u aan bij uw SC DPM-server als beheerder.
2. Open de DPM-console.
3. Klik op **Beheer** in de navigatie linksonder van de console.
4. Zoek in de informatie die op de linkernavigatie wordt weergegeven naar de versiegegevens van de Azure Backup-agent.
5. Als de versie lager is dan 2.0.9083.0, downloadt u het nieuwste Azure-installatieprogramma voor back-upagenten en voert u het installatieprogramma uit op de DPM-server om de Azure Backup-agent bij te werken.

Herhaal de bovenstaande stappen voor alle DPM-servers in uw omgeving.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van back-ups van Windows-machines met de Azure Backup MARS-agent](backup-windows-with-mars-agent.md)
