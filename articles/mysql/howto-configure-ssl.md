---
title: SSL - Azure Database voor MySQL configureren
description: Instructies voor het correct configureren van Azure Database voor MySQL en bijbehorende toepassingen om SSL-verbindingen correct te gebruiken
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: a68b63fa190aa67125b2bf8c09ae7f31aedc3905
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770264"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>SSL-connectiviteit in uw toepassing configureren om veilig verbinding te maken met Azure Database voor MySQL
Azure Database voor MySQL ondersteunt het verbinden van uw Azure Database voor MySQL-server met clienttoepassingen met behulp van Secure Sockets Layer (SSL). Het afdwingen van SSL-verbindingen tussen uw databaseserver en clienttoepassingen zorgt dat u bent beschermt tegen 'man in the middle'-aanvallen omdat de gegevensstroom tussen de server en uw toepassing wordt versleuteld.

## <a name="step-1-obtain-ssl-certificate"></a>Stap 1: SSL-certificaat verkrijgen
Download het certificaat dat nodig is om via SSL [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) te communiceren met uw Azure Database voor MySQL-server van en sla het certificaatbestand op uw lokale schijf (deze zelfstudie maakt bijvoorbeeld gebruik van c:\ssl).
**Voor Microsoft Internet Explorer en Microsoft Edge:** Nadat de download is voltooid, wijzigt u het certificaat naar BaltimoreCyberTrustRoot.crt.pem.

## <a name="step-2-bind-ssl"></a>Stap 2: SSL binden

Voor specifieke programmeertaalverbindingstekenreeksen verwijzen wij u naar de [onderstaande voorbeeldcode.](howto-configure-ssl.md#sample-code)

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
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Wanneer u de MySQL-opdrachtregelinterface op Windows `SSL connection error: Certificate signature check failed`gebruikt, ontvangt u mogelijk een foutmelding . Als dit gebeurt, `--ssl-mode=REQUIRED --ssl-ca={filepath}` vervangt `--ssl`u de parameters door .

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>Stap 3: SSL-verbindingen afdwingen in Azure 
### <a name="using-the-azure-portal"></a>Azure Portal gebruiken
Ga met de Azure-portal naar uw Azure Database voor MySQL-server en klik vervolgens op **Verbindingsbeveiliging**. Gebruik de knop Schakelen om de instelling **SSL-verbinding afdwingen** in of uit te schakelen en klik vervolgens op **Opslaan**. Microsoft raadt aan om altijd de instelling **SSL-verbinding afdwingen** in te schakelen voor verbeterde beveiliging.
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Azure CLI gebruiken
U de parameter **ssl-handhaving** in- of uitschakelen met respectievelijk ingeschakelde of uitgeschakelde waarden in Azure CLI.
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>Stap 4: De SSL-verbinding verifiëren
Voer de **mysql-statusopdracht** uit om te controleren of u verbinding hebt gemaakt met uw MySQL-server via SSL:
```dos
mysql> status
```
Bevestig dat de verbinding is versleuteld door de uitvoer te bekijken, die moet worden weergegeven: **SSL: Cipher in gebruik is AES256-SHA** 

## <a name="sample-code"></a>Voorbeeldcode
Raadpleeg de volgende codevoorbeelden voor het maken van een beveiligde verbinding met Azure Database voor MySQL via SSL via SSL:

Raadpleeg de lijst met [compatibele stuurprogramma's](concepts-compatibility.md) die worden ondersteund door de Azure Database for MySQL-service.

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP (met bob)
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mysql.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```

### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mysql.database.azure.com',
                       ssl={'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```

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
            'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com',
        :username => 'myadmin@mydemoserver',
        :password => 'yourpassword',
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
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
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-mysql-connector-for-java"></a>Java (MySQL Connector voor Java)
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

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb-connector-for-java"></a>Java (MariaDB Connector voor Java)
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

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
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

## <a name="next-steps"></a>Volgende stappen
Verschillende opties voor toepassingsconnectiviteit controleren na [Verbindingsbibliotheken voor Azure Database voor MySQL](concepts-connection-libraries.md)
