---
title: Verbindingstekenreeksen - Azure Database voor MariaDB
description: In dit document worden de momenteel ondersteunde verbindingstekenreeksen weergegeven voor toepassingen die verbinding kunnen maken met Azure Database voor MariaDB, waaronder ADO.NET (C#), JDBC, Node.js, ODBC, PHP, Python en Ruby.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 74574fb905ae4ebd2552ef97cd0b5430eea6363a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530220"
---
# <a name="how-to-connect-applications-to-azure-database-for-mariadb"></a>Toepassingen verbinden met Azure Database for MariaDB
In dit onderwerp worden de verbindingstekenreekstypen weergegeven die worden ondersteund door Azure Database voor MariaDB, samen met sjablonen en voorbeelden. Mogelijk hebt u verschillende parameters en instellingen in uw verbindingstekenreeks.

- Zie SSL configureren als u het certificaat [wilt verkrijgen.](./howto-configure-ssl.md)
- {your_host} = [servernaam].mariadb.database.azure.com
- {your_user}@{servername} = userID-indeling voor verificatie correct.  Als u alleen de gebruikers-id gebruikt, mislukt de verificatie.

## <a name="adonet"></a>ADO.NET
```csharp
Server={your_host}; Port=3306; Database={your_database}; Uid={username@servername}; Pwd={your_password}; SslMode=Preferred;
```

In dit `mydemoserver`voorbeeld is de servernaam, `wpdb`de databasenaam `WPAdmin`is, de `mypassword!2`gebruikersnaam is , en het wachtwoord is . Als gevolg hiervan moet de verbindingstekenreeks zijn:

```csharp
Server= "mydemoserver.mariadb.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```java
String url ="jdbc:mariadb://{your_host}:3306/{your_database}?useSSL=true&trustServerCertificate=true"; myDbConn = DriverManager.getConnection(url, "{username@servername}", {your_password});
```

## <a name="nodejs"></a>Node.js
```javascript
var conn = mysql.createConnection({host: "{your_host}", user: "{your_username}", password: {your_password}, database: {your_database}, port: 3306, ssl:{ca:fs.readFileSync({ca-cert filename})}});
```

## <a name="odbc"></a>ODBC
```cpp
DRIVER={MARIADB ODBC 3.0 Driver}; Server="{your_host}"; Port=3306; Database={your_database}; Uid="{username@servername}"; Pwd={your_password}; sslca={ca-cert filename}; sslverify=1;
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "{your_host}", "{username@servername}", {your_password}, {your_database}, 3306);
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user="{username@servername}", password={your_password}, host="{your_host}", port=3306, database={your_database}, ssl_ca={ca-cert filename}, ssl_verify_cert=true)
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: "{username@servername}", password: {your_password}, database: {your_database}, host: "{your_host}", port: 3306, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA')
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>De details van de verbindingstekenreeks ophalen van de Azure-portal
Ga in de [Azure-portal](https://portal.azure.com)naar uw Azure Database voor MariaDB-server en klik ![vervolgens op **Verbindingstekenreeksen** om de tekenreeksenlijst voor uw instantie op te halen: het deelvenster Verbindingstekenreeksen in de Azure-portal](./media/howto-connection-strings/connection-strings-on-portal.png)

De tekenreeks bevat details zoals het stuurprogramma, de server en andere parameters voor databaseverbindingen. Wijzig deze voorbeelden om uw eigen parameters te gebruiken, zoals databasenaam, wachtwoord, enzovoort. U deze tekenreeks vervolgens gebruiken om verbinding te maken met de server vanuit uw code en toepassingen.

<!-- 
## Next steps
- For more information about connection libraries, see [Concepts - Connection libraries](./concepts-connection-libraries.md).
- -->
