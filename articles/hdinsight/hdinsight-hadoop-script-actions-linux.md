---
title: Scriptacties ontwikkelen om Azure HDInsight-clusters aan te passen
description: Meer informatie over het gebruik van Bash-scripts om HDInsight-clusters aan te passen. Met scriptacties u scripts uitvoeren tijdens of na het maken van het cluster om de instellingen voor clusterconfiguratie te wijzigen of extra software te installeren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: db37a56ffbf0cb64530f8f7af38841bac72c77d4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767552"
---
# <a name="script-action-development-with-hdinsight"></a>Scriptactieontwikkeling met HDInsight

Meer informatie over het aanpassen van uw HDInsight-cluster met behulp van Bash-scripts. Scriptacties zijn een manier om HDInsight aan te passen tijdens of na het maken van het cluster.

## <a name="what-are-script-actions"></a>Wat zijn scriptacties

Scriptacties zijn Bash-scripts die Azure uitvoert op de clusterknooppunten om configuratiewijzigingen aan te brengen of software te installeren. Een scriptactie wordt uitgevoerd als root en biedt volledige toegangsrechten tot de clusterknooppunten.

Scriptacties kunnen worden toegepast via de volgende methoden:

| Gebruik deze methode om een script toe te passen... | Tijdens het maken van clusteren... | Op een lopend cluster... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klassieke versie van Azure-CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-sjabloon |✓ |&nbsp; |

