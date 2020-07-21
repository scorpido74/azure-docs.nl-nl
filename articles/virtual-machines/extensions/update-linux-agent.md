---
title: De Azure Linux-agent bijwerken vanuit GitHub
description: Meer informatie over het bijwerken van de Azure Linux-agent voor uw virtuele Linux-machine in azure
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
ms.openlocfilehash: 0b13dca7f4a33a7fb9ea55a1505c26a97160d0d8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502942"
---
# <a name="how-to-update-the-azure-linux-agent-on-a-vm"></a>De Azure Linux-agent op een virtuele machine bijwerken

Als u uw [Azure Linux-agent](https://github.com/Azure/WALinuxAgent) op een virtuele Linux-machine in azure wilt bijwerken, moet u al beschikken over:

- Een actieve Linux-VM in Azure.
- Een verbinding met die virtuele Linux-machine met behulp van SSH.

U moet altijd controleren op een pakket in de Linux distributie-opslag plaats. Het is mogelijk dat het beschik bare pakket niet de meest recente versie is, maar het inschakelen van auto update zorgt ervoor dat de Linux-agent altijd de meest recente update krijgt. Als u problemen hebt met de installatie van de pakket beheerders, moet u de distributie-leverancier ondersteunen.

> [!NOTE]
> Zie [goedgekeurde Linux-distributies op Azure](../linux/endorsed-distros.md) voor meer informatie

## <a name="minimum-virtual-machine-agent-support-in-azure"></a>Minimale ondersteuning van Virtual Machine agent in azure
Controleer de [minimale versie ondersteuning voor virtuele-machine agenten in azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) voordat u doorgaat.

## <a name="updating-the-azure-linux-agent"></a>De Azure Linux-agent bijwerken

## <a name="ubuntu"></a>Ubuntu

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
apt list --installed | grep walinuxagent
```

#### <a name="update-package-cache"></a>Pakket cache bijwerken

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo apt-get install walinuxagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="restart-the-waagent-service"></a>De waagent-service opnieuw starten

#### <a name="restart-agent-for-1404"></a>Agent opnieuw starten voor 14,04

```bash
initctl restart walinuxagent
```

#### <a name="restart-agent-for-1604--1704"></a>Agent opnieuw starten voor 16,04/17,04

```bash
systemctl restart walinuxagent.service
```

## <a name="red-hat--centos"></a>Red Hat/CentOS

### <a name="rhelcentos-6"></a>RHEL/CentOS 6

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Beschik bare updates controleren

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo yum install WALinuxAgent
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

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

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
sudo yum list WALinuxAgent
```

#### <a name="check-available-updates"></a>Beschik bare updates controleren

```bash
sudo yum check-update WALinuxAgent
```

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo yum install WALinuxAgent  
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

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

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Beschik bare updates controleren

In de bovenstaande uitvoer wordt weer gegeven of het pakket up-to-date is.

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

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

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
zypper info python-azure-agent
```

#### <a name="check-available-updates"></a>Beschik bare updates controleren

In de uitvoer van de bovenstaande wordt weer gegeven of het pakket up-to-date is.

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo zypper install python-azure-agent
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld 

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

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

### <a name="debian-7-jesse-debian-7-stretch"></a>Debian 7 "Jesse"/Debian 7 "stretch"

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
dpkg -l | grep waagent
```

#### <a name="update-package-cache"></a>Pakket cache bijwerken

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo apt-get install waagent
```

#### <a name="enable-agent-auto-update"></a>Automatische update van de agent inschakelen
Deze versie van Debian heeft geen versie >= 2.0.16, daarom is auto update niet beschikbaar voor IT. Met de uitvoer van de bovenstaande opdracht wordt weer gegeven of het pakket up-to-date is.



### <a name="debian-8-jessie--debian-9-stretch"></a>Debian 8 "Jessie"/Debian 9 "stretch"

#### <a name="check-your-current-package-version"></a>De huidige pakket versie controleren

```bash
apt list --installed | grep waagent
```

#### <a name="update-package-cache"></a>Pakket cache bijwerken

```bash
sudo apt-get -qq update
```

#### <a name="install-the-latest-package-version"></a>Installeer de meest recente pakket versie

```bash
sudo apt-get install waagent
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld
Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

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

Zorg ervoor dat de `Addons` opslag plaats is ingeschakeld voor Oracle Linux. Kies voor het bewerken van het bestand `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) of `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux) en wijzig de regel in `enabled=0` `enabled=1` onder **[ol6_addons]** of **[ol7_addons]** in dit bestand.

