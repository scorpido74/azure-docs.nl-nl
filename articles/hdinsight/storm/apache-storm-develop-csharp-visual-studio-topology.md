---
title: Apache Storm topologieën met Visual Studio C# en-Azure HDInsight
description: Meer informatie over het maken van Storm C#-topologieën in. Maak een eenvoudige topologie voor het tellen van woorden in Visual Studio met behulp van de Hadoop-hulpprogram ma's voor Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 11/27/2017
ROBOTS: NOINDEX
ms.openlocfilehash: e6f6ba131a4fb5dd31f113afd2b6de2d65aeaea0
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915169"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Topologieën ontwikkelen C# voor Apache Storm met behulp van de data Lake-hulpprogram Ma's voor Visual Studio

Meer informatie over het maken C# van een Apache Storm topologie met behulp van de Azure data Lake-hulpprogram ma's (Apache Hadoop) voor Visual Studio. Dit document gaat over het proces van het maken van een storm-project in Visual Studio, het lokaal testen en het implementeren van een Apache Storm op een Azure HDInsight-cluster.

U leert ook hoe u hybride topologieën maakt die C# gebruikmaken van en Java-onderdelen.

> [!NOTE]  
> Hoewel de stappen in dit document afhankelijk zijn van een Windows-ontwikkel omgeving met Visual Studio, kan het gecompileerde project worden verzonden naar een op Linux of Windows gebaseerd HDInsight-cluster. Alleen op Linux gebaseerde clusters die zijn gemaakt na 28 oktober 2016, ondersteunen SCP.NET-topologieën.

Als u een C# topologie met een op Linux gebaseerd cluster wilt gebruiken, moet u het micro soft. scp. net. SDK NuGet-pakket bijwerken dat door uw project wordt gebruikt voor versie 0.10.0.6 of hoger. De versie van het pakket moet ook overeenkomen met de primaire versie van Storm die op HDInsight is geïnstalleerd.

| HDInsight-versie | Apache Storm versie | SCP.NET-versie | Standaard-mono-versie |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.3 |0,10. x |0,10. x. x</br>(alleen op HDInsight op basis van Windows) | N.v.t. |
| 3.4 | 0.10.0. x | 0.10.0. x | 3.2.8 |
| 3,5 | 1.0.2. x | 1.0.0. x | 4.2.1 |
| 3.6 | 1.1.0. x | 1.0.0. x | 4.2.8 |

> [!IMPORTANT]  
> C#-topologieën met op Linux gebaseerde clusters moeten .NET 4.5 en Mono gebruiken om op het HDInsight-cluster te worden uitgevoerd. Controleer de [compatibiliteit van mono](https://www.mono-project.com/docs/about-mono/compatibility/) voor mogelijke incompatibiliteits problemen.

## <a name="install-visual-studio"></a>Visual Studio installeren

U kunt topologieën ontwikkelen C# met SCP.net met behulp van een van de volgende versies van Visual Studio:

* Visual Studio 2012 met update 4

* Visual Studio 2013 met update 4 of [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)

