---
title: Toepassings pakketten implementeren op reken knooppunten
description: Gebruik de functie toepassings pakketten van Azure Batch om eenvoudig meerdere toepassingen en versies te beheren voor installatie op batch Compute-knoop punten.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 328b08acbc6d13dd03956bb501b4d4a51310c9c0
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147212"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Toepassingen implementeren op reken knooppunten met batch-toepassings pakketten

De functie voor toepassings pakketten van Azure Batch biedt eenvoudig beheer van taak toepassingen en de implementatie ervan tot de reken knooppunten in uw pool. Met toepassings pakketten kunt u meerdere versies van de toepassingen die door uw taken worden uitgevoerd, uploaden en beheren, met inbegrip van de bijbehorende ondersteunende bestanden. U kunt vervolgens automatisch een of meer van deze toepassingen implementeren op de reken knooppunten in uw pool.

In dit artikel leert u hoe u toepassings pakketten uploadt en beheert in de Azure Portal. Vervolgens leert u hoe u deze kunt installeren op de reken knooppunten van een pool met de [batch .net][api_net] -bibliotheek.

> [!NOTE]
> Toepassingspakketten worden ondersteund in alle Batch-pools die na 5 juli 2017 zijn gemaakt. De pakketten worden ondersteund in Batch-pools die zijn gemaakt tussen 10 maart 2016 en 5 juli 2017, maar alleen als de pool is gemaakt met behulp van een cloudservice-configuratie. Batch-pools die zijn gemaakt vóór 10 maart 2016 bieden geen ondersteuning voor toepassingspakketten.
>
> De Api's voor het maken en beheren van toepassings pakketten maken deel uit van de [Batch Management .net][api_net_mgmt] -bibliotheek. De Api's voor het installeren van toepassings pakketten op een reken knooppunt maken deel uit van de [batch .net][api_net] -bibliotheek. Vergelijk bare functies bevinden zich in de beschikbaar batch-Api's voor andere talen. 
>
> De functie voor toepassings pakketten die hier wordt beschreven, vervangt de functie Batch-apps die beschikbaar is in vorige versies van de service.

