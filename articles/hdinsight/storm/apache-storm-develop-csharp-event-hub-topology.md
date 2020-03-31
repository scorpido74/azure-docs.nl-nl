---
title: Gebeurtenissen van gebeurtenishubs verwerken met Storm - Azure HDInsight
description: Meer informatie over het verwerken van gegevens uit Azure Event Hubs met een C# Storm-topologie die is gemaakt in Visual Studio, met behulp van de HDInsight-tools voor Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 85b7093df99127b690c51e8f2f28d18e3f5f3c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981640"
---
# <a name="process-events-from-azure-event-hubs-with-apache-storm-on-hdinsight-c"></a>Gebeurtenissen uit Azure-gebeurtenishubs verwerken met Apache Storm op HDInsight (C#)

Meer informatie over het werken met Azure Event Hubs van [Apache Storm](https://storm.apache.org/) op HDInsight. Dit document gebruikt een C# Storm-topologie om gegevens van gebeurtenishubs te lezen en te schrijven

> [!NOTE]  
> Zie [Gebeurtenissen van Azure Event Hubs met Apache Storm op HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)voor een Java-versie van dit project.

## <a name="scpnet"></a>SCP.NET

De stappen in dit document gebruiken SCP.NET, een NuGet-pakket waarmee u eenvoudig C# topologieën en componenten maken voor gebruik met Storm on HDInsight.

HDInsight 3.4 en meer gebruiken Mono om C# topologieën uit te voeren. Het voorbeeld dat in dit document wordt gebruikt, werkt met HDInsight 3.6. Als u van plan bent uw eigen .NET-oplossingen voor HDInsight te maken, controleert u het [monocompatibiliteitsdocument](https://www.mono-project.com/docs/about-mono/compatibility/) op mogelijke onverenigbaarheden.

### <a name="cluster-versioning"></a>Clusterversies

Het Microsoft.SCP.Net.SDK NuGet-pakket dat u voor uw project gebruikt, moet overeenkomen met de belangrijkste versie van Storm die op HDInsight is geïnstalleerd. HDInsight-versies 3.5 en 3.6 gebruiken Storm 1.x, dus u moet SCP.NET versie 1.0.x.x gebruiken met deze clusters.

C# topologieën moeten ook gericht zijn op .NET 4.5.

## <a name="how-to-work-with-event-hubs"></a>Werken met eventhubs

Microsoft biedt een reeks Java-componenten die kunnen worden gebruikt om te communiceren met gebeurtenishubs vanuit een Storm-topologie. U vindt het Java-archief (JAR) bestand dat een HDInsight 3.6 compatibele versie van deze componenten bevat op [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar).

> [!IMPORTANT]  
> Terwijl de componenten zijn geschreven in Java, u ze gemakkelijk gebruiken vanuit een C # topologie.

In dit voorbeeld worden de volgende componenten gebruikt:

* __EventHubSpout__: Leest gegevens van Gebeurtenishubs.
* __EventHubBolt__: Schrijft gegevens naar gebeurtenishubs.
* __EventHubSpoutConfig__: Wordt gebruikt om EventHubSpout te configureren.
* __EventHubBoltConfig__: Wordt gebruikt om EventHubBolt te configureren.

### <a name="example-spout-usage"></a>Voorbeeld van uitloopgebruik

SCP.NET biedt methoden voor het toevoegen van een EventHubSpout aan uw topologie. Deze methoden maken het gemakkelijker om een tuit toe te voegen dan het gebruik van de generieke methoden voor het toevoegen van een Java-component. In het volgende voorbeeld wordt uitgelegd hoe u een uitloop maken met behulp van de __Methoden SetEventHubSpout__ en **EventHubSpoutConfig** van SCP.NET:

```csharp
 topologyBuilder.SetEventHubSpout(
    "EventHubSpout",
    new EventHubSpoutConfig(
        ConfigurationManager.AppSettings["EventHubSharedAccessKeyName"],
        ConfigurationManager.AppSettings["EventHubSharedAccessKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        ConfigurationManager.AppSettings["EventHubEntityPath"],
        eventHubPartitions),
    eventHubPartitions);
```

In het vorige voorbeeld wordt een nieuwe uitloopcomponent gemaakt met de naam __EventHubSpout__en wordt deze geconfigureerd om te communiceren met een gebeurtenishub. De parallellismehint voor de component is ingesteld op het aantal partities in de gebeurtenishub. Met deze instelling kan Storm voor elke partitie een instantie van de component maken.

### <a name="example-bolt-usage"></a>Voorbeeld van boutgebruik

Gebruik de **JavaComponmentConstructor-methode** om een instantie van de bout te maken. In het volgende voorbeeld wordt uitgelegd hoe u een nieuw exemplaar van de **EventHubBolt**maakt en configureert:

```csharp
// Java construcvtor for the Event Hub Bolt
JavaComponentConstructor constructor = JavaComponentConstructor.CreateFromClojureExpr(
    String.Format(@"(org.apache.storm.eventhubs.bolt.EventHubBolt. (org.apache.storm.eventhubs.bolt.EventHubBoltConfig. " +
        @"""{0}"" ""{1}"" ""{2}"" ""{3}"" ""{4}"" {5}))",
        ConfigurationManager.AppSettings["EventHubPolicyName"],
        ConfigurationManager.AppSettings["EventHubPolicyKey"],
        ConfigurationManager.AppSettings["EventHubNamespace"],
        "servicebus.windows.net",
        ConfigurationManager.AppSettings["EventHubName"],
        "true"));

// Set the bolt to subscribe to data from the spout
topologyBuilder.SetJavaBolt(
    "eventhubbolt",
    constructor,
    partitionCount)
        .shuffleGrouping("Spout");
```

> [!NOTE]  
> In dit voorbeeld wordt een Clojure-expressie gebruikt die als tekenreeks is doorgegeven, in plaats van **JavaComponentConstructor** te gebruiken om een **EventHubBoltConfig**te maken, zoals het uitloopvoorbeeld deed. Beide methoden werken. Gebruik de methode die het beste voelt voor jou.

## <a name="download-the-completed-project"></a>Het voltooide project downloaden

U een volledige versie van het project dat in dit artikel is gemaakt downloaden van [GitHub.](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub) U moet echter nog steeds configuratie-instellingen opgeven door de stappen in dit artikel te volgen.

### <a name="prerequisites"></a>Vereisten

* Een Apache Storm cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en Selecteer **Storm** voor **clustertype**.

* Een [Azure-gebeurtenishub](../../event-hubs/event-hubs-create.md).

* De [Azure .NET SDK](https://azure.microsoft.com/downloads/).

* De [HDInsight-tools voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

* Java JDK 1.8 of hoger op uw ontwikkelomgeving. JDK-downloads zijn beschikbaar bij [Oracle.](https://aka.ms/azure-jdks)

  * De **JAVA_HOME** omgevingsvariabele moet wijzen op de map die Java bevat.
  * De **map %JAVA_HOME%/opslaglocatie** moet zich in het pad bevinden.

## <a name="download-the-event-hubs-components"></a>De onderdelen van Event Hubs downloaden

Download de uitloop- en [https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar](https://github.com/hdinsight/mvn-repo/raw/master/org/apache/storm/storm-eventhubs/1.1.0.1/storm-eventhubs-1.1.0.1.jar)boutcomponent Event Hubs van .

Maak een `eventhubspout`map met de naam en sla het bestand op in de map.

## <a name="configure-event-hubs"></a>Gebeurtenishubs configureren

Event Hubs is de gegevensbron voor dit voorbeeld. Gebruik de informatie in het gedeelte 'Een gebeurtenishub maken' van [Aan de slag met gebeurtenishubs.](../../event-hubs/event-hubs-create.md)

1. Nadat de gebeurtenishub is gemaakt, bekijkt u de **EventHub-instellingen** in de Azure-portal en selecteert u **Beleid voor gedeelde toegang**. Selecteer **+ Toevoegen** om het volgende beleid te maken:

   | Name | Machtigingen |
   | --- | --- |
   | Schrijver |Verzenden |
   | Lezer |Luisteren |

    ![Schermafbeelding van het venster Toegangsbeleid voor delen](./media/apache-storm-develop-csharp-event-hub-topology/share-access-policies.png)

2. Selecteer het **beleid voor lezers** en **schrijvers.** Kopieer en sla de primaire sleutelwaarde voor beide beleidsregels op, omdat deze waarden later worden gebruikt.

## <a name="configure-the-eventhubwriter"></a>De EventHubWriter configureren

1. Zie Aan de [slag met HDInsight-tools voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)als u de nieuwste versie van de HDInsight-tools voor Visual Studio nog niet hebt geïnstalleerd.

2. Download de oplossing van [eventhub-storm-hybrid.](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)

3. Open **EventHubExample.sln**. Open in het **EventHubWriter-project** het bestand **App.config.** Gebruik de informatie van de gebeurtenishub die u eerder hebt geconfigureerd om de waarde voor de volgende toetsen in te vullen:

   | Sleutel | Waarde |
   | --- | --- |
   | EventHubPolicyName |schrijver (Als u een andere naam voor het beleid hebt gebruikt met *Machtigingen* verzenden, gebruikt u deze in plaats daarvan.) |
   | EventHubPolicyKey |De sleutel voor het schrijversbeleid. |
   | EventHubNamespace |De naamruimte die uw gebeurtenishub bevat. |
   | EventHubName |De naam van uw gebeurtenishub. |
   | EventHubPartitionCount |Het aantal partities in uw gebeurtenishub. |

4. Sla het **app.config-bestand** op en sluit deze.

## <a name="configure-the-eventhubreader"></a>De EventHubReader configureren

1. Open het **EventHubReader-project.**

2. Open het **app.config-bestand** voor de **EventHubReader**. Gebruik de informatie van de gebeurtenishub die u eerder hebt geconfigureerd om de waarde voor de volgende toetsen in te vullen:

   | Sleutel | Waarde |
   | --- | --- |
   | EventHubPolicyName |lezer (Als u een andere naam voor het beleid met *luistertoestemming* hebt gebruikt, gebruikt u deze in plaats daarvan.) |
   | EventHubPolicyKey |De sleutel voor het lezersbeleid. |
   | EventHubNamespace |De naamruimte die uw gebeurtenishub bevat. |
   | EventHubName |De naam van uw gebeurtenishub. |
   | EventHubPartitionCount |Het aantal partities in uw gebeurtenishub. |

3. Sla het **app.config-bestand** op en sluit deze.

## <a name="deploy-the-topologies"></a>De topologieën implementeren

1. Klik vanuit **Solution Explorer**met de rechtermuisknop op het **EventHubReader-project** en selecteer **Verzenden naar Storm op HDInsight**.

    ![Schermafbeelding van Solution Explorer, met Ondermening aan Storm op HDInsight gemarkeerd](./media/apache-storm-develop-csharp-event-hub-topology/submit-to-apache-storm.png)

2. Selecteer in het dialoogvenster **Topologie verzenden** de optie Uw **stormcluster**. Extra **configuraties**uitbreiden, **Java-bestandspaden**selecteren, selecteer **...** en selecteer de map met het JAR-bestand dat u eerder hebt gedownload. Klik tot slot op **Verzenden**.

    ![Schermafbeelding van het dialoogvenster Topologie verzenden](./media/apache-storm-develop-csharp-event-hub-topology/submit-storm-topology.png)

3. Wanneer de topologie is ingediend, wordt de **Storm Topologieën Viewer** weergegeven. Als u informatie over de topologie wilt weergeven, selecteert u de **Topologie van EventHubReader** in het linkerdeelvenster.

    ![Schermafbeelding van Storm Topologieën Viewer](./media/apache-storm-develop-csharp-event-hub-topology/storm-topology-viewer.png)

4. Klik vanuit **Solution Explorer**met de rechtermuisknop op het **EventHubWriter-project** en selecteer **Verzenden naar Storm op HDInsight.**

5. Selecteer in het dialoogvenster **Topologie verzenden** de optie Uw **stormcluster**. Extra **configuraties**uitbreiden, **Java-bestandspaden**selecteren, selecteer **...** en selecteer de map met het JAR-bestand dat u eerder hebt gedownload. Klik tot slot op **Verzenden**.

6. Wanneer de topologie is ingediend, vernieuwt u de topologielijst in de **Viewer Stormtopologieën** om te controleren of beide topologieën op het cluster worden uitgevoerd.

7. Selecteer in **Storm Topologieën Viewer**de **EventHubReader-topologie.**

8. Als u de componentsamenvatting voor de bout wilt openen, dubbelklikt u op de component **LogBolt** in het diagram.

9. Selecteer in de sectie **Executors** een van de koppelingen in de kolom **Poort.** Hier wordt informatie weergegeven die door de component is vastgelegd. De geregistreerde informatie is vergelijkbaar met de volgende tekst:

    ```output
    2017-03-02 14:51:29.255 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,255 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1830978598,"deviceId":"8566ccbc-034d-45db-883d-d8a31f34068e"}
    2017-03-02 14:51:29.283 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,283 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1756413275,"deviceId":"647a5eff-823d-482f-a8b4-b95b35ae570b"}
    2017-03-02 14:51:29.313 m.s.p.TaskHost [INFO] Received C# STDOUT: 2017-03-02 14:51:29,312 [1] INFO  EventHubReader_LogBolt [(null)] - Received data: {"deviceValue":1108478910,"deviceId":"206a68fa-8264-4d61-9100-bfdb68ee8f0a"}
    ```

## <a name="stop-the-topologies"></a>De topologieën stoppen

Als u de topologieën wilt stoppen, selecteert u elke topologie in de **Storm Topology Viewer**en klikt u op **Doden**.

![Schermafbeelding van Storm Topology Viewer, met de knop Kill gemarkeerd](./media/apache-storm-develop-csharp-event-hub-topology/kill-storm-topology1.png)

## <a name="delete-your-cluster"></a>Uw cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de java-gebeurtenishubs gebruiken om met gegevens in Azure Event Hubs te werken. Zie het volgende voor meer informatie over het maken van C#-topologieën:

* [Ontwikkel C# topologieën voor Apache Storm op HDInsight met behulp van Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [SCP-programmeergids](apache-storm-scp-programming-guide.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)
