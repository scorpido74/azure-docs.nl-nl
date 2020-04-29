---
title: Linux-computers verbinden met Azure Monitor | Microsoft Docs
description: In dit artikel wordt beschreven hoe u verbinding maakt tussen Linux-computers die worden gehost in andere Clouds of on-premises naar Azure Monitor met de Log Analytics-agent voor Linux.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/21/2020
ms.openlocfilehash: 9807d6eeb07b953ab75b328ce64c5166ca52dd2a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80637522"
---
# <a name="connect-linux-computers-to-azure-monitor"></a>Linux-computers verbinden met Azure Monitor

Als u virtuele machines of fysieke computers in uw lokale Data Center of een andere cloud omgeving wilt bewaken en beheren met Azure Monitor, moet u de Log Analytics agent implementeren en configureren om te rapporteren aan een Log Analytics-werk ruimte. De agent biedt ook ondersteuning voor de Hybrid Runbook Worker rol voor Azure Automation.

De Log Analytics-agent voor Linux kan worden geïnstalleerd met behulp van een van de volgende methoden. Verderop in dit artikel vindt u meer informatie over het gebruik van elke methode.

* De agent [hand matig downloaden en installeren](#install-the-agent-manually) . Dit is vereist wanneer de Linux-computer geen toegang tot internet heeft en communiceert met Azure Monitor of Azure Automation via de [log Analytics gateway](gateway.md). 
* [Installeer de agent voor Linux met behulp van een wrapper-script](#install-the-agent-using-wrapper-script) dat wordt gehost op github. Dit is de aanbevolen methode om de agent te installeren en bij te werken wanneer de computer verbinding heeft met internet, rechtstreeks of via een proxy server.

Als u meer wilt weten over de ondersteunde configuratie, kunt u de informatie over [ondersteunde Linux-besturingssystemen](log-analytics-agent.md#supported-linux-operating-systems) en de [ netwerkconfiguratie voor de firewall](log-analytics-agent.md#network-requirements) raadplegen.

>[!NOTE]
>De Log Analytics-agent voor Linux kan niet worden geconfigureerd om aan meer dan één Log Analytics-werkruimte te rapporteren. Deze kan alleen worden geconfigureerd om te rapporteren aan een System Center Operations Manager-beheer groep en Log Analytics-werk ruimte gelijktijdig of afzonderlijk.

## <a name="agent-install-package"></a>Agent installatie pakket

De Log Analytics-agent voor Linux bestaat uit meerdere pakketten. Het release bestand bevat de volgende pakketten, die beschikbaar zijn door de shell bundel uit te voeren met `--extract` de para meter:

**Pakket** | **Versie** | **Beschrijving**
----------- | ----------- | --------------
omsagent | 1.12.15 | De Log Analytics-agent voor Linux
omsconfig | 1.1.1 | Configuratie agent voor de Log Analytics-agent
Omi | 1.6.3 | Open Management Infrastructure (OMI)--een Lightweight CIM-server. *Houd er rekening mee dat OMI een cron-taak moet uitvoeren die nodig is voor de werking van de service*
SCx | 1.6.3 | OMI CIM-providers voor metrische gegevens over de prestaties van het besturings systeem
Apache-cimprov | 1.0.1 | Bewakings provider van Apache HTTP-server prestaties voor OMI. Alleen geïnstalleerd als de Apache HTTP-server wordt gedetecteerd.
MySQL-cimprov | 1.0.1 | MySQL-server prestatie bewakings provider voor OMI. Alleen geïnstalleerd als MySQL/MariaDB-server wordt gedetecteerd.
docker-cimprov | 1.0.0 | Docker-provider voor OMI. Alleen geïnstalleerd als docker wordt gedetecteerd.

### <a name="agent-installation-details"></a>Details van de agent installatie

Na de installatie van de Log Analytics agent voor Linux-pakketten, worden de volgende aanvullende configuratie wijzigingen van het hele systeem toegepast. Deze artefacten worden verwijderd wanneer u het omsagent-pakket verwijdert.

* Er is een niet-bevoegde gebruiker `omsagent` met de naam: gemaakt. De daemon wordt uitgevoerd onder deze referentie. 
* Er wordt een sudo- *include* -bestand gemaakt in `/etc/sudoers.d/omsagent`. Hiermee wordt geautoriseerd `omsagent` om de syslog-en omsagent-daemons opnieuw te starten. Als sudo *bevatten* instructies worden niet ondersteund in de geïnstalleerde versie van sudo, worden deze vermeldingen geschreven naar `/etc/sudoers`.
* De syslog-configuratie is gewijzigd om een subset van gebeurtenissen naar de agent door te sturen. Zie [syslog-gegevens verzameling configureren](data-sources-syslog.md)voor meer informatie.

Op een bewaakte Linux-computer wordt de agent vermeld `omsagent`als. `omsconfig`is de Log Analytics agent voor Linux-configuratie agent die elke vijf minuten zoekt naar de nieuwe configuratie van de portal-zijde. De nieuwe en bijgewerkte configuratie wordt toegepast op de agent configuratie bestanden die zich `/etc/opt/microsoft/omsagent/conf/omsagent.conf`bevinden op.

## <a name="obtain-workspace-id-and-key"></a>Werkruimte-ID en -sleutel ophalen

Voordat u de Log Analytics-agent voor Linux installeert, hebt u eerst de werkruimte-id en -sleutel voor de Log Analytics-werkruimte nodig. Deze informatie is vereist tijdens de installatie van de agent om deze correct te configureren en ervoor te zorgen dat deze kan communiceren met Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. Selecteer in de linkerbovenhoek van de Azure Portal **alle services**. Voer in het zoekvak **log Analytics**in. Terwijl u typt, worden de lijst filters op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.

2. Selecteer de werk ruimte die u eerder hebt gemaakt in de lijst met Log Analytics-werk ruimten. (Mogelijk hebt u de naam **standaardlawerkruimte**.)

3. **Geavanceerde instellingen**selecteren:

    ![Menu Geavanceerde instellingen voor Log Analytics in het Azure Portal](../learn/media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Selecteer **Verbonden bronnen** en selecteer vervolgens **Linux-servers**.

5. De waarde rechts van **Werkruimte-id** en **Primaire sleutel**. Kopieer en plak beide in uw favoriete editor.

## <a name="install-the-agent-manually"></a>De agent handmatig installeren

De Log Analytics-agent voor Linux bevindt zich in een zelfextraherende shell-script bundel en kan worden geïnstalleerd. Deze bundel bevat Debian-en RPM-pakketten voor elk van de agent onderdelen en kan rechtstreeks of geëxtraheerd worden geïnstalleerd om de afzonderlijke pakketten op te halen. Er is één bundel voor x64 en één voor x86-architecturen. 

> [!NOTE]
> Voor virtuele Azure-machines wordt u aangeraden de agent te installeren met behulp van de [Azure log Analytics VM-extensie](../../virtual-machines/extensions/oms-linux.md) voor Linux. 

1. [Down load](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) en breng de juiste bundel (x64 of x86) om naar uw virtuele Linux-machine of fysieke computer met SCP/SFTP.

2. Installeer de bundel met behulp van `--install` het argument. Als u tijdens de installatie wilt voorbereiden op een Log Analytics- `-w <WorkspaceID>` werk `-s <workspaceKey>` ruimte, geeft u de para meters en op die eerder zijn gekopieerd.

    >[!NOTE]
    >U moet het `--upgrade` argument gebruiken als er afhankelijke pakketten, zoals Omi, SCx, omsconfig of hun oudere versies, worden geïnstalleerd. Dit is het geval als de system Center Operations Manager-agent voor Linux al is geïnstalleerd. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Als u de Linux-agent wilt configureren om een Log Analytics-werk ruimte te installeren en er verbinding mee te maken via een Log Analytics gateway, voert u de volgende opdracht uit om de para meters proxy, werk ruimte-ID en werkruimte sleutel op te geven. Deze configuratie kan worden opgegeven op de opdracht regel met behulp van `-p [protocol://][user:password@]proxyhost[:port]`. De eigenschap *proxyhost* accepteert een Fully Qualified Domain name of IP-adres van de log Analytics-Gateway server.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Als verificatie is vereist, moet u de gebruikers naam en het wacht woord opgeven. Bijvoorbeeld: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werk ruimte in Azure Government Cloud, voert u de volgende opdracht uit om de werk ruimte-ID en de primaire sleutel die u eerder hebt gekopieerd,

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Voer de volgende opdracht uit als u de agent pakketten wilt installeren en zo wilt configureren dat deze op een later tijdstip worden gerapporteerd aan een specifieke Log Analytics-werk ruimte:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Als u de agent pakketten wilt uitpakken uit de bundel zonder de agent te installeren, voert u de volgende opdracht uit:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="install-the-agent-using-wrapper-script"></a>De agent installeren met behulp van het wrapper-script

Met de volgende stappen configureert u de installatie van de agent voor Log Analytics in Azure en Azure Government Cloud met het wrapper-script voor Linux-computers die rechtstreeks kunnen communiceren of via een proxy server om de agent te downloaden die wordt gehost op GitHub en de agent te installeren.  

Als uw Linux-computer moet communiceren via een proxy server om Log Analytics, kan deze configuratie worden opgegeven op de opdracht regel met behulp van `-p [protocol://][user:password@]proxyhost[:port]`. De *protocol* eigenschap accepteert `http` of `https`, en de eigenschap *proxyhost* accepteert een Fully Qualified Domain name of IP-adres van de proxy server. 

Bijvoorbeeld: `https://proxy01.contoso.com:30443`

Als verificatie vereist is in beide gevallen, moet u de gebruikers naam en het wacht woord opgeven. Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics werkruimte, voert u de volgende opdracht uit om de werk ruimte-ID en de primaire sleutel op te geven. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    De volgende opdracht bevat de `-p` proxy parameter en de voorbeeld syntaxis wanneer de verificatie is vereist voor de proxy server:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Als u de Linux-computer wilt configureren om verbinding te maken met Log Analytics werk ruimte in Azure Government Cloud, voert u de volgende opdracht uit om de werk ruimte-ID en de primaire sleutel die u eerder hebt gekopieerd, Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    De volgende opdracht bevat de `-p` proxy parameter en de voorbeeld syntaxis wanneer de verificatie is vereist voor de proxy server:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Start de agent opnieuw door de volgende opdracht uit te voeren: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="upgrade-from-a-previous-release"></a>Upgrade uitvoeren van een eerdere versie

Een upgrade uitvoeren van een eerdere versie, te beginnen met versie 1.0.0-47, wordt in elke release ondersteund. Voer de installatie uit met `--upgrade` de para meter om alle onderdelen van de agent bij te werken naar de meest recente versie.

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [de log Analytics-agent voor Windows en Linux beheren en onderhouden voor](agent-manage.md) meer informatie over het opnieuw configureren, bijwerken of verwijderen van de agent van de virtuele machine.

- Raadpleeg [problemen met de Linux-agent oplossen](agent-linux-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.