Als u de nieuwste versie van de Azure Linux-agent wilt installeren, typt u:

```bash
sudo yum install WALinuxAgent
```

Als u de opslag plaats voor invoeg toepassingen niet vindt, kunt u deze regels aan het einde van het. opslag plaats-bestand toevoegen volgens uw Oracle Linux-release:

Voor virtuele machines van Oracle Linux 6:

```sh
[ol6_addons]
name=Add-Ons for Oracle Linux $releasever ($basearch)
baseurl=https://public-yum.oracle.com/repo/OracleLinux/OL6/addons/x86_64
gpgkey=https://public-yum.oracle.com/RPM-GPG-KEY-oracle-ol6
gpgcheck=1
enabled=1
```

Voor virtuele machines van Oracle Linux 7:

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

Normaal gesp roken hoeft u alleen maar de volgende stappen uit te voeren als u deze om een bepaalde reden rechtstreeks wilt installeren https://github.com .


## <a name="update-the-linux-agent-when-no-agent-package-exists-for-distribution"></a>De Linux-agent bijwerken wanneer er geen agent pakket bestaat voor distributie

Wget installeren (er zijn enkele distributies die niet standaard worden geïnstalleerd, zoals Red Hat, CentOS en Oracle Linux versie 6,4 en 6,5) door te typen `sudo yum install wget` op de opdracht regel.

### <a name="1-download-the-latest-version"></a>1. down load de nieuwste versie
Open [de release van de Azure Linux-agent in github](https://github.com/Azure/WALinuxAgent/releases) op een webpagina en ontdek het meest recente versie nummer. (U kunt uw huidige versie vinden door te typen `waagent --version` .)

#### <a name="for-version-22x-or-later-type"></a>Voor versie 2.2. x of hoger typt u:
```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.x.zip
unzip v2.2.x.zip
cd WALinuxAgent-2.2.x
```

In de volgende regel wordt versie 2.2.0 als voor beeld gebruikt:

```bash
wget https://github.com/Azure/WALinuxAgent/archive/v2.2.14.zip
unzip v2.2.14.zip  
cd WALinuxAgent-2.2.14
```

### <a name="2-install-the-azure-linux-agent"></a>2. de Azure Linux-agent installeren

#### <a name="for-version-22x-use"></a>Gebruik voor versie 2.2. x het volgende:
Het is mogelijk dat u het pakket `setuptools` eerst moet installeren. Zie [hier](https://pypi.python.org/pypi/setuptools). Voer vervolgens

```bash
sudo python setup.py install
```

#### <a name="ensure-auto-update-is-enabled"></a>Controleren of automatisch bijwerken is ingeschakeld

Controleer eerst of deze is ingeschakeld:

```bash
cat /etc/waagent.conf
```

Zoek auto update. enabled. Als u deze uitvoer ziet, wordt deze ingeschakeld:

```bash
# AutoUpdate.Enabled=y
AutoUpdate.Enabled=y
```

Uitvoeren inschakelen:

```bash
sudo sed -i 's/# AutoUpdate.Enabled=n/AutoUpdate.Enabled=y/g' /etc/waagent.conf
```

### <a name="3-restart-the-waagent-service"></a>3. Start de waagent-service opnieuw
Voor de meeste Linux-distributies:

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

### <a name="4-confirm-the-azure-linux-agent-version"></a>4. Controleer de versie van de Azure Linux-agent
    
```bash
waagent -version
```

De bovenstaande opdracht werkt mogelijk niet voor CoreOS.

U zult zien dat de versie van de Azure Linux-agent is bijgewerkt naar de nieuwe versie.

Zie het [Leesmij-bestand voor Azure Linux-agent](https://github.com/Azure/WALinuxAgent)voor meer informatie over de Azure Linux-agent.