## <a name="application-package-requirements"></a>Toepassings pakket vereisten
Als u toepassings pakketten wilt gebruiken, moet u [een Azure Storage-account koppelen](#link-a-storage-account) aan uw batch-account.

## <a name="about-applications-and-application-packages"></a>Over toepassingen en toepassings pakketten
Binnen Azure Batch verwijst een *toepassing* naar een set binaire bestanden die automatisch kunnen worden gedownload naar de reken knooppunten in uw pool. Een *toepassings pakket* verwijst naar een *specifieke set* binaire bestanden en vertegenwoordigt een bepaalde *versie* van de toepassing.

![Diagram van toepassingen en toepassings pakketten op hoog niveau][1]

### <a name="applications"></a>Toepassingen
Een toepassing in batch bevat een of meer toepassings pakketten en bevat configuratie opties voor de toepassing. Een toepassing kan bijvoorbeeld de standaard versie van het toepassings pakket opgeven die op reken knooppunten moet worden geïnstalleerd en of de pakketten kunnen worden bijgewerkt of verwijderd.

### <a name="application-packages"></a>Toepassingspakketten
Een toepassings pakket is een zip-bestand dat de binaire bestanden van de toepassing bevat en de ondersteunings dossiers die vereist zijn voor uw taken om de toepassing uit te voeren. Elk toepassings pakket vertegenwoordigt een specifieke versie van de toepassing.

U kunt toepassings pakketten opgeven in het groeps-en taak niveau. U kunt een of meer van deze pakketten en (eventueel) een versie opgeven wanneer u een groep of taak maakt.

* **Groeps toepassings pakketten** worden geïmplementeerd op *elk* knoop punt in de pool. Toepassingen worden geïmplementeerd wanneer een knoop punt wordt toegevoegd aan een pool en wanneer deze opnieuw wordt opgestart of een installatie kopie wordt gemaakt.
  
    Groeps toepassings pakketten zijn geschikt wanneer alle knoop punten in een pool de taken van een taak uitvoeren. U kunt een of meer toepassings pakketten opgeven wanneer u een pool maakt en u kunt de pakketten van een bestaande groep toevoegen of bijwerken. Als u de toepassings pakketten van een bestaande groep bijwerkt, moet u de knoop punten opnieuw opstarten om het nieuwe pakket te installeren.
* **Taak toepassings pakketten** worden alleen geïmplementeerd op een reken knooppunt dat is gepland voor het uitvoeren van een taak, net voordat de opdracht regel van de taak wordt uitgevoerd. Als het opgegeven toepassings pakket en de versie zich al op het knoop punt bevindt, wordt het niet opnieuw geïmplementeerd en wordt het bestaande pakket gebruikt.
  
    Taak toepassings pakketten zijn handig in omgevingen met gedeelde groepen, waarbij verschillende taken worden uitgevoerd op één groep en de groep niet wordt verwijderd wanneer een taak is voltooid. Als de job minder taken dan knooppunten in de groep heeft, kunnen toepassingspakketten van taken gegevensoverdracht minimaliseren omdat uw toepassing alleen wordt geïmplementeerd op de knooppunten die taken uitvoeren.
  
    Andere scenario's die kunnen profiteren van taak toepassings pakketten zijn taken die een grote toepassing uitvoeren, maar slechts enkele taken. Een voor beeld: een voorbereidings fase of een samenvoeg taak, waarbij de preprocessing-of Merge-toepassing Heavyweight is, kan profiteren van het gebruik van taak toepassings pakketten.

> [!IMPORTANT]
> Er gelden beperkingen voor het aantal toepassingen en toepassings pakketten in een batch-account en op de maximale grootte van het toepassings pakket. Zie [quota's en limieten voor de Azure batch-service](batch-quota-limit.md) voor meer informatie over deze limieten.
> 
> 

### <a name="benefits-of-application-packages"></a>Voor delen van toepassings pakketten
Toepassings pakketten kunnen de code in uw batch-oplossing vereenvoudigen en de overhead verminderen die nodig is voor het beheren van de toepassingen die door uw taken worden uitgevoerd.

Met toepassings pakketten hoeft de begin taak van de pool geen lange lijst op te geven met afzonderlijke bron bestanden die op de knoop punten moeten worden geïnstalleerd. U hoeft niet hand matig meerdere versies van uw toepassings bestanden in Azure Storage of op uw knoop punten te beheren. En u hoeft zich geen zorgen te maken over het genereren van [SAS-url's](../storage/common/storage-sas-overview.md) voor toegang tot de bestanden in uw opslag account. Batch werkt op de achtergrond met Azure Storage om toepassings pakketten op te slaan en te implementeren op reken knooppunten.

> [!NOTE] 
> De totale grootte van een begintaak moet kleiner zijn dan of gelijk zijn aan 32.768 tekens, inclusief bronbestanden en omgevingsvariabelen. Als uw begin taak deze limiet overschrijdt, is het gebruik van toepassings pakketten een andere optie. U kunt ook een zip-archief met uw bron bestanden maken, dit uploaden als een BLOB naar Azure Storage en vervolgens uitpakken van de opdracht regel van de begin taak. 
>
>

## <a name="upload-and-manage-applications"></a>Toepassingen uploaden en beheren
U kunt de [Azure Portal][portal] -of de batch beheer-api's gebruiken om de toepassings pakketten in uw batch-account te beheren. In de volgende secties laten we eerst zien hoe u een opslag account koppelt en hoe u toepassingen en pakketten toevoegt en ze beheert met de portal.

### <a name="link-a-storage-account"></a>Een opslag account koppelen
Als u toepassings pakketten wilt gebruiken, moet u eerst een [Azure Storage-account](accounts.md#azure-storage-accounts) koppelen aan uw batch-account. Als u nog geen opslag account hebt geconfigureerd, wordt in de Azure Portal een waarschuwing weer gegeven wanneer u voor het eerst op **toepassingen** in uw batch-account klikt.



![Waarschuwing ' er is geen opslag account geconfigureerd ' in Azure Portal][9]

De batch-service gebruikt het bijbehorende opslag account om uw toepassings pakketten op te slaan. Nadat u de twee accounts hebt gekoppeld, kan batch de pakketten die zijn opgeslagen in het gekoppelde opslag account automatisch implementeren in de reken knooppunten. Als u een opslag account wilt koppelen aan uw batch-account, klikt u op **opslag account** in het **waarschuwings** venster en klikt u vervolgens nogmaals op **opslag account** .

![Kies Blade opslag account in Azure Portal][10]

We raden u aan om een opslag account te maken dat *speciaal is bedoeld* voor gebruik met uw batch-account en hier te selecteren. Nadat u een opslag account hebt gemaakt, kunt u dit koppelen aan uw batch-account met behulp van het venster voor het **opslag account** .

> [!IMPORTANT] 
> - Op dit moment kunt u geen toepassings pakketten gebruiken met een Azure Storage-account dat is geconfigureerd met [firewall regels](../storage/common/storage-network-security.md).
> - Een Azure Storage account waarvoor een **hiërarchische naam ruimte** is ingesteld op **ingeschakeld** , kan niet worden gebruikt voor toepassings pakketten.

De batch-service gebruikt Azure Storage om uw toepassings pakketten op te slaan als blok-blobs. Er worden [kosten in rekening gebracht][storage_pricing] voor de blok-BLOB-gegevens en de grootte van elk pakket kan niet groter zijn dan de maximale grootte van de blok-blob. Zie [Azure Storage schaalbaarheids-en prestatie doelen voor opslag accounts](../storage/blobs/scalability-targets.md)voor meer informatie. Houd rekening met de grootte en het aantal van uw toepassings pakketten en verwijder regel matig afgeschafte pakketten om de kosten te minimaliseren.

### <a name="view-current-applications"></a>Huidige toepassingen weer geven
Als u de toepassingen wilt weer geven in uw batch-account, klikt u op het menu-item **toepassingen** in het menu links terwijl u uw **batch-account**bekijkt.

![Tegel toepassingen][2]

Als u deze menu optie selecteert, wordt het venster **toepassingen** geopend:

![Toepassingen weergeven][3]

In dit venster wordt de ID weer gegeven van elke toepassing in uw account en de volgende eigenschappen:

* **Pakketten**: het aantal versies dat is gekoppeld aan deze toepassing.
* **Standaard versie**: de versie van de toepassing die is geïnstalleerd als u geen versie aangeeft wanneer u de toepassing voor een groep opgeeft. Deze instelling is optioneel.
* **Updates toestaan**: de waarde die aangeeft of pakket updates, verwijderingen en toevoegingen zijn toegestaan. Als dit is ingesteld op **Nee**, worden pakket updates en verwijderingen uitgeschakeld voor de toepassing. Alleen nieuwe versies van toepassings pakketten kunnen worden toegevoegd. De standaard waarde is **Ja**.

Als u de bestands structuur van het toepassings pakket op het reken knooppunt wilt zien, gaat u naar uw batch-account in de portal. Navigeer vanuit uw batch-account naar **groepen**. Selecteer de pool met de reken knooppunten waarin u bent geïnteresseerd.

![Knoop punten in pool][13]

Wanneer u de pool hebt geselecteerd, gaat u naar het reken knooppunt waarop het toepassings pakket is geïnstalleerd. Van daaruit bevinden de details van het toepassings pakket zich in de map **toepassingen** . Extra mappen op het berekenings knooppunt bevatten andere bestanden, zoals Start taken, uitvoer bestanden, fout uitvoer, enzovoort.

![Bestanden op het knoop punt][14]

### <a name="view-application-details"></a>Details van toepassing weer geven
Als u de details van een toepassing wilt bekijken, selecteert u de toepassing in het venster **toepassingen** .

![Toepassingsgegevens][4]

In de toepassings Details kunt u de volgende instellingen voor uw toepassing configureren.

* **Updates toestaan**: Geef op of de toepassings pakketten kunnen worden bijgewerkt of verwijderd. Zie ' een toepassings pakket bijwerken of verwijderen ' verderop in dit artikel.
* **Standaard versie**: Geef een standaard toepassings pakket op dat moet worden geïmplementeerd op Compute-knoop punten.
* **Weergave naam**: Geef een beschrijvende naam op die door uw batch-oplossing kan worden gebruikt wanneer er informatie over de toepassing wordt weer gegeven, bijvoorbeeld in de gebruikers interface van een service die u aan uw klanten verstrekt via batch.

### <a name="add-a-new-application"></a>Een nieuwe toepassing toevoegen
Als u een nieuwe toepassing wilt maken, voegt u een toepassings pakket toe en geeft u een nieuwe, unieke toepassings-ID op. Het eerste toepassings pakket dat u met de nieuwe toepassings-ID toevoegt, maakt ook de nieuwe toepassing.

Klik op **toepassingen**  >  **toevoegen**.

![De Blade nieuwe toepassing in Azure Portal][5]

Het venster **nieuwe toepassing** bevat de volgende velden om de instellingen van uw nieuwe toepassing en toepassings pakket op te geven.

**Toepassings-id**

In dit veld wordt de ID van de nieuwe toepassing opgegeven, die is onderhevig aan de standaard regels voor validatie van de Azure Batch-ID. De regels voor het opgeven van een toepassings-ID zijn als volgt:

* Op Windows-knoop punten kan de ID een wille keurige combi natie van alfanumerieke tekens, afbreek streepjes en Under scores bevatten. Op Linux-knoop punten zijn alleen alfanumerieke tekens en onderstrepings rechten toegestaan.
* Mag niet meer dan 64 tekens bevatten.
* Moet uniek zijn binnen het batch-account.
* Is hoofdletter gevoelig en is niet hoofdletter gevoeligheid.

**Versie**

Dit veld bevat de versie van het toepassings pakket dat u uploadt. Versie teken reeksen zijn onderhevig aan de volgende validatie regels:

* Op Windows-knoop punten kan de versie teken reeks een wille keurige combi natie van alfanumerieke tekens, afbreek streepjes, onderscores en punten bevatten. Op Linux-knoop punten mag de versie teken reeks alleen alfanumerieke tekens en onderstrepings streepjes bevatten.
* Mag niet meer dan 64 tekens bevatten.
* Moet uniek zijn binnen de toepassing.
* Zijn case-behoud en niet hoofdletter gevoelig.

**Toepassings pakket**

In dit veld geeft u het zip-bestand op dat de binaire bestanden van de toepassing bevat en de ondersteunings bestands die vereist zijn voor het uitvoeren van de toepassing. Klik op het vak **Selecteer een bestand** of het mappictogram om naar te bladeren en selecteer een zip-bestand met de bestanden van uw toepassing.

Nadat u een bestand hebt geselecteerd, klikt u op **OK** om te beginnen met uploaden naar Azure Storage. Wanneer de upload bewerking is voltooid, wordt in de portal een melding weer gegeven. Afhankelijk van de grootte van het bestand dat u uploadt en de snelheid van uw netwerk verbinding, kan deze bewerking enige tijd duren.

> [!WARNING]
> Sluit het **nieuwe toepassings** venster niet voordat de upload bewerking is voltooid. Hiermee wordt het upload proces gestopt.
> 
> 

### <a name="add-a-new-application-package"></a>Een nieuw toepassings pakket toevoegen
Als u een toepassings pakket versie voor een bestaande toepassing wilt toevoegen, selecteert u een toepassing in de Windows- **toepassingen** en klikt u op **pakketten**  >  **toevoegen**.

![De Blade toepassings pakket toevoegen in Azure Portal][8]

Zoals u ziet, worden de velden overeenkomen met die van het **nieuwe toepassings** venster, maar wordt het vak **toepassings-id** uitgeschakeld. Geef, net als voor de nieuwe toepassing, de **versie** van het nieuwe pakket op, blader naar uw **toepassings pakket** . zip-bestand en klik vervolgens op **OK** om het pakket te uploaden.

### <a name="update-or-delete-an-application-package"></a>Een toepassings pakket bijwerken of verwijderen
Als u een bestaand toepassings pakket wilt bijwerken of verwijderen, opent u de Details voor de toepassing, klikt u op **pakketten**, klikt u op het **beletsel teken** in de rij van het toepassings pakket dat u wilt wijzigen en selecteert u de actie die u wilt uitvoeren.

![Een pakket bijwerken of verwijderen in Azure Portal][7]

**Bijwerken**

Wanneer u op **bijwerken**klikt, wordt de Windows **Update-pakket** weer gegeven. Dit venster is vergelijkbaar met het venster **Nieuw toepassings pakket** , maar alleen het selectie veld pakket is ingeschakeld, zodat u een nieuw zip-bestand kunt opgeven dat u wilt uploaden.

![De Blade pakket bijwerken in Azure Portal][11]

**Verwijderen**

Wanneer u op **verwijderen**klikt, wordt u gevraagd om het verwijderen van de pakket versie te bevestigen en wordt het pakket verwijderd uit Azure Storage. Als u de standaard versie van een toepassing verwijdert, wordt de **standaard versie** -instelling voor de toepassing verwijderd.

![Toepassing verwijderen][12]

## <a name="install-applications-on-compute-nodes"></a>Toepassingen op reken knooppunten installeren
Nu u hebt geleerd hoe u toepassings pakketten met de Azure Portal kunt beheren, kunnen we bespreken hoe u ze implementeert in reken knooppunten en ze uitvoert met batch taken.

### <a name="install-pool-application-packages"></a>Groeps toepassings pakketten installeren
Als u een toepassings pakket wilt installeren op alle reken knooppunten in een pool, geeft u een of meer toepassings pakket *verwijzingen* op voor de groep. De toepassings pakketten die u voor een groep opgeeft, worden op elk reken knooppunt geïnstalleerd wanneer dat knoop punt aan de pool wordt toegevoegd en wanneer het knoop punt opnieuw wordt opgestart of een installatie kopie wordt gemaakt.

Geef in batch .NET een of meer [CloudPool][net_cloudpool]op. [ApplicationPackageReferences][net_cloudpool_pkgref] wanneer u een nieuwe groep maakt of voor een bestaande groep. De klasse [ApplicationPackageReference][net_pkgref] geeft een toepassings-id en versie op die op de reken knooppunten van een pool moet worden geïnstalleerd.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Als een implementatie van een toepassings pakket om welke reden dan ook mislukt, markeert de batch-service het knoop punt [onbruikbaar][net_nodestate]en worden er geen taken gepland voor uitvoering op dat knoop punt. In dit geval moet u het knoop punt **opnieuw opstarten** om de implementatie van het pakket opnieuw te starten. Wanneer het knoop punt opnieuw wordt gestart, wordt de taak planning ook opnieuw ingeschakeld op het knoop punt.
> 
> 

### <a name="install-task-application-packages"></a>Taak toepassings pakketten installeren
Net als bij een pool geeft u toepassings pakket *verwijzingen* op voor een taak. Wanneer een taak is gepland om te worden uitgevoerd op een knoop punt, wordt het pakket gedownload en geëxtraheerd net voordat de opdracht regel van de taak wordt uitgevoerd. Als een opgegeven pakket en versie al op het knoop punt zijn geïnstalleerd, wordt het pakket niet gedownload en wordt het bestaande pakket gebruikt.

Als u een taak toepassings pakket wilt installeren, moet u de [CloudTask][net_cloudtask]van de taak configureren. Eigenschap [ApplicationPackageReferences][net_cloudtask_pkgref] :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>De geïnstalleerde toepassingen uitvoeren
De pakketten die u voor een groep of taak hebt opgegeven, worden gedownload en geëxtraheerd naar een benoemde map binnen het `AZ_BATCH_ROOT_DIR` knoop punt. Batch maakt ook een omgevings variabele die het pad naar de benoemde map bevat. De opdracht regels van uw taak gebruiken deze omgevings variabele bij het verwijzen naar de toepassing op het knoop punt. 

Op Windows-knoop punten heeft de variabele de volgende indeling:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Op Linux-knoop punten is de indeling iets anders. Punten (.), afbreek streepjes (-) en hekjes (#) worden afgevlakt tot onderstrepings tekens in de omgevings variabele. Houd er ook rekening mee dat het hoofdletter gebruik van de toepassings-ID behouden blijft. Bijvoorbeeld:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`en `version` zijn waarden die overeenkomen met de versie van de toepassing en het pakket die u voor de implementatie hebt opgegeven. Als u bijvoorbeeld hebt opgegeven dat versie 2,7 van de toepassings *blender* moet worden geïnstalleerd op Windows-knoop punten, gebruiken de opdracht regels van de taak deze omgevings variabele voor toegang tot de bestanden:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Op Linux-knoop punten geeft u de omgevings variabele op in deze indeling. De punten (.), afbreek streepjes (-) en hekjes (#) afvlakken tot onderstrepings tekens en het hoofdletter gebruik van de toepassings-ID behouden:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Wanneer u een toepassings pakket uploadt, kunt u een standaard versie opgeven om te implementeren op uw reken knooppunten. Als u een standaard versie voor een toepassing hebt opgegeven, kunt u het versie achtervoegsel weglaten wanneer u verwijst naar de toepassing. U kunt de standaard versie van de toepassing opgeven in de Azure Portal, in het venster **toepassingen** , zoals wordt weer gegeven in [toepassingen uploaden en beheren](#upload-and-manage-applications).

Als u bijvoorbeeld ' 2,7 ' hebt ingesteld als de standaard versie voor de toepassing *blender*en uw taken verwijzen naar de volgende omgevings variabele, wordt versie 2,7 van uw Windows-knoop punten uitgevoerd:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Het volgende code fragment toont een voor beeld van een opdracht regel van de taak waarmee de standaard versie van de *blender* -toepassing wordt gestart:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zie [omgevings instellingen voor taken](jobs-and-tasks.md#environment-settings-for-tasks)voor meer informatie over de instellingen van de omgeving voor het berekenen van knoop punten. 

## <a name="update-a-pools-application-packages"></a>De toepassingspakketten van een groep bijwerken
Als een bestaande pool al is geconfigureerd met een toepassings pakket, kunt u een nieuw pakket voor de groep opgeven. Als u een nieuwe pakket verwijzing voor een groep opgeeft, is het volgende van toepassing:

* De batch-service installeert het zojuist opgegeven pakket op alle nieuwe knoop punten die lid zijn van de groep en op elk bestaand knoop punt dat opnieuw wordt opgestart of een nieuwe installatie kopie heeft gemaakt.
* Reken knooppunten die zich al in de groep bevinden wanneer u de pakket verwijzingen bijwerkt, installeren het nieuwe toepassings pakket niet automatisch. Deze reken knooppunten moeten opnieuw worden opgestart of worden geimageeerd om het nieuwe pakket te ontvangen.
* Wanneer een nieuw pakket wordt geïmplementeerd, worden de nieuwe toepassings pakket verwijzingen door de gemaakte omgevings variabelen weer gegeven.

In dit voor beeld heeft de bestaande pool versie 2,7 van de toepassing voor *blender* die is geconfigureerd als een van de [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Als u de knoop punten van de pool wilt bijwerken met versie 2.76 b, geeft u een nieuwe [ApplicationPackageReference][net_pkgref] op met de nieuwe versie en voert u de wijziging door.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Nu de nieuwe versie is geconfigureerd, installeert de batch-Service versie 2.76 b naar een *Nieuw* knoop punt dat lid is van de groep. Als u 2.76 b wilt installeren op de knoop punten die zich *al* in de groep bevinden, moet u deze opnieuw opstarten of de installatie kopie ervan terugzetten. Houd er rekening mee dat opnieuw gestarte knoop punten de bestanden van eerdere pakket implementaties behouden.

## <a name="list-the-applications-in-a-batch-account"></a>De toepassingen in een batch-account weer geven
U kunt de toepassingen en hun pakketten in een batch-account weer geven met behulp van de [ApplicationOperations][net_appops]. Methode [ListApplicationSummaries][net_appops_listappsummaries] .

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Inpakken
Met toepassings pakketten kunt u uw klanten helpen bij het selecteren van de toepassingen voor hun taken en de exacte versie opgeven die moet worden gebruikt bij het verwerken van taken met uw batch-service. U kunt uw klanten ook de mogelijkheid bieden om hun eigen toepassingen te uploaden en bij te houden in uw service.

## <a name="next-steps"></a>Volgende stappen
* De [batch-rest API][api_rest] biedt ook ondersteuning voor het werken met toepassings pakketten. Zie bijvoorbeeld het element [applicationPackageReferences][rest_add_pool_with_packages] in [een groep toevoegen aan een account][rest_add_pool] voor informatie over het opgeven van pakketten die moeten worden geïnstalleerd met behulp van de rest API. Zie [toepassingen][rest_applications] voor meer informatie over het verkrijgen van toepassings gegevens met behulp van de Batch-rest API.
* Meer informatie over het programmatisch [beheren van Azure batch accounts en quota's met Batch Management .net](batch-management-dotnet.md). Met de [Batch Management .net][api_net_mgmt] -bibliotheek kunt u functies voor het maken en verwijderen van accounts inschakelen voor uw batch toepassing of service.

[api_net]: /dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: /rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_appops_listappsummaries]: /dotnet/api/microsoft.azure.batch.applicationoperations
[net_cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudpool_pkgref]: /dotnet/api/microsoft.azure.batch.cloudpool
[net_cloudtask]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_cloudtask_pkgref]: /dotnet/api/microsoft.azure.batch.cloudtask
[net_nodestate]: /dotnet/api/microsoft.azure.batch.computenode
[net_pkgref]: /dotnet/api/microsoft.azure.batch.applicationpackagereference
[portal]: https://portal.azure.com
[rest_applications]: /rest/api/batchservice/application
[rest_add_pool]: /rest/api/batchservice/pool/add
[rest_add_pool_with_packages]: /rest/api/batchservice/pool/add#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram van toepassings pakketten op hoog niveau"
[2]: ./media/batch-application-packages/app_pkg_02.png "De tegel toepassingen in Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "De Blade toepassingen in Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "De Blade toepassings Details in Azure Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "De Blade nieuwe toepassing in Azure Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "De vervolg keuzelijst met pakketten bijwerken of verwijderen in Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nieuwe blade voor toepassings pakket in Azure Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Waarschuwing voor geen gekoppeld opslag account"
[10]: ./media/batch-application-packages/app_pkg_10.png "Kies Blade opslag account in Azure Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "De Blade pakket bijwerken in Azure Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Bevestigings dialoogvenster voor pakket verwijderen in Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Gegevens van reken knooppunten in Azure Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Bestanden op het reken knooppunt dat wordt weer gegeven in Azure Portal"
