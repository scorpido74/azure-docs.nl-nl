---
title: Linux-computers verbinden met Azure Monitor | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Linux-computers die in andere clouds of on-premises worden gehost, verbinden met Azure Monitor met de Agent Log Analytics voor Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: aa2356901403c7a63aa4aa96dcb38f9c0c971e58
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528353"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Linux-computers verbinden met Azure Monitor

Als u virtuele machines of fysieke computers in uw lokale datacenter of andere cloudomgeving met Azure Monitor wilt bewaken en beheren, moet u de loganalytics-agent implementeren en configureren om te rapporteren aan een Log Analytics-werkruimte. De agent ondersteunt ook de rol Hybride Runbook Worker voor Azure Automation.

De Log Analytics-agent voor Linux kan worden geïnstalleerd met behulp van een van de volgende methoden. Details over het gebruik van elke methode worden later in het artikel verstrekt.

* [De agent handmatig downloaden en installeren.](#install-the-agent-manually) Dit is vereist wanneer de Linux-computer geen toegang heeft tot internet en via de [Log Analytics-gateway](gateway.md)communiceert met Azure Monitor of Azure Automation. 
* [Installeer de agent voor Linux met behulp van een wrapper-script](#install-the-agent-using-wrapper-script) gehost op GitHub. Dit is de aanbevolen methode om de agent te installeren en te upgraden wanneer de computer verbinding heeft met internet, rechtstreeks of via een proxyserver.

Als u meer wilt weten over de ondersteunde configuratie, kunt u de informatie over [ondersteunde Linux-besturingssystemen](log-analytics-agent.md#supported-linux-operating-systems) en de [ netwerkconfiguratie voor de firewall](log-analytics-agent.md#firewall-requirements) raadplegen.

>[!NOTE]
>De Log Analytics-agent voor Linux kan niet worden geconfigureerd om aan meer dan één Log Analytics-werkruimte te rapporteren. Het kan alleen worden geconfigureerd om gelijktijdig te rapporteren aan zowel een beheergroep van System Center Operations Manager als log analytics, of aan een afzonderlijk systeemcentrum.

## <a name="agent-install-package"></a>Installatiepakket van agent

De Log Analytics-agent voor Linux bestaat uit meerdere pakketten. Het releasebestand bevat de volgende pakketten, die beschikbaar `--extract` zijn door de shellbundel met de parameter uit te voeren:

**Pakket** | **Versie** | **Beschrijving**
----------- | ----------- | --------------
omsagent | 1.12.15 | De Log Analytics Agent voor Linux
omsconfig | 1.1.1 | Configuratieagent voor de Log Analytics-agent
Omi | 1.6.3 | Open Management Infrastructure (OMI) - een lichtgewicht CIM-server. *Merk op dat OMI roottoegang vereist om een cron-taak uit te voeren die nodig is voor het functioneren van de service*
scx scx | 1.6.3 | OMI CIM-providers voor prestatiestatistieken voor besturingssystemen
apache-cimprov apache-cimprov | 1.0.1 | Apache HTTP Server performance monitoring provider voor OMI. Alleen geïnstalleerd als Apache HTTP Server wordt gedetecteerd.
mysql-cimprov | 1.0.1 | MySQL Server performance monitoring provider voor OMI. Alleen geïnstalleerd als mySQL/MariaDB-server wordt gedetecteerd.
docker-cimprov | 1.0.0 | Docker provider voor OMI. Alleen geïnstalleerd als Docker wordt gedetecteerd.

### <a name="agent-installation-details"></a>Details van de installatie van agent

Na het installeren van de Log Analytics-agent voor Linux-pakketten worden de volgende aanvullende systeembrede configuratiewijzigingen toegepast. Deze artefacten worden verwijderd wanneer het omsagent-pakket is verwijderd.

* Een niet-bevoorrechte gebruiker `omsagent` met de naam: wordt gemaakt. De daemon draait onder deze referentie. 
* Er wordt een bestand *met* `/etc/sudoers.d/omsagent`sudoers gemaakt in . Dit geeft `omsagent` toestemming om de syslog en omsagent daemons opnieuw op te starten. Als sudo-richtlijnen niet worden ondersteund in de geïnstalleerde versie van sudo, worden deze vermeldingen geschreven naar *include* `/etc/sudoers`.
* De syslog-configuratie wordt gewijzigd om een subset van gebeurtenissen door te sturen naar de agent. Zie [Syslog-gegevensverzameling configureren](data-sources-syslog.md)voor meer informatie.

Op een bewaakte Linux-computer wordt de `omsagent`agent vermeld als . `omsconfig`is de Log Analytics-agent voor Linux-configuratieagent die elke 5 minuten op zoek is naar nieuwe portalconfiguratie. De nieuwe en bijgewerkte configuratie wordt toegepast `/etc/opt/microsoft/omsagent/conf/omsagent.conf`op de agentconfiguratiebestanden op .

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen

Voordat u de Log Analytics-agent voor Linux installeert, hebt u eerst de werkruimte-id en -sleutel voor de Log Analytics-werkruimte nodig. Deze informatie is vereist tijdens het instellen van de agent om deze correct te configureren en ervoor te zorgen dat deze met Azure Monitor kan communiceren.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Selecteer **Alle services**in de linkerbovenhoek van de Azure-portal . Voer in het zoekvak **Log Analytics**in . Terwijl u typt, worden de lijstfilters op basis van uw invoer weergegeven. Selecteer **Logboekanalysewerkruimten**.

2. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die u eerder hebt gemaakt. (U hebt de naam **DefaultLAWorkspace**mogelijk genoemd .)

3. Selecteer **Geavanceerde instellingen:**

    ![Menu Geavanceerde instellingen voor Logboekanalyse in de Azure-portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Linux-servers**.

5. De waarde rechts van **Werkruimte-id** en **Primaire sleutel**. Kopieer en plak beide in uw favoriete editor.

## <a name="install-the-agent-manually"></a>De agent handmatig installeren

De Log Analytics-agent voor Linux wordt geleverd in een zelfextraherende en installeerbare shell scriptbundel. Deze bundel bevat Debian- en RPM-pakketten voor elk van de agentcomponenten en kan direct worden geïnstalleerd of geëxtraheerd om de afzonderlijke pakketten op te halen. Een bundel is voorzien voor x64 en een voor x86 architecturen. 

> [!NOTE]
> Voor Azure VM's raden we u aan de agent erop te installeren met behulp van de [Azure Log Analytics VM-extensie](../../virtual-machines/extensions/oms-linux.md) voor Linux. 

1. [Download](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) en breng de juiste bundel (x64 of x86) over naar uw Linux-vm of fysieke computer, met behulp van scp/sftp.

2. Installeer de bundel `--install` met behulp van het argument. Als u tijdens de installatie wilt `-w <WorkspaceID>` inbouwen naar een Log Analytics-werkruimte, moet u de eerder gekopieerde parameters en `-s <workspaceKey>` parameters opgeven.

    >[!NOTE]
    >U moet het `--upgrade` argument gebruiken als er afhankelijke pakketten zoals omi, scx, omsconfig of hun oudere versies zijn geïnstalleerd, zoals het geval zou zijn als de system Center Operations Manager-agent voor Linux al is geïnstalleerd. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Als u de Linux-agent wilt configureren om een Log Analytics-werkruimte te installeren en verbinding te maken via een Log Analytics-gateway, voert u de volgende opdracht uit met de parameters proxy, werkruimte-id en werkruimtesleutel. Deze configuratie kan worden opgegeven op `-p [protocol://][user:password@]proxyhost[:port]`de opdrachtregel door . De eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of IP-adres van de Log Analytics-gatewayserver.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Als verificatie vereist is, moet u de gebruikersnaam en het wachtwoord opgeven. Bijvoorbeeld: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werkruimte in de Azure Government-cloud, voert u de volgende opdracht uit met de werkruimte-id en de primaire sleutel die eerder zijn gekopieerd.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Als u de agentpakketten wilt installeren en configureren om op een later tijdstip te rapporteren aan een specifieke Log Analytics-werkruimte, voert u de volgende opdracht uit:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Als u de agentpakketten uit de bundel wilt halen zonder de agent te installeren, voert u de volgende opdracht uit:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>De agent installeren met behulp van wrapper script

De volgende stappen configureren de installatie van de agent voor Log Analytics in Azure en Azure Government cloud met behulp van het wrapper script voor Linux-computers die rechtstreeks of via een proxyserver kunnen communiceren om de agent te downloaden die op GitHub wordt gehost en de agent te installeren.  

Als uw Linux-computer via een proxyserver moet communiceren naar Log Analytics, `-p [protocol://][user:password@]proxyhost[:port]`kan deze configuratie op de opdrachtregel worden opgegeven door . De *eigenschap* protocol `http` `https`accepteert of en de eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of IP-adres van de proxyserver. 

Bijvoorbeeld: `https://proxy01.contoso.com:30443`

Als verificatie in beide gevallen vereist is, moet u de gebruikersnaam en het wachtwoord opgeven. Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een werkruimte van Log Analytics, voert u de volgende opdracht uit met de werkruimte-id en de primaire sleutel. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    De volgende opdracht `-p` bevat de proxyparameter en voorbeeldsyntaxis wanneer verificatie vereist is door uw proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Als u de Linux-computer wilt configureren om verbinding te maken met de werkruimte Log Analytics in de Azure Government-cloud, voert u de volgende opdracht uit met de eerder gekopieerde werkruimte-id en primaire sleutel. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    De volgende opdracht `-p` bevat de proxyparameter en voorbeeldsyntaxis wanneer verificatie vereist is door uw proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Start de agent opnieuw door de volgende opdracht uit te voeren: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Upgraden van een vorige release

Upgraden van een vorige versie, te beginnen met versie 1.0.0-47, wordt ondersteund in elke release. Voer de installatie `--upgrade` uit met de parameter om alle onderdelen van de agent te upgraden naar de nieuwste versie.

## <a name="next-steps"></a>Volgende stappen

- Controleer [het beheren en onderhouden van de Log Analytics-agent voor Windows en Linux](agent-manage.md) voor meer informatie over het opnieuw configureren, upgraden of verwijderen van de agent uit de virtuele machine.

- Controleer [het oplossen van problemen met de Linux-agent](agent-linux-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.
