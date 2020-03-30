---
title: PerfInsights Linux gebruiken in Microsoft Azure| Microsoft Documenten
description: Leert hoe u PerfInsights gebruiken om problemen met de prestaties van Linux VM op te lossen.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 19b2fcaed2c80d4ca52ada9f9f0898479e73bcf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266986"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights gebruiken

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) is een zelfhulpdiagnosetool die de diagnostische gegevens verzamelt en analyseert en een rapport biedt om problemen met de prestaties van Linux-virtuele machines in Azure op te lossen. PerfInsights kan worden uitgevoerd op ondersteunde virtuele machines als een zelfstandig hulpmiddel, of rechtstreeks vanuit de portal met behulp van [Performance Diagnostics voor Azure virtuele machines.](performance-diagnostics.md)

Als u prestatieproblemen ondervindt met virtuele machines, voert u dit hulpprogramma uit voordat u contact opneemt met de ondersteuning.

## <a name="supported-troubleshooting-scenarios"></a>Ondersteunde scenario's voor het oplossen van problemen

PerfInsights kan verschillende soorten informatie verzamelen en analyseren. De volgende secties hebben betrekking op veelvoorkomende scenario's.

### <a name="quick-performance-analysis"></a>Snelle prestatieanalyse

Dit scenario verzamelt basisinformatie zoals opslag en hardwareconfiguratie van uw virtuele machine, verschillende logboeken, waaronder:

- Informatie over het besturingssysteem

- INFORMATIE over PCI-apparaten

- Algemene logboeken van gastbesturingssysteem

- Configuratiebestanden

- Opslaggegevens

