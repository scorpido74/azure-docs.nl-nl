---
title: Aangepaste kaart beperken programma's uitvoeren - Azure HDInsight
description: Wanneer en hoe aangepaste Apache Map-programma's kunnen worden uitgevoerdReduce-programma's op Azure HDInsight-clusters.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 78623f738285e781cb561a3844db8fbf37226929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645018"
---
# <a name="run-custom-mapreduce-programs"></a>Aangepaste MapReduce-programma's uitvoeren

Apache Hadoop-gebaseerde big data-systemen zoals HDInsight maken gegevensverwerking mogelijk met behulp van een breed scala aan tools en technologieën. In de volgende tabel worden de belangrijkste voordelen en overwegingen voor elk van deze tabel beschreven.

| Querymechanisme | Voordelen | Overwegingen |
| --- | --- | --- |
| **Apache Hive met HiveQL** | <ul><li>Een uitstekende oplossing voor batchverwerking en analyse van grote hoeveelheden onveranderlijke gegevens, voor gegevenssomsoming en voor on demand-query's. Het maakt gebruik van een bekende SQL-achtige syntaxis.</li><li>Het kan worden gebruikt om permanente tabellen met gegevens te produceren die gemakkelijk kunnen worden verdeeld en geïndexeerd.</li><li>Meerdere externe tabellen en weergaven kunnen via dezelfde gegevens worden gemaakt.</li><li>Het ondersteunt een eenvoudige implementatie van het gegevensmagazijn die enorme scale-out- en fouttolerantiemogelijkheden biedt voor gegevensopslag en -verwerking.</li></ul> | <ul><li>Het vereist dat de brongegevens ten minste een identificeerbare structuur hebben.</li><li>Het is niet geschikt voor real-time query's en rijniveau updates. Het is het beste gebruikt voor batch taken over grote sets van gegevens.</li><li>Het kan mogelijk niet in staat zijn om bepaalde soorten complexe verwerkingstaken uit te voeren.</li></ul> |
| **Apache Pig met Pig Latin** | <ul><li>Een uitstekende oplossing voor het manipuleren van gegevens als verzamelingen, het samenvoegen en filteren van gegevenssets, het toepassen van functies op records of groepen records, en voor het herstructureren van gegevens door kolommen te definiëren, door waarden te groeperen of door kolommen om te zetten in rijen.</li><li>Het kan een workflow-gebaseerde aanpak gebruiken als een reeks bewerkingen op gegevens.</li></ul> | <ul><li>SQL-gebruikers kunnen vinden Pig Latin is minder bekend en moeilijker te gebruiken dan HiveQL.</li><li>De standaarduitvoer is meestal een tekstbestand en kan dus moeilijker te gebruiken zijn met visualisatietools zoals Excel. Meestal laagje een Hive-tabel over de uitvoer.</li></ul> |
| **Aangepaste kaart/vermindering** | <ul><li>Het biedt volledige controle over de kaart en vermindert fasen en uitvoering.</li><li>Hiermee kunnen query's worden geoptimaliseerd om maximale prestaties van het cluster te bereiken of om de belasting op de servers en het netwerk te minimaliseren.</li><li>De componenten kunnen worden geschreven in een reeks bekende talen.</li></ul> | <ul><li>Het is moeilijker dan het gebruik van Pig of Hive omdat je je eigen kaart moet maken en componenten moet verminderen.</li><li>Processen waarvoor gegevensmoeten worden verbonden, zijn moeilijker te implementeren.</li><li>Hoewel er testframeworks beschikbaar zijn, is foutopsporingscode complexer dan een normale toepassing omdat de code wordt uitgevoerd als een batchtaak onder de controle van de Hadoop-taakplanner.</li></ul> |
| **Apache HCatalog** | <ul><li>Het abstraheert de paddetails van opslag, waardoor beheer eenvoudiger wordt en gebruikers niet meer hoeven te weten waar de gegevens worden opgeslagen.</li><li>Hiermee kan worden gemeld dat gebeurtenissen zoals de beschikbaarheid van gegevens beschikbaar zijn, zodat andere hulpprogramma's zoals Oozie kunnen detecteren wanneer bewerkingen zijn uitgevoerd.</li><li>Het legt een relationele weergave van gegevens bloot, inclusief partitionering per sleutel, en maakt de gegevens gemakkelijk toegankelijk.</li></ul> | <ul><li>Het ondersteunt standaard RCFile-, CSV-tekst-, JSON-tekst-, SequenceFile- en ORC-bestandsindelingen, maar mogelijk moet u een aangepaste SerDe schrijven voor andere indelingen.</li><li>HCatalog is niet draadveilig.</li><li>Er zijn enkele beperkingen voor de gegevenstypen voor kolommen bij het gebruik van de HCatalog-lader in Pig-scripts. Zie [Gegevenstypen hcatloader in](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) de documentatie over Apache HCatalog voor meer informatie.</li></ul> |