Zie [HDInsight-clusters aanpassen met behulp van scriptacties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van deze methoden om scriptacties toe te passen.

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Aanbevolen procedures voor scriptontwikkeling

Wanneer u een aangepast script ontwikkelt voor een HDInsight-cluster, zijn er verschillende aanbevolen procedures om in gedachten te houden:

* [Richt u op de Apache Hadoop-versie](#bPS1)
* [Target de OS-versie](#bps10)
* [Stabiele koppelingen naar scriptbronnen bieden](#bPS2)
* [Vooraf samengestelde resources gebruiken](#bPS4)
* [Ervoor zorgen dat het script voor het aanpassen van het cluster idempotent is](#bPS3)
* [Zorgen voor een hoge beschikbaarheid van de clusterarchitectuur](#bPS5)
* [De aangepaste componenten configureren om Azure Blob-opslag te gebruiken](#bPS6)
* [Informatie schrijven naar SOAOUT en STDERR](#bPS7)
* [Bestanden opslaan als ASCII met LF-regeleinde](#bps8)
* [Logica opnieuw proberen gebruiken om te herstellen van tijdelijke fouten](#bps9)

> [!IMPORTANT]  
> Scriptacties moeten binnen 60 minuten zijn voltooid of het proces mislukt. Tijdens het inrichten van het knooppunt wordt het script gelijktijdig uitgevoerd met andere installatie- en configuratieprocessen. Concurrentie om resources zoals CPU-tijd of netwerkbandbreedte kan ertoe leiden dat het script langer duurt dan in uw ontwikkelomgeving.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Richt u op de Apache Hadoop-versie

Verschillende versies van HDInsight hebben verschillende versies van Hadoop-services en componenten geïnstalleerd. Als uw script een specifieke versie van een service of component verwacht, moet u het script alleen gebruiken met de versie van HDInsight die de vereiste componenten bevat. U informatie vinden over componentversies die bij HDInsight zijn meegeleverd met behulp [van het HDInsight-componentversiedocument.](hdinsight-component-versioning.md)

### <a name="checking-the-operating-system-version"></a>De versie van het besturingssysteem controleren

Verschillende versies van HDInsight zijn afhankelijk van specifieke versies van Ubuntu. Er kunnen verschillen zijn tussen OS-versies die u moet controleren in uw script. Het kan bijvoorbeeld nodig zijn om een binaire te installeren die is gekoppeld aan de versie van Ubuntu.

Als u de OS-versie wilt controleren, gebruikt u `lsb_release`. In het volgende script wordt bijvoorbeeld uitgelegd hoe u naar een specifiek teerbestand verwijzen, afhankelijk van de OS-versie:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>De versie van het besturingssysteem targeten

HDInsight is gebaseerd op de Ubuntu Linux distributie. Verschillende versies van HDInsight zijn afhankelijk van verschillende versies van Ubuntu, wat de manier waarop uw script zich gedraagt kan veranderen. HDInsight 3.4 en eerder zijn bijvoorbeeld gebaseerd op Ubuntu-versies die Upstart gebruiken. Versies 3.5 en hoger zijn gebaseerd op Ubuntu 16.04, dat Systemd gebruikt. Systemd en Upstart vertrouwen op verschillende opdrachten, dus uw script moet worden geschreven om met beide te werken.

Een ander belangrijk verschil tussen HDInsight 3.4 en 3.5 is dat `JAVA_HOME` nu wijst naar Java 8. De volgende code laat zien hoe u bepalen of het script wordt uitgevoerd op Ubuntu 14 of 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

U vindt het volledige script met https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.shdeze fragmenten op .

Zie voor de versie van Ubuntu die door HDInsight wordt gebruikt, het [hdInsight-componentversiedocument.](hdinsight-component-versioning.md)

Zie [Systemd voor Upstart-gebruikers](https://wiki.ubuntu.com/SystemdForUpstartUsers)om de verschillen tussen Systemd en Upstart te begrijpen.

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Stabiele koppelingen naar scriptbronnen bieden

Het script en de bijbehorende resources moeten beschikbaar blijven gedurende de gehele levensduur van het cluster. Deze resources zijn vereist als er nieuwe knooppunten aan het cluster worden toegevoegd tijdens schaalbewerkingen.

Het beste is om alles te downloaden en te archiveren in een Azure Storage-account op uw abonnement.

> [!IMPORTANT]  
> Het gebruikte opslagaccount moet het standaardopslagaccount voor het cluster of een openbare, alleen-lezen container op een ander opslagaccount zijn.

De door Microsoft verstrekte voorbeelden worden [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) bijvoorbeeld opgeslagen in het opslagaccount. Deze locatie is een openbare, alleen-lezen container die wordt onderhouden door het HDInsight-team.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Vooraf samengestelde resources gebruiken

Als u de tijd wilt verkorten die nodig is om het script uit te voeren, vermijdt u bewerkingen die resources uit de broncode compileren. Stel resources bijvoorbeeld vooraf samen en sla ze op in een Blob van azure-opslagaccountin hetzelfde datacenter als HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Ervoor zorgen dat het script voor het aanpassen van het cluster idempotent is

Scripts moeten idempotent zijn. Als het script meerdere keren wordt uitgevoerd, moet het cluster telkens naar dezelfde status worden teruggegeven.

Een script dat configuratiebestanden wijzigt, mag bijvoorbeeld geen dubbele vermeldingen toevoegen als deze meerdere keren worden uitgevoerd.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Zorgen voor een hoge beschikbaarheid van de clusterarchitectuur

HdInsight-clusters op Basis van Linux bieden twee hoofdknooppunten die actief zijn binnen het cluster en scriptacties worden uitgevoerd op beide knooppunten. Als de onderdelen die u installeert slechts één hoofdknooppunt verwachten, installeer de componenten dan niet op beide hoofdknooppunten.

> [!IMPORTANT]  
> Services die worden geleverd als onderdeel van HDInsight zijn ontworpen om te mislukken tussen de twee hoofdknooppunten als dat nodig is. Deze functionaliteit wordt niet uitgebreid naar aangepaste componenten die zijn geïnstalleerd via scriptacties. Als u een hoge beschikbaarheid voor aangepaste componenten nodig hebt, moet u uw eigen failovermechanisme implementeren.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>De aangepaste componenten configureren om Azure Blob-opslag te gebruiken

Onderdelen die u op het cluster installeert, hebben mogelijk een standaardconfiguratie die gebruikmaakt van Apache Hadoop Distributed File System (HDFS)-opslag. HDInsight gebruikt Azure Storage of Data Lake Storage als standaardopslag. Beide bieden een HDFS-compatibel bestandssysteem dat gegevens blijft bestaan, zelfs als het cluster wordt verwijderd. Mogelijk moet u onderdelen die u installeert configureren om WASB of ADL te gebruiken in plaats van HDFS.

Voor de meeste bewerkingen hoeft u het bestandssysteem niet op te geven. De volgende kopieën van het bestand hadoop-common.jar van het lokale bestandssysteem naar clusteropslag:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

In dit voorbeeld `hdfs` maakt de opdracht op transparante wijze gebruik van de standaardclusteropslag. Voor sommige bewerkingen moet u mogelijk de URI opgeven. Bijvoorbeeld `adl:///example/jars` voor Azure Data Lake Storage `abfs:///example/jars` Gen1, voor `wasb:///example/jars` Data Lake Storage Gen2 of voor Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Informatie schrijven naar SOAOUT en STDERR

HDInsight registreert scriptuitvoer die is geschreven naar STDOUT en STDERR. U deze informatie bekijken via de gebruikersinterface van het Ambari-web.

> [!NOTE]  
> Apache Ambari is alleen beschikbaar als het cluster is gemaakt. Zie [Scriptacties oplossen](./troubleshoot-script-action.md) voor andere manieren om toegang te krijgen tot geregistreerde informatie als u een scriptactie gebruikt tijdens het maken van een cluster en dat het maken mislukt.

De meeste hulpprogramma's en installatiepakketten schrijven al informatie naar STDOUT en STDERR, maar misschien wilt u extra logboekregistratie toevoegen. Als u tekst naar STDOUT wilt verzenden, gebruikt u `echo`. Bijvoorbeeld:

```bash
echo "Getting ready to install Foo"
```

`echo` Stuurt de tekenreeks standaard naar SOAOUT. Als u deze naar STDERR wilt leiden, voegt u eerder `>&2` `echo`toe . Bijvoorbeeld:

```bash
>&2 echo "An error occurred installing Foo"
```

Hiermee wordt informatie die is geschreven naar STDOUT omgeleid naar STDERR (2). Zie voor meer informatie over [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)io-omleiding .

Zie [Scriptacties oplossen](./troubleshoot-script-action.md)voor meer informatie over het weergeven van gegevens die zijn vastgelegd door scriptacties.

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a>Bestanden opslaan als ASCII met LF-regeleinde

Bash-scripts moeten worden opgeslagen als ASCII-indeling, waarbij regels worden beëindigd door LF. Bestanden die zijn opgeslagen als UTF-8 of CRLF gebruiken als de einde van de regel, kunnen mislukken met de volgende fout:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a>Logica opnieuw proberen gebruiken om te herstellen van tijdelijke fouten

Bij het downloaden van bestanden, het installeren van pakketten met apt-get of andere acties die gegevens via internet verzenden, kan de actie mislukken als gevolg van tijdelijke netwerkfouten. De externe bron waarmee u communiceert, is bijvoorbeeld mogelijk niet naar een back-upknooppunt.

Als u uw script bestand wilt maken tegen tijdelijke fouten, u logica voor opnieuw proberen implementeren. De volgende functie laat zien hoe logica opnieuw proberen te implementeren. Het herprobeert de bewerking drie keer voordat deze mislukt.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

In de volgende voorbeelden wordt uitgelegd hoe u deze functie gebruiken.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Helpermethoden voor aangepaste scripts

Script actie helper methoden zijn hulpprogramma's die u gebruiken tijdens het schrijven van aangepaste scripts. Deze methoden zijn opgenomen [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) in het script. Gebruik het volgende om ze te downloaden en te gebruiken als onderdeel van je script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

De volgende helpers beschikbaar voor gebruik in uw script:

| Helpergebruik | Beschrijving |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Downloadt een bestand van de bron URI naar het opgegeven bestandspad. Standaard overschrijft het een bestaand bestand niet. |
| `untar_file TARFILE DESTDIR` |Haalt een teerbestand `-xf`(met behulp van) uit naar de doelmap. |
| `test_is_headnode` |Als u op een clusterhoofdknooppunt wordt uitgevoerd, geeft u 1 terug; anders, 0. |
| `test_is_datanode` |Als het huidige knooppunt een knooppunt voor gegevens (werknemer) is, retourneert u een 1; anders, 0. |
| `test_is_first_datanode` |Als het huidige knooppunt het knooppunt van de eerste gegevens (werknemer) is (workernode0) retourneert een 1; anders, 0. |
| `get_headnodes` |Retourneer de volledig gekwalificeerde domeinnaam van de hoofdnoden in het cluster. Namen zijn komma afgebakend. Een lege tekenreeks wordt op fout geretourneerd. |
| `get_primary_headnode` |Krijgt de volledig gekwalificeerde domeinnaam van de primaire headnode. Een lege tekenreeks wordt op fout geretourneerd. |
| `get_secondary_headnode` |Krijgt de volledig gekwalificeerde domeinnaam van de secundaire headnode. Een lege tekenreeks wordt op fout geretourneerd. |
| `get_primary_headnode_number` |Krijgt het numerieke achtervoegsel van de primaire headnode. Een lege tekenreeks wordt op fout geretourneerd. |
| `get_secondary_headnode_number` |Krijgt het numerieke achtervoegsel van de secundaire headnode. Een lege tekenreeks wordt op fout geretourneerd. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Algemene gebruikspatronen

In deze sectie vindt u richtlijnen voor het implementeren van enkele van de algemene gebruikspatronen die u tijdens het schrijven van uw eigen aangepaste script tegenkomen.

### <a name="passing-parameters-to-a-script"></a>Parameters doorgeven aan een script

In sommige gevallen vereist uw script mogelijk parameters. U hebt bijvoorbeeld het beheerderswachtwoord voor het cluster nodig wanneer u de Ambari REST API gebruikt.

Parameters die aan het script worden doorgegeven, `$1` worden *positionele parameters*genoemd en worden toegewezen aan de eerste parameter, `$2` voor de tweede en enzovoort. `$0`bevat de naam van het script zelf.

Waarden die aan het script worden doorgegeven omdat parameters moeten worden ingesloten door enkele aanhalingstekens ('). Dit zorgt ervoor dat de doorgegeven waarde wordt behandeld als een letterlijke.

### <a name="setting-environment-variables"></a>Omgevingsvariabelen instellen

Het instellen van een omgevingsvariabele wordt uitgevoerd met de volgende instructie:

    VARIABLENAME=value

Waar VARIABLENAME de naam van de variabele is. Gebruik `$VARIABLENAME`. Als u bijvoorbeeld een waarde van een positionele parameter wilt toewijzen als omgevingsvariabele met de naam PASSWORD, gebruikt u de volgende instructie:

    PASSWORD=$1

Latere toegang tot de `$PASSWORD`informatie kan dan gebruik maken van .

Omgevingsvariabelen die in het script zijn ingesteld, bestaan alleen binnen het bereik van het script. In sommige gevallen moet u mogelijk omgevingsvariabelen voor het hele systeem toevoegen die blijven bestaan nadat het script is voltooid. Als u omgevingsvariabelen voor het hele `/etc/environment`systeem wilt toevoegen, voegt u de variabele toe aan . In de volgende verklaring `HADOOP_CONF_DIR`wordt bijvoorbeeld het volgende toegevoegd:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Toegang tot locaties waar de aangepaste scripts zijn opgeslagen

Scripts die worden gebruikt om een cluster aan te passen, moeten worden opgeslagen op een van de volgende locaties:

* Een __Azure Storage-account__ dat is gekoppeld aan het cluster.

* Een __extra opslagaccount__ dat is gekoppeld aan het cluster.

* Een __publiek leesbare URI__. Bijvoorbeeld een URL naar gegevens die zijn opgeslagen in OneDrive, Dropbox of een andere bestandshostingservice.

* Een __Azure Data Lake Storage-account__ dat is gekoppeld aan het HDInsight-cluster. Zie [Quickstart: Clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)voor meer informatie over het gebruik van Azure Data Lake Storage met HDInsight.

    > [!NOTE]  
    > De serviceprincipal die HDInsight gebruikt om toegang te krijgen tot Data Lake Storage moet leestoegang tot het script hebben.

Bronnen die door het script worden gebruikt, moeten ook openbaar beschikbaar zijn.

Het opslaan van de bestanden in een Azure Storage-account of Azure Data Lake Storage biedt snelle toegang, zowel binnen het Azure-netwerk.

> [!NOTE]  
> De URI-indeling die wordt gebruikt om naar het script te verwijzen, verschilt afhankelijk van de service die wordt gebruikt. Voor opslagaccounts die zijn gekoppeld `wasb://` aan `wasbs://`het HDInsight-cluster, gebruikt of . Voor openbaar leesbare URI's u gebruik maken `http://` van of `https://`. Gebruik voor `adl://`Data Lake Storage .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Controlelijst voor het implementeren van een scriptactie

Hier volgen de stappen die worden genomen bij het voorbereiden van het implementeren van een script:

* Plaats de bestanden die de aangepaste scripts bevatten op een plaats die toegankelijk is voor de clusterknooppunten tijdens de implementatie. Bijvoorbeeld de standaardopslag voor het cluster. Bestanden kunnen ook worden opgeslagen in openbaar leesbare hostingservices.
* Controleer of het script idempotent is. Hierdoor kan het script meerdere keren worden uitgevoerd op hetzelfde knooppunt.
* Gebruik een tijdelijke bestandsmap /tmp om de gedownloade bestanden die door de scripts worden gebruikt te behouden en vervolgens op te schonen nadat scripts zijn uitgevoerd.
* Als instellingen op OS-niveau of Hadoop-serviceconfiguratiebestanden worden gewijzigd, u HDInsight-services opnieuw starten.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Een scriptactie uitvoeren

U scriptacties gebruiken om HDInsight-clusters aan te passen met behulp van de volgende methoden:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-sjablonen
* De HDInsight .NET SDK.

Zie [Scriptactie gebruiken](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over het gebruik van elke methode.

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Aangepaste scriptvoorbeelden

Microsoft biedt voorbeeldscripts om onderdelen op een HDInsight-cluster te installeren. Zie [Hue installeren en gebruiken op HDInsight-clusters](hdinsight-hadoop-hue-linux.md) als voorbeeldscriptactie.

## <a name="troubleshooting"></a>Problemen oplossen

De volgende fouten die u tegenkomen bij het gebruik van scripts die u hebt ontwikkeld:

**Fout** `$'\r': command not found`: . Soms gevolgd `syntax error: unexpected end of file`door .

*Oorzaak:* Deze fout wordt veroorzaakt wanneer de regels in een script eindigen met CRLF. Unix-systemen verwachten alleen LF als de lijn eindigt.

Dit probleem treedt meestal op wanneer het script is geschreven op een Windows-omgeving, omdat CRLF een veelvoorkomende regel is die eindigt voor veel teksteditors op Windows.

*Oplossing:* Als het een optie is in uw teksteditor, selecteert u Unix-indeling of LF voor het einde van de regel. U ook de volgende opdrachten op een Unix-systeem gebruiken om de CRLF in een LF te wijzigen:

> [!NOTE]  
> De volgende opdrachten zijn ongeveer gelijkwaardig in die zin dat ze de CRLF-lijn eindes moeten veranderen in LF. Selecteer er een op basis van de hulpprogramma's die beschikbaar zijn op uw systeem.

| Opdracht | Opmerkingen |
| --- | --- |
| `unix2dos -b INFILE` |Er wordt een back-up van het oorspronkelijke bestand ondersteund met een . BAK uitbreiding |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE bevat een versie met alleen LF-uitgangen |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Wijzigt het bestand rechtstreeks |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE bevat een versie met alleen LF-uitgangen. |

**Fout** `line 1: #!/usr/bin/env: No such file or directory`: .

*Oorzaak:* deze fout treedt op wanneer het script is opgeslagen als UTF-8 met een stempel van byteorder (BOM).

*Resolutie:* Sla het bestand op als ASCII of als UTF-8 zonder bom. U ook de volgende opdracht op een Linux- of Unix-systeem gebruiken om een bestand te maken zonder de BOM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Vervang `INFILE` het bestand met de bom. `OUTFILE`moet een nieuwe bestandsnaam zijn, die het script zonder de BOM bevat.

## <a name="next-steps"></a><a name="seeAlso"></a>Volgende stappen

* Meer informatie over het [aanpassen van HDInsight-clusters met scriptactie](hdinsight-hadoop-customize-cluster-linux.md)
* Gebruik de [HDInsight .NET SDK-verwijzing](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) voor meer informatie over het maken van .NET-toepassingen die HDInsight beheren
* Gebruik de [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) om te leren hoe u REST gebruiken om beheeracties uit te voeren op HDInsight-clusters.
