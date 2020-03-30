---
title: De Azure Linux-agent van GitHub bijwerken
description: Meer informatie over het bijwerken van Azure Linux Agent voor uw Linux VM in Azure
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: f1f19300-987d-4f29-9393-9aba866f049c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 08/02/2017
ms.author: mimckitt
ms.openlocfilehash: e4489f7c810799ca8e89565fe698f398f942b089
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78251710"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>De Azure Linux Agent bijwerken op een vm

Als u uw [Azure Linux Agent](https://github.com/Azure/WALinuxAgent) wilt bijwerken op een Linux-vm in Azure, moet u al het:

- Een draaiende Linux VM in Azure.
- Een verbinding met die Linux VM met SSH.

Je moet altijd eerst controleren op een pakket in de Linux distro repository. Het is mogelijk dat het pakket beschikbaar is misschien niet de nieuwste versie, maar het inschakelen van auto-update zal ervoor zorgen dat de Linux Agent altijd de nieuwste update krijgt. Mocht u problemen hebben met het installeren van de package managers, moet u ondersteuning zoeken bij de distro-leverancier.

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimale ondersteuning voor virtuele machineagent in Azure
Controleer de [minimale versieondersteuning voor virtuele machineagents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voordat u verdergaat.

## <a name="updating-the-azure-linux-agent"></a>De Azure Linux-agent bijwerken

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Pakketcache bijwerken

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>De waagent-service opnieuw starten

#### <a name="restart-agent-for-1404"></a>Herstartagent voor 14.04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Herstart agent voor 16.04 / 17.04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat / CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Beschikbare updates controleren

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/\# AutoUpdate.Enabled=y/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>De waagent-service opnieuw starten

```
sudo service waagent restart
```

### <a name="rhelcentos-7"></a>RHEL/CentOS 7

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Beschikbare updates controleren

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>De waagent-service opnieuw starten

```bash
sudo systemctl restart waagent.service
```

## <a name="suse-sles"></a>SUSE SLES

### <a name="suse-sles-11-sp4"></a>SUSE SLES 11 SP4

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Beschikbare updates controleren

De bovenstaande uitvoer laat u zien of het pakket up-to-date is.

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>De waagent-service opnieuw starten

```bash
sudo /etc/init.d/waagent restart
```

### <a name="suse-sles-12-sp2"></a>SUSE SLES 12 SP2

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Beschikbare updates controleren

In de uitvoer van het bovenstaande wordt u weergegeven of het pakket up-to-date is.

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>De waagent-service opnieuw starten

```bash
sudo systemctl restart waagent.service
```

## <a name="debian"></a>Debian

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/ Debian 7 "Stretch"

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Pakketcache bijwerken

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Automatische update van agent inschakelen
Deze versie van Debian heeft geen versie >= 2.0.16, daarom is AutoUpdate er niet voor beschikbaar. De uitvoer van de bovenstaande opdracht toont u of het pakket up-to-date is.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie" / Debian 9 "Stretch"

#### <a name="check-your-current-package-version"></a>Uw huidige pakketversie controleren

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Pakketcache bijwerken

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>De nieuwste pakketversie installeren

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld
Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
Restart the waagent service
sudo systemctl restart walinuxagent.service
```

## <a name="oracle-linux-6-and-oracle-linux-7"></a>Oracle Linux 6 en Oracle Linux 7

Voor Oracle Linux moet `Addons` u ervoor zorgen dat de repository is ingeschakeld. Kies ervoor om `/etc/yum.repos.d/public-yum-ol6.repo`het bestand (Oracle `/etc/yum.repos.d/public-yum-ol7.repo`Linux 6) of `enabled=0` (Oracle Linux) te bewerken en de regel te wijzigen in `enabled=1` onder **[ol6_addons]** of **[ol7_addons]** in dit bestand.

Typ vervolgens de nieuwste versie van de Azure Linux Agent om de nieuwste versie van de Azure Linux-agent te installeren:

```bash
sudo yum install WALinuxAgent
```

Als u de add-on repository niet vindt, u deze regels eenvoudig toevoegen aan het einde van uw .repo-bestand volgens uw Oracle Linux-release:

Voor Oracle Linux 6 virtuele machines:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Voor Oracle Linux 7 virtuele machines:

```sh
[ol7_addons]
name=Oracle Linux $releasever Add ons ($basearch)
baseurl=http://public-yum.oracle.com/repo/OracleLinux/OL7/addons/$basearch/
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-oracle
gpgcheck=1
enabled=1
```

Typ vervolgens:

```bash
sudo yum update WALinuxAgent
```

Meestal is dit alles wat je nodig hebt, maar https://github.com als je om wat voor reden dan ook nodig hebt om het direct te installeren, gebruik dan de volgende stappen.


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>Update de Linux Agent wanneer er geen agent pakket bestaat voor distributie

Installeer wget (er zijn een aantal distro's die het niet standaard installeren, zoals Red Hat, CentOS en `sudo yum install wget` Oracle Linux-versies 6.4 en 6.5) door op de opdrachtregel te typen.

### <a name="1-download-the-latest-version"></a>1. Download de nieuwste versie
Open [de release van Azure Linux Agent in GitHub](https://github.com/Azure/WALinuxAgent/releases) op een webpagina en ontdek het nieuwste versienummer. (U uw huidige `waagent --version`versie vinden door te typen .)

#### <a name="for-version-22x-or-later-type"></a>Typ voor versie 2.2.x of hoger:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

In de volgende regel wordt bijvoorbeeld versie 2.2.0 gebruikt:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. Installeer de Azure Linux Agent

#### <a name="for-version-22x-use"></a>Gebruik voor versie 2.2.x:
Het kan nodig zijn `setuptools` om het pakket eerst te installeren - zie [hier](https://pypi.python.org/pypi/setuptools). Voer vervolgens

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Ervoor zorgen dat automatische update is ingeschakeld

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek 'AutoUpdate.Enabled'. Als u deze uitvoer ziet, is deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. De waagent-service opnieuw starten
Voor de meeste Linux distro's:

```bash
sudo service waagent restart
```

Gebruik voor Ubuntu:

```bash
sudo service walinuxagent restart
```

Gebruik voor CoreOS:

```bash
sudo systemctl restart waagent
```

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Bevestig de Azure Linux Agent-versie
    
```bash
waagent -version
```

Voor CoreOS werkt de bovenstaande opdracht mogelijk niet.

U zult zien dat de Azure Linux Agent-versie is bijgewerkt naar de nieuwe versie.

Zie [Azure Linux Agent README voor](https://github.com/Azure/WALinuxAgent)meer informatie over de Azure Linux Agent.
