---
title: Apache Storm topologieën met Visual Studio en C# - Azure HDInsight
description: Meer informatie over het maken van Storm-topologieën in C#. Maak een woordtellingstopologie in Visual Studio met de Hadoop-tools voor Visual Studio.
ROBOTS: NOINDEX
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/31/2019
ms.openlocfilehash: 1903c2faab865152d1f3666f3c9dadd745058b56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612288"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Ontwikkel C# topologieën voor Apache Storm met behulp van de Data Lake tools voor Visual Studio

Meer informatie over het maken van een C# Apache Storm-topologie met behulp van de Azure Data Lake -hulpprogramma's (Apache Hadoop) voor Visual Studio. Dit document loopt door het proces van het maken van een Storm-project in Visual Studio, het lokaal testen en implementeren in een Apache Storm op Azure HDInsight-cluster.

U leert ook hoe u hybride topologieën maakt die C#- en Java-componenten gebruiken.

C# topologieën gebruiken .NET 4.5 en gebruiken Mono om op het HDInsight-cluster te draaien. Zie [Monocompatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/)voor informatie over mogelijke onverenigbaarheden. Als u een C#-topologie `Microsoft.SCP.Net.SDK` wilt gebruiken, moet u het NuGet-pakket dat door uw project wordt gebruikt, bijwerken naar versie 0.10.0.6 of hoger. De versie van het pakket moet ook overeenkomen met de primaire versie van Storm die op HDInsight is geïnstalleerd.

| HDInsight-versie | Apache Storm versie | SCP.NET versie | Standaard Mono-versie |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0,10.0.x | 0,10.0.x | 3.2.8 |
| 3,5 | 1.0.2.x | 1.0.0.x | 4.2.1 |
| 3.6 | 1.1.0.x | 1.0.0.x | 4.2.8 |

## <a name="prerequisite"></a>Vereiste

