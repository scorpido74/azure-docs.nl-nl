---
title: MySQL installeren op een OpenSUSE VM in Azure
description: Leer MySQL installeren op een Virtuele OpenSUSE Linux-machine in Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 07/11/2018
ms.author: cynthn
ms.openlocfilehash: 0d3f0a61da3654c31c99cfac43c86b081876f700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944580"
---
# <a name="install-mysql-on-a-virtual-machine-running-opensuse-linux-in-azure"></a>MySQL installeren op een virtuele machine met OpenSUSE Linux in Azure

[MySQL](https://www.mysql.com) is een populaire, open-source SQL-database. In deze zelfstudie ziet u hoe u een virtuele machine maakt waarop OpenSUSE Linux wordt uitgevoerd en installeert u MySQL.


Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, hebt u Azure CLI versie 2.0 of hoger nodig. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli).

## <a name="create-a-virtual-machine-running-opensuse-linux"></a>Een virtuele machine maken met OpenSUSE Linux

Maak eerst een resourcegroep. In dit voorbeeld wordt de resourcegroep *mySQSUSEResourceGroup* genoemd en wordt deze gemaakt in de regio *Oost-VS.*

```azurecli-interactive
az group create --name mySQLSUSEResourceGroup --location eastus
```

Maak de VM. In dit voorbeeld wordt de VM *myVM* genoemd en wordt de VM-grootte *Standard_D2s_v3,* maar u moet de [VM-grootte](sizes.md) kiezen die volgens u het meest geschikt is voor uw werkbelasting.

```azurecli-interactive
az vm create --resource-group mySQLSUSEResourceGroup \
   --name myVM \
   --image openSUSE-Leap \
   --size Standard_D2s_v3 \
   --generate-ssh-keys
```

U moet ook een regel toevoegen aan de netwerkbeveiligingsgroep om verkeer via poort 3306 voor MySQL toe te staan.

```azurecli-interactive
az vm open-port --port 3306 --resource-group mySQLSUSEResourceGroup --name myVM
```

## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

U gebruikt SSH om verbinding te maken met de VM. In dit voorbeeld is het openbare IP-adres van de VM *10.111.112.113*. U het IP-adres in de uitvoer zien wanneer u de vm hebt gemaakt.

```azurecli-interactive  
ssh 10.111.112.113
```

 
## <a name="update-the-vm"></a>De VM bijwerken
 
Nadat u bent verbonden met de VM, installeert u systeemupdates en patches. 
   
```bash
sudo zypper update
```

Volg de aanwijzingen om uw vm bij te werken.

## <a name="install-mysql"></a>MySQL installeren 


Installeer de MySQL in de VM over SSH. Beantwoord de vragen waar nodig.

```bash
sudo zypper install mysql
```
 
Stel MySQL in om te starten wanneer het systeem wordt opgestart. 

```bash
sudo systemctl enable mysql
```
Controleer of MySQL is ingeschakeld.

```bash
systemctl is-enabled mysql
```

Dit moet terugkeren: ingeschakeld.

Start de server opnieuw op.

```bash
sudo reboot
```


## <a name="mysql-password"></a>MySQL-wachtwoord

Na de installatie is het MySQL-hoofdwachtwoord standaard leeg. Voer het **mysql\_secure installation\_** script uit om MySQL te beveiligen. Het script vraagt u om het MySQL-hoofdwachtwoord te wijzigen, anonieme gebruikersaccounts te verwijderen, externe hoofdaanmelding uit te schakelen, testdatabases te verwijderen en de lijst met bevoegdheden opnieuw te laden. 

Zodra de server opnieuw wordt opgestart, ssh naar de VM weer.

```azurecli-interactive  
ssh 10.111.112.113
```



```bash
mysql_secure_installation
```

## <a name="sign-in-to-mysql"></a>Aanmelden bij MySQL

U zich nu aanmelden en de MySQL-prompt invoeren.

```bash  
mysql -u root -p
```
Hiermee wordt u overgezet naar de MySQL-prompt, waar u SQL-instructies uitgeven om met de database te communiceren.

Maak nu een nieuwe MySQL-gebruiker.

```sql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
De puntkomma (;) aan het einde van de lijn is cruciaal voor het beëindigen van het commando.


## <a name="create-a-database"></a>Database maken


Maak een database `mysqluser` en geef de gebruiker machtigingen.

```sql
CREATE DATABASE testdatabase;
GRANT ALL ON testdatabase.* TO 'mysqluser'@'localhost' IDENTIFIED BY 'password';
```
   
Namen en wachtwoorden van databasegebruikers worden alleen gebruikt door scripts die verbinding maken met de database.  Database gebruikersaccountnamen vertegenwoordigen niet noodzakelijkerwijs werkelijke gebruikersaccounts op het systeem.

Aanmelden vanaf een andere computer inschakelen. In dit voorbeeld is het IP-adres van de computer om u aan te melden vanaf *10.112.113.114*.

```sql
GRANT ALL ON testdatabase.* TO 'mysqluser'@'10.112.113.114' IDENTIFIED BY 'password';
```
   
Als u het hulpprogramma voor mysql-databasebeheer wilt afsluiten, typt u:

```    
quit
```


## <a name="next-steps"></a>Volgende stappen
Zie de [MySQL-documentatie](https://dev.mysql.com/doc)voor meer informatie over MySQL.




