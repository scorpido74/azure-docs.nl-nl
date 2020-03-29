---
title: SCP.NET programmeerhandleiding voor Storm in Azure HDInsight
description: Meer informatie over het gebruik van SCP.NET om te maken. NET-gebaseerde Storm-topologieën voor gebruik met Storm running in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: ddf69a75a39911293277a4a4189cf4e79256e09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77186866"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP-programmeergids voor Apache Storm in Azure HDInsight

SCP is een platform voor het bouwen van realtime, betrouwbare, consistente en krachtige gegevensverwerkingstoepassingen. Het is gebouwd op de top van [Apache Storm](https://storm.incubator.apache.org/), dat is een stream-processing systeem ontworpen door open-source software gemeenschappen. Nathan Marz heeft Storm gemaakt. Het werd gepubliceerd als open source door Twitter. Storm maakt gebruik van [Apache ZooKeeper](https://zookeeper.apache.org/), dat is een ander Apache-project dat zeer betrouwbare gedistribueerde coördinatie en staatsbeheer mogelijk maakt.

Het SCP-project heeft niet alleen Storm op Windows geport, maar ook met projecttoegevoegde extensies en aanpassingen voor de Windows-omgeving. De extensies omvatten de .NET-ontwikkelaarservaring en .NET-bibliotheken. De aanpassing omvat Windows-gebaseerde implementatie.

Met de extensies en aanpassingen hoeft u de open-source softwareprojecten niet te forken. U afgeleide omgevingen gebruiken die bovenop Storm zijn gebouwd.

## <a name="processing-model"></a>Verwerkingsmodel

De gegevens in SCP worden gemodelleerd als continue stroom van tuples. Typisch, de tuples:

1. Stroom in een wachtrij.
1. Worden opgepikt en getransformeerd door bedrijfslogica gehost in een Storm topologie.
1. Ofwel hebben hun output piped als tuples naar een ander SCP-systeem of zijn toegewijd aan winkels zoals gedistribueerde bestandssystemen en databases zoals SQL Server.

![Een diagram van een wachtrij die gegevens aan verwerking voert, die op zijn beurt een gegevensarchief voedt](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

In Storm definieert een toepassingstopologie een berekeningsgrafiek. Elk knooppunt in een topologie bevat verwerkingslogica. Koppelingen tussen knooppunten geven de gegevensstroom aan.

Knooppunten die invoergegevens in de topologie injecteren, worden _tuiten_genoemd. U ze gebruiken om de gegevens te sequencen. De invoergegevens kunnen afkomstig zijn van een bron zoals bestandslogboeken, een transactionele database of een systeemprestatiemeter.

Knooppunten met zowel invoer- als uitvoergegevensstromen worden _bouten_genoemd. Ze doen de werkelijke gegevens filtering, selecties, en aggregatie.

SCP ondersteunt de beste inspanningen, ten minste één keer, en precies één keer gegevensverwerking.

In een gedistribueerde toepassing voor streamverwerking kunnen fouten optreden tijdens de gegevensverwerking. Dergelijke fouten omvatten een netwerkstoring, een machinestoring of een fout in uw code. Ten minste eenmaal verwerking zorgt ervoor dat alle gegevens ten minste eenmaal worden verwerkt door automatisch dezelfde gegevens opnieuw af te spelen wanneer er een fout optreedt.

Ten minste eenmaal verwerking is eenvoudig en betrouwbaar, en het past bij vele toepassingen. Maar wanneer een toepassing exacte telling vereist, is ten minste één keer de verwerking onvoldoende omdat dezelfde gegevens kunnen worden afgespeeld in de toepassingstopologie. In dat geval zorgt precies één keer dat de verwerking ervoor zorgt dat het resultaat correct is, zelfs wanneer de gegevens meerdere keren worden afgespeeld en verwerkt.

Scp laat .NET-ontwikkelaars realtime gegevensverwerkingstoepassingen maken terwijl ze een Java Virtual Machine (JVM) met Storm gebruiken. Een JVM en .NET communiceren via tcp lokale sockets. Elke tuit/bout is een .NET/Java-procespaar, waarbij de gebruikerslogica in een .NET-proces als plug-in wordt uitgevoerd.

Voer de volgende stappen uit om een toepassing voor gegevensverwerking boven op SCP te bouwen:

1. Ontwerpen en implementeren van touts om gegevens uit wachtrijen op te halen.
1. Ontwerp en implementeer bouten die de invoergegevens verwerken en opslaan in externe winkels zoals een database.
1. Ontwerp de topologie, stuur deze in en voer deze uit.

De topologie definieert hoekpunten en de gegevens die ertussen stromen. SCP neemt een topologiespecificatie en implementeert deze op een Stormcluster, waarbij elk hoekpunt op één logisch knooppunt wordt uitgevoerd. De stormtaakplanner zorgt voor failover en schaling.

In dit artikel worden enkele eenvoudige voorbeelden gebruikt om te doorlopen hoe u gegevensverwerkingstoepassingen bouwen met SCP.

## <a name="scp-plug-in-interface"></a>SCP-plug-in-interface

SCP-plug-ins zijn zelfstandige toepassingen. Ze kunnen tijdens de ontwikkeling in Visual Studio worden uitgevoerd en na de productie-implementatie worden aangesloten op de Storm-pijplijn.

Het schrijven van een SCP-plug-in is hetzelfde als het schrijven van een andere Windows-consoletoepassing. De SCP.NET platform verklaart een aantal interfaces voor tuit / bout. Uw plug-in code implementeert deze interfaces. Het belangrijkste doel van dit ontwerp is om u te laten focussen op uw bedrijfslogica, terwijl het SCP.NET platform andere dingen te laten afhandelen.

Uw plug-in code implementeert een van de volgende interfaces. Welke interface afhankelijk is van de vraag of de topologie transactioneel of niet-transactioneel is en of het onderdeel een uitloop of een bout is.

* **ISCPSpout**
* **ISCPBolt (ISCPBolt)**
* **ISCPTxSpout**
* **ISCPBatchBolt ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** is de gemeenschappelijke interface voor veel plug-ins. Momenteel is het een dummy-interface.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** is de interface voor een niet-transactionele uitloop.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

Wanneer **NextTuple** wordt aangeroepen, kan uw C#-code een of meer tuples uitzenden. Als er niets uit te zenden, moet deze methode terug te keren zonder iets uit te stoten.

De **NextTuple,** **Ack**, en **Fail** methoden worden allemaal genoemd in een strakke lus in een enkele thread van een C # proces. Wanneer er geen tuples uit te zenden, hebben **NextTuple** slaap voor een korte tijd als 10 milliseconden. Deze slaap helpt voorkomen dat het verspillen van CPU beschikbaarheid.

De **Methoden Ack** en **Fail** worden alleen aangeroepen wanneer een specificatiebestand het bevestigingsmechanisme inschakelt. De *parameter seqId* identificeert de tuple die wordt erkend of is mislukt. Als bevestiging is ingeschakeld in een niet-transactionele topologie, moet de volgende **functie Emit** in een tuit worden gebruikt:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Als een niet-transactionele topologie geen bevestiging ondersteunt, kunnen **Ack** en **Fail** als lege functies worden achtergelaten.

De *parameter parms-invoer* in deze functies geeft een leeg woordenboek op en is gereserveerd voor toekomstig gebruik.

### <a name="iscpbolt"></a>ISCPBolt (ISCPBolt)

**ISCPBolt** is de interface voor een niet-transactionele bout.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

Wanneer een nieuwe tuple beschikbaar is, wordt de functie **Uitvoeren** aangeroepen om deze te verwerken.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** is de interface voor een transactionele uitloop.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Net als hun niet-transactionele tegenhangers, **NextTx**, **Ack**, en **Fail** zijn allemaal genoemd in een strakke lus in een enkele thread van een C # proces. Wanneer er geen tuples uit te zenden, hebben **NextTx** slaap voor een korte tijd als 10 milliseconden. Deze slaap helpt voorkomen dat het verspillen van CPU beschikbaarheid.

Wanneer **NextTx** wordt aangeroepen om een nieuwe transactie te starten, identificeert de *seqId-uitvoerparameter* de transactie. De transactie wordt ook gebruikt in **Ack** en **Fail**. Uw **NextTx-methode** kan gegevens uitzenden naar de Java-kant. De gegevens worden opgeslagen in ZooKeeper om replay te ondersteunen. Omdat ZooKeeper een beperkte capaciteit heeft, moet uw code alleen metagegevens uitzenden en geen bulkgegevens in een transactionele uitloop.

Omdat Storm automatisch een mislukte transactie opnieuw speelt, wordt **Fail** meestal niet aangeroepen. Maar als SCP de metagegevens kan controleren die door een transactionele uitloop worden uitgezonden, kan het **Fail** aanroepen wanneer de metagegevens ongeldig zijn.

De *parameter parms-invoer* in deze functies geeft een leeg woordenboek op en is gereserveerd voor toekomstig gebruik.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt ISCPBatchBolt

**ISCPBatchBolt** is de interface voor een transactionele bout.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

De **methode Uitvoeren** wordt aangeroepen wanneer een nieuwe tuple bij de bout aankomt. De methode **FinishBatch** wordt aangeroepen wanneer deze transactie afloopt. De *parameter parms-invoer* is gereserveerd voor toekomstig gebruik.

Voor een transactionele topologie is **StormTxAttempt** een belangrijke klasse. Het heeft twee leden: **TxId** en **AttemptId**. Het **TxId-lid** identificeert een specifieke transactie. Een transactie kan meerdere keren worden geprobeerd als deze mislukt en opnieuw wordt afgespeeld.

SCP.NET maakt een nieuw **ISCPBatchBolt-object** om elk **StormTxAttempt-object** te verwerken, net als wat Storm in Java doet. Het doel van dit ontwerp is om parallelle transactieverwerking te ondersteunen. Nadat een transactiepoging is voltooid, wordt het bijbehorende **ISCPBatchBolt-object** vernietigd en wordt afval verzameld.

## <a name="object-model"></a>Objectmodel

SCP.NET biedt ook een eenvoudige set van belangrijke objecten voor ontwikkelaars om mee te programmeren. De objecten zijn **Context,** **StateStore**en **SCPRuntime**. Ze worden besproken in deze sectie.

### <a name="context"></a>Context

Het object **Context** biedt een beheeromgeving voor een toepassing. Elke **ISCPPlugin-instantie** van **ISCPSpout,** **ISCPBolt,** **ISCPTxSpout**of **ISCPBatchBolt** heeft een bijbehorende **contextinstantie.** De functionaliteit van **Context** is verdeeld in deze twee delen:

* Het statische deel, dat beschikbaar is in het hele C#-proces
* Het dynamische deel, dat alleen beschikbaar is voor de specifieke **contextinstantie**

### <a name="static-part"></a>Statisch onderdeel

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

Het **Logger-object** is bedoeld voor loggingdoeleinden.

Het **object pluginType** geeft het type plug-in van het C#-proces aan. Als het proces wordt uitgevoerd in de lokale testmodus zonder Java, wordt het type plug-in **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

De eigenschap **Config** krijgt configuratieparameters van de Java-kant, die ze passeert wanneer een C# plug-in wordt geïnitialiseerd. De **Config** parameters zijn verdeeld in twee delen: **stormConf** en **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

Het **stormConf-onderdeel** is parameters gedefinieerd door Storm en het **plug-inConf-gedeelte** is parameters die door SCP zijn gedefinieerd. Hier volgt een voorbeeld:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

Het type **TopologyContext** krijgt de topologiecontext. Het is het meest handig voor meerdere parallelle componenten. Hier volgt een voorbeeld:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dynamisch onderdeel

De volgende interfaces zijn relevant voor een bepaalde **contextinstantie,** die door het SCP.NET-platform wordt gemaakt en wordt doorgegeven aan uw code:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

Voor een niet-transactionele tuit die bevestiging ondersteunt, wordt de volgende methode verstrekt:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

Een niet-transactionele bout die bevestiging ondersteunt, moet **ack** of **fail** expliciet aanroepen met de tuple die het heeft ontvangen. Bij het uitzenden van een nieuwe tuple moet de bout ook de ankers van de tuple specificeren. De volgende methoden worden gegeven:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore (Staatswinkel)

Het **StateStore-object** biedt metadataservices, monotonische sequentiegeneratie en wachtvrije coördinatie. U gedistribueerde gelijktijdigheidsabstracties op een hoger niveau bouwen op **StateStore**. Deze abstracties omvatten gedistribueerde vergrendelingen, gedistribueerde wachtrijen, barrières en transactieservices.

SCP-toepassingen kunnen het **doel Status** gebruiken om informatie te serialiseren in [Apache ZooKeeper](https://zookeeper.apache.org/). Deze vaardigheid is vooral waardevol voor een transactionele topologie. Als een transactionele uitloop niet meer reageert en opnieuw wordt opgestart, kan **de staat** de benodigde informatie ophalen bij ZooKeeper en de pijplijn opnieuw starten.

Het **StateStore-object** heeft de volgende hoofdmethoden:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Het **staatsobject** heeft de volgende hoofdmethoden:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

Wanneer **simpleMode** is ingesteld op **true,** verwijdert de **Commit-methode** de bijbehorende ZNode in ZooKeeper. Anders verwijdert de methode de huidige ZNode en voegt\_een nieuw knooppunt toe aan het TOEGEWEZEN PAD.

### <a name="scpruntime"></a>SCPRuntime

De klasse **SCPRuntime** biedt de volgende twee methoden:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Met **de methode Initialiseren** initialiseert u de RUNTime-omgeving van SCP. In deze methode maakt het C#-proces verbinding met de Java-kant om configuratieparameters en topologiecontext te krijgen.

Met de methode **LaunchPlugin** wordt de berichtverwerkingslus gestart. In deze lus ontvangt de C# plug-in berichten van de Java-kant. Deze berichten bevatten tuples en controlesignalen. De plug-in verwerkt vervolgens de berichten, misschien door de interfacemethode van uw code aan te roepen.

De invoerparameter voor **LaunchPlugin** is een gemachtigde. De methode kan een object retourneren dat de **ISCPSpout-,** ISCPBolt,ISCPTxSpout- of **ISCPBatchBolt-interface** implementeert. **ISCPBolt** **ISCPTxSpout**

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

Voor **ISCPBatchBolt**u een **Object StormTxAttempt** uit de *parameter parms* halen en het gebruiken om te beoordelen of de poging een afgespeelde poging is. De controle op een replay poging wordt vaak gedaan op de commit bout. De HelloWorldTx voorbeeld later in dit artikel toont deze controle.

SCP-plug-ins kunnen meestal in twee modi worden uitgevoerd: de lokale testmodus en de normale modus.

#### <a name="local-test-mode"></a>Lokale testmodus

In deze modus worden de SCP-plug-ins in uw C#-code uitgevoerd in Visual Studio tijdens de ontwikkelingsfase. U de **iLocalContext-interface** in deze modus gebruiken. De interface biedt methoden om de uitgezonden tuples te serialiseren naar lokale bestanden en ze terug te lezen in RAM.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Normale modus

In deze modus worden de SCP-plug-ins uitgevoerd in het Storm Java-proces. Hier is een voorbeeld:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Taal topologiespecificatie

SCP Topology Specification is een domeinspecifieke taal (DSL) voor het beschrijven en configureren van SCP-topologieën. Het is gebaseerd op [Storm's Clojure DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) en wordt uitgebreid door SCP.

U topologiespecificaties rechtstreeks indienen bij een cluster van Storm voor uitvoering via de opdracht **runSpec.**

SCP.NET heeft de volgende functies toegevoegd om transactionele topologieën te definiëren:

| Nieuwe functie | Parameters | Beschrijving |
| --- | --- | --- |
| **tx-topolopy** |*topologie-naam*<br />*tuitkaart*<br />*boutkaart* |Definieert een transactionele topologie met de topologienaam, de definitiekaart van tuiten en de definitiekaart van bouten. |
| **scp-tx-uitloop** |*exec-naam*<br />*argumenten*<br />*Velden* |Definieert een transactionele tuit. De functie voert de toepassing uit die is opgegeven met *exec-naam* en maakt gebruik van *args.*<br /><br />De parameter *velden* geeft de uitvoervelden voor de uitloop op. |
| **scp-tx-batch-bout** |*exec-naam*<br />*argumenten*<br />*Velden* |Hiermee definieert u een transactionele batchbout. De functie voert de toepassing uit die is opgegeven met *exec-naam* en maakt gebruik van *args.*<br /><br />De parameter *velden* geeft de uitvoervelden voor de bout aan. |
| **scp-tx-commit-bolt** |*exec-naam*<br />*argumenten*<br />*Velden* |Definieert een transactionele commit bolt. De functie voert de toepassing uit die is opgegeven met *exec-naam* en maakt gebruik van *args.*<br /><br />De parameter *velden* geeft de uitvoervelden voor de bout aan. |
| **nontx-topologie** |*topologie-naam*<br />*tuitkaart*<br />*boutkaart* |Definieert een niet-transactionele topologie met de topologienaam, de definitiekaart van tuiten en de definitiekaart van bouten. |
| **scp-uitloop** |*exec-naam*<br />*argumenten*<br />*Velden*<br />*parameters* |Definieert een niet-transactionele tuit. De functie voert de toepassing uit die is opgegeven met *exec-naam* en maakt gebruik van *args.*<br /><br />De parameter *velden* geeft de uitvoervelden voor de uitloop op.<br /><br />De *parameter parameters* is optioneel. Gebruik het om parameters op te geven zoals "nontransactional.ack.enabled". |
| **scp-bout** |*exec-naam*<br />*argumenten*<br />*Velden*<br />*parameters* |Hiermee definieert u een niet-transactionele bout. De functie voert de toepassing uit die is opgegeven met *exec-naam* en maakt gebruik van *args.*<br /><br />De parameter *velden* geeft de uitvoervelden voor de bout op<br /><br />De *parameter parameters* is optioneel. Gebruik het om parameters op te geven zoals "nontransactional.ack.enabled". |

SCP.NET definieert de volgende trefwoorden:

| Trefwoord | Beschrijving |
| --- | --- |
| **:naam** |De naam van de topologie |
| **:topologie** |De topologie met behulp van de functies in de vorige tabel en ingebouwde functies |
| **:p** |De parallellisme hint voor elke tuit of bout |
| **:config** |Parameters configureren of bijwerken |
| **:schema** |Het schema van de stream |

SCP.NET definieert ook deze veelgebruikte parameters:

| Parameter | Beschrijving |
| --- | --- |
| "plugin.name" |De .exe-bestandsnaam van de C#-plug-in |
| "plugin.args" |De argumenten voor plug-in |
| "output.schema" |Het uitvoerschema |
| "nontransactional.ack.enabled" |Of bevestiging is ingeschakeld voor een niet-transactionele topologie |

De opdracht **runSpec** wordt samen met de bits geïmplementeerd. Hier is het commandogebruik:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

De *parameter resource-dir* is optioneel. Geef deze op wanneer u een C#-toepassing wilt aansluiten. De opgegeven map bevat de toepassing, afhankelijkheden en configuraties.

De *parameter classpath* is ook optioneel. Het geeft het Java-klassepad op als het specificatiebestand een Java-uitloop of bout bevat.

## <a name="miscellaneous-features"></a>Diverse functies

### <a name="input-and-output-schema-declarations"></a>Invoer- en uitvoerschemadeclaratie

Uw C#-processen kunnen tuples uitzenden. Om dit te doen, het platform serialiseert tuples in **byte[]** objecten en brengt de objecten naar de Java-kant. Storm brengt deze tuples dan over naar de doelen.

In downstream-componenten ontvangen C#-processen tuples terug van de Java-kant en converteren ze naar de oorspronkelijke typen van het platform. Al deze bewerkingen zijn verborgen door het platform.

Om serialisatie en deserialisatie te ondersteunen, moet uw code het schema van de invoer en uitvoer declareren. Het schema wordt gedefinieerd als een woordenboek. De stream-ID is de woordenboeksleutel. De belangrijkste waarde zijn de typen kolommen. Een component kan meerdere streams declareren.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

De volgende functie wordt toegevoegd aan een **object Context:**

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Ontwikkelaars moeten ervoor zorgen dat de uitgezonden tuples voldoen aan het schema gedefinieerd voor een stream. Anders zal het systeem een runtime uitzondering gooien.

### <a name="multistream-support"></a>Ondersteuning voor Multistream

SCP laat uw code uitzenden naar of ontvangen van meerdere verschillende streams op hetzelfde moment. Het object **Context** weerspiegelt deze ondersteuning als de optionele stream-ID-parameter van de **methode Emit.**

Er zijn twee methoden in het object SCP.NET **Context** toegevoegd. Ze stoten een of meer tuples uit naar specifieke streams. De parameter *streamId* is een tekenreeks. De waarde moet hetzelfde zijn in zowel C# code als de topologie-definitie specificatie.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Het uitzenden naar een niet-bestaande stream veroorzaakt runtime-uitzonderingen.

### <a name="fields-grouping"></a>Velden groeperen

De ingebouwde velden die in Storm zijn gegroepeerd, werken niet goed in SCP.NET. Aan de Java-proxykant is het gegevenstype van alle velden eigenlijk **byte[]**. De groepering velden gebruikt de hashcode van het object **Byte** om de groepering uit te brengen. De hashcode is het adres van dit object in RAM. Dus de groepering zal verkeerd zijn voor multibyte objecten die dezelfde inhoud te delen, maar niet hetzelfde adres.

SCP.NET voegt een aangepaste groeperingsmethode toe en gebruikt de inhoud van het object **byte[]** om de groepering uit te halen. In een specificatiebestand ziet de syntaxis er als volgt uit:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

In het vorige specificatiebestand:

* `scp-field-group`hiermee wordt opgegeven dat de groepering een aangepaste veldgroepering is die door SCP is geïmplementeerd.
* `:tx`of `:non-tx` geeft aan of de topologie transactioneel is. U hebt deze informatie nodig omdat de startindex verschilt tussen transactionele en niet-transactionele topologieën.
* `[0,1]`hiermee geeft u een hashset veld-id's op die met nul beginnen.

### <a name="hybrid-topology"></a>Hybride topologie

Native Storm code is geschreven in Java. SCP.NET heeft Storm verbeterd om u C# code te laten schrijven om uw bedrijfslogica te behandelen. Maar SCP.NET ondersteunt ook hybride topologieën, die niet alleen C# tuiten/bouten bevatten, maar ook Java tuiten/bouten.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Java-uitloop/bout opgeven in een specificatiebestand

U **scp-tuit** en **scp-bout** gebruiken in een specificatiebestand om Java-uitstes en bouten op te geven. Hier volgt een voorbeeld:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Hier `microsoft.scp.example.HybridTopology.Generator` is de naam van de Java tuit klasse.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Het Java-klassepad opgeven in een runSpec-opdracht

Als u topologie wilt indienen die Java-tuiten of bouten bevat, compileer ze dan eerst om JAR-bestanden te produceren. Geef vervolgens het java-klassepad op dat de JAR-bestanden bevat wanneer u topologie verzendt. Hier volgt een voorbeeld:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Hier `examples\HybridTopology\java\target\` is de map met de Java tuit / bout JAR-bestand.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisatie en deserialisatie tussen Java en C #

Een SCP-component bevat de Java-kant en de C#-kant. Om te communiceren met native Java tuiten / bouten, serialisatie en deserialisatie moet plaatsvinden tussen de Java-kant en de C # kant, zoals geïllustreerd in de volgende grafiek:

![Diagram van Java-component die naar SCP-component wordt verzonden, die vervolgens naar een andere Java-component wordt verzonden](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialisatie in de Java-kant en deserialisatie in de C # kant

Zorg eerst voor de standaardimplementatie voor serialisatie aan de Java-kant en deserialisatie in de C#-kant.

Geef de serialisatiemethode van de Java-kant op in een specificatiebestand.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Geef de deserialisatiemethode van de C#-kant op in uw C#-code.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Als het gegevenstype niet te complex is, moet deze standaardimplementatie de meeste gevallen afhandelen. Hier zijn gevallen waarin u uw eigen implementatie aansluiten:

* Uw gegevenstype is te complex voor de standaardimplementatie.
* De uitvoering van uw standaardimplementatie voldoet niet aan uw vereisten.

De serialisatie-interface in de Java-kant wordt gedefinieerd als:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

De deserialisatie-interface in de C#-kant wordt gedefinieerd als:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialisatie in de C# kant en deserialisatie in de Java-kant

Geef de serialisatiemethode van de C#-kant op in uw C#-code.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Geef de deserialisatiemethode van de Java-kant op in een specificatiebestand.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Hier `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` is de naam van de `"microsoft.scp.example.HybridTopology.Person"` deserializer en is de doelklasse waarde gegevens worden gedeserialiseerd.

U ook uw eigen implementatie van een C# serializer en een Java deserializer aansluiten.

Deze code is de interface voor de C# serializer:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Deze code is de interface voor de Java-deserializer:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>SCP-hostmodus

In deze modus u uw code compileren als een DLL en SCPHost.exe gebruiken zoals verstrekt door SCP om een topologie in te dienen. Een specificatiebestand ziet eruit als deze code:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Hier, `"plugin.name"` wordt `"SCPHost.exe"`gespecificeerd als , die wordt geleverd door de SCP SDK. SCPHost.exe accepteert drie parameters in de volgende volgorde:

1. De naam DLL, `"HelloWorld.dll"` die in dit voorbeeld staat.
1. De klassenaam, `"Scp.App.HelloWorld.Generator"` die in dit voorbeeld is.
1. De naam van een openbare statische methode, die kan worden aangeroepen om een exemplaar van **ISCPPlugin**te krijgen.

In de hostmodus compileerje je code als een DLL voor aanroep door het SCP-platform. Omdat het platform vervolgens volledige controle kan krijgen over de hele verwerkingslogica, raden we u aan topologie in te dienen in de SCP-hostmodus. Dit vereenvoudigt de ontwikkelingservaring. Het brengt u ook meer flexibiliteit en betere achterwaartse compatibiliteit voor latere releases.

## <a name="scp-programming-examples"></a>Voorbeelden van SCP-programmering

### <a name="helloworld"></a>Helloworld

Het volgende eenvoudige HelloWorld-voorbeeld toont een voorproefje van SCP.NET. Het maakt gebruik van een niet-transactionele topologie met een uitloop genaamd **generator** en twee bouten genaamd **splitter** en **teller**. De **generator** tuit willekeurig genereert zinnen en stoten deze zinnen uit om **splitter**. De **splitter** bout splitst de zinnen in woorden en zendt deze woorden aan de **tegenbout.** De **tegenbout** gebruikt een woordenboek om het optreden van elk woord vast te leggen.

Dit voorbeeld heeft twee specificatiebestanden: HelloWorld.spec en HelloWorld\_EnableAck.spec. De C#-code kan achterhalen of bevestiging `pluginConf` is ingeschakeld door het object van de Java-kant te halen.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Als bevestiging is ingeschakeld in de tuit, slaat een woordenboek de tuples in de cache die niet zijn erkend. Als `Fail` wordt aangeroepen, wordt de mislukte tuple opnieuw afgespeeld.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx HelloWorldTx

In het volgende HelloWorldTx-voorbeeld wordt uitgelegd hoe transactionele topologie kan worden geïmplementeerd. Het voorbeeld heeft een uitloop genaamd **generator,** een batch bout genaamd **partiële telling**, en een commit bout genaamd **count-sum**. Het voorbeeld bevat ook drie bestaande tekstbestanden: DataSource0.txt, DataSource1.txt en DataSource2.txt.

In elke transactie selecteert de **generator** willekeurig twee bestanden uit de bestaande drie bestanden en zendt de twee bestandsnamen uit aan de **bout voor het aantal gedeeltelijk telling.** De **gedeeltelijke telling** bout:

1. Krijgt een bestandsnaam van de ontvangen tuple.
1. Hiermee opent u het bijbehorende bestand.
1. Telt het aantal woorden in het bestand.
1. Zendt het aantal woorden uit aan de **tel-som** bout.

De **tel-som** bout vat het totale aantal samen.

Om precies één keer semantiek te bereiken, moet de **count-sum** commit bolt beoordelen of het een overgespeelde transactie is. In dit voorbeeld heeft het de volgende statische lidvariabele:

```csharp
public static long lastCommittedTxId = -1; 
```

Wanneer een **ISCPBatchBolt-instantie** wordt gemaakt, `txAttempt` wordt de waarde van het object opgehaald van invoerparameters.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

Wanneer `FinishBatch` wordt `lastCommittedTxId` aangeroepen, wordt bijgewerkt als het geen opnieuw afgespeelde transactie is.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopologie

Deze topologie bevat een Java tuit en een C # bout. Het maakt gebruik van de standaard serialisatie en deserialisatie implementatie die door het SCP-platform. Zie het bestand HybridTopology.spec\\in de map HybridTopology voor de details van het specificatiebestand. Zie ook SubmitTopology.bat voor het opgeven van het Java-klassepad.

### <a name="scphostdemo"></a>SCPHostDemo (SCPHostDemo)

Dit voorbeeld is in wezen hetzelfde als HelloWorld. Het enige verschil is dat uw code wordt gecompileerd als een DLL en de topologie wordt ingediend met behulp van SCPHost.exe. Zie de sectie SCP-hostmodus voor een meer gedetailleerde uitleg.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor voorbeelden van Apache Storm-topologieën die met SCP zijn gemaakt:

* [Ontwikkel C# topologieën voor Apache Storm op HDInsight met behulp van Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Gebeurtenissen uit Azure Event Hubs verwerken met Apache Storm op HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Voertuigsensorgegevens van Event Hubs verwerken met Apache Storm op HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL extraheren, transformeren en laden (ETL) van Azure Event Hubs naar Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
