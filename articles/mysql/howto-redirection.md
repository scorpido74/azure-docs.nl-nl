---
title: Verbinding maken met omleiding - Azure Database voor MySQL
description: In dit artikel wordt beschreven hoe u uw toepassing configureren om verbinding te maken met Azure Database voor MySQL met omleiding.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246332"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Verbinding maken met Azure Database voor MySQL met omleiding

In dit onderwerp wordt uitgelegd hoe u een toepassing met uw Azure Database voor MySQL-server met de omleidingsmodus verbindt. Omleiding heeft tot doel de netwerklatentie tussen clienttoepassingen en MySQL-servers te verminderen door toepassingen in staat te stellen rechtstreeks verbinding te maken met backend-serverknooppunten.

## <a name="before-you-begin"></a>Voordat u begint
Meld u aan bij [Azure Portal](https://portal.azure.com). Maak een Azure Database voor MySQL-server met engineversie 5.6, 5.7 of 8.0. Zie [Azure Database voor MySQL-server maken voor](quickstart-create-mysql-server-database-using-azure-portal.md) meer informatie via Portal of Azure Database voor [MySQL-server maken met CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Omleiding wordt momenteel alleen ondersteund wanneer **SSL is ingeschakeld** op uw Azure Database voor MySQL-server. Zie [SSL gebruiken met Azure Database voor MySQL voor](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure)meer informatie over het configureren van SSL.

## <a name="php"></a>PHP

Ondersteuning voor omleiding in PHP-toepassingen is beschikbaar via de [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extensie, ontwikkeld door Microsoft. 

De mysqlnd_azure extensie is beschikbaar om toe te voegen aan PHP-toepassingen via PECL en het is sterk aanbevolen om de extensie te installeren en configureren via het officieel gepubliceerde [PECL-pakket.](https://pecl.php.net/package/mysqlnd_azure)

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extensie is momenteel in preview.

### <a name="redirection-logic"></a>Omleidingslogica

>[!IMPORTANT]
> Omleidingslogica/gedrag beginversie 1.1.0 is bijgewerkt en **het wordt aanbevolen om versie 1.1.0+ te gebruiken.**

Het omleidingsgedrag wordt bepaald `mysqlnd_azure.enableRedirect`door de waarde van . De onderstaande tabel schetst het gedrag van omleiding op basis van de waarde van deze parameter die begint in **versie 1.1.0+**.

Als u een oudere versie van de mysqlnd_azure extensie gebruikt (versie 1.0.0-1.0.3), `mysqlnd_azure.enabled`wordt het omleidingsgedrag bepaald door de waarde van . De geldige `off` waarden zijn (werkt op dezelfde manier als `on` het gedrag `preferred` dat in de onderstaande tabel wordt beschreven) en (werkt zoals in de onderstaande tabel).  

|**mysqlnd_azure., waarde voor omleiding inschakelen**| **Gedrag**|
|----------------------------------------|-------------|
|`off` of `0`|Omleiding wordt niet gebruikt. |
|`on` of `1`|- Als SSL niet is ingeschakeld op de Azure Database voor MySQL-server, wordt er geen verbinding gemaakt. De volgende fout wordt geretourneerd: *"mysqlnd_azure.enableRedirect is ingeschakeld, maar de SSL-optie is niet ingesteld in verbindingstekenreeks. Omleiding is alleen mogelijk met SSL."*<br>- Als SSL is ingeschakeld op de MySQL-server, maar omleiding niet wordt ondersteund op de server, wordt de eerste verbinding afgebroken en wordt de volgende fout geretourneerd: *"Verbinding afgebroken omdat omleiding niet is ingeschakeld op de MySQL-server of het netwerkpakket voldoet niet aan het omleidingsprotocol."*<br>- Als de MySQL-server omleiding ondersteunt, maar de omgeleide verbinding om welke reden dan ook is mislukt, wordt ook de eerste proxyverbinding afgebroken. De fout van de omgeleide verbinding retourneren.|
|`preferred` of `2`<br> (standaardwaarde)|- mysqlnd_azure zal indien mogelijk gebruik maken van omleiding.<br>- Als de verbinding geen SSL gebruikt, de server geen omleiding ondersteunt of als de omgeleide verbinding om een niet-fatale reden geen verbinding maakt terwijl de proxyverbinding nog steeds geldig is, valt deze terug naar de eerste proxyverbinding.|

In de volgende delen van het `mysqlnd_azure` document wordt beschreven hoe u de extensie met PECL installeert en de waarde van deze parameter instellen.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15+ en 7.3.2+
- PHP-PEER 
- php-mysql
- Azure Database voor MySQL-server met SSL ingeschakeld

1. Installeer [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) met [PECL](https://pecl.php.net/package/mysqlnd_azure). Het wordt aanbevolen om versie 1.1.0+ te gebruiken.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Zoek de extensiemap (`extension_dir`) door het onderstaande uit te voeren:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Wijzig mappen in de geretourneerde map en zorg ervoor dat `mysqlnd_azure.so` deze zich in deze map bevindt. 

4. Zoek de map voor .ini-bestanden door het onderstaande uit te voeren: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Mappen wijzigen in deze geretourneerde map. 

6. Maak een nieuw .ini-bestand voor `mysqlnd_azure`. Zorg ervoor dat de alfabetvolgorde van de naam achter die van mysqnld aanzit, omdat de modules worden geladen volgens de naamvolgorde van de ini-bestanden. Als `mysqlnd` bijvoorbeeld .ini een `10-mysqlnd.ini`naam heeft, geeft u `20-mysqlnd-azure.ini`de mysqlnd ini een naam als .

7. Voeg in het nieuwe .ini-bestand de volgende regels toe om omleiding in te schakelen.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Vereisten 
- PHP-versies 7.2.15+ en 7.3.2+
- php-mysql
- Azure Database voor MySQL-server met SSL ingeschakeld

1. Bepaal of u een x64- of x86-versie van PHP uitvoert door de volgende opdracht uit te voeren:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Download de bijbehorende x64- of x86-versie van de [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL van [PECL](https://pecl.php.net/package/mysqlnd_azure) die overeenkomt met uw versie van PHP. Het wordt aanbevolen om versie 1.1.0+ te gebruiken.

3. Haal het zip-bestand eruit `php_mysqlnd_azure.dll`en zoek de DLL met de naam .

4. Zoek de uitbreidingsmap ( )`extension_dir`door de onderstaande opdracht uit te voeren:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Kopieer `php_mysqlnd_azure.dll` het bestand naar de map die in stap 4 is geretourneerd. 

6. Zoek de PHP-map `php.ini` met het bestand met de volgende opdracht:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Wijzig `php.ini` het bestand en voeg de volgende extra regels toe om omleiding in te schakelen. 

    Onder de sectie Dynamische extensies: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    Onder de sectie Module-instellingen:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Omleiding bevestigen

U ook bevestigen dat omleiding is geconfigureerd met de onderstaande PHP-code. Maak een PHP-bestand genaamd `mysqlConnect.php` en plak de onderstaande code. Werk de servernaam, gebruikersnaam en wachtwoord bij met uw eigen wachtwoord. 
 
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
Zie [Verbindingstekenreeksen](howto-connection-string.md)voor meer informatie over verbindingstekenreeksen.
