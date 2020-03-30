---
title: Apache Maven gebruiken om een Java HBase-client te bouwen voor Azure HDInsight
description: Lees hoe u Apache Maven gebruiken om een Op Java gebaseerde Apache HBase-toepassing te bouwen en deze vervolgens te implementeren op HBase op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seodec18
ms.date: 12/24/2019
ms.openlocfilehash: 3e9b23ce450e45dfedcee8b20e09b1c2b52b6e68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495780"
---
# <a name="build-java-applications-for-apache-hbase"></a>Java-toepassingen bouwen voor Apache HBase

Meer informatie over het maken van een [Apache HBase-toepassing](https://hbase.apache.org/) in Java. Gebruik vervolgens de toepassing met HBase op Azure HDInsight.

De stappen in dit document gebruiken [Apache Maven](https://maven.apache.org/) om het project te maken en te bouwen. Maven is een software project management en begrip tool waarmee u software, documentatie en rapporten voor Java-projecten te bouwen.

## <a name="prerequisites"></a>Vereisten

* Een Apache HBase cluster op HDInsight. Zie [Aan de slag met Apache HBase](./apache-hbase-tutorial-get-started-linux.md).

* [Java Developer Kit (JDK) versie 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) goed [geïnstalleerd](https://maven.apache.org/install.html) volgens Apache.  Maven is een project build systeem voor Java projecten.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Als u PowerShell gebruikt, hebt u de [AZ-module](https://docs.microsoft.com/powershell/azure/overview)nodig.

* Een teksteditor. In dit artikel wordt gebruik genomen van Microsoft Notepad.

## <a name="test-environment"></a>Testomgeving

De omgeving die voor dit artikel wordt gebruikt, was een computer met Windows 10.  De opdrachten werden uitgevoerd in een opdrachtprompt en de verschillende bestanden zijn bewerkt met Kladblok. Dienovereenkomstig aanpassen voor uw omgeving.

Voer in een opdrachtprompt de onderstaande opdrachten in om een werkomgeving te maken:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-a-maven-project"></a>Een Maven-project maken

1. Voer de volgende opdracht in om een Maven-project met de naam **hbaseapp**te maken:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=hbaseapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    cd hbaseapp
    mkdir conf
    ```

    Met deze opdracht `hbaseapp` wordt een map gemaakt met de naam op de huidige locatie, die een basisproject van Maven bevat. De tweede opdracht wijzigt `hbaseapp`de werkmap in . De derde opdracht maakt `conf`een nieuwe map, die later zal worden gebruikt. De `hbaseapp` map bevat de volgende items:

    * `pom.xml`: Het Project Object Model[(POM)](https://maven.apache.org/guides/introduction/introduction-to-the-pom.html)bevat informatie en configuratiedetails die worden gebruikt om het project te bouwen.
    * `src\main\java\com\microsoft\examples`: Bevat uw toepassingscode.
    * `src\test\java\com\microsoft\examples`: Bevat tests voor uw toepassing.

2. Verwijder de gegenereerde voorbeeldcode. Verwijder de gegenereerde `AppTest.java`test- `App.java` en toepassingsbestanden en voer de onderstaande opdrachten in:

    ```cmd
    DEL src\main\java\com\microsoft\examples\App.java
    DEL src\test\java\com\microsoft\examples\AppTest.java
    ```

## <a name="update-the-project-object-model"></a>Het projectobjectmodel bijwerken

Zie https://maven.apache.org/pom.htmlvoor een volledige verwijzing naar het pom.xml-bestand .  Open `pom.xml` door onderstaande opdracht in te voeren:

```cmd
notepad pom.xml
```

### <a name="add-dependencies"></a>Afhankelijkheden toevoegen

Voeg `pom.xml`in de volgende `<dependencies>` tekst in de sectie toe:

```xml
<dependency>
    <groupId>org.apache.hbase</groupId>
    <artifactId>hbase-shaded-client</artifactId>
    <version>1.1.2</version>
</dependency>
<dependency>
    <groupId>org.apache.phoenix</groupId>
    <artifactId>phoenix-core</artifactId>
    <version>4.14.1-HBase-1.1</version>
</dependency>
```  

Deze sectie geeft aan dat het project **hbase-client-** en **phoenix-core** componenten nodig heeft. Op het moment van compileren worden deze afhankelijkheden gedownload van de standaard Maven-repository. U de [Maven Central Repository Search](https://search.maven.org/artifact/org.apache.hbase/hbase-client/1.1.2/jar) gebruiken om meer te weten te komen over deze afhankelijkheid.

> [!IMPORTANT]  
> Het versienummer van de hbase-client moet overeenkomen met de versie van Apache HBase die is voorzien van uw HDInsight-cluster. Gebruik de volgende tabel om het juiste versienummer te vinden.

| HDInsight-clusterversie | Apache HBase-versie te gebruiken |
| --- | --- |
| 3.6 | 1.1.2 |
| 4,0 | 2.0.0 |

Zie [Wat zijn de verschillende Apache Hadoop-componenten die beschikbaar zijn met HDInsight](../hdinsight-component-versioning.md)voor meer informatie over HDInsight-versies en -componenten.

### <a name="build-configuration"></a>Configuratie bouwen

Met Maven-plug-ins u de bouwfasen van het project aanpassen. Deze sectie wordt gebruikt om plug-ins, resources en andere buildconfiguratieopties toe te voegen.

Voeg de volgende `pom.xml` code toe aan het bestand en sla het bestand op en sluit deze. Deze tekst moet `<project>...</project>` zich in de tags in `</dependencies>` `</project>`het bestand bevinden, bijvoorbeeld tussen en .

```xml
<build>
    <sourceDirectory>src</sourceDirectory>
    <resources>
    <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
        <include>hbase-site.xml</include>
        </includes>
    </resource>
    </resources>
    <plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
        </plugin>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>3.2.1</version>
        <configuration>
        <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
        </transformers>
        </configuration>
        <executions>
        <execution>
            <phase>package</phase>
            <goals>
            <goal>shade</goal>
            </goals>
        </execution>
        </executions>
    </plugin>
    </plugins>
</build>
```

In deze sectie wordt`conf/hbase-site.xml`een bron ( ) geconfigureerd die configuratiegegevens voor HBase bevat.

> [!NOTE]  
> U ook configuratiewaarden instellen via code. Zie de opmerkingen `CreateTable` in het voorbeeld.

In deze sectie worden ook de [Apache Maven Compiler Plugin](https://maven.apache.org/plugins/maven-compiler-plugin/) en Apache [Maven Shade Plugin geconfigureerd.](https://maven.apache.org/plugins/maven-shade-plugin/) De compilerplug-in wordt gebruikt om de topologie samen te stellen. De schaduwplug-in wordt gebruikt om licentieduplicatie te voorkomen in het JAR-pakket dat door Maven is gebouwd. Deze plug-in wordt gebruikt om een fout met "dubbele licentiebestanden" te voorkomen tijdens het uitvoeren van de looptijd op het HDInsight-cluster. Het gebruik van maven-shade-plugin met de `ApacheLicenseResourceTransformer` implementatie voorkomt de fout.

De maven-shade-plugin produceert ook een uber-pot die alle afhankelijkheden bevat die nodig zijn voor de toepassing.

### <a name="download-the-hbase-sitexml"></a>Download de hbase-site.xml

Gebruik de volgende opdracht om de HBase-configuratie `conf` van het HBase-cluster naar de map te kopiëren. Vervang `CLUSTERNAME` de naam van het HDInsight-cluster en voer de opdracht in:

```cmd
scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml ./conf/hbase-site.xml
```

## <a name="create-the-application"></a>De toepassing maken

### <a name="implement-a-createtable-class"></a>Een klasse CreateTable implementeren

Voer de onderstaande opdracht in `CreateTable.java`om een nieuw bestand te maken en te openen. Selecteer **Ja** bij de prompt om een nieuw bestand te maken.

```cmd
notepad src\main\java\com\microsoft\examples\CreateTable.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand. Sluit vervolgens het bestand.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.HTableDescriptor;
import org.apache.hadoop.hbase.TableName;
import org.apache.hadoop.hbase.HColumnDescriptor;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class CreateTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Example of setting zookeeper values for HDInsight
    // in code instead of an hbase-site.xml file
    //
    // config.set("hbase.zookeeper.quorum",
    //            "zookeepernode0,zookeepernode1,zookeepernode2");
    //config.set("hbase.zookeeper.property.clientPort", "2181");
    //config.set("hbase.cluster.distributed", "true");
    //
    //NOTE: Actual zookeeper host names can be found using Ambari:
    //curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts"

    //Linux-based HDInsight clusters use /hbase-unsecure as the znode parent
    config.set("zookeeper.znode.parent","/hbase-unsecure");

    // create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // create the table...
    HTableDescriptor tableDescriptor = new HTableDescriptor(TableName.valueOf("people"));
    // ... with two column families
    tableDescriptor.addFamily(new HColumnDescriptor("name"));
    tableDescriptor.addFamily(new HColumnDescriptor("contactinfo"));
    admin.createTable(tableDescriptor);

    // define some people
    String[][] people = {
        { "1", "Marcel", "Haddad", "marcel@fabrikam.com"},
        { "2", "Franklin", "Holtz", "franklin@contoso.com" },
        { "3", "Dwayne", "McKee", "dwayne@fabrikam.com" },
        { "4", "Rae", "Schroeder", "rae@contoso.com" },
        { "5", "Rosalie", "burton", "rosalie@fabrikam.com"},
        { "6", "Gabriela", "Ingram", "gabriela@contoso.com"} };

    HTable table = new HTable(config, "people");

    // Add each person to the table
    //   Use the `name` column family for the name
    //   Use the `contactinfo` column family for the email
    for (int i = 0; i< people.length; i++) {
        Put person = new Put(Bytes.toBytes(people[i][0]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("first"), Bytes.toBytes(people[i][1]));
        person.add(Bytes.toBytes("name"), Bytes.toBytes("last"), Bytes.toBytes(people[i][2]));
        person.add(Bytes.toBytes("contactinfo"), Bytes.toBytes("email"), Bytes.toBytes(people[i][3]));
        table.put(person);
    }
    // flush commits and close the table
    table.flushCommits();
    table.close();
    }
}
```

Deze code `CreateTable` is de klasse, `people` waarmee een tabel met de naam wordt gemaakt en deze wordt ingevuld met een aantal vooraf gedefinieerde gebruikers.

### <a name="implement-a-searchbyemail-class"></a>Een klasse SearchByEmail implementeren

Voer de onderstaande opdracht in `SearchByEmail.java`om een nieuw bestand te maken en te openen. Selecteer **Ja** bij de prompt om een nieuw bestand te maken.

```cmd
notepad src\main\java\com\microsoft\examples\SearchByEmail.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand. Sluit vervolgens het bestand.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HTable;
import org.apache.hadoop.hbase.client.Scan;
import org.apache.hadoop.hbase.client.ResultScanner;
import org.apache.hadoop.hbase.client.Result;
import org.apache.hadoop.hbase.filter.RegexStringComparator;
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter;
import org.apache.hadoop.hbase.filter.CompareFilter.CompareOp;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.util.GenericOptionsParser;

public class SearchByEmail {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Use GenericOptionsParser to get only the parameters to the class
    // and not all the parameters passed (when using WebHCat for example)
    String[] otherArgs = new GenericOptionsParser(config, args).getRemainingArgs();
    if (otherArgs.length != 1) {
        System.out.println("usage: [regular expression]");
        System.exit(-1);
    }

    // Open the table
    HTable table = new HTable(config, "people");

    // Define the family and qualifiers to be used
    byte[] contactFamily = Bytes.toBytes("contactinfo");
    byte[] emailQualifier = Bytes.toBytes("email");
    byte[] nameFamily = Bytes.toBytes("name");
    byte[] firstNameQualifier = Bytes.toBytes("first");
    byte[] lastNameQualifier = Bytes.toBytes("last");

    // Create a regex filter
    RegexStringComparator emailFilter = new RegexStringComparator(otherArgs[0]);
    // Attach the regex filter to a filter
    //   for the email column
    SingleColumnValueFilter filter = new SingleColumnValueFilter(
        contactFamily,
        emailQualifier,
        CompareOp.EQUAL,
        emailFilter
    );

    // Create a scan and set the filter
    Scan scan = new Scan();
    scan.setFilter(filter);

    // Get the results
    ResultScanner results = table.getScanner(scan);
    // Iterate over results and print  values
    for (Result result : results ) {
        String id = new String(result.getRow());
        byte[] firstNameObj = result.getValue(nameFamily, firstNameQualifier);
        String firstName = new String(firstNameObj);
        byte[] lastNameObj = result.getValue(nameFamily, lastNameQualifier);
        String lastName = new String(lastNameObj);
        System.out.println(firstName + " " + lastName + " - ID: " + id);
        byte[] emailObj = result.getValue(contactFamily, emailQualifier);
        String email = new String(emailObj);
        System.out.println(firstName + " " + lastName + " - " + email + " - ID: " + id);
    }
    results.close();
    table.close();
    }
}
```

De `SearchByEmail` klasse kan worden gebruikt om rijen op e-mailadres op te vragen. Omdat het een regelmatig expressiefilter gebruikt, u een tekenreeks of een reguliere expressie geven wanneer u de klasse gebruikt.

### <a name="implement-a-deletetable-class"></a>Een klasse DeleteTable implementeren

Voer de onderstaande opdracht in `DeleteTable.java`om een nieuw bestand te maken en te openen. Selecteer **Ja** bij de prompt om een nieuw bestand te maken.

```cmd
notepad src\main\java\com\microsoft\examples\DeleteTable.java
```

Kopieer en plak vervolgens de javacode hieronder in het nieuwe bestand. Sluit vervolgens het bestand.

```java
package com.microsoft.examples;
import java.io.IOException;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;

public class DeleteTable {
    public static void main(String[] args) throws IOException {
    Configuration config = HBaseConfiguration.create();

    // Create an admin object using the config
    HBaseAdmin admin = new HBaseAdmin(config);

    // Disable, and then delete the table
    admin.disableTable("people");
    admin.deleteTable("people");
    }
}
```

De `DeleteTable` klasse ruimt de HBase-tabellen op die in dit voorbeeld `CreateTable` zijn gemaakt door de tabel die door de klasse is gemaakt, uit te schakelen en te laten vallen.

## <a name="build-and-package-the-application"></a>De toepassing bouwen en verpakken

1. Gebruik `hbaseapp` in de map de volgende opdracht om een JAR-bestand te maken dat de toepassing bevat:

    ```cmd
    mvn clean package
    ```

    Deze opdracht bouwt en verpakt de toepassing in een .jar-bestand.

2. Wanneer de opdracht is `hbaseapp/target` voltooid, bevat `hbaseapp-1.0-SNAPSHOT.jar`de map een bestand met de naam .

   > [!NOTE]  
   > Het `hbaseapp-1.0-SNAPSHOT.jar` bestand is een uber pot. Het bevat alle afhankelijkheden die nodig zijn om de toepassing uit te voeren.

## <a name="upload-the-jar-and-run-jobs-ssh"></a>De JAR uploaden en taken uitvoeren (SSH)

De volgende `scp` stappen worden gebruikt om de JAR te kopiëren naar het primaire hoofdknooppunt van uw Apache HBase op het HDInsight-cluster. De `ssh` opdracht wordt vervolgens gebruikt om verbinding te maken met het cluster en het voorbeeld rechtstreeks op het hoofdknooppunt uit te voeren.

1. Upload de pot naar het cluster. Vervang `CLUSTERNAME` de naam van uw HDInsight-cluster en voer de volgende opdracht in:

    ```cmd
    scp ./target/hbaseapp-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:hbaseapp-1.0-SNAPSHOT.jar
    ```

2. Maak verbinding met het HBase-cluster. Vervang `CLUSTERNAME` de naam van uw HDInsight-cluster en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Als u een HBase-tabel wilt maken met de Java-toepassing, gebruikt u de volgende opdracht in uw geopende ssh-verbinding:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.CreateTable
    ```

    Met deze opdracht wordt een HBase-tabel met de naam **personen**en wordt deze gevuld met gegevens.

4. Als u wilt zoeken naar e-mailadressen die in de tabel zijn opgeslagen, gebruikt u de volgende opdracht:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.SearchByEmail contoso.com
    ```

    U ontvangt de volgende resultaten:

        Franklin Holtz - ID: 2
        Franklin Holtz - franklin@contoso.com - ID: 2
        Rae Schroeder - ID: 4
        Rae Schroeder - rae@contoso.com - ID: 4
        Gabriela Ingram - ID: 6
        Gabriela Ingram - gabriela@contoso.com - ID: 6

5. Als u de tabel wilt verwijderen, gebruikt u de volgende opdracht:

    ```bash
    yarn jar hbaseapp-1.0-SNAPSHOT.jar com.microsoft.examples.DeleteTable
    ```

## <a name="upload-the-jar-and-run-jobs-powershell"></a>De JAR uploaden en taken uitvoeren (PowerShell)

De volgende stappen gebruiken de Azure PowerShell [AZ-module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) om de JAR te uploaden naar de standaardopslag voor uw Apache HBase-cluster. HDInsight-cmdlets worden vervolgens gebruikt om de voorbeelden op afstand uit te voeren.

1. Nadat u de AZ-module hebt geïnstalleerd `hbase-runner.psm1`en geconfigureerd, maakt u een bestand met de naam . Gebruik de volgende tekst als de inhoud van dit bestand:

   ```powershell
    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.CreateTable"
    -clusterName "MyHDInsightCluster"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "contoso.com"

    .EXAMPLE
    Start-HBaseExample -className "com.microsoft.examples.SearchByEmail"
    -clusterName "MyHDInsightCluster"
    -emailRegex "^r" -showErr
    #>

    function Start-HBaseExample {
    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
    #The class to run
    [Parameter(Mandatory = $true)]
    [String]$className,

    #The name of the HDInsight cluster
    [Parameter(Mandatory = $true)]
    [String]$clusterName,

    #Only used when using SearchByEmail
    [Parameter(Mandatory = $false)]
    [String]$emailRegex,

    #Use if you want to see stderr output
    [Parameter(Mandatory = $false)]
    [Switch]$showErr
    )

    Set-StrictMode -Version 3

    # Is the Azure module installed?
    FindAzure

    # Get the login for the HDInsight cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # The JAR
    $jarFile = "wasb:///example/jars/hbaseapp-1.0-SNAPSHOT.jar"

    # The job definition
    $jobDefinition = New-AzHDInsightMapReduceJobDefinition `
        -JarFile $jarFile `
        -ClassName $className `
        -Arguments $emailRegex

    # Get the job output
    $job = Start-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzHDInsightJob `
        -ClusterName $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds
    if($showErr)
    {
    Write-Host "STDERR"
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds `
                -DisplayOutputType StandardError
    }
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzHDInsightJobOutput `
                -Clustername $clusterName `
                -JobId $job.JobId `
                -HttpCredential $creds
    }

    <#
    .SYNOPSIS
    Copies a file to the primary storage of an HDInsight cluster.
    .DESCRIPTION
    Copies a file from a local directory to the blob container for
    the HDInsight cluster.
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    .EXAMPLE
    Add-HDInsightFile -localPath "C:\temp\data.txt"
    -destinationPath "example/data/data.txt"
    -ClusterName "MyHDInsightCluster"
    -Container "MyContainer"
    #>

    function Add-HDInsightFile {
        [CmdletBinding(SupportsShouldProcess = $true)]
        param(
            #The path to the local file.
            [Parameter(Mandatory = $true)]
            [String]$localPath,

            #The destination path and file name, relative to the root of the container.
            [Parameter(Mandatory = $true)]
            [String]$destinationPath,

            #The name of the HDInsight cluster
            [Parameter(Mandatory = $true)]
            [String]$clusterName,

            #If specified, overwrites existing files without prompting
            [Parameter(Mandatory = $false)]
            [Switch]$force
        )

        Set-StrictMode -Version 3

        # Is the Azure module installed?
        FindAzure

        # Get authentication for the cluster
        $creds=Get-Credential

        # Does the local path exist?
        if (-not (Test-Path $localPath))
        {
            throw "Source path '$localPath' does not exist."
        }

        # Get the primary storage container
        $storage = GetStorage -clusterName $clusterName

        # Upload file to storage, overwriting existing files if -force was used.
        Set-AzStorageBlobContent -File $localPath `
            -Blob $destinationPath `
            -force:$force `
            -Container $storage.container `
            -Context $storage.context
    }

    function FindAzure {
        # Is there an active Azure subscription?
        $sub = Get-AzSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Connect-AzAccount
        }
    }

    function GetStorage {
        param(
            [Parameter(Mandatory = $true)]
            [String]$clusterName
        )
        $hdi = Get-AzHDInsightCluster -ClusterName $clusterName
        # Does the cluster exist?
        if (!$hdi)
        {
            throw "HDInsight cluster '$clusterName' does not exist."
        }
        # Create a return object for context & container
        $return = @{}
        $storageAccounts = @{}

        # Get storage information
        $resourceGroup = $hdi.ResourceGroup
        $storageAccountName=$hdi.DefaultStorageAccount.split('.')[0]
        $container=$hdi.DefaultStorageContainer
        $storageAccountKey=(Get-AzStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Get the resource group, in case we need that
        $return.resourceGroup = $resourceGroup
        # Get the storage context, as we can't depend
        # on using the default storage context
        $return.context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        # Get the container, so we know where to
        # find/store blobs
        $return.container = $container
        # Return storage accounts to support finding all accounts for
        # a cluster
        $return.storageAccount = $storageAccountName
        $return.storageAccountKey = $storageAccountKey

        return $return
    }
    # Only export the verb-phrase things
    export-modulemember *-*
   ```

    Dit bestand bevat twee modules:

   * **Add-HDInsightFile** - gebruikt om bestanden naar het cluster te uploaden
   * **Start-HBaseExample** - gebruikt om de eerder gemaakte klassen uit te voeren

2. Sla `hbase-runner.psm1` het bestand `hbaseapp` op in de map.

3. Registreer de modules met Azure PowerShell. Open een nieuw Azure PowerShell-venster en bewerk de opdracht hieronder door te vervangen door `CLUSTERNAME` de naam van uw cluster. Voer vervolgens de volgende opdrachten in:

    ```powershell
    cd C:\HDI\hbaseapp
    $myCluster = "CLUSTERNAME"
    Import-Module .\hbase-runner.psm1
    ```

4. Gebruik de volgende opdracht `hbaseapp-1.0-SNAPSHOT.jar` om de opdracht naar uw cluster te uploaden.

    ```powershell
    Add-HDInsightFile -localPath target\hbaseapp-1.0-SNAPSHOT.jar -destinationPath example/jars/hbaseapp-1.0-SNAPSHOT.jar -clusterName $myCluster
    ```

    Voer de naam en het wachtwoord van de clusterlogin (admin) in wanneer u daarom wordt gevraagd. De opdracht uploadt `hbaseapp-1.0-SNAPSHOT.jar` `example/jars` de locatie naar de primaire opslag voor uw cluster.

5. Als u een `hbaseapp`tabel wilt maken met de volgende opdracht:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.CreateTable -clusterName $myCluster
    ```

    Voer de naam en het wachtwoord van de clusterlogin (admin) in wanneer u daarom wordt gevraagd.

    Met deze opdracht wordt een tabel met de naam **personen** in HBase op uw HDInsight-cluster geaorimeerd. Met deze opdracht wordt geen uitvoer weergegeven in het consolevenster.

6. Als u wilt zoeken naar vermeldingen in de tabel, gebruikt u de volgende opdracht:

    ```powershell
    Start-HBaseExample -className com.microsoft.examples.SearchByEmail -clusterName $myCluster -emailRegex contoso.com
    ```

    Voer de naam en het wachtwoord van de clusterlogin (admin) in wanneer u daarom wordt gevraagd.

    Met deze `SearchByEmail` opdracht wordt de klasse `contactinformation` gebruikt om `email` te zoeken naar `contoso.com`rijen waarin de kolomfamilie en de kolom de tekenreeks bevatten. U ontvangt de volgende resultaten:

          Franklin Holtz - ID: 2
          Franklin Holtz - franklin@contoso.com - ID: 2
          Rae Schroeder - ID: 4
          Rae Schroeder - rae@contoso.com - ID: 4
          Gabriela Ingram - ID: 6
          Gabriela Ingram - gabriela@contoso.com - ID: 6

    Als **u** `-emailRegex` fabrikam.com gebruikt voor de waarde, worden de gebruikers die fabrikam.com in het e-mailveld hebben **fabrikam.com** geretourneerd. U ook reguliere expressies als zoekterm gebruiken. **^r** retourneert bijvoorbeeld e-mailadressen die beginnen met de letter 'r'.

7. Als u de tabel wilt verwijderen, gebruikt u de volgende opdracht:

    ```PowerShell
    Start-HBaseExample -className com.microsoft.examples.DeleteTable -clusterName $myCluster
    ```

### <a name="no-results-or-unexpected-results-when-using-start-hbaseexample"></a>Geen resultaten of onverwachte resultaten bij het gebruik van Start-HBaseExample

Gebruik `-showErr` de parameter om de standaardfout (STDERR) weer te geven die wordt geproduceerd tijdens het uitvoeren van de taak.

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gebruik van SQLLine met Apache HBase](apache-hbase-query-with-phoenix.md)