* Visual Studio 2015 of [Visual studio 2015 Community](https://go.microsoft.com/fwlink/?LinkId=532606)

* Visual Studio 2017 (alle edities)

## <a name="install-data-lake-tools-for-visual-studio"></a>Data Lake-hulpprogram ma's voor Visual Studio installeren

Als u Data Lake-hulpprogram ma's voor Visual Studio wilt installeren, volgt u de stappen in [aan de slag met data Lake-hulpprogram ma's voor Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="install-java"></a>Java installeren

Wanneer u een storm-topologie vanuit Visual Studio verzendt, genereert SCP.NET een zip-bestand dat de topologie en de afhankelijkheden bevat. Java wordt gebruikt om deze zip-bestanden te maken, omdat er een indeling wordt gebruikt die compatibel is met op Linux gebaseerde clusters.

1. Installeer de Java Developer Kit (JDK) 7 of hoger in uw ontwikkel omgeving. U kunt de Oracle-JDK ophalen van [Oracle](https://aka.ms/azure-jdks). U kunt ook [andere Java-distributies](https://openjdk.java.net/)gebruiken.

2. De `JAVA_HOME` omgevings variabele moet verwijzen naar de map die Java bevat.

3. De `PATH` omgevings variabele moet de `%JAVA_HOME%\bin` Directory bevatten.

U kunt de volgende C# console toepassing gebruiken om te controleren of Java en de JDK correct zijn geïnstalleerd:

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

## <a name="apache-storm-templates"></a>Apache Storm sjablonen

De Data Lake-hulpprogram ma's voor Visual Studio bieden de volgende sjablonen:

| Projecttype | Laat zien |
| --- | --- |
| Storm-toepassing |Een leeg Storm-topologie project. |
| Voor beeld van Storm voor Azure SQL Writer |Schrijven naar Azure SQL Database. |
| Voor beeld van Storm Azure Cosmos DB Reader |Lezen uit Azure Cosmos DB. |
| Voor beeld Storm Azure Cosmos DB Writer |Schrijven naar Azure Cosmos DB. |
| Voor beeld Storm EventHub Reader |Lezen van Azure Event Hubs. |
| Voor beeld Storm EventHub Writer |Schrijven naar Azure Event Hubs. |
| Voor beeld Storm HBase Reader |Lezen van HBase in HDInsight-clusters. |
| Voor beeld Storm HBase Writer |Schrijven naar HBase in HDInsight-clusters. |
| Storm hybride-voor beeld |Een Java-onderdeel gebruiken. |
| Storm-voor beeld |Een eenvoudige topologie voor het tellen van woorden. |

> [!WARNING]  
> Niet alle sjablonen werken met HDInsight op basis van Linux. NuGet pakketten die door de sjablonen worden gebruikt, zijn mogelijk niet compatibel met mono. Controleer het document met [zwartwit compatibiliteit](https://www.mono-project.com/docs/about-mono/compatibility/) en gebruik de [.net-portabiliteit Analyzer](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis) om mogelijke problemen te identificeren.

In de stappen in dit document gebruikt u het Basic Storm-toepassings project type om een topologie te maken.

### <a name="apache-hbase-templates-notes"></a>Info over Apache HBase-sjablonen

De HBase Reader-en Writer-sjablonen gebruiken de HBase REST API, niet de HBase Java API, om te communiceren met een HBase in HDInsight-cluster.

### <a name="eventhub-templates-notes"></a>Opmerkingen bij EventHub-sjablonen

> [!IMPORTANT]  
> Het op Java gebaseerde EventHub Spout-onderdeel dat is opgenomen in de EventHub Reader-sjabloon, werkt mogelijk niet met storm in HDInsight-versie 3,5 of hoger. Een bijgewerkte versie van dit onderdeel is beschikbaar op [github](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Zie [github](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub)voor een voorbeeld topologie die gebruikmaakt van dit onderdeel en werkt met Storm op HDInsight 3,5.

## <a name="create-a-c-topology"></a>Een C# topologie maken

1. Open Visual Studio, selecteer **bestand** > **Nieuw**en selecteer vervolgens **project**.

2. Vouw in het venster **Nieuw project** de optie **geïnstalleerde** > **sjablonen**uit en selecteer **Azure data Lake**. Selecteer in de lijst met sjablonen **Storm-toepassing**. Voer onder aan het scherm **WordCount** in als de naam van de toepassing.

    ![Scherm afbeelding van het nieuwe project venster](./media/apache-storm-develop-csharp-visual-studio-topology/new-project.png)

3. Nadat u het project hebt gemaakt, hebt u de volgende bestanden nodig:

   * **Program.cs**: Dit bestand definieert de topologie voor uw project. Een standaard topologie die bestaat uit één Spout en één schicht, wordt standaard gemaakt.

   * **Spout.cs**: Een voor beeld van een Spout die wille keurige getallen verzendt.

   * **Bolt.cs**: Een voor beeld van een flits waarmee het aantal getallen wordt bijgehouden dat door de Spout wordt gegenereerd.

     Wanneer u het project maakt, downloadt NuGet het meest recente [SCP.net-pakket](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

     [!INCLUDE [scp.net version important](../../../includes/hdinsight-storm-scpdotnet-version.md)]

### <a name="implement-the-spout"></a>De Spout implementeren

1. Open **Spout.cs**. Spouts worden gebruikt om gegevens in een topologie te lezen vanuit een externe bron. De belangrijkste onderdelen voor een Spout zijn:

   * **NextTuple**: Aangeroepen door storm wanneer de Spout nieuwe Tuples mag verzenden.

   * **ACK** (alleen transactionele topologie): Hiermee worden bevestigingen verwerkt door andere onderdelen in de topologie voor Tuples die vanuit de Spout zijn verzonden. Door een tuple te bevestigen, kan de Spout weten dat deze is verwerkt door downstream-onderdelen.

   * **Mislukt** (alleen transactionele topologie): Hiermee worden Tuples verwerkt die andere onderdelen in de topologie niet verwerken. Als u een fout methode implementeert, kunt u de tuple opnieuw verzenden zodat deze opnieuw kan worden verwerkt.

2. Vervang de inhoud van de klasse **Spout** door de volgende tekst: Deze Spout verzendt een zin wille keurig in de topologie.

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

1. Verwijder het bestaande **Bolt.cs** -bestand uit het project.

2. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer**Nieuw item** **toevoegen** > . Selecteer in de lijst **Storm bout**en voer **splitter.cs** in als de naam. Herhaal dit proces om een tweede Schicht te maken met de naam **Counter.cs**.

   * **Splitter.cs**: Hiermee wordt een Schicht geïmplementeerd waarmee zinnen in afzonderlijke woorden worden gesplitst en een nieuwe stroom van woorden wordt uitgesplitst.

   * **Counter.cs**: Implementeert een Schicht die elk woord telt en een nieuwe stroom van woorden en het aantal voor elk woord verzendt.

     > [!NOTE]  
     > Deze schichten lezen en schrijven naar stromen, maar u kunt ook een Schicht gebruiken om te communiceren met bronnen zoals een Data Base of service.

3. Open **splitter.cs**. Er wordt standaard slechts één methode gebruikt: **Uitvoeren**. De methode Execute wordt aangeroepen wanneer de schicht een tuple voor verwerking ontvangt. Hier kunt u binnenkomende Tuples lezen en verwerken en uitgaande Tuples verzenden.

4. Vervang de inhoud van de **Splits** klasse door de volgende code:

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

5. Open **Counter.cs**en vervang de inhoud van de klasse door de volgende code:

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

Spouts en schichten worden gerangschikt in een grafiek, waarmee wordt gedefinieerd hoe de gegevens stromen tussen onderdelen. Voor deze topologie is de grafiek als volgt:

![Diagram van de rang schikking van onderdelen](./media/apache-storm-develop-csharp-visual-studio-topology/wordcount-topology.png)

Zinnen worden verzonden vanaf de Spout en worden gedistribueerd naar instanties van de Splits schicht. De Splits Schicht breekt de zinnen op in woorden, die worden gedistribueerd naar de Counter-bout.

Omdat het aantal woorden lokaal in het teller-exemplaar wordt bewaard, wilt u er zeker van zijn dat de specifieke tekst naar hetzelfde exemplaar van Counters Schicht stroomt. Elk exemplaar houdt specifieke woorden bij. Omdat de Splits Schicht geen status heeft, is het niet belang rijk welk exemplaar van de Splitser de zin niet ontvangt.

Open **Program.cs**. De belang rijke methode is **GetTopologyBuilder**, die wordt gebruikt voor het definiëren van de topologie die wordt verzonden naar Storm. Vervang de inhoud van **GetTopologyBuilder** door de volgende code om de topologie te implementeren die eerder is beschreven:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder("WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

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

## <a name="submit-the-topology"></a>De topologie verzenden

1. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer **verzenden naar Storm op HDInsight**.

   > [!NOTE]  
   > Voer de referenties voor uw Azure-abonnement in als u hierom wordt gevraagd. Als u meer dan één abonnement hebt, meldt u zich aan bij de versie met uw Storm op HDInsight-cluster.

2. Selecteer uw Storm op HDInsight-cluster in de vervolg keuzelijst **Storm-cluster** en selecteer vervolgens **verzenden**. U kunt controleren of de verzen ding is geslaagd met behulp van het **uitvoer** venster.

3. Wanneer de topologie is verzonden, moeten de **Storm-topologieën** voor het cluster worden weer gegeven. Selecteer de **WordCount** -topologie in de lijst om informatie over de actieve topologie weer te geven.

   > [!NOTE]  
   > U kunt Storm- **topologieën** ook bekijken vanuit **Server Explorer**. Vouw **Azure** > **HDInsight**uit, klik met de rechter muisknop op een storm op HDInsight-cluster en selecteer vervolgens **Storm-topologieën weer geven**.

    Als u informatie wilt weer geven over de onderdelen in de topologie, dubbelklikt u op het onderdeel in het diagram.

4. Klik in de weer gave **topologie overzicht** op **Kill** om de topologie te stoppen.

   > [!NOTE]  
   > Storm-topologieën blijven actief totdat ze worden gedeactiveerd of het cluster wordt verwijderd.

## <a name="transactional-topology"></a>Transactionele topologie

De vorige topologie is niet transactioneel. De onderdelen in de topologie implementeren geen functionaliteit om berichten opnieuw af te spelen. Voor een voor beeld van een transactionele topologie maakt u een project en selecteert u **Storm sample** als het project type.

Transactionele topologieën implementeren het volgende om het opnieuw afspelen van gegevens te ondersteunen:

* **Meta gegevens in cache opslaan**: De Spout moet meta gegevens over de verzonden gegevens opslaan, zodat de gegevens kunnen worden opgehaald en opnieuw worden verzonden als er een fout optreedt. Omdat de gegevens die door het voor beeld worden verzonden, klein zijn, worden de onbewerkte gegevens voor elke tuple opgeslagen in een woorden lijst voor opnieuw afspelen.

* **ACK**: Elke Schicht in de topologie kan worden `this.ctx.Ack(tuple)` opgeroepen om te bevestigen dat een tuple is verwerkt. Wanneer alle schichten de tuple hebben bevestigd, wordt `Ack` de methode van de Spout aangeroepen. Met `Ack` de-methode kan de Spout gegevens verwijderen die in de cache zijn opgeslagen voor opnieuw afspelen.

* **Mislukt**: Elke Schicht kan aanroepen `this.ctx.Fail(tuple)` om aan te geven dat de verwerking voor een tuple is mislukt. De fout wordt door gegeven aan `Fail` de methode van de Spout, waarbij de tuple opnieuw kan worden afgespeeld met behulp van meta gegevens in de cache.

* **Reeks-id**: Wanneer een tuple wordt verzonden, kan een unieke reeks-ID worden opgegeven. Met deze waarde wordt de tuple geïdentificeerd voor de verwerking van replay (ACK en fail). De spout in het **Storm-voorbeeld** project maakt bijvoorbeeld gebruik van het volgende bij het verzenden van gegevens:

        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);

    Met deze code wordt een tuple verzonden die een zin bevat voor de standaard stroom, met de reeks-ID-waarde in **lastSeqId**. Voor dit voor beeld wordt **lastSeqId** verhoogd voor elke tupel die wordt verzonden.

Zoals gedemonstreerd in het **Storm-voorbeeld** project, of een onderdeel transactioneel kan worden ingesteld tijdens runtime, op basis van de configuratie.

## <a name="hybrid-topology-with-c-and-java"></a>Hybride topologie met C# en Java

U kunt ook Data Lake-hulpprogram ma's voor Visual Studio gebruiken om hybride topologieën te maken, C# waarbij sommige onderdelen zijn en andere Java zijn.

Voor een voor beeld van een hybride topologie maakt u een project en selecteert u **Storm hybride voor beeld**. In dit voorbeeld type worden de volgende concepten gedemonstreerd:

* **Java-Spout** en  **C# -flits**: Gedefinieerd in **HybridTopology_javaSpout_csharpBolt**.

    * Er wordt een transactionele versie gedefinieerd in **HybridTopologyTx_javaSpout_csharpBolt**.

* Spout en **Java-flits**: **C#** Gedefinieerd in **HybridTopology_csharpSpout_javaBolt**.

    * Er wordt een transactionele versie gedefinieerd in **HybridTopologyTx_csharpSpout_javaBolt**.

  > [!NOTE]  
  > In deze versie wordt ook gedemonstreerd hoe u clojure code uit een tekst bestand kunt gebruiken als Java-onderdeel.


Als u wilt overschakelen naar de topologie die wordt gebruikt wanneer het project wordt verzonden `[Active(true)]` , moet u de instructie verplaatsen naar de topologie die u wilt gebruiken, voordat u deze naar het cluster verzendt.

> [!NOTE]  
> Alle Java-bestanden die zijn vereist, worden in de map **JavaDependency** als onderdeel van dit project verstrekt.

Houd rekening met het volgende bij het maken en verzenden van een hybride topologie:

* Gebruik **JavaComponentConstructor** om een exemplaar van de Java-klasse voor een Spout of bout te maken.

* Gebruik **micro soft. scp. Storm. multilang. CustomizedInteropJSONSerializer** om gegevens van Java-objecten naar JSON te serialiseren of te gebruiken.

* Wanneer u de topologie verzendt naar de-server, moet u de optie **extra configuraties** gebruiken om de **Java-bestands paden**op te geven. Het opgegeven pad moet de map zijn met het JAR-bestand dat uw Java-klassen bevat.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET versie 0.9.4.203 introduceert een nieuwe klasse en methode voor het werken met de Event hub Spout (een Java-Spout die van Event Hubs wordt gelezen). Wanneer u een topologie maakt die gebruikmaakt van een event hub-Spout, gebruikt u de volgende methoden:

* Klasse **EventHubSpoutConfig** : Hiermee maakt u een-object dat de configuratie voor het onderdeel Spout bevat.

* Methode **TopologyBuilder. SetEventHubSpout** : Hiermee voegt u het onderdeel Event hub Spout toe aan de topologie.

> [!NOTE]  
> U moet nog steeds de **CustomizedInteropJSONSerializer** gebruiken om gegevens te serialiseren die zijn geproduceerd door de Spout.

## <a id="configurationmanager"></a>ConfigurationManager gebruiken

Gebruik **ConfigurationManager** niet om configuratie waarden van de onderdelen bout en Spout op te halen. Dit kan een null-wijzer uitzondering veroorzaken. In plaats daarvan wordt de configuratie voor uw project door gegeven aan de Storm-topologie als een sleutel-en waarde-paar in de topologie context. Elk onderdeel dat afhankelijk is van configuratie waarden, moet deze ophalen uit de context tijdens de initialisatie.

De volgende code laat zien hoe u deze waarden ophaalt:

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
            this.configuration = parms[Constants.USER_CONFIG] as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Als u een `Get` -methode gebruikt om een exemplaar van uw onderdeel te retour neren, moet u ervoor zorgen dat `Context` de `Dictionary<string, Object>` para meters en worden door gegeven aan de constructor. Het volgende voor beeld is een `Get` basis methode waarmee deze waarden correct worden door gegeven:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>SCP.NET bijwerken

Recente releases van de SCP.NET-ondersteunings pakket upgrade via NuGet. Wanneer een nieuwe update beschikbaar is, ontvangt u een upgrade melding. Voer de volgende stappen uit om hand matig te controleren op een upgrade:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en klik op **NuGet-pakketten beheren**.

2. Selecteer **updates**in Package Manager. Als er een update beschikbaar is, wordt deze weer gegeven. Klik op **bijwerken** voor het pakket om het te installeren.

> [!IMPORTANT]  
> Als uw project is gemaakt met een eerdere versie van SCP.NET die geen NuGet gebruikt, moet u de volgende stappen uitvoeren om naar een nieuwere versie bij te werken:
>
> 1. Klik in **Solution Explorer** met de rechtermuisknop op het project en klik op **NuGet-pakketten beheren**.
> 2. Zoek met behulp van het veld **zoeken** naar en voeg **micro soft. scp. net. SDK** toe aan het project.

## <a name="troubleshoot-common-issues-with-topologies"></a>Veelvoorkomende problemen met topologieën oplossen

### <a name="null-pointer-exceptions"></a>Null-aanwijzer uitzonderingen

Wanneer u een C# topologie gebruikt met een HDInsight-cluster op basis van Linux, worden de Spout-onderdelen die gebruikmaken van **ConfigurationManager** om configuratie-instellingen tijdens runtime te lezen, mogelijk null-pointer-uitzonde ringen retour neren.

De configuratie voor uw project wordt door gegeven aan de Storm-topologie als een sleutel-en waarde-paar in de topologie context. Het kan worden opgehaald uit het Dictionary-object dat wordt door gegeven aan uw onderdelen wanneer ze worden geïnitialiseerd.

Zie de sectie [ConfigurationManager](#configurationmanager) van dit document voor meer informatie.

### <a name="systemtypeloadexception"></a>System. TypeLoadException

Wanneer u een C# topologie met een HDInsight-cluster op basis van Linux gebruikt, kan de volgende fout optreden:

    System.TypeLoadException: Failure has occurred while loading a type.

Deze fout treedt op wanneer u een binair element gebruikt dat niet compatibel is met de versie van .NET die door mono wordt ondersteund.

Voor HDInsight-clusters op basis van Linux moet u ervoor zorgen dat uw project binaire bestanden gebruikt die zijn gecompileerd voor .NET 4,5.

### <a name="test-a-topology-locally"></a>Een topologie lokaal testen

Hoewel het eenvoudig is om een topologie te implementeren in een cluster, moet u in sommige gevallen een topologie lokaal testen. Gebruik de volgende stappen om de voorbeeld topologie in dit artikel lokaal in uw ontwikkel omgeving uit te voeren en te testen.

> [!WARNING]  
> Lokale tests werken alleen voor basis- C#,-alleen-topologieën. U kunt geen lokale tests gebruiken voor hybride topologieën of topologieën die gebruikmaken van meerdere streams.

1. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer **Eigenschappen**. Wijzig het **uitvoer type** in de project eigenschappen in **console toepassing**.

    ![Scherm opname van project eigenschappen, waarbij het uitvoer type is gemarkeerd](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Vergeet niet om het **uitvoer type** terug te wijzigen in **Class Library** voordat u de topologie op een cluster implementeert.

2. Klik in **Solution Explorer**met de rechter muisknop op het project en selecteer vervolgens**Nieuw item** **toevoegen** > . Selecteer **klasse**en voer **LocalTest.cs** in als de naam van de klasse. Klik ten slotte op **toevoegen**.

3. Open **LocalTest.cs**en voeg het volgende toe **met behulp** van de instructie bovenaan:

    ```csharp
    using Microsoft.SCP;
    ```

4. Gebruik de volgende code als de inhoud van de klasse **LocalTest** :

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

    Neem even de tijd om de opmerkingen van de code te lezen. Deze code maakt gebruik van **LocalContext** voor het uitvoeren van de onderdelen in de ontwikkel omgeving en de gegevens stroom tussen onderdelen naar tekst bestanden op het lokale station.

1. Open **Program.cs**en voeg het volgende toe aan de methode **Main** :

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

2. Sla de wijzigingen op en klik vervolgens op **F5** of **Selecteer** > fout**opsporing starten** om het project te starten. Er moet een console venster worden weer gegeven en de status van het logboek registreren als de tests worden uitgevoerd. Wanneer de **tests zijn voltooid** , drukt u op een wille keurige toets om het venster te sluiten.

3. Gebruik **Windows Verkenner** om de map te zoeken die het project bevat. Bijvoorbeeld: **C:\Users\<your_user_name>\Documents\Visual Studio 2013\Projects\WordCount\WordCount**. Open **bin**deze map en klik vervolgens op **fouten opsporen**. U moet de tekst bestanden zien die zijn gemaakt toen de tests werden uitgevoerd: zinnen. txt, Counter. txt en splitter. txt. Open elk tekst bestand en controleer de gegevens.

   > [!NOTE]  
   > Teken reeks gegevens blijven bestaan als een matrix met decimale waarden in deze bestanden. Bijvoorbeeld: \[[97.103.111]] in het bestand **Splitt. txt** is het woord *en*.

> [!NOTE]  
> Zorg ervoor dat u het **project type** weer instelt op **Class Library** voordat u implementeert in een storm op HDInsight-cluster.

### <a name="log-information"></a>Logboek gegevens

U kunt eenvoudig gegevens uit uw topologie onderdelen vastleggen met behulp `Context.Logger`van. Met de volgende opdracht wordt bijvoorbeeld een informatie logboek vermelding gemaakt:

```csharp
Context.Logger.Info("Component started");
```

Geregistreerde gegevens kunnen worden bekeken in het **logboek**van de Hadoop-service, dat is te vinden in **Server Explorer**. Vouw de vermelding voor uw Storm in HDInsight-cluster uit en vouw vervolgens het **Hadoop-service logboek**uit. Selecteer ten slotte het logboek bestand dat u wilt weer geven.

> [!NOTE]  
> De logboeken worden opgeslagen in het Azure-opslag account dat door uw cluster wordt gebruikt. Als u de logboeken in Visual Studio wilt weer geven, moet u zich aanmelden bij het Azure-abonnement dat eigenaar is van het opslag account.

### <a name="view-error-information"></a>Fout gegevens weer geven

Voer de volgende stappen uit om fouten te bekijken die zich hebben voorgedaan in een actieve topologie:

1. Klik vanuit **Server Explorer**met de rechter muisknop op de storm in HDInsight-cluster en selecteer **Storm-topologieën weer geven**.

2. Voor de **Spout** en- **bouten**bevat de **laatste fout** kolom informatie over de laatste fout.

3. Selecteer de **Spout-id** of de **Schicht-id** voor het onderdeel waarvoor een fout wordt weer gegeven. Op de pagina Details die wordt weer gegeven, wordt aanvullende fout informatie weer gegeven in de sectie **fouten** onder aan de pagina.

4. Als u meer informatie wilt verkrijgen, selecteert u een **poort** in het gedeelte **uitvoerendeers** van de pagina om het Storm-werk logboek voor de laatste paar minuten te bekijken.

### <a name="errors-submitting-topologies"></a>Fouten bij het verzenden van topologieën

Als er fouten optreden bij het verzenden van een topologie aan HDInsight, kunt u Logboeken vinden voor de Server onderdelen die de verzen ding van de topologie op uw HDInsight-cluster verwerken. Als u deze logboeken wilt ophalen, gebruikt u de volgende opdracht vanaf een opdracht regel:

    scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .

Vervang __sshuser__ door het SSH-gebruikers account voor het cluster. Vervang __clustername__ door de naam van het HDInsight-cluster. Zie [SSH gebruiken met hdinsight](../hdinsight-hadoop-linux-use-ssh-unix.md)voor `ssh` meer informatie over het gebruik `scp` van en met hdinsight.

Inzendingen kunnen om verschillende redenen mislukken:

* JDK is niet geïnstalleerd of bevindt zich niet in het pad.
* Vereiste Java-afhankelijkheden worden niet opgenomen in de verzen ding.
* Incompatibele afhankelijkheden.
* Dubbele topologie namen.

Als het `hdinsight-scpwebapi.out` logboek een `FileNotFoundException`bevat, kan dit worden veroorzaakt door de volgende voor waarden:

* De JDK bevindt zich niet in het pad van de ontwikkel omgeving. Controleer of de jdk in de ontwikkel omgeving is geïnstalleerd en of deze `%JAVA_HOME%/bin` zich in het pad bevindt.
* Er ontbreekt een Java-afhankelijkheid. Zorg ervoor dat u alle vereiste. jar-bestanden opneemt als onderdeel van de verzen ding.

## <a name="next-steps"></a>Volgende stappen

Zie [gebeurtenissen van Azure Event hubs verwerken met Storm op HDInsight](apache-storm-develop-csharp-event-hub-topology.md)voor een voor beeld van het verwerken van gegevens uit Event hubs.

[Zie C# Storm voor beeld](https://github.com/Blackmist/csharp-storm-example)voor C# een voor beeld van een topologie waarmee gegevens van streams in meerdere streams worden gesplitst.

Zie github voor meer informatie over C# het maken van [](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md)topologieën.

Raadpleeg de volgende documenten voor meer manieren om te werken met HDInsight en meer Storm over HDInsight-voor beelden:

**Microsoft SCP.NET**

* [SCP-programmeer gids](apache-storm-scp-programming-guide.md)

**Apache Storm op HDInsight**

* [Topologieën implementeren en bewaken met Apache Storm op HDInsight](apache-storm-deploy-monitor-topology.md)
* [Example Storm toplogies and components for Apache Storm on HDInsight](apache-storm-example-topology.md) (Voorbeelden van Storm-topologieën en -onderdelen in HDInsight)

**Apache Hadoop op HDInsight**

* [Apache Hive gebruiken met Apache Hadoop op HDInsight](../hadoop/hdinsight-use-hive.md)
* [Apache Pig gebruiken met Apache Hadoop op HDInsight](../hadoop/hdinsight-use-pig.md)
* [Apache Hadoop MapReduce gebruiken met Apache Hadoop op HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase in HDInsight**

* [Aan de slag met Apache HBase in HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
