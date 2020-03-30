---
title: Overzicht van Azure Linux VM-agent
description: Meer informatie over het installeren en configureren van Linux Agent (waagent) om de interactie van uw virtuele machine met Azure Fabric Controller te beheren.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073858"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>De Azure Linux-agent begrijpen en gebruiken

De Microsoft Azure Linux Agent (waagent) beheert Linux-& FreeBSD-inrichting en VM-interactie met de Azure Fabric Controller. Naast de Linux Agent die provisioning-functionaliteit biedt, biedt Azure ook de mogelijkheid om cloud-init te gebruiken voor sommige Linux-beste's. De Linux Agent biedt de volgende functionaliteit voor Linux en FreeBSD IaaS implementaties:

> [!NOTE]
> Zie voor meer informatie de [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Image Provisioning**
  
  * Een gebruikersaccount aanmaken
  * SSH-verificatietypen configureren
  * Implementatie van Openbare Sleutels en sleutelparen van SSH
  * De hostnaam instellen
  * De hostnaam publiceren op het platform DNS
  * Rapportage SSH host key fingerprint naar het platform
  * Resourceschijfbeheer
  * De resourceschijf opmaken en monteren
  * Swapruimte configureren
* **Networking**
  
  * Beheert routes om de compatibiliteit met dhcp-servers van het platform te verbeteren
  * Zorgt voor de stabiliteit van de naam van de netwerkinterface
* **Kernel**
  
  * Virtuele NUMA configureren (uitschakelen `2.6.37`voor kernel <)
  * Verbruikt Hyper-V entropie voor /dev/random
  * SCSI-time-outs configureren voor het hoofdapparaat (wat op afstand kan zijn)
* **Diagnostiek**
  
  * Console omleiding naar de seriële poort
* **SCVMM-implementaties**
  
  * Detecteert en startstraps de VMM-agent voor Linux bij het uitvoeren in een System Center Virtual Machine Manager 2012 R2-omgeving
* **VM-extensie**
  
  * Injecteer component geschreven door Microsoft en Partners in Linux VM (IaaS) om software- en configuratieautomatisering mogelijk te maken
  * VM Extension referentie-implementatie op[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Communicatie
De informatiestroom van het platform naar de agent vindt plaats via twee kanalen:

* Een opstarttijd gekoppelde DVD voor IaaS-implementaties. Deze dvd bevat een OVF-compatibel configuratiebestand dat alle informatie bevat die andere informatie bevat dan de werkelijke SSH-sleutelparen.
* Een TCP-eindpunt dat een REST-API blootstelt die wordt gebruikt om implementatie- en topologieconfiguratie te verkrijgen.

## <a name="requirements"></a>Vereisten
De volgende systemen zijn getest en staan bekend om het werken met de Azure Linux Agent:

> [!NOTE]
> Deze lijst kan afwijken van de officiële lijst met ondersteunde systemen op het Microsoft Azure Platform, zoals hier beschreven:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

Andere ondersteunde systemen:

* FreeBSD 10+ (Azure Linux Agent v2.0.10+)

De Linux-agent is afhankelijk van sommige systeempakketten om goed te kunnen functioneren:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Filesystem hulpprogramma's: sfdisk, fdisk, mkfs, gescheiden
* Wachtwoord tools: chpasswd, sudo
* Tekstverwerkingstools: sed, grep
* Netwerkhulpprogramma's: ip-route
* Kernel-ondersteuning voor het monteren van UDF-bestandssystemen.

## <a name="installation"></a>Installeren
Installatie met behulp van een RPM of een DEB-pakket uit de pakketopslagplaats van uw distributie is de voorkeursmethode voor het installeren en upgraden van de Azure Linux Agent. Alle [goedgekeurde distributieproviders](../linux/endorsed-distros.md) integreren het Azure Linux-agentpakket in hun afbeeldingen en repositories.

Raadpleeg de documentatie in de [Azure Linux Agent repo op GitHub](https://github.com/Azure/WALinuxAgent) voor geavanceerde installatieopties, zoals het installeren van de bron of naar aangepaste locaties of voorvoegsels.

## <a name="command-line-options"></a>Opdrachtregelopties
### <a name="flags"></a>Vlaggen
* verbose: verbositeit van de opgegeven opdracht vergroten
* force: Interactieve bevestiging overslaan voor sommige opdrachten

### <a name="commands"></a>Opdrachten
* help: geeft de ondersteunde opdrachten en vlaggen weer.
* deprovision: Probeer het systeem schoon te maken en geschikt te maken voor hervoorziening. De volgende bewerking wordt verwijderd:
  
  * Alle SSH-hostsleutels (als Provisioning.Regenerateing.RegenerateSshHostKeyPair 'y' in het configuratiebestand is)
  * Nameserverconfiguratie in /etc/resolv.conf
  * Hoofdwachtwoord van /etc/shadow (als Provisioning.DeleteRootPassword 'y' in het configuratiebestand is)
  * DHCP-clientleases in cache
  * De hostnaam opnieuw instellen naar localhost.localdomain

> [!WARNING]
> Deprovisioning garandeert niet dat het beeld wordt gewist van alle gevoelige informatie en geschikt is voor herverdeling.
> 
> 

* deprovision+user: Voert alles uit in -deprovision (boven) en verwijdert ook het laatst ingerichte gebruikersaccount (verkregen uit /var/lib/waagent) en bijbehorende gegevens. Deze parameter is bij het de-provisioneren van een afbeelding die eerder was ingericht op Azure, zodat deze kan worden vastgelegd en opnieuw kan worden gebruikt.
* versie: Geeft de versie van waagent weer
* serialconsole: configureert GRUB om ttyS0 (de eerste seriële poort) te markeren als de opstartconsole. Dit zorgt ervoor dat kernel bootup logs worden verzonden naar de seriële poort en beschikbaar gesteld voor het opsporen van debugging.
* daemon: Voer waagent uit als daemon om de interactie met het platform te beheren. Dit argument is opgegeven als waagent in het waagent init-script.
* start: Waagent uitvoeren als achtergrondproces

## <a name="configuration"></a>Configuratie
Een configuratiebestand (/etc/waagent.conf) regelt de acties van waagent. Als volgt ziet u een voorbeeldconfiguratiebestand:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

De volgende verschillende configuratieopties worden beschreven. Configuratieopties zijn van drie typen; Booleaan, tekenreeks of geheel getal. De Booleaanse configuratieopties kunnen worden opgegeven als "y" of "n". Het speciale trefwoord 'Geen' kan worden gebruikt voor bepaalde configuratievermeldingen van tekenreekstypen als de volgende details:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Hierdoor kan de gebruiker de inrichtingsfunctionaliteit in de agent in- of uitschakelen. Geldige waarden zijn "y" of "n". Als de inrichting is uitgeschakeld, blijven SSH-host- en gebruikerssleutels in de afbeelding behouden en wordt elke configuratie die is opgegeven in de Azure Provisioning API genegeerd.

> [!NOTE]
> De `Provisioning.Enabled` parameter standaard op 'n' op Ubuntu Cloud Images die cloud-init gebruiken voor provisioning.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Als ingesteld, wordt het hoofdwachtwoord in het /etc/schaduwbestand gewist tijdens het inrichtingsproces.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Indien ingesteld, worden alle SSH host sleutelparen (ecdsa, dsa en rsa) verwijderd tijdens het inrichtingsproces van /etc/ssh/. En er wordt één vers sleutelpaar gegenereerd.

Het versleutelingstype voor het nieuwe sleutelpaar is configureerbaar via het provisioning.SshHostKeyPairType-item. Sommige distributies maken opnieuw SSH-sleutelparen voor ontbrekende versleutelingstypen wanneer de SSH-daemon opnieuw wordt gestart (bijvoorbeeld bij een herstart).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Dit kan worden ingesteld op een type versleutelingsalgoritme dat wordt ondersteund door de SSH-daemon op de virtuele machine. De doorgaans ondersteunde waarden zijn "rsa", "dsa" en "ecdsa". "putty.exe" op Windows ondersteunt geen "ecdsa". Dus, als u van plan bent om putty.exe te gebruiken op Windows om verbinding te maken met een Linux-implementatie, gebruik dan "rsa" of "dsa".

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Als deze optie is ingesteld, controleert waagent de virtuele Linux-machine op hostnamewijzigingen (zoals geretourneerd door de opdracht Hostname) en werkt deze automatisch de netwerkconfiguratie in de afbeelding bij om de wijziging weer te geven. Om de naamswijziging naar de DNS-servers te duwen, wordt netwerken opnieuw gestart in de virtuele machine. Dit resulteert in een kort verlies van internetverbinding.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Als deze is ingesteld, decodeert waagent CustomData van Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Als deze is ingesteld, voert waagent CustomData uit na inlevering.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Met deze optie kan het wachtwoord voor de sys-gebruiker worden gereset; standaard is uitgeschakeld.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritme gebruikt door crypte bij het genereren van wachtwoord hash.  
 1 - MD5  
 2a - Blaasvis  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Lengte van willekeurig zout dat wordt gebruikt bij het genereren van wachtwoordhash.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Als deze is ingesteld, wordt de door het platform geleverde resourceschijf opgemaakt en gemonteerd door waagent als het bestandssysteemtype dat door de gebruiker wordt aangevraagd in "ResourceDisk.Filesystem" iets anders is dan "ntfs". Een enkele partitie van het type Linux (83) wordt beschikbaar gesteld op de schijf. Deze partitie is niet geformatteerd als deze met succes kan worden gemonteerd.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Hiermee wordt het bestandssysteemtype voor de resourceschijf opgegeven. Ondersteunde waarden verschillen per Linux-distributie. Als de tekenreeks X is, dan mkfs. X moet aanwezig zijn op de Linux-afbeelding. SLES 11-afbeeldingen moeten doorgaans 'ext3' gebruiken. FreeBSD beelden moeten gebruik maken van 'ufs2' hier.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Hiermee wordt het pad opgegeven waarop de resourceschijf is gemonteerd. De resourceschijf is een *tijdelijke* schijf en kan worden geleegd wanneer de vm is gedeprovisioneerd.

**ResourceDisk.MountOpties**  
```
Type: String  
Default: None
```
Hiermee geeft u de opties voor het instellen van schijven op die moeten worden doorgegeven aan de opdracht mount -o. Dit is een door komma's gescheiden lijst met waarden, bijvoorbeeld. 'nodev, nosuid'. Zie mount(8) voor meer informatie.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Als ingesteld, wordt een swapbestand (/swapfile) gemaakt op de resourceschijf en toegevoegd aan de systeemswapruimte.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
De grootte van het swapbestand in megabytes.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Als ingesteld, log verbosity wordt versterkt. Waagent logt in op /var/log/waagent.log en maakt gebruik van de systeemlogfunctie om logboeken te roteren.

**Os. InschakelenRDMA**  
```
Type: Boolean  
Default: n
```
Als deze is ingesteld, probeert de agent een RDMA-kernelstuurprogramma te installeren en te laden dat overeenkomt met de versie van de firmware op de onderliggende hardware.

**Os. RootDeviceScsiTime-out:**  
```
Type: Integer  
Default: 300
```
Met deze instelling configureert u de SCSI-time-out in enkele seconden op de os-schijf- en gegevensstations. Als dit niet is ingesteld, worden de standaardinstellingen van het systeem gebruikt.

**Os. OpensslPath:**  
```
Type: String  
Default: None
```
Deze instelling kan worden gebruikt om een alternatief pad op te geven voor de openssl-binaire om te gebruiken voor cryptografische bewerkingen.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Als deze is ingesteld, gebruikt de agent deze proxyserver om toegang te krijgen tot het internet. 

**AutoUpdate.Ingeschakeld**
```
Type: Boolean
Default: y
```
Automatische update in- of uitschakelen voor doelstatusverwerking; standaard is ingeschakeld.



## <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Afbeeldingen
Ubuntu Cloud Images maakt gebruik van [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) om veel configuratietaken uit te voeren die anders zouden worden beheerd door de Azure Linux Agent. De volgende verschillen zijn van toepassing:

* **Provisioning.Enabled** defaults to "n" on Ubuntu Cloud Images that use cloud-init to performing tasks.
* De volgende configuratieparameters hebben geen effect op Ubuntu Cloud Images die cloud-init gebruiken om de resourceschijf te beheren en ruimte te wisselen:
  
  * **ResourceDisk.Opmaak**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Zie voor meer informatie de volgende bronnen om het bevestigingspunt voor resourceschijven te configureren en ruimte te wisselen op Ubuntu Cloud Images tijdens de inrichting:
  
  * [Ubuntu Wiki: Swappartities configureren](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Aangepaste gegevens injecteren in een Azure Virtual Machine](../windows/classic/inject-custom-data.md)