Meestal gebruikt u de eenvoudigste van deze benaderingen die de gewenste resultaten kunnen opleveren. U bijvoorbeeld dergelijke resultaten bereiken door alleen Hive te gebruiken, maar voor complexere scenario's moet u mogelijk Pig gebruiken, of zelfs uw eigen kaart schrijven en onderdelen verminderen. U ook besluiten, na het experimenteren met Hive of Pig, dat aangepaste kaart en componenten te verminderen betere prestaties kunnen bieden door dat u de verwerking verfijnen en optimaliseren.

## <a name="custom-mapreduce-components"></a>Aangepaste kaart/onderdelen verkleinen

Kaart/reduce code bestaat uit twee afzonderlijke functies die als **kaart** worden geïmplementeerd en componenten **verminderen.** De **kaartcomponent** wordt parallel uitgevoerd op meerdere clusterknooppunten, waarbij elk knooppunt de toewijzing toepast op de eigen subset van de gegevens van het knooppunt. De **component verminderen** verzamelt en vat de resultaten van alle kaartfuncties samen. Zie [MapReduce gebruiken in Hadoop op HDInsight](hdinsight-use-mapreduce.md)voor meer informatie over deze twee componenten.

In de meeste HDInsight-verwerkingsscenario's is het eenvoudiger en efficiënter om een abstractie op een hoger niveau te gebruiken, zoals Pig of Hive. U ook aangepaste kaart maken en componenten verminderen voor gebruik in Hive-scripts om meer geavanceerde verwerking uit te voeren.

Aangepaste kaart/verminderen componenten worden meestal geschreven in Java. Hadoop biedt een streaming-interface waarmee ook componenten kunnen worden gebruikt die zijn ontwikkeld in andere talen zoals C#, F#, Visual Basic, Python en JavaScript.

* Zie [Java MapReduce-programma's ontwikkelen voor Hadoop op HDInsight voor](apache-hadoop-develop-deploy-java-mapreduce-linux.md)een walkthrough over het ontwikkelen van aangepaste Java MapReduce-programma's.

Overweeg het maken van uw eigen kaart en verminderen componenten voor de volgende voorwaarden:

* U moet gegevens verwerken die volledig ongestructureerd zijn door de gegevens te ontleeden en aangepaste logica te gebruiken om er gestructureerde informatie uit te halen.
* U wilt complexe taken uitvoeren die moeilijk (of onmogelijk) zijn om uit te drukken in Pig of Hive zonder gebruik te maken van het maken van een UDF. Het kan bijvoorbeeld nodig zijn om een externe geocoderingsservice te gebruiken om breedte- en lengtecoördinaten of IP-adressen in de brongegevens om te zetten in geografische locatienamen.
* U wilt uw bestaande .NET-, Python- of JavaScript-code opnieuw gebruiken in kaart/vermindering van componenten met behulp van de Hadoop-streaming-interface.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Uw aangepaste MapReduce-programma uploaden en uitvoeren

De meest voorkomende MapReduce programma's zijn geschreven in Java en gecompileerd naar een pot bestand.

1. Nadat u uw MapReduce-programma hebt ontwikkeld, gecompileerd en getest, gebruikt u de `scp` opdracht om uw jar-bestand naar de headnode te uploaden.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Clusternaam vervangen door de clusternaam. Als u een wachtwoord hebt gebruikt om het SSH-account te beveiligen, wordt u gevraagd het wachtwoord in te voeren. Als u een certificaat hebt gebruikt, `-i` moet u mogelijk de parameter gebruiken om het bestand met privésleutels op te geven.

1. Gebruik [de ssh-opdracht](../hdinsight-hadoop-linux-use-ssh-unix.md) om verbinding te maken met uw cluster. Bewerk de onderstaande opdracht door CLUSTERNAME te vervangen door de naam van uw cluster en voer de opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Voer vanuit de SSH-sessie uw MapReduce-programma uit via YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Met deze opdracht wordt de taak MapReduce ingediend bij YARN. Het invoerbestand `/example/data/sample.log`is en de `/example/data/logoutput`uitvoermap is . Het invoerbestand en eventuele uitvoerbestanden worden opgeslagen in de standaardopslag voor het cluster.

## <a name="next-steps"></a>Volgende stappen

* [C# gebruiken met MapReduce streaming op Apache Hadoop in HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Java MapReduce-programma's ontwikkelen voor Apache Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Azure Toolkit voor Eclipse gebruiken om Apache Spark-toepassingen te maken voor een HDInsight-cluster](../spark/apache-spark-eclipse-tool-plugin.md)
* [Gebruik Python User Defined Functions (UDF) met Apache Hive en Apache Pig in HDInsight](python-udf-hdinsight.md)
