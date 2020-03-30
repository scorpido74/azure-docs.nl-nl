---
title: Verbinding maken met Azure App Service - Azure Database voor MySQL
description: Instructies voor het correct verbinden van een bestaande Azure App Service met Azure Database voor MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ba75daa47ca1f77cd5828d13877238a64deeeb41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062467"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Een bestaande Azure App-service verbinden met Azure Database voor MySQL-server
In dit onderwerp wordt uitgelegd hoe u een bestaande Azure App-service verbinden met uw Azure Database voor MySQL-server.

## <a name="before-you-begin"></a>Voordat u begint
Meld u aan bij [Azure Portal](https://portal.azure.com). Maak een Azure Database voor MySQL-server. Zie [Azure Database voor MySQL-server maken voor](quickstart-create-mysql-server-database-using-azure-portal.md) meer informatie via Portal of Azure Database voor [MySQL-server maken met CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Momenteel zijn er twee oplossingen om toegang van een Azure App Service naar een Azure Database voor MySQL in te schakelen. Beide oplossingen omvatten het instellen van firewallregels op serverniveau.

## <a name="solution-1---allow-azure-services"></a>Oplossing 1 - Azure-services toestaan
Azure Database voor MySQL biedt toegangsbeveiliging met behulp van een firewall om uw gegevens te beschermen. Houd er bij het verbinden van een Azure App Service met Azure Database voor MySQL-server rekening mee dat de uitgaande IP's van App Service dynamisch van aard zijn. Als u de optie 'Toegang tot Azure-services toestaan' kiest, kan de app-service verbinding maken met de MySQL-server.

1. Klik op het MySQL-serverblad onder de kop Instellingen op **Verbindingsbeveiliging** om het blade Connection Security voor Azure Database voor MySQL te openen.

   ![Azure-portal - klik op Verbindingsbeveiliging](./media/howto-connect-webapp/1-connection-security.png)

2. Selecteer **AAN** in **Toegang geven tot Azure-services**en **sla op**.
   ![Azure-portal - Azure-toegang toestaan](./media/howto-connect-webapp/allow-azure.png)

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Oplossing 2 - Een firewallregel maken om uitgaande IP's expliciet toe te staan
U expliciet alle uitgaande IP's van uw Azure App-service toevoegen.

1. Bekijk in het blade App Service Properties het **uitgaande IP-adres**.

   ![Azure-portal - Uitgaande IP's weergeven](./media/howto-connect-webapp/2_1-outbound-ip-address.png)

2. Voeg op het mySQL-verbindingsbeveiligingsblad uitgaande IP's één voor één toe.

   ![Azure-portal - Expliciete IP's toevoegen](./media/howto-connect-webapp/2_2-add-explicit-ips.png)

3. Vergeet niet om uw firewallregels op te **slaan.**

Hoewel de Azure App-service probeert IP-adressen in de loop van de tijd constant te houden, zijn er gevallen waarin de IP-adressen kunnen veranderen. Dit kan bijvoorbeeld gebeuren wanneer de app wordt gerecycled of een schaalbewerking plaatsvindt of wanneer nieuwe computers worden toegevoegd in regionale datacenters van Azure om de capaciteit te vergroten. Wanneer de IP-adressen veranderen, kan de app downtime ervaren in het geval dat deze geen verbinding meer kan maken met de MySQL-server. Houd hier rekening mee bij het kiezen van een van de voorgaande oplossingen.

## <a name="ssl-configuration"></a>SSL-configuratie
Azure Database voor MySQL heeft SSL standaard ingeschakeld. Als uw toepassing geen SSL gebruikt om verbinding te maken met de database, moet u SSL uitschakelen op de MySQL-server. Zie [SSL gebruiken met Azure Database voor MySQL voor](howto-configure-ssl.md)meer informatie over het configureren van SSL.

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Zie [Verbindingstekenreeksen](howto-connection-string.md)voor meer informatie over verbindingstekenreeksen .
