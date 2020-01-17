---
title: SCP.NET-programmeer gids voor Storm in azure HDInsight
description: Meer informatie over het gebruik van SCP.NET om te maken. Op NET gebaseerde Storm-topologieën voor gebruik met storm in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: f462fd88acf04fc8dced3db739a555c371c184ab
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154479"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>SCP-programmeer gids voor Apache Storm in azure HDInsight

SCP is een platform voor het bouwen van real-time, betrouw bare, consistente en hoogwaardige toepassingen voor gegevens verwerking. Het is gebouwd op [Apache Storm](https://storm.incubator.apache.org/) : een gegevensstroom verwerkings systeem dat door de OSS-community's is ontworpen. Storm is ontworpen door Nathan Marz en is geopend door Twitter. Het maakt gebruik van [Apache ZooKeeper](https://zookeeper.apache.org/), een ander Apache-project om zeer betrouw bare gedistribueerde coördinatie en status beheer mogelijk te maken.

Niet alleen het SCP-project heeft Storm over Windows, maar ook het project heeft uitbrei dingen en aanpassingen voor het Windows-ecosysteem toegevoegd. De uitbrei dingen bevatten .NET-ontwikkel aars en-bibliotheken. de aanpassing omvat Windows-implementatie.

De uitbrei ding en aanpassing geschieden op een zodanige manier dat we de OSS-projecten niet hoeven te splitsen en we kunnen afgeleide ecosystemen gebruiken die bovenop Storm zijn gebouwd.

## <a name="processing-model"></a>Verwerkings model

De gegevens in SCP zijn gemodelleerd als continue streams van Tuples. Doorgaans worden de Tuples eerst in een wachtrij geplaatst, vervolgens opgehaald en getransformeerd op bedrijfs logica die wordt gehost in een storm-topologie, ten slotte kan de uitvoer worden gezien als Tuples naar een ander SCP-systeem of worden doorgevoerd naar winkels als gedistribueerd bestands systeem of data bases Als SQL Server.

![Een diagram van een wachtrij invoer gegevens die moeten worden verwerkt, waarmee een gegevens archief wordt gevoed](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

In Storm wordt in een toepassings topologie een reken grafiek gedefinieerd. Elk knoop punt in een topologie bevat verwerkings logica en koppelingen tussen knoop punten geven de gegevens stroom aan. De knoop punten voor het injecteren van invoer gegevens in de topologie worden _spouts_genoemd. deze kunnen worden gebruikt om de gegevens te sequentieel. De invoer gegevens kunnen zich bevinden in bestands logboeken, transactionele data bases, systeem prestatie meter items, enzovoort. De knoop punten met zowel invoer-als uitvoer gegevens stromen worden ' _bouten_' genoemd. Dit zijn de werkelijke gegevens filters en selecties en aggregatie.

SCP ondersteunt aanbevolen inspanningen, ten minste één keer en precies eenmaal per gegevens verwerking. In een gedistribueerde toepassing voor verwerking van streaming kunnen er diverse fouten optreden tijdens de verwerking van gegevens, zoals netwerk onderbreking, storing in de computer of fout in gebruikers code, enzovoort. Ten minste één keer wordt gegarandeerd dat alle gegevens ten minste één keer worden verwerkt door automatisch dezelfde gegevens te herhalen als er een fout optreedt. De verwerking ten minste eenmaal is eenvoudig en betrouwbaar en is geschikt voor veel toepassingen. Als voor een toepassing echter exact moet worden geteld, is de verwerking ten minste eenmaal ontoereikend omdat dezelfde gegevens mogelijk in de toepassings topologie kunnen worden afgespeeld. In dat geval is het proces precies eenmaal is ontworpen om te controleren of het resultaat correct is, zelfs wanneer de gegevens kunnen worden geplayd en meerdere keren worden verwerkt.

Met SCP kunnen .NET-ontwikkel aars realtime data process-toepassingen ontwikkelen met behulp van Java Virtual Machine (JVM) met Storm op basis van de voor vallen. De .NET-en JVM communiceren via TCP Local sockets. Elk Spout/flits is in principe een .NET/Java-proces paar, waarbij de gebruikers logica wordt uitgevoerd in .NET process als een invoeg toepassing.

Als u een gegevens verwerkings toepassing boven op SCP wilt bouwen, zijn er verschillende stappen nodig:

* Ontwerp en implementeer de Spouts voor het ophalen van gegevens uit de wachtrij.
* Ontwerp en implementeer Schichten om de invoer gegevens te verwerken en sla gegevens op in externe winkels zoals een Data Base.
* Ontwerp de topologie en verzend de topologie en voer deze uit. De topologie definieert hoek punten en de gegevens stromen tussen de hoek punten. SCP maakt gebruik van de topologie specificatie en implementeert deze in een storm-cluster, waarbij elk hoek punt op één logisch knoop punt wordt uitgevoerd. De failover en het schalen worden verwerkt door de Storm-taak planner.

Dit document maakt gebruik van enkele eenvoudige voor beelden om te leren hoe u een toepassing voor gegevens verwerking kunt bouwen met SCP.

## <a name="scp-plugin-interface"></a>SCP-invoeg toepassing-interface

SCP-plugins (of-toepassingen) zijn zelfstandige exe die zowel binnen Visual Studio als tijdens de ontwikkelings fase kunnen worden uitgevoerd en in de Storm-pijp lijn worden aangesloten na de implementatie van de productie. Het schrijven van de SCP-invoeg toepassing is net hetzelfde als het schrijven van andere standaard Windows-console toepassingen. SCP.NET-platform declareert een bepaalde interface voor spout/flits en de code van de gebruikers-invoeg toepassing moet deze interfaces implementeren. Het belangrijkste doel van dit ontwerp is dat de gebruiker zich kan concentreren op hun eigen bedrijfs logica en dat andere zaken worden afgehandeld door het SCP.NET-platform.

De code van de gebruikers-invoeg toepassing moet een van de volgende interfaces implementeren, afhankelijk van het feit of de topologie transactioneel of niet transactioneel is en of het onderdeel een Spout of Schicht is.

* ISCPSpout
* ISCPBolt
* ISCPTxSpout
* ISCPBatchBolt

### <a name="iscpplugin"></a>ISCPPlugin

ISCPPlugin is de gemeen schappelijke interface voor allerlei soorten invoeg toepassingen. Het is momenteel een dummy-interface.

    public interface ISCPPlugin 
    {
    }

### <a name="iscpspout"></a>ISCPSpout

ISCPSpout is de interface voor niet-transactionele Spout.

     public interface ISCPSpout : ISCPPlugin                    
     {
         void NextTuple(Dictionary<string, Object> parms);         
         void Ack(long seqId, Dictionary<string, Object> parms);   
         void Fail(long seqId, Dictionary<string, Object> parms);  
     }

Wanneer `NextTuple()` wordt aangeroepen, kan C# de gebruikers code een of meer Tuples verzenden. Als er niets is om te verzenden, moet deze methode worden geretourneerd zonder dat er iets hoeft te worden verzonden. Er moet worden opgemerkt dat `NextTuple()`, `Ack()`en `Fail()` allemaal in een kleine lus worden genoemd in een enkele thread in C# het proces. Als er geen Tuples zijn om te verzenden, is het courteous NextTuple-slaap stand te hebben gedurende een korte periode (zoals 10 milliseconden), zodat er niet te veel CPU-ruimte wordt verspild.

`Ack()` en `Fail()` worden alleen aangeroepen wanneer het mechanisme ACK is ingeschakeld in het spec-bestand. De `seqId` wordt gebruikt om de tuple te identificeren die wordt bevestigd of mislukt. Als ACK is ingeschakeld in de niet-transactionele topologie, moet de volgende verzend functie worden gebruikt in Spout:

    public abstract void Emit(string streamId, List<object> values, long seqId); 

Als ACK niet wordt ondersteund in de niet-transactionele topologie, kunnen de `Ack()` en `Fail()` als lege functie worden gelaten.

De `parms` invoer parameter in deze functies is een lege woorden lijst en is gereserveerd voor toekomstig gebruik.

### <a name="iscpbolt"></a>ISCPBolt

ISCPBolt is de interface voor niet-transactionele flits.

    public interface ISCPBolt : ISCPPlugin 
    {
    void Execute(SCPTuple tuple);           
    }

Als er een nieuwe tuple beschikbaar is, wordt de functie `Execute()` aangeroepen om deze te verwerken.

### <a name="iscptxspout"></a>ISCPTxSpout

ISCPTxSpout is de interface voor transactionele Spout.

    public interface ISCPTxSpout : ISCPPlugin
    {
        void NextTx(out long seqId, Dictionary<string, Object> parms);  
        void Ack(long seqId, Dictionary<string, Object> parms);         
        void Fail(long seqId, Dictionary<string, Object> parms);        
    }

Net als bij de niet-transactionele teller-deel, `NextTx()`, `Ack()`en `Fail()` worden alle in een kleine lus genoemd in een enkele thread in C# het proces. Als er geen gegevens zijn om te verzenden, is het courteous de slaap stand gedurende een korte periode (10 milliseconden) zodanig `NextTx` ingesteld dat er niet te veel CPU-ruimte wordt verspild.

`NextTx()` wordt aangeroepen om een nieuwe trans actie te starten, de out-para meter `seqId` wordt gebruikt om de trans actie te identificeren, die ook wordt gebruikt in `Ack()` en `Fail()`. In `NextTx()`kan de gebruiker gegevens naar Java-zijde verzenden. De gegevens worden opgeslagen in ZooKeeper om replay te ondersteunen. Omdat de capaciteit van ZooKeeper beperkt is, moet de gebruiker alleen meta gegevens verzenden, niet massaal in de transactionele Spout.

Met storm wordt automatisch een trans actie opnieuw afgespeeld als dit mislukt, dus `Fail()` niet in normale gevallen worden aangeroepen. Maar als SCP de meta gegevens kan controleren die worden verzonden door transactionele Spout, kan deze `Fail()` aanroepen wanneer de meta gegevens ongeldig zijn.

De `parms` invoer parameter in deze functies is een lege woorden lijst en is gereserveerd voor toekomstig gebruik.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

ISCPBatchBolt is de interface voor transactionele flits.

    public interface ISCPBatchBolt : ISCPPlugin           
    {
        void Execute(SCPTuple tuple);
        void FinishBatch(Dictionary<string, Object> parms);  
    }

`Execute()` wordt aangeroepen wanneer er een nieuwe tuple bij de flits arriveert. `FinishBatch()` wordt aangeroepen wanneer deze trans actie wordt beëindigd. De `parms` invoer parameter is gereserveerd voor toekomstig gebruik.

Voor transactionele topologie is er een belang rijk concept-`StormTxAttempt`. Het heeft twee velden, `TxId` en `AttemptId`. `TxId` wordt gebruikt om een specifieke trans actie te identificeren en voor een bepaalde trans actie kunnen er meerdere pogingen optreden als de trans actie mislukt en opnieuw wordt afgespeeld. SCP.NET maakt een nieuw ISCPBatchBolt-object voor de verwerking van elke `StormTxAttempt`, net zoals bij storm in Java. Het doel van dit ontwerp is het ondersteunen van verwerking van parallelle trans acties. Houd er rekening mee dat als de trans actie is uitgevoerd, het bijbehorende ISCPBatchBolt-object wordt vernietigd en dat de garbage collector wordt verzameld.

## <a name="object-model"></a>Object model

SCP.NET biedt ook een eenvoudige set van belang rijke objecten waarmee ontwikkel aars kunnen Program meren. Ze zijn **context**, **State Store**en **SCPRuntime**. Deze worden besproken in het rest gedeelte van deze sectie.

### <a name="context"></a>Context

Context biedt een actieve omgeving voor de toepassing. Elk ISCPPlugin-exemplaar (ISCPSpout/ISCPBolt/ISCPTxSpout/ISCPBatchBolt) heeft een bijbehorend context exemplaar. De functionaliteit van de context kan worden onderverdeeld in twee delen: (1) het statische deel, dat beschikbaar is in het hele C# proces, (2) het dynamische deel, dat alleen beschikbaar is voor het specifieke context exemplaar.

### <a name="static-part"></a>Statisch deel

    public static ILogger Logger = null;
    public static SCPPluginType pluginType;                      
    public static Config Config { get; set; }                    
    public static TopologyContext TopologyContext { get; set; }  

`Logger` is bedoeld voor de logboek doel einden.

`pluginType` wordt gebruikt om het type invoeg toepassing van het C# proces aan te geven. Als het C# proces wordt uitgevoerd in de lokale test modus (zonder Java), wordt het type invoeg toepassing `SCP_NET_LOCAL`.

    public enum SCPPluginType 
    {
        SCP_NET_LOCAL = 0,       
        SCP_NET_SPOUT = 1,       
        SCP_NET_BOLT = 2,        
        SCP_NET_TX_SPOUT = 3,   
        SCP_NET_BATCH_BOLT = 4  
    }

`Config` wordt opgegeven om configuratie parameters van Java-zijde op te halen. De para meters worden door gegeven aan C# de Java-zijde wanneer de invoeg toepassing is geïnitialiseerd. De `Config`-para meters zijn onderverdeeld in twee delen: `stormConf` en `pluginConf`.

    public Dictionary<string, Object> stormConf { get; set; }  
    public Dictionary<string, Object> pluginConf { get; set; }  

`stormConf` zijn para meters gedefinieerd door storm en `pluginConf` zijn de para meters die zijn gedefinieerd door SCP. Bijvoorbeeld:

    public class Constants
    {
        … …

        // constant string for pluginConf
        public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

        // constant string for stormConf
        public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";           
        public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";                 
    }

`TopologyContext` wordt weer gegeven om de topologie context op te halen, is het nuttig voor onderdelen met meerdere parallelle uitvoeringen. Hier volgt een voorbeeld:

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

### <a name="dynamic-part"></a>Dynamisch deel

De volgende interfaces zijn relevant voor een bepaald context exemplaar. Het context exemplaar wordt gemaakt door het SCP.NET-platform en door gegeven aan de gebruikers code:

    // Declare the Output and Input Stream Schemas

    public void DeclareComponentSchema(ComponentStreamSchema schema);   

    // Emit tuple to default stream.
    public abstract void Emit(List<object> values);                   

    // Emit tuple to the specific stream.
    public abstract void Emit(string streamId, List<object> values);  

Voor de niet-transactionele Spout die de ACK ondersteunt, wordt de volgende methode gegeven:

    // for non-transactional Spout which supports ack
    public abstract void Emit(string streamId, List<object> values, long seqId);  

Voor niet-transactionele flits die ACK ondersteunt, moet deze de ontvangen tuple expliciet `Ack()` of `Fail()`. En wanneer de nieuwe tuple wordt verzonden, moet deze ook de ankers van de nieuwe tuple opgeven. De volgende methoden zijn aanwezig.

    public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values); 
    public abstract void Ack(SCPTuple tuple);
    public abstract void Fail(SCPTuple tuple);

### <a name="statestore"></a>State Store

`StateStore` biedt meta gegevens Services, het genereren van monotone reeksen en een wacht vrije coördinatie. Op hoger niveau gedistribueerde gelijktijdigheids samenvattingen kunnen worden gebaseerd op `StateStore`, waaronder gedistribueerde vergren delingen, gedistribueerde wacht rijen, barrières en transactie Services.

SCP-toepassingen kunnen gebruikmaken van het `State`-object om bepaalde gegevens in [Apache ZooKeeper](https://zookeeper.apache.org/)te behouden, met name voor transactionele topologie. Als transactionele Spout vastloopt en opnieuw wordt opgestart, kan deze de benodigde gegevens ophalen uit ZooKeeper en de pijp lijn opnieuw starten.

Het `StateStore`-object heeft hoofd zakelijk de volgende methoden:

    /// <summary>
    /// Static method to retrieve a state store of the given path and connStr 
    /// </summary>
    /// <param name="storePath">StateStore Path</param>
    /// <param name="connStr">StateStore Address</param>
    /// <returns>Instance of StateStore</returns>
    public static StateStore Get(string storePath, string connStr);

    /// <summary>
    /// Create a new state object in this state store instance
    /// </summary>
    /// <returns>State from StateStore</returns>
    public State Create();

    /// <summary>
    /// Retrieve all states that were previously uncommitted, excluding all aborted states 
    /// </summary>
    /// <returns>Uncommitted States</returns>
    public IEnumerable<State> GetUnCommitted();

    /// <summary>
    /// Get all the States in the StateStore
    /// </summary>
    /// <returns>All the States</returns>
    public IEnumerable<State> States();

    /// <summary>
    /// Get state or registry object
    /// </summary>
    /// <param name="info">Registry Name(Registry only)</param>
    /// <typeparam name="T">Type, Registry or State</typeparam>
    /// <returns>Return Registry or State</returns>
    public T Get<T>(string info = null);

    /// <summary>
    /// List all the committed states
    /// </summary>
    /// <returns>Registries contain the Committed State </returns> 
    public IEnumerable<Registry> Committed();

    /// <summary>
    /// List all the Aborted State in the StateStore
    /// </summary>
    /// <returns>Registries contain the Aborted State</returns>
    public IEnumerable<Registry> Aborted();

    /// <summary>
    /// Retrieve an existing state object from this state store instance 
    /// </summary>
    /// <returns>State from StateStore</returns>
    /// <typeparam name="T">stateId, id of the State</typeparam>
    public State GetState(long stateId)

Het `State`-object heeft hoofd zakelijk de volgende methoden:

    /// <summary>
    /// Set the status of the state object to commit 
    /// </summary>
    public void Commit(bool simpleMode = true); 

    /// <summary>
    /// Set the status of the state object to abort 
    /// </summary>
    public void Abort();

    /// <summary>
    /// Put an attribute value under the give key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <param name="attribute">State Attribute</param> 
    public void PutAttribute<T>(string key, T attribute); 

    /// <summary>
    /// Get the attribute value associated with the given key 
    /// </summary>
    /// <param name="key">Key</param> 
    /// <returns>State Attribute</returns>               
    public T GetAttribute<T>(string key);                    

Als simpleMode is ingesteld op True, wordt de bijbehorende ZNode in ZooKeeper verwijderd. `Commit()` Anders wordt de huidige ZNode verwijderd en wordt er een nieuw knoop punt toegevoegd in het toegewezen\_pad.

### <a name="scpruntime"></a>SCPRuntime

SCPRuntime biedt de volgende twee methoden:

    public static void Initialize();

    public static void LaunchPlugin(newSCPPlugin createDelegate);  

`Initialize()` wordt gebruikt voor het initialiseren van de SCP runtime-omgeving. In deze methode maakt het C# proces verbinding met de Java-zijde en worden configuratie parameters en topologie context opgehaald.

`LaunchPlugin()` wordt gebruikt om de bericht verwerkings lus te starten. In deze lus ontvangt de C# invoeg toepassing berichten formulier Java-zijde (inclusief Tuples en besturings signalen), en verwerkt vervolgens de berichten, mogelijk bij het aanroepen van de interface methode voor de gebruikers code. De invoer parameter voor de methode `LaunchPlugin()` is een gemachtigde die een object kan retour neren dat de ISCPSpout/IScpBolt/ISCPTxSpout/ISCPBatchBolt-interface implementeert.

    public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary\<string, Object\> parms); 

Voor ISCPBatchBolt kunnen we `StormTxAttempt` van `parms`ophalen en gebruiken om te beoordelen of het een opnieuw gespelde poging is. De controle op een replay-poging vindt vaak plaats op de door voering en wordt gedemonstreerd in het `HelloWorldTx`-voor beeld.

Over het algemeen kunnen de SCP-invoeg toepassingen in twee modi hier worden uitgevoerd:

1. Lokale test modus: in deze modus worden de SCP-invoeg toepassingen C# (de gebruikers code) uitgevoerd in Visual Studio tijdens de ontwikkelings fase. `LocalContext` kan worden gebruikt in deze modus, waarmee u de verzonden Tuples kunt serialiseren naar lokale bestanden en ze terug kunt lezen naar het geheugen.

        public interface ILocalContext
        {
            List\<SCPTuple\> RecvFromMsgQueue();
            void WriteMsgQueueToFile(string filepath, bool append = false);  
            void ReadFromFileToMsgQueue(string filepath);                    
        }

2. Normale modus: in deze modus worden de SCP-invoeg toepassingen gestart door storm Java process.

    Hier volgt een voor beeld van het starten van de SCP-invoeg toepassing:

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

## <a name="topology-specification-language"></a>Taal van topologie specificatie

De SCP-topologie specificatie is een domein-specifieke taal voor het beschrijven en configureren van SCP-topologieën. Het is gebaseerd op de clojure DSL (<https://storm.incubator.apache.org/documentation/Clojure-DSL.html>) van Storm en wordt uitgebreid door SCP.

Topologie specificaties kunnen rechtstreeks naar het Storm-cluster worden verzonden voor uitvoering via de opdracht ***runspec*** .

SCP.NET heeft de volgende functies toegevoegd voor het definiëren van transactionele topologieën:

| Nieuwe functies | Parameters | Beschrijving |
| --- | --- | --- |
| TX-topolopy |topologie-naam<br />Spout-kaart<br />bout-kaart |Definieer een transactionele topologie met de naam van de topologie, &nbsp;spouts definitie toewijzing en de bouten definitie toewijzing |
| SCP-TX-Spout |exec-naam<br />argumenten<br />velden |Definieer een transactionele Spout. De toepassing wordt uitgevoerd met de ***exec-naam*** met behulp van ***argumenten***.<br /><br />De ***velden*** zijn de uitvoer velden voor spout |
| SCP-TX-batch-Schicht |exec-naam<br />argumenten<br />velden |Definieer een transactionele batch-schicht. De toepassing wordt uitgevoerd met de ***exec-naam*** met behulp van ***argumenten.***<br /><br />De velden zijn de uitvoer velden voor schicht. |
| SCP-TX-door Voer-flits |exec-naam<br />argumenten<br />velden |Definieer een transactionele doorvoer flits. De toepassing wordt uitgevoerd met de ***exec-naam*** met behulp van ***argumenten***.<br /><br />De ***velden*** zijn de uitvoer velden voor schicht |
| nontx-topolopy |topologie-naam<br />Spout-kaart<br />bout-kaart |Definieer een niet-transactionele topologie met de naam van de topologie,&nbsp; spouts definitie toewijzing en de bouten definitie toewijzing |
| SCP-Spout |exec-naam<br />argumenten<br />velden<br />parameters |Definieer een niet-transactionele Spout. De toepassing wordt uitgevoerd met de ***exec-naam*** met behulp van ***argumenten***.<br /><br />De ***velden*** zijn de uitvoer velden voor spout<br /><br />De ***para meters*** zijn optioneel, waarbij een aantal para meters zoals "niet-trans Actional. ack. enabled" worden opgegeven. |
| SCP-flits |exec-naam<br />argumenten<br />velden<br />parameters |Definieer een niet-transactionele schicht. De toepassing wordt uitgevoerd met de ***exec-naam*** met behulp van ***argumenten***.<br /><br />De ***velden*** zijn de uitvoer velden voor schicht<br /><br />De ***para meters*** zijn optioneel, waarbij een aantal para meters zoals "niet-trans Actional. ack. enabled" worden opgegeven. |

Voor SCP.NET zijn de volgende tref woorden gedefinieerd:

| Trefwoorden | Beschrijving |
| --- | --- |
| : naam |De naam van de topologie definiëren |
| : topologie |Definieer de topologie met de vorige functies en maak deze op. |
| :p |Definieer de parallellisme Hint voor elke Spout of Schicht. |
| : config |Definieer de para meter configureren of werk de bestaande items bij |
| : schema |Definieer het schema van de stroom. |

En vaak gebruikte para meters:

| Parameter | Beschrijving |
| --- | --- |
| ' plugin.name ' |exe-bestands naam van C# de invoeg toepassing |
| "plugin. args" |argumenten voor de invoeg toepassing |
| "uitvoer. schema" |Uitvoer schema |
| "niet-transactioneel. ack. enabled" |Of ACK is ingeschakeld voor niet-transactionele topologie |

De runspec-opdracht wordt samen met de bits geïmplementeerd en het gebruik is als volgt:

    .\bin\runSpec.cmd
    usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
    ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target

De para meter ***resource-dir*** is optioneel, u moet deze opgeven wanneer u een C# toepassing wilt koppelen en deze map bevat de toepassing, de afhankelijkheden en de configuraties.

De para meter ***classpath*** is ook optioneel. Het wordt gebruikt om het Java-klassenpad op te geven als het specificatie bestand Java Spout of bout bevat.

## <a name="miscellaneous-features"></a>Diverse functies

### <a name="input-and-output-schema-declaration"></a>Declaratie van invoer-en uitvoer schema

Gebruikers kunnen Tuples in C# processen verzenden, het platform moet de tuple serialiseren naar byte [], overdracht naar Java-zijde, en Storm stuurt deze tuple over naar de doelen. In de downstream- C# onderdelen ontvangen processen een back-upbericht van Java naast en converteren ze naar het oorspronkelijke type per platform, al deze bewerkingen worden verborgen door het platform.

Voor de ondersteuning van serialisatie en deserialisatie moet de gebruikers code het schema van de invoer en uitvoer declareren.

Het invoer/uitvoer-stroom schema wordt gedefinieerd als een woorden lijst. De sleutel is het StreamId. De waarde is de typen kolommen. Voor het onderdeel kunnen meerdere streams worden gedeclareerd.

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


In context object zijn de volgende API toegevoegd:

    public void DeclareComponentSchema(ComponentStreamSchema schema)

Ontwikkel aars moeten ervoor zorgen dat de Tuples het schema dat voor die stroom is gedefinieerd, op een andere manier door het systeem wordt gegenereerd.

### <a name="multi-stream-support"></a>Ondersteuning voor meerdere streams

SCP ondersteunt gebruikers code voor het verzenden of ontvangen van meerdere afzonderlijke streams tegelijk. De ondersteuning weerspiegelt in het context object als de verzend methode een optionele stream ID-para meter.

Er zijn twee methoden in het context object SCP.NET toegevoegd. Ze worden gebruikt om tupel of Tuples te verzenden om StreamId op te geven. De StreamId is een teken reeks die consistent moet zijn in zowel C# als de definitie specificatie van de topologie.

    /* Emit tuple to the specific stream. */
    public abstract void Emit(string streamId, List<object> values);

    /* for non-transactional Spout only */
    public abstract void Emit(string streamId, List<object> values, long seqId);

De verzen ding naar een niet-bestaande stroom veroorzaakt runtime-uitzonde ringen.

### <a name="fields-grouping"></a>Groepering van velden

De ingebouwde veld groepering in Storm werkt niet correct in SCP.NET. Op de Java-proxy zijde zijn alle gegevens typen van de velden in feite byte [], en de groepering van velden gebruikt de byte [] object hash-code om de groepering uit te voeren. De byte code van het object bytes [] is het adres van dit object in het geheugen. Daarom is de groepering onjuist voor twee-byte objecten die dezelfde inhoud delen, maar niet hetzelfde adres.

SCP.NET voegt een aangepaste groeperings methode toe en maakt gebruik van de inhoud van de byte [] om de groepering uit te voeren. De syntaxis van het bestand **spec** is als volgt:

    (bolt-spec
        {
            "spout_test" (scp-field-group :non-tx [0,1])
        }
        …
    )

Veld

1. "SCP-veld-Group" betekent "aangepaste veld groepering geïmplementeerd door SCP".
2. ": TX" of ": non-TX" betekent als het om een transactionele topologie gaat. We hebben deze informatie nodig omdat de begin index verschilt in TX versus niet-TX-topologieën.
3. [0, 1] betekent een hash-set veld-Id's, beginnend bij 0.

### <a name="hybrid-topology"></a>Hybride topologie

De systeem eigen Storm wordt geschreven in Java. En SCP.NET heeft IT verbeterd zodat C# ontwikkel aars code kunnen C# schrijven voor het afhandelen van hun bedrijfs logica. Maar biedt ook ondersteuning voor hybride topologieën die niet alleen C# spouts/schichten, maar ook Java Spout/Schicht bevatten.

### <a name="specify-java-spoutbolt-in-spec-file"></a>Java Spout/Schicht opgeven in specificatie bestand

In specificatie bestand kan ' SCP-Spout ' en ' SCP-Schicht ' ook worden gebruikt om Java-Spouts en-bouten op te geven. Hier volgt een voor beeld:

    (spout-spec 
      (microsoft.scp.example.HybridTopology.Generator.)           
      :p 1)

Hier `microsoft.scp.example.HybridTopology.Generator` de naam van de klasse java Spout.

### <a name="specify-java-classpath-in-runspec-command"></a>Java classpath opgeven in de opdracht runSpec

Als u een topologie met Java-Spouts of-grendels wilt verzenden, moet u eerst de Java-Spouts of-grendels compileren en de jar-bestanden ophalen. Vervolgens moet u het Java-klassenpad opgeven dat de jar-bestanden bevat bij het verzenden van de topologie. Hier volgt een voorbeeld:

    bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*

Hier vindt u **voor beelden\\HybridTopology\\java\\doel\\** de map met het Java Spout/bout jar-bestand is.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisatie en deserialisatie van Java enC#

Het SCP-onderdeel bevat Java C# naast elkaar. Om te kunnen communiceren met systeem eigen Java Spouts/-flitsen, moet serialisatie/deserialisatie worden uitgevoerd tussen Java- C# zijde en naast elkaar, zoals in de volgende grafiek wordt geïllustreerd.

![diagram van Java-onderdeel dat wordt verzonden naar een SCP-onderdeel dat naar Java-onderdeel wordt verzonden](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

1. Serialisatie in Java-zijde en deserialisatie C# in de zijkant

   Eerst bieden we standaard implementatie voor serialisatie in Java-zijde en deserialisatie C# in de zijkant. De serialisatie methode op de Java-zijde kan worden opgegeven in het specificatie bestand:

       (scp-bolt
           {
               "plugin.name" "HybridTopology.exe"
               "plugin.args" ["displayer"]
               "output.schema" {}
               "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
           })

   De deserialisatie methode aan C# de kant moet worden opgegeven C# in gebruikers code:

       Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
       inputSchema.Add("default", new List<Type>() { typeof(Person) });
       this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
       this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());            

   Deze standaard implementatie moet de meeste cases verwerken die het gegevens type niet te complex is. Voor bepaalde gevallen, omdat het gegevens type van de gebruiker te complex is, of omdat de prestaties van de standaard implementatie niet voldoen aan de vereisten van de gebruiker, kunnen gebruikers hun eigen implementatie aansluiten.

   De serialisatie interface in Java-zijde is als volgt gedefinieerd:

       public interface ICustomizedInteropJavaSerializer {
           public void prepare(String[] args);
           public List<ByteBuffer> serialize(List<Object> objectList);
       }

   De deserialisatie interface aan C# de zijkant is als volgt gedefinieerd:

   open bare interface ICustomizedInteropCSharpDeserializer

       public interface ICustomizedInteropCSharpDeserializer
       {
           List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
       }
2. Serialisatie aan C# de zijkant en deserialisatie in Java-zijde

   De serialisatie methode aan C# de kant moet worden opgegeven C# in gebruikers code:

       this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 

   De deserialisatie methode op de Java-zijde moet worden opgegeven in het specificatie bestand:

    ```
    (scp-spout
       {
         "plugin.name" "HybridTopology.exe"
         "plugin.args" ["generator"]
         "output.schema" {"default" ["person"]}
         "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
       }
    )
    ```

   Hier is ' Microsoft. scp. Storm. CustomizedInteropJSONDeserializer ' de naam van de deserializer en ' micro soft. scp. example. HybridTopology. person ' is de doel klasse waarin de gegevens worden gedeserialiseerd.

   De gebruiker kan ook een eigen implementatie van C# serializer en Java deserializer aansluiten. Deze code is de interface voor C# serialisatiefunctie:

       public interface ICustomizedInteropCSharpSerializer
       {
           List<byte[]> Serialize(List<object> dataList);
       }

   Deze code is de interface voor Java-deserializer:

       public interface ICustomizedInteropJavaDeserializer {
           public void prepare(String[] targetClassNames);
           public List<Object> Deserialize(List<ByteBuffer> dataList);
       }

## <a name="scp-host-mode"></a>SCP-host-modus

In deze modus kan de gebruiker hun codes op DLL-bestand compileren en SCPHost. exe gebruiken dat door SCP wordt verstrekt om de topologie in te dienen. Het specificatie bestand ziet er als volgt uit:

    (scp-spout
      {
        "plugin.name" "SCPHost.exe"
        "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
        "output.schema" {"default" ["sentence"]}
      })

Hier is `plugin.name` opgegeven als `SCPHost.exe` van de SCP-SDK. SCPHost. exe accepteert drie para meters:

1. De eerste is de DLL-naam, die in dit voor beeld `"HelloWorld.dll"` is.
2. De tweede is de naam van de klasse, die `"Scp.App.HelloWorld.Generator"` in dit voor beeld.
3. De derde is de naam van een open bare statische methode die kan worden aangeroepen om een exemplaar van ISCPPlugin op te halen.

In de host-modus wordt gebruikers code gecompileerd als DLL en aangeroepen door het SCP-platform. Daarom kan het SCP-platform volledige controle krijgen over de volledige verwerkings logica. Daarom raden we onze klanten aan om topologie in de SCP-host-modus in te dienen, omdat IT de ontwikkelings ervaring kan vereenvoudigen en ons meer flexibiliteit en betere achterwaartse compatibiliteit kan bieden voor latere versies.

## <a name="scp-programming-examples"></a>Voor beelden van SCP-programmering

### <a name="helloworld"></a>HelloWorld

**Hallo wereld** is een eenvoudig voor beeld om een smaak van SCP.net weer te geven. Er wordt een niet-transactionele topologie gebruikt, met een Spout- **Generator**, en twee bouten met de naam **splitter** en **Counter**. De Spout- **Generator** genereert wille keurig zinnen en levert deze zinnen naar **splitter**. Met de schicht * * splitter worden de zinnen gesplitst in woorden en worden deze woorden op **Counter** -bout verzonden. De schicht teller gebruikt een woorden lijst om het aantal exemplaren van elk woord vast te leggen.

Er zijn twee spec-bestanden, **HelloWorld. spec** en **HelloWorld\_EnableAck. spec** voor dit voor beeld. In de C# code kunt u nagaan of ACK is ingeschakeld door de PluginConf van Java aan te vragen.

    /* demo how to get pluginConf info */
    if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
    {
        enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
    }
    Context.Logger.Info("enableAck: {0}", enableAck);

Als in de Spout ACK is ingeschakeld, wordt er een woorden lijst gebruikt om de Tuples in de cache op te slaan die nog niet zijn bevestigd. Als fail () wordt aangeroepen, wordt de mislukte tuple opnieuw afgespeeld:

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

### <a name="helloworldtx"></a>HelloWorldTx

Het **HelloWorldTx** -voor beeld laat zien hoe u transactionele topologie implementeert. Het heeft één Spout genaamd **Generator**, een batch-Schicht met de naam **gedeeltelijke telling**en een commit-Schicht met de naam **Count-Sum**. Er zijn ook drie vooraf gemaakte txt-bestanden: **DataSource0. txt**, **DataSource1. txt**en **DataSource2. txt**.

In elke trans actie selecteert de Spout- **Generator** twee bestanden van de vooraf gemaakte drie bestanden en worden de twee bestands namen naar de schicht van het **gedeeltelijke aantal** geverzendd. Met het **aantal schichten** wordt de bestands naam opgehaald uit de ontvangen tuple, wordt het bestand geopend en wordt het aantal woorden in dit bestand geteld en wordt het woord nummer ten slotte verzonden naar de schicht **Count-Sum** . Het **aantal-Sum-** schicht geeft een samen vatting van het totale aantal.

Voor het uitvoeren van **exact dezelfde** semantiek moet het **aantal optellingen voor de opvolgings teller** worden beoordeeld of het een opnieuw gespeelde trans actie is. In dit voor beeld heeft het een statische lidvariabele:

    public static long lastCommittedTxId = -1; 

Wanneer een ISCPBatchBolt-exemplaar wordt gemaakt, wordt de `txAttempt` van invoer parameters opgehaald:

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

Als `FinishBatch()` wordt aangeroepen, wordt het `lastCommittedTxId` bijgewerkt als het geen opnieuw gespeelde trans actie is.

    public void FinishBatch(Dictionary<string, Object> parms)
    {
        /* judge whether it is a replayed transaction? */
        bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

        if (!replay)
        {
            /* If it is not replayed, update the totalCount and lastCommittedTxId value */
            totalCount = totalCount + this.count;
            lastCommittedTxId = this.txAttempt.TxId;
        }
        … …
    }

### <a name="hybridtopology"></a>HybridTopology

Deze topologie bevat een Java-Spout en C# een schicht. Hierbij wordt gebruikgemaakt van de standaard implementatie van serialisatie en deserialisatie van het SCP-platform. Zie de **HybridTopology. spec** in **voor beelden\\HybridTopology** -map voor de details van het specificatie bestand en **SubmitTopology. bat** voor het opgeven van Java classpath.

### <a name="scphostdemo"></a>SCPHostDemo

Dit voor beeld is hetzelfde als HelloWorld in essentie. Het enige verschil is dat de gebruikers code wordt gecompileerd als DLL en de topologie wordt verzonden met behulp van SCPHost. exe. Zie de sectie ' SCP host mode ' voor meer gedetailleerde informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende documenten voor voor beelden van Apache Storm topologieën die zijn gemaakt met SCP:

* [Topologieën ontwikkelen C# voor Apache Storm op HDInsight met behulp van Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Gebeurtenissen verwerken vanuit Azure Event Hubs met Apache Storm in HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Voertuig sensor gegevens van Event Hubs verwerken met Apache Storm op HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [ETL van Azure Event Hubs uitpakken, transformeren en laden naar Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
