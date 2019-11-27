---
title: Verbinding maken met Azure Database for MySQL met behulp van omleiding
description: In dit artikel wordt beschreven hoe u een toepassing kunt configureren om verbinding te maken met Azure Database for MySQL met behulp van omleiding.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233738"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Verbinding maken met Azure Database for MySQL met behulp van omleiding

In dit onderwerp wordt uitgelegd hoe u met de omleidings modus verbinding maakt met een toepassing op uw Azure Database for MySQL-server. Omleiding is gericht op het verminderen van de netwerk latentie tussen client toepassingen en MySQL-servers door toepassingen toe te staan rechtstreeks verbinding te maken met back-end-server knooppunten.

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP- [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) is momenteel als preview-versie beschikbaar.

## <a name="before-you-begin"></a>Voordat u begint
Meld u aan bij [Azure Portal](https://portal.azure.com). Een Azure Database for MySQL-server met Engine versie 5,6, 5,7 of 8,0 maken. Raadpleeg voor meer informatie [hoe u Azure database for mysql server maakt vanuit de portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [hoe u Azure database for mysql server maakt met behulp van CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Omleiding wordt momenteel alleen ondersteund als SSL is ingeschakeld. Zie [using SSL with Azure database for MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure)voor meer informatie over het configureren van SSL. 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15 + en 7.3.2 +
- PHP PEER 
- php-mysql
- Azure Database for MySQL server waarop SSL is ingeschakeld

1. Installeer [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) met [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Ga naar de extensie Directory (`extension_dir`) door de onderstaande stappen uit te voeren:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Wijzig de mappen in de map die wordt geretourneerd en zorg ervoor dat `mysqlnd_azure.so` zich in deze map bevindt. 

4. Ga naar de map voor. ini-bestanden door de onderstaande stappen uit te voeren: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Wijzig de mappen in deze geretourneerde map. 

6. Maak een nieuw. ini-bestand voor `mysqlnd_azure`. Zorg ervoor dat de alfabetische volg orde van de naam na mysqnld is, omdat de modules zijn geladen op basis van de naam volgorde van de ini-bestanden. Als `mysqlnd`. ini bijvoorbeeld `10-mysqlnd.ini`heet, moet u de naam van het mysqlnd ini als `20-mysqlnd-azure.ini`.

7. Voeg in het nieuwe ini-bestand de volgende regels toe om omleiding in te scha kelen.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15 + en 7.3.2 +
- php-mysql
- Azure Database for MySQL server waarop SSL is ingeschakeld

1. Bepaal of u een x64-of x86-versie van PHP gebruikt door de volgende opdracht uit te voeren:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Down load de bijbehorende x64-of x86-versie van de [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll van [PECL](https://pecl.php.net/package/mysqlnd_azure) die overeenkomt met uw versie van PHP. 

3. Pak het zip-bestand uit en zoek de DLL met de naam `php_mysqlnd_azure.dll`.

4. Ga naar de extensie Directory (`extension_dir`) door de onderstaande opdracht uit te voeren:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Kopieer het `php_mysqlnd_azure.dll` bestand naar de map die u in stap 4 hebt geretourneerd. 

6. Zoek de PHP-map met het `php.ini`-bestand met behulp van de volgende opdracht:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Wijzig het `php.ini`-bestand en voeg de volgende extra regels toe om omleiding in te scha kelen. 

    Onder de sectie dynamische extensies: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Klik onder de sectie module-instellingen op:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Omleiding bevestigen

U kunt ook bevestigen dat de omleiding is geconfigureerd met behulp van de onderstaande PHP-voorbeeld code. Maak een PHP-bestand met de naam `mysqlConnect.php` en plak de onderstaande code. Werk de server naam, gebruikers naam en het wacht woord bij met uw eigen account. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
$db->close();
  }
 ?>
 ```

## <a name="next-steps"></a>Volgende stappen
Raadpleeg [verbindings reeksen](howto-connection-string.md)voor meer informatie over verbindings reeksen.