- Azure Virtual Machine Configuration (verzameld met [Azure Instance Metadata Service)](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

- Lijst met lopende processen, schijf-, geheugen- en CPU-gebruik

- Netwerkinformatie

Dit is een passieve verzameling van informatie die geen invloed mag hebben op het systeem.

>[!Note]
>Snel prestatieanalysescenario wordt automatisch opgenomen in elk van de volgende scenario's:

### <a name="performance-analysis"></a>Prestatieanalyse

Dit scenario is vergelijkbaar met quick performance analyse, maar maakt het mogelijk om diagnostische informatie voor langere tijd vast te leggen.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Wat voor informatie wordt verzameld door PerfInsights

Informatie over de Virtuele Machine van Linux, werkend systeem, blokapparaten, hoge bronconsumenten, configuratie, en diverse logboeken worden verzameld. Hier vindt u meer informatie:

- Besturingssysteem
  - Linux distributie en versie
  - Kernelinformatie
  - Gegevens van de chauffeur

- Hardware
  - PCI-apparaten`*`[ ]

- Processen en geheugen
  - Lijst met processen (taaknaam, gebruikte geheugen, geopende bestanden)
  - Totaal, beschikbaar en gratis fysiek geheugen
  - Totaal, beschikbaar en gratis swapgeheugen
  - Profilering van CPU-opname en verwerkt CPU-gebruik met interval van 5 seconden
  - Profilering van het vastleggen van het geheugengebruik van processen met een interval van 5 seconden

- Netwerken  
  - Lijst met netwerkadapters met statistieken over adapters
  - Tabel netwerkroutering
  - Geopende poorten en status

- Storage
  - Lijst met apparaten blokkeren
  - Lijst met partities
  - Lijst met punten monteren
  - MDADM volume-informatie
  - GEGEVENS over het LVM-volume
  - Profilering vastleggen op alle schijven op 5-seconden interval

- Logboeken
  - /var/log/berichten
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/azure/[extensiemap]/\*logboek\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Output van journalctl voor de laatste vijf dagen

- [Metagegevens van Azure-virtuele machine-instantie](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)

>[!Note]
>[`*`] PCI-informatie wordt nog niet verzameld over Debian- en SLES-distributies

## <a name="run-the-perfinsights-linux-on-your-vm"></a>Voer de PerfInsights Linux uit op uw VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Wat moet ik weten voordat ik de tool uitvoer

#### <a name="tool-requirements"></a>Gereedschapsvereisten

- Deze tool moet worden uitgevoerd op de VM die het prestatieprobleem heeft.
- Python 2.7 moet op de VM worden geïnstalleerd

- De volgende distributies worden momenteel ondersteund:

    | Distributie               | Versie                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux-server        | 6.10`*`[ ], 7.3, 7.6, 7.5 (Oracle-Database-Ee 13.8 marketplace image)|
    | CentOS                     | 6,5`*`[ ], 7,6                                    |
    | RHEL                       | 7.2, 7.5, 8.0 [ ]`*`                               |
    | Ubuntu                     | 14.04, 16.04, 18.04                               |
    | Debian                     | 8, 9, 10 [`*`]                                    |
    | SLES                       | 12 SP4`*`[ ]                                      |
    |                            |                                                   |

>[!Note]
>[`*`] Raadpleeg de sectie [Bekende problemen](#known-issues)

### <a name="known-issues"></a>Bekende problemen

- RHEL 8 heeft Python niet standaard geïnstalleerd. Om PerfInsights Linux uit te voeren, moet u eerst Python 2.7 installeren

- Het verzamelen van informatie van gastagenten kan mislukken op CentOS 6.x

- INFORMATIE over PCI-apparaten wordt niet verzameld op op Debian gebaseerde distributies

- LVM-informatie wordt gedeeltelijk verzameld over sommige distributies

### <a name="how-do-i-run-perfinsights"></a>Hoe kan ik PerfInsights uitvoeren

U PerfInsights op een virtuele machine uitvoeren door Azure Performance Diagnostics te installeren vanuit Azure-portal. U het ook uitvoeren als een standalone tool.

>[!Note]
>PerfInsights verzamelt en analyseert eenvoudig de gegevens. Het maakt geen wijzigingen aan het systeem.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>PerfInsights installeren en uitvoeren vanuit de Azure-portal

Zie [Azure Performance Diagnostics](performance-diagnostics.md)voor meer informatie over deze optie.  

#### <a name="run-perfinsights-in-standalone-mode"></a>PerfInsights uitvoeren in zelfstandige modus

Voer de volgende stappen uit om het gereedschap PerfInsights uit te voeren:

1. Download [PerfInsights.tar.gz](https://aka.ms/perfinsightslinuxdownload) naar een map op uw virtuele machine en haal de inhoud uit met de onderstaande opdrachten van de terminal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Navigeer naar de `perfinsights.py` map die bestand `perfinsights.py` bevat en voer deze uit om de beschikbare opdrachtregelparameters weer te geven.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Schermafbeelding van PerfInsights Linux commandline-uitvoer](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    De basissyntaxis voor het uitvoeren van PerfInsights-scenario's is:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    U het onderstaande voorbeeld gebruiken om een scenario voor snelle prestatieanalyse gedurende 1 minuut uit te voeren en de resultaten te maken onder /tmp/outputmap:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    U het onderstaande voorbeeld gebruiken om een scenario voor prestatieanalyse gedurende 5 minuten uit te voeren en de resultaatteerbal naar het opslagaccount te uploaden:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Voordat een scenario wordt uitgevoerd, vraagt PerfInsights de gebruiker om in te stemmen met het delen van diagnostische informatie en in te stemmen met de EULA. Gebruik **-een optie --accepteren-disclaimer-en-share-diagnostics** om deze prompts over te slaan.
    >
    >Als u een actief ondersteuningsticket hebt bij Microsoft en PerfInsights uitvoert op verzoek van de ondersteuningstechnicus waarmee u werkt, moet u het ondersteuningsticketnummer opgeven met behulp van de optie **-s of --support-request.**

Wanneer de run is voltooid, verschijnt er een nieuw teerbestand in dezelfde map als PerfInsights, tenzij er geen uitvoermap is opgegeven. De naam van het bestand is **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz.** U dit bestand naar de ondersteuningsmedewerker sturen voor analyse of het rapport in het bestand openen om bevindingen en aanbevelingen te bekijken.

## <a name="review-the-diagnostics-report"></a>Het diagnoserapport bekijken

In het **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz-bestand** vindt u een HTML-rapport met de bevindingen van PerfInsights. Als u het rapport wilt bekijken, vouwt u het bestand **PerformanceDiagnostics\_\_yyyy-MM-dd hh-mm-ss-fff.tar.gz** uit en opent u het bestand **PerfInsights Report.html.**

### <a name="overview-tab"></a>Tabblad Overzicht

Het tabblad **Overzicht** bevat basisgegevens voor de uitgevoerd en informatie over virtuele machines. Op het tabblad **Bevindingen** wordt een overzicht weergegeven van de aanbevelingen uit alle verschillende secties van het rapport PerfInsights.

![Schermafbeelding van perfInsights-rapport](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Schermafbeelding van perfInsights-rapport](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Bevindingen gecategoriseerd als hoog zijn bekende problemen die prestatieproblemen kunnen veroorzaken. Bevindingen die als medium zijn gecategoriseerd, vertegenwoordigen niet-optimale configuraties die niet noodzakelijkerwijs prestatieproblemen veroorzaken. Bevindingen gecategoriseerd als laag zijn informatieve verklaringen alleen.

Bekijk de aanbevelingen en links voor alle hoge en middelgrote bevindingen. Meer informatie over hoe ze de prestaties kunnen beïnvloeden, en ook over best practices voor prestatiegeoptimaliseerde configuraties.

### <a name="cpu-tab"></a>Tabblad CPU

**Het CPU-tabblad** bevat informatie over het cpu-verbruik voor het hele systeem tijdens de PerfInsights-run. Informatie over hoge CPU-gebruiksperioden en de best lopende CPU-consumenten zullen nuttig zijn om hoge CPU-gerelateerde problemen op te lossen.

![Schermafbeelding van het CPU-tabblad Van PerfInsights Report](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Tabblad Opslag

De **sectie Bevindingen** toont verschillende bevindingen en aanbevelingen met betrekking tot opslag.

De **tabbladen Blokapparaten** en andere gerelateerde secties, zoals **partities,** **LVM**en **MDADM,** beschrijven hoe blokapparaten zijn geconfigureerd en aan elkaar zijn gerelateerd.

![Schermafbeelding van het tabblad Opslag](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Schermafbeelding van het tabblad MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Tabblad Linux

Het **tabblad Linux** bevat informatie over de hardware en het besturingssysteem dat in uw VM wordt uitgevoerd. Details omvatten een lijst met lopende processen en informatie over Gastagent, PCI, CPU, Drivers en LIS-stuurprogramma's.

![Schermafbeelding van het tabblad Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Volgende stappen

U diagnostische logboeken en rapporten uploaden naar Microsoft Support voor verdere controle. Wanneer u met de Microsoft Support-medewerkers werkt, wordt u mogelijk gevraagd de uitvoer die door PerfInsights wordt gegenereerd, te verzenden om te helpen bij het probleemoplossingsproces.

In de volgende schermafbeelding wordt een bericht weergegeven dat vergelijkbaar is met wat u mogelijk ontvangt:

![Schermafbeelding van voorbeeldbericht van Microsoft Support](media/how-to-use-perfinsights-linux/support-email.png)

Volg de instructies in het bericht om toegang te krijgen tot de werkruimte voor bestandsoverdracht. Voor extra beveiliging moet u uw wachtwoord bij het eerste gebruik wijzigen.

Nadat u zich hebt aangemeld, vindt u een dialoogvenster om het **\_PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.tar.gz-bestand** te uploaden dat is verzameld door PerfInsights.