Een Apache Storm cluster op HDInsight. Zie [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en Selecteer **Storm** voor **clustertype**.

## <a name="install-visual-studio"></a>Visual Studio installeren

U C# topologieën ontwikkelen met SCP.NET met behulp van [Visual Studio](https://visualstudio.microsoft.com/downloads/). De instructies hier maken gebruik van Visual Studio 2019, maar je ook eerdere versies van Visual Studio gebruiken.

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake-hulpprogramma's installeren voor Visual Studio

Als u Data Lake-hulpprogramma's voor Visual Studio wilt installeren, voert u de stappen uit in [Aan de slag met de tools data lake voor Visual Studio.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio)

## <a name="install-java"></a>Java installeren

Wanneer u een Storm-topologie indient vanuit Visual Studio, genereert SCP.NET een zip-bestand met de topologie en afhankelijkheden. Java wordt gebruikt om deze zip-bestanden te maken, omdat het gebruik maakt van een formaat dat meer compatibel is met Linux-gebaseerde clusters.

1. Installeer de Java Developer Kit (JDK) 7 of hoger op uw ontwikkelomgeving. U het Oracle JDK van [Oracle](https://openjdk.java.net/)krijgen. U ook [andere Java-distributies](/java/azure/jdk/)gebruiken.

2. Stel `JAVA_HOME` de omgevingsvariabele in op de map die Java bevat.

3. Stel `PATH` de omgevingsvariabele `%JAVA_HOME%\bin` in om de map op te nemen.

U de volgende C#-consoletoepassing bouwen en uitvoeren om te controleren of Java en de JDK correct zijn geïnstalleerd:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm-sjablonen

De tools data lake voor Visual Studio bieden de volgende sjablonen:

| Projecttype | Demonstreert |
| --- | --- |
| Storm toepassing |Een leeg Storm topologie project. |
| Voorbeeld van Azure SQL-schrijver bestormen |Schrijven naar Azure SQL Database. |
| Voorbeeld van Storm Azure Cosmos DB-lezer |Lezen vanuit Azure Cosmos DB. |
| Voorbeeld van Storm Azure Cosmos DB-schrijver |Schrijven naar Azure Cosmos DB. |
| Voorbeeld van GebeurtenisHub-lezer van Storm |Lezen vanuit Azure Event Hubs. |
| Voorbeeld van Storm EventHub-writer |Schrijven naar Azure Event Hubs. |
| Voorbeeld van Storm HBase Reader |Hoe te lezen van HBase op HDInsight clusters. |
| Storm HBase Writer Voorbeeld |Hoe schrijf je naar HBase op HDInsight clusters. |
| Hybride voorbeeld van Storm |Een Java-component gebruiken. |
| Stormmonster |Een basis woord telling topologie. |

> [!WARNING]  
> Niet alle sjablonen werken met HdInsight op Linux. NuGet-pakketten die door de sjablonen worden gebruikt, zijn mogelijk niet compatibel met Mono. Zie [Monocompatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/) en gebruik de [.NET Portability Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis)om potentiële problemen te identificeren.

In de stappen in dit document gebruikt u het projecttype Basisstormtoepassing om een topologie te maken.

### <a name="apache-hbase-templates"></a>Apache HBase-sjablonen

De HBase-lezer- en schrijversjablonen gebruiken de HBase REST API, niet de HBase Java API, om te communiceren met een HBase op hdinsight-cluster.

### <a name="eventhub-templates"></a>EventHub-sjablonen

> [!IMPORTANT]  
> De op Java gebaseerde EventHub-uitloopcomponent die bij de EventHub Reader-sjabloon is opgenomen, werkt mogelijk niet met Storm op HDInsight-versie 3.5 of hoger. Een bijgewerkte versie van dit onderdeel is beschikbaar op [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Zie [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)voor een voorbeeldvan topologie die deze component gebruikt en werkt met Storm op HDInsight 3.5.

## <a name="create-a-c-topology"></a>Een C#-topologie maken

Ga als lid van het c#-topologieproject in Visual Studio:

1. Open Visual Studio.

1. Selecteer **in** het venster Start de optie **Een nieuw project maken**.

1. Schuif **in het venster Een nieuw project maken** naar en kies **Stormtoepassing**en selecteer **Volgende**.

1. Voer in het **venster Uw nieuwe project configureren** een **projectnaam** van *WordCount*in, ga naar of maak een **locatiemappad** voor het project en selecteer **Vervolgens Maken**.

    ![Stormtoepassing, Dialoogvenster Uw nieuwe project configureren, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

Nadat u het project hebt gemaakt, moet u de volgende bestanden hebben:

* *Program.cs*: De topologiedefinitie voor uw project. Er wordt standaard een standaardtopologie gemaakt die bestaat uit één uitloop en één bout.

* *Spout.cs*: Een voorbeeld tuit die willekeurige getallen uitzendt.

* *Bolt.cs*: Een voorbeeldbout die een telling bijhoudt van de getallen die door de uitloop worden uitgestoten.

Wanneer u het project maakt, downloadt NuGet het nieuwste [SCP.NET-pakket.](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/)

### <a name="implement-the-spout"></a>De uitloop implementeren

Voeg vervolgens de code voor de uitloop toe, die wordt gebruikt om gegevens in een topologie uit een externe bron te lezen. Deze tuit zendt willekeurig een zin in de topologie.

1. Open *Spout.cs*. De belangrijkste componenten voor een tuit zijn:

   * `NextTuple`: Gebeld door Storm wanneer de tuit nieuwe tuples mag uitzenden.

   * `Ack`(alleen transactionele topologie): hiermee worden bevestigingen verwerkt die zijn gestart door andere componenten in de topologie voor tuples die vanuit de tuit worden verzonden. Het erkennen van een tuple laat de tuit weten dat het met succes is verwerkt door downstream componenten.

   * `Fail`(alleen transactionele topologie): verwerkt tuples die andere componenten in de topologie niet verwerken. Door een `Fail` methode te implementeren, u de tuple opnieuw uitzenden, zodat deze opnieuw kan worden verwerkt.

2. Vervang de inhoud `Spout` van de klasse door de volgende tekst:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>De bouten implementeren

Maak nu twee Storm bouten in dit voorbeeld:

1. Verwijder het bestaande *Bolt.cs* bestand uit het project.

2. Klik in **Solution Explorer**met de rechtermuisknop op het project en selecteer **Nieuw** > **item**toevoegen . Selecteer **Stormbout**in de lijst en voer *Splitter.cs* in als naam. Wijzig in de code van het nieuwe `WordCount`bestand de naamruimtenaam in . Herhaal vervolgens dit proces om een tweede bout met de naam *Counter.cs*te maken.

   * *Splitter.cs*: Implementeert een bout die zinnen in afzonderlijke woorden splitst en een nieuwe stroom woorden uitzendt.

   * *Counter.cs*: Implementeert een bout die elk woord telt en zendt een nieuwe stroom van woorden en de telling voor elk woord.

     > [!NOTE]  
     > Deze bouten lezen en schrijven naar streams, maar u ook een bout gebruiken om te communiceren met bronnen zoals een database of service.

3. Open *Splitter.cs*. Het heeft standaard slechts `Execute`één methode: . De `Execute` methode wordt aangeroepen wanneer de bout een tuple ontvangt voor verwerking. Hier u binnenkomende tuples lezen en verwerken en uitgaande tuples uitzenden.

4. Vervang de inhoud `Splitter` van de klasse door de volgende code:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Open *Counter.cs*en vervang de inhoud van de klasse door de volgende code:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>De topologie definiëren

Uitste en bouten zijn gerangschikt in een grafiek, die bepaalt hoe de gegevens stromen tussen componenten. Voor deze topologie ziet de grafiek er als volgt uit:

![Tuit- en boutcomponentdiagram, Stormtopologie](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

De uitloop zendt zinnen uit die worden verdeeld over exemplaren van de Splitter-bout. De Splitter bout breekt de zinnen in woorden, die worden verdeeld over de teller bout.

Omdat de instantie Teller het aantal woorden lokaal bevat, wilt u ervoor zorgen dat specifieke woorden naar dezelfde instantie Tegenbout stromen. Elke instantie houdt specifieke woorden bij. Aangezien de Splitter bout houdt geen staat, het maakt echt niet uit welke instantie van de splitter ontvangt welke zin.

Open *Program.cs*. De belangrijke `GetTopologyBuilder`methode is , die wordt gebruikt om de topologie die wordt voorgelegd aan Storm te definiëren. Vervang de `GetTopologyBuilder` inhoud van de volgende code om de eerder beschreven topologie te implementeren:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>De topologie indienen

U bent nu klaar om de topologie in te dienen bij uw HDInsight-cluster.

1. Navigeer naar Server Explorer **weergeven.** > **Server Explorer**

1. Klik met de rechtermuisknop op **Azure,** selecteer **Verbinding maken met Microsoft Azure-abonnement...** en voltooi het aanmeldingsproces.

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en kies Verzenden naar Storm **op HDInsight**.

1. Kies in het dialoogvenster **Topologie verzenden** onder de vervolgkeuzelijst **Stormcluster** de optie Storm op het cluster HDInsight en selecteer **Vervolgens Verzenden**. U controleren of de indiening is gelukt door het deelvenster **Uitvoer te** bekijken.

    Wanneer de topologie is ingediend, moet het venster **Stormtopologieënweergave** voor het cluster worden weergegeven. Kies de **WordCount-topologie** in de lijst om informatie over de uitvoerende topologie weer te geven.

    ![Venster over de topologie van storm, HDInsight-cluster, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > U **stormtopologieën** ook bekijken vanuit **Server Explorer.**  > Azure **Azure****HDInsight uitvouwen,** met de rechtermuisknop op een cluster Storm op HDInsight klikken en vervolgens **Stormtopologieën weergeven.**

    Als u informatie wilt weergeven over de componenten in de topologie, selecteert u een component in het diagram.

1. Selecteer in de sectie **Topologiesamenvatting** de optie **Doden** om de topologie te stoppen.

    > [!NOTE]  
    > Stormtopologieën blijven worden uitgevoerd totdat ze zijn gedeactiveerd of het cluster is verwijderd.

## <a name="transactional-topology"></a>Transactionele topologie

De vorige topologie is niet-transactioneel. De componenten in de topologie implementeren geen functionaliteit om berichten opnieuw af te spelen. Maak voor een voorbeeld van een transactionele topologie een project en selecteer **Stormsample** als projecttype.

Transactionele topologieën implementeren het volgende ter ondersteuning van het opnieuw afspelen van gegevens:

* **Metagegevens incache:** De uitloop moet metagegevens opslaan over de gegevens die worden uitgestoten, zodat de gegevens kunnen worden opgehaald en opnieuw kunnen worden uitgezonden als er een fout optreedt. Omdat de gegevens die door het voorbeeld worden uitgestraald klein zijn, worden de ruwe gegevens voor elke tuple opgeslagen in een woordenboek voor herhaling.

* **Ack**: Elke bout in `this.ctx.Ack(tuple)` de topologie kan bellen om te erkennen dat het met succes een tuple heeft verwerkt. Wanneer alle bouten de tuple `Ack` hebben erkend, wordt de methode van de tuit aangeroepen. Met `Ack` de methode kan de uitloop gegevens verwijderen die in de cache zijn opgeslagen voor herhaling.

* **Mislukt:** Elke bout `this.ctx.Fail(tuple)` kan aanroepen om aan te geven dat de verwerking is mislukt voor een tuple. De fout verspreidt `Fail` zich naar de methode van de tout, waar de tuple kan worden afgespeeld met behulp van metagegevens in de cache.

* **Sequentie-ID:** Bij het uitzenden van een tuple kan een unieke sequentie-ID worden opgegeven. Deze waarde identificeert de tuple `Fail`voor replay (en)`Ack` verwerking. De uitloop in het project **Stormsample** gebruikt bijvoorbeeld de volgende methodeaanroep bij het uitzenden van gegevens:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Deze code zendt een tuple uit die een zin bevat naar de `lastSeqId`standaardstream, met de volgorde-id-waarde in . Voor dit `lastSeqId` voorbeeld wordt verhoogd voor elke tuple die wordt uitgestoten.

Zoals aangetoond in het **Storm Sample-project,** kan worden ingesteld of een component transactioneel is op runtime, op basis van configuratie.

## <a name="hybrid-topology-with-c-and-java"></a>Hybride topologie met C# en Java

U ook Data Lake-tools voor Visual Studio gebruiken om hybride topologieën te maken, waarbij sommige componenten C# zijn en andere Java.

Maak een project en selecteer Storm Hybrid **Sample**voor een voorbeeld van een hybride topologie. Dit voorbeeldtype toont de volgende concepten aan:

* **Java tuit** en **C #** bout `HybridTopology_javaSpout_csharpBolt` : Gedefinieerd in de klasse.

  Een transactionele versie wordt `HybridTopologyTx_javaSpout_csharpBolt` gedefinieerd in de klasse.

* **C# tuit** en **Java bout** `HybridTopology_csharpSpout_javaBolt` : Gedefinieerd in de klasse.

  Een transactionele versie wordt `HybridTopologyTx_csharpSpout_javaBolt` gedefinieerd in de klasse.

  > [!NOTE]  
  > Deze versie laat ook zien hoe clojure-code uit een tekstbestand als Java-component te gebruiken.

Als u de topologie wilt schakelen die wordt `[Active(true)]` gebruikt wanneer het project wordt ingediend, verplaatst u de instructie naar de topologie die u wilt gebruiken, voordat u deze indient in het cluster.

> [!NOTE]  
> Alle Java-bestanden die nodig zijn, worden geleverd als onderdeel van dit project in de map *JavaDependency.*

Houd rekening met het volgende wanneer u een hybride topologie maakt en indient:

* Gebruik `JavaComponentConstructor` om een instantie van de Klasse Java te maken voor een uitloop of bout.

* Gebruiken `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` om gegevens te serialiseren in of uit Java-componenten van Java-objecten naar JSON.

* Wanneer u de topologie naar de server indient, moet u de optie **Extra configuraties** gebruiken om de **Java-bestandspaden**op te geven. Het opgegeven pad moet de map zijn met de JAR-bestanden die uw Java-klassen bevatten.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET versie 0.9.4.203 introduceert een nieuwe klasse en methode speciaal voor het werken met de Event Hub uitloop (een Java-tuit die leest van Event Hubs). Wanneer u een topologie maakt die een uitloop van een gebeurtenishub gebruikt (bijvoorbeeld met behulp van de voorbeeldsjabloon **Storm EventHub Reader),** gebruikt u de volgende API's:

* `EventHubSpoutConfig`klasse: Hiermee maakt u een object dat de configuratie voor de uitloopcomponent bevat.

* `TopologyBuilder.SetEventHubSpout`methode: Voegt de spuitcomponent van gebeurtenishub toe aan de topologie.

> [!NOTE]  
> U moet nog `CustomizedInteropJSONSerializer` steeds gebruik maken van de om gegevens geproduceerd door de tuit serialiseren.

## <a name="use-configurationmanager"></a>ConfigurationManager gebruiken

Gebruik **ConfigurationManager** niet om configuratiewaarden op te halen uit bout- en uitloopcomponenten. Als u dit doet, kan dit leiden tot een uitzondering op de null-aanwijzer. Geef in plaats daarvan de configuratie voor uw project door aan de topologie van Storm als een sleutel- en waardepaar in de topologiecontext. Elk onderdeel dat afhankelijk is van configuratiewaarden, moet deze ophalen uit de context tijdens de initialisatie.

In de volgende code wordt uitgelegd hoe u deze waarden ophalen:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Als u `Get` een methode gebruikt om een instantie van uw component `Context` `Dictionary<string, Object>` terug te sturen, moet u ervoor zorgen dat deze zowel de parameters als de parameters doorgeeft aan de constructor. Het volgende voorbeeld `Get` is een basismethode die deze waarden goed doorgeeft:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Hoe SCP.NET bij te werken

Recente releases van SCP.NET ondersteuningspakketupgrade via NuGet. Wanneer er een nieuwe update beschikbaar is, ontvangt u een upgrademelding. Voer de volgende stappen uit om handmatig te controleren op een upgrade:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en klik op **NuGet-pakketten beheren**.

2. Selecteer **Updates**in de package manager . Als er een update voor het SCP.NET-ondersteuningspakket beschikbaar is, wordt deze weergegeven. Selecteer **Bijwerken** voor het pakket en selecteer vervolgens in het dialoogvenster **Voorvertoningswijzigingen** de optie **OK** om het te installeren.

> [!IMPORTANT]  
> Als uw project is gemaakt met een eerdere versie van SCP.NET die NuGet niet heeft gebruikt, moet u de volgende stappen uitvoeren om te updaten naar een nieuwere versie:
>
> 1. Klik in **Solution Explorer** met de rechtermuisknop op het project en klik op **NuGet-pakketten beheren**.
> 2. Zoek het veld **Zoeken,** zoeken naar `Microsoft.SCP.Net.SDK` en vervolgens toevoegen aan het project.

## <a name="troubleshoot-common-issues-with-topologies"></a>Veelvoorkomende problemen met topologieën oplossen

### <a name="null-pointer-exceptions"></a>Uitzonderingen voor null-aanwijzer

Wanneer u een C#-topologie met een HDInsight-cluster op Basis van Linux gebruikt, kunnen bout- en uitloopcomponenten die **ConfigurationManager** gebruiken om configuratie-instellingen tijdens uitvoering te lezen, null-aanwijzeruitzonderingen retourneren.

De configuratie voor uw project wordt doorgegeven aan de Topologie van de Storm als een sleutel- en waardepaar in de topologiecontext. Het kan worden opgehaald uit het woordenboekobject dat wordt doorgegeven aan uw componenten wanneer ze zijn geïnitialiseerd.

Zie de sectie [ConfigurationManager](#use-configurationmanager) van dit document gebruiken voor meer informatie.

### <a name="systemtypeloadexception"></a>System.TypeLoadException

Wanneer u een C#-topologie gebruikt met een HDInsight-cluster op Linux, u de volgende fout tegenkomen:

`System.TypeLoadException: Failure has occurred while loading a type.`

Deze fout treedt op wanneer u een binaire gebruikt die niet compatibel is met de versie van .NET die Mono ondersteunt.

Voor HDInsight-clusters op basis van Linux moet u ervoor zorgen dat uw project binaries gebruikt die zijn samengesteld voor .NET 4.5.

### <a name="test-a-topology-locally"></a>Een topologie lokaal testen

Hoewel het eenvoudig is om een topologie in een cluster te implementeren, moet u in sommige gevallen een topologie lokaal testen. Gebruik de volgende stappen om de voorbeeldtopologie in dit artikel lokaal uit te voeren en te testen in uw ontwikkelomgeving.

> [!WARNING]  
> Lokale testen werkt alleen voor eenvoudige, C #-only topologieën. U geen lokale tests gebruiken voor hybride topologieën of topologieën die meerdere streams gebruiken.

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en selecteer **Eigenschappen**. In de projecteigenschappen. Wijzig vervolgens het **type Uitvoer** in **consoletoepassing**.

   ![HDInsight Storm-toepassing, projecteigenschappen, uitvoertype](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Vergeet niet het **type Uitvoer** terug te wijzigen in **de klassenbibliotheek** voordat u de topologie implementeert in een cluster.

1. Klik in **Solution Explorer**met de rechtermuisknop op het project en selecteer **Nieuw** > **item**toevoegen . Selecteer **Klasse**en voer *LocalTest.cs* in als de klassenaam. Selecteer tot slot **Toevoegen**.

1. Open *LocalTest.cs*en voeg `using` bovenaan de volgende instructie toe:

    ```csharp
    using Microsoft.SCP;
    ```

1. Gebruik de volgende code als `LocalTest` de inhoud van de klasse:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Neem even de tijd om de codeopmerkingen door te lezen. Deze code `LocalContext` wordt gebruikt om de componenten in de ontwikkelomgeving uit te voeren. Het blijft de gegevensstroom tussen componenten naar tekstbestanden op het lokale station.

1. Open *Program.cs*en voeg de `Main` volgende code toe aan de methode:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Sla de wijzigingen op en selecteer **Vervolgens F5** of kies**Foutopsporing voor foutopsporing debuggen voor foutopsporing** **debuggen** > om het project te starten. Er moet een consolevenster worden weergegeven en de status van logboek status worden weergegeven naarmate de tests vorderen. Wanneer `Tests finished` u wordt weergegeven, selecteert u een sleutel om het venster te sluiten.

1. Gebruik **Windows Verkenner** om de map met uw project te zoeken. (Bijvoorbeeld: *C:\\\\\<Gebruikers \\your_user_name\\>\\\\bronrepos WordCount WordCount*.) Open vervolgens in deze map *Opslaglocatie*en selecteer *Foutopsporing*. U zou de tekstdossiers moeten zien die werden geproduceerd toen de tests liepen: *zinnen.txt,* *counter.txt*, en *splitter.txt*. Open elk tekstbestand en inspecteer de gegevens.

   > [!NOTE]  
   > Tekenreeksgegevens blijven bestaan als een array met decimale waarden in deze bestanden. Bijvoorbeeld, `[[97,103,111]]` in de **splitter.txt** bestand vertegenwoordigt het woord *geleden*.

> [!NOTE]  
> Zorg ervoor dat u het **projecttype** terugzet naar **Klassebibliotheek** in de projecteigenschappen voordat u deze implementeert in een cluster Storm op HDInsight.

### <a name="log-information"></a>Logboekgegevens

U eenvoudig informatie uit uw `Context.Logger`topologiecomponenten registreren met behulp van. Met de volgende opdracht wordt bijvoorbeeld een informatieve logboekvermelding gezoend:

`Context.Logger.Info("Component started");`

Geregistreerde informatie kan worden bekeken vanuit het **Hadoop-servicelogboek,** dat te vinden is in **Server Explorer.** Vouw de vermelding voor uw cluster Storm op HDInsight uit en vouw **de Hadoop Service Log**uit. Selecteer ten slotte het logboekbestand dat u wilt weergeven.

> [!NOTE]  
> De logboeken worden opgeslagen in het Azure-opslagaccount dat door uw cluster wordt gebruikt. Als u de logboeken in Visual Studio wilt weergeven, moet u zich aanmelden bij het Azure-abonnement dat eigenaar is van het opslagaccount.

### <a name="view-error-information"></a>Foutgegevens weergeven

Als u fouten wilt weergeven die zijn opgetreden in een hardlooptopologie, voert u de volgende stappen uit:

1. Klik in **Server Explorer**met de rechtermuisknop op het cluster Storm op HDInsight en selecteer **Stormtopologieën weergeven.**

   Voor de **spout** en **bouten**bevat de kolom **Laatste fout** informatie over de laatste fout.

2. Selecteer de **spout-id** of **bout-id** voor de component met een fout die wordt weergegeven. Op de pagina details worden aanvullende foutgegevens weergegeven in de sectie **Fouten** onder aan de pagina.

3. Als u meer informatie wilt verkrijgen, selecteert u een **poort** in het gedeelte **Executors** van de pagina om het logboek van de stormwerker van de laatste paar minuten te bekijken.

### <a name="errors-submitting-topologies"></a>Fouten indienen topologieën

Als u fouten tegenkomt bij het indienen van een topologie bij HDInsight, u logboeken vinden voor de servercomponenten die topologie-indiening verwerken op uw HDInsight-cluster. Als u deze logboeken wilt downloaden, gebruikt u de volgende opdracht van een opdrachtregel:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Vervang *sshuser door* het SSH-gebruikersaccount voor het cluster. Vervang *clusternaam* door de naam van het HDInsight-cluster. Zie SSH `scp` gebruiken `ssh` [met HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor meer informatie over het gebruik en met HDInsight.

Inzendingen kunnen om meerdere redenen mislukken:

* De JDK is niet geïnstalleerd of is niet in het pad.
* Vereiste Java-afhankelijkheden zijn niet opgenomen in de indiening.
* Afhankelijkheden zijn onverenigbaar.
* Topologienamen worden gedupliceerd.

Als het *logbestand hdinsight-scpwebapi.out* een `FileNotFoundException`bestand bevat, kan de uitzondering worden veroorzaakt door de volgende voorwaarden:

* De JDK is niet in het pad op de ontwikkeling van de omgeving. Controleer of de JDK is geïnstalleerd in `%JAVA_HOME%/bin` de ontwikkelomgeving en dat is in het pad.
* Je mist een Java-afhankelijkheid. Zorg ervoor dat je alle vereiste .jar-bestanden opje hebt als onderdeel van de indiening.

## <a name="next-steps"></a>Volgende stappen

Zie Gebeurtenissen verwerken [vanuit Azure Event Hubs met Storm op HDInsight](apache-storm-develop-csharp-event-hub-topology.md)voor een voorbeeld van het verwerken van gegevens uit gebeurtenishubs.

Zie [C# Storm voorbeeld](https://github.com/Blackmist/csharp-storm-example)voor een voorbeeld van een C#-topologie die streamgegevens in meerdere streams splitst.

Zie [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)voor meer informatie over het maken van C# topologieën.

Zie de volgende documenten voor meer manieren om met HDInsight en meer Storm op HDInsight-voorbeelden te werken:

**Microsoft SCP.NET**

* [SCP-programmeergids voor Apache Storm in Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm in HDInsight**

* [Apache Storm-topologieën implementeren en beheren op Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Voorbeeld Apache Storm-topologieën in Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop op HDInsight**

* [Wat is Apache Hive en HiveQL op Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken in Apache Hadoop in HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

* [Apache HBase gebruiken in Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
