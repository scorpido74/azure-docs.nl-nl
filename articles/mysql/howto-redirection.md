---
title: Verbinding maken met omleiding-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u een toepassing kunt configureren om verbinding te maken met Azure Database for MySQL met behulp van omleiding.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246332"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Verbinding maken met Azure Database for MySQL met behulp van omleiding

In dit onderwerp wordt uitgelegd hoe u met de omleidings modus verbinding maakt met een toepassing op uw Azure Database for MySQL-server. Omleiding is gericht op het verminderen van de netwerk latentie tussen client toepassingen en MySQL-servers door toepassingen toe te staan rechtstreeks verbinding te maken met back-end-server knooppunten.

## <a name="before-you-begin"></a>Voordat u begint
Meld u aan bij de [Azure-portal](https://portal.azure.com). Een Azure Database for MySQL-server met Engine versie 5,6, 5,7 of 8,0 maken. Raadpleeg voor meer informatie [hoe u Azure database for mysql server maakt vanuit de portal](quickstart-create-mysql-server-database-using-azure-portal.md) of [hoe u Azure database for mysql server maakt met behulp van CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Omleiding wordt momenteel alleen ondersteund als **SSL is ingeschakeld** op uw Azure database for mysql server. Zie [using SSL with Azure database for MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)voor meer informatie over het configureren van SSL.

## <a name="php"></a>PHP

Ondersteuning voor omleiding in PHP-toepassingen is beschikbaar via de extensie [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , ontwikkeld door micro soft. 

De uitbrei ding mysqlnd_azure is beschikbaar om toe te voegen aan PHP-toepassingen via PECL en het wordt ten zeerste aanbevolen om de uitbrei ding te installeren en te configureren via het officieel gepubliceerde [PECL-pakket](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) -uitbrei ding is momenteel als preview-versie beschikbaar.

### <a name="redirection-logic"></a>Logica voor omleiding

>[!IMPORTANT]
> De logica/het gedrag van het omleidings scenario 1.1.0 is bijgewerkt en **wordt aanbevolen om versie 1.1.0 + te gebruiken**.

Het omleidings gedrag wordt bepaald door de waarde van `mysqlnd_azure.enableRedirect`. De onderstaande tabel geeft een overzicht van het gedrag van omleiding op basis van de waarde van deze para meter vanaf **versie 1.1.0 +**.

Als u een oudere versie van de mysqlnd_azure extensie (versie 1.0.0-1.0.3) gebruikt, wordt het omleidings gedrag bepaald door de waarde van `mysqlnd_azure.enabled`. De geldige waarden zijn `off` (reageren op dezelfde manier als het gedrag dat wordt beschreven in de onderstaande tabel `on` ) en ( `preferred` in de onderstaande tabel).  

|**waarde van mysqlnd_azure. enableRedirect**| **Gedrag**|
|----------------------------------------|-------------|
|`off` of `0`|Omleiding wordt niet gebruikt. |
|`on` of `1`|-Als SSL niet is ingeschakeld op de Azure Database for MySQL server, wordt er geen verbinding gemaakt. De volgende fout wordt geretourneerd: *"mysqlnd_azure. enableRedirect is ingeschakeld, maar de SSL-optie is niet ingesteld in Connection String. Omleiding is alleen mogelijk met SSL. "*<br>-Als SSL is ingeschakeld op de MySQL-server, maar de omleiding wordt niet ondersteund op de server, wordt de eerste verbinding afgebroken en wordt de volgende fout geretourneerd: *' de verbinding is afgebroken omdat omleiding is niet ingeschakeld op de mysql-server of het netwerk pakket komt niet overeen met het omleidings Protocol '. '*<br>-Als de MySQL-server omleiding ondersteunt, maar de omgeleide verbinding om een of andere reden mislukt, wordt ook de eerste proxy verbinding afgebroken. Retourneert de fout van de omgeleide verbinding.|
|`preferred` of `2`<br> (standaard waarde)|-mysqlnd_azure wordt indien mogelijk omleiding gebruikt.<br>-Als de verbinding geen gebruikmaakt van SSL, biedt de server geen ondersteuning voor omleiding of de omgeleide verbinding kan geen verbinding maken voor een niet-fatale reden terwijl de proxy verbinding nog steeds een geldig is, wordt deze teruggestuurd naar de eerste proxy verbinding.|

In de volgende secties van het document wordt uitgelegd hoe u de `mysqlnd_azure` uitbrei ding installeert met behulp van PECL en de waarde van deze para meter instelt.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15 + en 7.3.2 +
- PHP PEER 
- php-mysql
- Azure Database for MySQL server waarop SSL is ingeschakeld

1. Installeer [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) met [PECL](https://pecl.php.net/package/mysqlnd_azure). Het is raadzaam versie 1.1.0 + te gebruiken.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Ga naar de extensie Directory`extension_dir`() door de onderstaande stappen uit te voeren:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Wijzig mappen in de map die wordt geretourneerd `mysqlnd_azure.so` en zorg ervoor dat deze zich in deze map bevindt. 

4. Ga naar de map voor. ini-bestanden door de onderstaande stappen uit te voeren: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Wijzig de mappen in deze geretourneerde map. 

6. Maak een nieuw. ini-bestand `mysqlnd_azure`voor. Zorg ervoor dat de alfabetische volg orde van de naam na mysqnld is, omdat de modules zijn geladen op basis van de naam volgorde van de ini-bestanden. Bijvoorbeeld, als `mysqlnd` . ini een naam heeft `10-mysqlnd.ini`, de mysqlnd ini als `20-mysqlnd-azure.ini`.

7. Voeg in het nieuwe ini-bestand de volgende regels toe om omleiding in te scha kelen.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
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

2. Down load de bijbehorende x64-of x86-versie van de [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) dll van [PECL](https://pecl.php.net/package/mysqlnd_azure) die overeenkomt met uw versie van PHP. Het is raadzaam versie 1.1.0 + te gebruiken.

3. Pak het zip-bestand uit en zoek de `php_mysqlnd_azure.dll`naam van de dll.

4. Zoek de extensie Directory (`extension_dir`) door de onderstaande opdracht uit te voeren:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Kopieer het `php_mysqlnd_azure.dll` bestand naar de map die is geretourneerd in stap 4. 

6. Zoek de PHP-map met `php.ini` het bestand met behulp van de volgende opdracht:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Wijzig het `php.ini` bestand en voeg de volgende extra regels toe om omleiding in te scha kelen. 

    Onder de sectie dynamische extensies: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Klik onder de sectie module-instellingen op:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Omleiding bevestigen

U kunt ook bevestigen dat de omleiding is geconfigureerd met behulp van de onderstaande PHP-voorbeeld code. Maak een PHP-bestand `mysqlConnect.php` met de naam en plak de onderstaande code. Werk de server naam, gebruikers naam en het wacht woord bij met uw eigen account. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
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
Zie [verbindings reeksen](howto-connection-string.md)voor meer informatie over verbindings reeksen.
