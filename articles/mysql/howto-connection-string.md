---
title: Verbindings reeksen-Azure Database for MySQL
description: In dit document worden de momenteel ondersteunde verbindings reeksen vermeld voor toepassingen waarmee verbinding kan worden gemaakt met Azure Database for MySQL, waaronder ADO.NET (C#), JDBC, node. js, ODBC, PHP, python en Ruby.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.custom: tracking-python
ms.openlocfilehash: 1683fba2d8f085569c7cc62a06d0fe95803d4346
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559200"
---
# <a name="how-to-connect-applications-to-azure-database-for-mysql"></a>Toepassingen koppelen met Azure Database voor MySQL
In dit onderwerp vindt u een overzicht van de connection string typen die door Azure Database for MySQL worden ondersteund, samen met sjablonen en voor beelden. Er zijn mogelijk verschillende para meters en instellingen in uw connection string.

- Zie [SSL configureren voor meer informatie over](./howto-configure-ssl.md)het verkrijgen van het certificaat.
- {your_host} = \<servername> . mysql.database.Azure.com
- {your_user} @ {servername} = indeling van gebruikers-id voor de juiste verificatie.  Als u alleen de gebruikers-id gebruikt, mislukt de verificatie.

## <a name="adonet"></a>ADO.NET
```ado.net
Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password};[SslMode=Required;]
```

In dit voor beeld is de naam van de server `mydemoserver` , de naam van de data base, de `wpdb` gebruikers naam `WPAdmin` en het wacht woord `mypassword!2` . Als gevolg hiervan moet de connection string:

```ado.net
Server= "mydemoserver.mysql.database.azure.com"; Port=3306; Database= "wpdb"; Uid= "WPAdmin@mydemoserver"; Pwd="mypassword!2"; SslMode=Required;
```

## <a name="jdbc"></a>JDBC
```jdbc
String url ="jdbc:mysql://%s:%s/%s[?verifyServerCertificate=true&useSSL=true&requireSSL=true]",{your_host},{your_port},{your_database}"; myDbConn = DriverManager.getConnection(url, {username@servername}, {your_password}";
```

## <a name="nodejs"></a>Node.js
```node.js
var conn = mysql.createConnection({host: {your_host}, user: {username@servername}, password: {your_password}, database: {your_database}, Port: {your_port}[, ssl:{ca:fs.readFileSync({ca-cert filename})}}]);
```

## <a name="odbc"></a>ODBC
```odbc
DRIVER={MySQL ODBC 5.3 UNICODE Driver};Server={your_host};Port={your_port};Database={your_database};Uid={username@servername};Pwd={your_password}; [sslca={ca-cert filename}; sslverify=1; Option=3;]
```

## <a name="php"></a>PHP
```php
$con=mysqli_init(); [mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL);] mysqli_real_connect($con, {your_host}, {username@servername}, {your_password}, {your_database}, {your_port});
```

## <a name="python"></a>Python
```python
cnx = mysql.connector.connect(user={username@servername}, password={your_password}, host={your_host}, port={your_port}, database={your_database}[, ssl_ca={ca-cert filename}, ssl_verify_cert=true])
```

## <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(username: {username@servername}, password: {your_password}, database: {your_database}, host: {your_host}, port: {your_port}[, sslca:{ca-cert filename}, sslverify:false, sslcipher:'AES256-SHA'])
```

## <a name="get-the-connection-string-details-from-the-azure-portal"></a>De connection string Details ophalen uit de Azure Portal
Ga in het [Azure Portal](https://portal.azure.com)naar de Azure database for mysql-server en klik vervolgens op **verbindings reeksen** om de teken reeks lijst voor uw instantie op te halen: ![ het deel venster verbindings reeksen in de Azure Portal](./media/howto-connection-strings/connection-strings-on-portal.png)

De teken reeks bevat details zoals het stuur programma, de server en andere para meters voor database verbindingen. Wijzig deze voor beelden om uw eigen para meters te gebruiken, zoals de database naam, het wacht woord, enzovoort. U kunt deze teken reeks vervolgens gebruiken om vanuit uw code en toepassingen verbinding te maken met de server.

## <a name="next-steps"></a>Volgende stappen
- Zie [concepten-Connection Libraries](./concepts-connection-libraries.md)(Engelstalig) voor meer informatie over verbindings bibliotheken.
