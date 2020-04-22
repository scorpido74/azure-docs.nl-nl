---
title: SSL - Azure Database configureren voor MariaDB
description: Instructies voor het correct configureren van Azure Database voor MariaDB en bijbehorende toepassingen om SSL-verbindingen correct te gebruiken
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 0af94d04f4e50fd039f01c5746b3a5d47a3698b3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769950"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure Database voor MariaDB
Azure Database voor MariaDB ondersteunt het verbinden van uw Azure Database voor MariaDB-server met clienttoepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="obtain-ssl-certificate"></a>SSL-certificaat verkrijgen
Download het certificaat dat nodig is om via SSL [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) te communiceren met uw Azure Database voor MariaDB-server van en sla het certificaatbestand op uw lokale schijf (deze zelfstudie maakt bijvoorbeeld gebruik van c:\ssl).
**Voor Microsoft Internet Explorer en Microsoft Edge:** Nadat de download is voltooid, wijzigt u het certificaat naar BaltimoreCyberTrustRoot.crt.pem.

## <a name="bind-ssl"></a>SSL binden

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Verbinding maken met server via MySQL Workbench via SSL
Configureer MySQL Workbench om veilig verbinding te maken via SSL. 

1. Navigeer vanuit de dialoog Met nieuwe verbinding instellen naar het **tabblad SSL.** 

1. Werk het veld **SSL gebruiken** bij om 'Vereisen' te maken.

1. Voer in het veld **SSL CA-bestand** de bestandslocatie van de **BaltimoreCyberTrustRoot.crt.pem in.** 
    
    ![SSL-configuratie opslaan](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Voor bestaande verbindingen u SSL binden door met de rechtermuisknop op het verbindingspictogram te klikken en bewerken te kiezen. Navigeer vervolgens naar het **tabblad SSL** en bind het cert-bestand.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Verbinding maken met server via de MySQL CLI via SSL
Een andere manier om het SSL-certificaat te binden, is door de MySQL-opdrachtregelinterface te gebruiken door de volgende opdrachten uit te voeren. 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Wanneer u de MySQL-opdrachtregelinterface op Windows `SSL connection error: Certificate signature check failed`gebruikt, ontvangt u mogelijk een foutmelding . Als dit gebeurt, `--ssl-mode=REQUIRED --ssl-ca={filepath}` vervangt `--ssl`u de parameters door .

## <a name="enforcing-ssl-connections-in-azure"></a>SSL-verbindingen afdwingen in Azure 
### <a name="using-the-azure-portal"></a>Azure Portal gebruiken
Ga met de Azure-portal naar de Azure Database voor MariaDB-server en klik vervolgens op **Verbindingsbeveiliging**. Gebruik de knop Schakelen om de instelling **SSL-verbinding afdwingen** in of uit te schakelen en klik vervolgens op **Opslaan**. Microsoft raadt aan om altijd de instelling **SSL-verbinding afdwingen** in te schakelen voor verbeterde beveiliging.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken
U de parameter **ssl-handhaving** in- of uitschakelen met respectievelijk ingeschakelde of uitgeschakelde waarden in Azure CLI.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>De SSL-verbinding verifiëren
Voer de **mysql-statusopdracht** uit om te controleren of u verbinding hebt gemaakt met uw MariaDB-server via SSL:
```sql
status
```
Bevestig dat de verbinding is versleuteld door de uitvoer te bekijken, die moet worden weergegeven: **SSL: Cipher in gebruik is AES256-SHA** 

## <a name="sample-code"></a>Voorbeeldcode
Raadpleeg de volgende codevoorbeelden voor het maken van een beveiligde verbinding met Azure Database voor MariaDB voor MariaDB via SSL via uw toepassing:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby on Rails
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
