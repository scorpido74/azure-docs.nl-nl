---
title: Toepassingspakketten installeren op compute nodes - Azure Batch | Microsoft Documenten
description: Gebruik de functie toepassingspakketten van Azure Batch om eenvoudig meerdere toepassingen en versies voor installatie op batchcomputenodes te beheren.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30301832381bdc7b5f001eec2c449c571f9fd671
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086219"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Toepassingen implementeren om knooppunten te berekenen met batchtoepassingspakketten

De functie toepassingspakketten van Azure Batch biedt eenvoudig beheer van taaktoepassingen en de implementatie ervan naar de compute-knooppunten in uw groep. Met toepassingspakketten u meerdere versies van de toepassingen die uw taken uitvoeren uploaden en beheren, inclusief hun ondersteunende bestanden. U vervolgens automatisch een of meer van deze toepassingen implementeren naar de compute nodes in uw groep.

In dit artikel leert u hoe u toepassingspakketten uploadt en beheert in de Azure-portal. Vervolgens leert u hoe u ze installeren op de compute-knooppunten van een groep met de [Batch .NET-bibliotheek.][api_net]

> [!NOTE]
> Toepassingspakketten worden ondersteund in alle Batch-pools die na 5 juli 2017 zijn gemaakt. De pakketten worden ondersteund in Batch-pools die zijn gemaakt tussen 10 maart 2016 en 5 juli 2017, maar alleen als de pool is gemaakt met behulp van een cloudservice-configuratie. Batch-pools die zijn gemaakt vóór 10 maart 2016 bieden geen ondersteuning voor toepassingspakketten.
>
> De API's voor het maken en beheren van toepassingspakketten maken deel uit van de [Batch Management .NET-bibliotheek.][api_net_mgmt] De API's voor het installeren van toepassingspakketten op een compute node maken deel uit van de [Batch .NET-bibliotheek.][api_net] Vergelijkbare functies bevinden zich in de beschikbare Batch-API's voor andere talen. 
>
> De hier beschreven functie toepassingspakketten vervangt de batch-apps-functie die beschikbaar is in eerdere versies van de service.

## <a name="application-package-requirements"></a>Vereisten voor toepassingspakket
Als u toepassingspakketten wilt gebruiken, moet u [een Azure Storage-account koppelen](#link-a-storage-account) aan uw Batch-account.

## <a name="about-applications-and-application-packages"></a>Over toepassingen en toepassingspakketten
Binnen Azure Batch verwijst een *toepassing* naar een set versies die automatisch kunnen worden gedownload naar de compute nodes in uw groep. Een *toepassingspakket* verwijst naar een *specifieke set* van deze binaire bestanden en vertegenwoordigt een bepaalde *versie* van de toepassing.

![Diagram op hoog niveau van toepassingen en toepassingspakketten][1]

### <a name="applications"></a>Toepassingen
Een toepassing in Batch bevat een of meer toepassingspakketten en geeft configuratieopties voor de toepassing op. Een toepassing kan bijvoorbeeld de standaardversie van het toepassingspakket opgeven die moet worden geïnstalleerd op compute-knooppunten en of de pakketten kunnen worden bijgewerkt of verwijderd.

### <a name="application-packages"></a>Toepassingspakketten
Een toepassingspakket is een .zip-bestand dat de toepassingsbinaire bestanden bevat en ondersteunende bestanden die nodig zijn voor het uitvoeren van de toepassing. Elk toepassingspakket vertegenwoordigt een specifieke versie van de toepassing.

U toepassingspakketten opgeven op de pool- en taakniveaus. U een of meer van deze pakketten en (optioneel) een versie opgeven wanneer u een groep of taak maakt.

* Er worden **pakketten voor het groepen** geïmplementeerd op *elk* knooppunt in de groep. Toepassingen worden geïmplementeerd wanneer een knooppunt lid wordt van een groep en wanneer deze opnieuw wordt opgestart of opnieuw wordt weergegeven.
  
    Toepassingspakketten groeperen zijn geschikt wanneer alle knooppunten in een groep de taken van een taak uitvoeren. U een of meer toepassingspakketten opgeven wanneer u een groep maakt en u de pakketten van een bestaande groep toevoegen of bijwerken. Als u de toepassingspakketten van een bestaande groep bijwerkt, moet u de knooppunten opnieuw starten om het nieuwe pakket te installeren.
* **Taaktoepassingspakketten** worden alleen geïmplementeerd op een compute node die is gepland om een taak uit te voeren, net voordat de opdrachtregel van de taak wordt uitgevoerd. Als het opgegeven toepassingspakket en de opgegeven versie al op het knooppunt staan, wordt het niet opnieuw geïmplementeerd en wordt het bestaande pakket gebruikt.
  
    Taaktoepassingspakketten zijn handig in gedeelde groepomgevingen, waar verschillende taken op één groep worden uitgevoerd en de groep niet wordt verwijderd wanneer een taak is voltooid. Als de job minder taken dan knooppunten in de groep heeft, kunnen toepassingspakketten van taken gegevensoverdracht minimaliseren omdat uw toepassing alleen wordt geïmplementeerd op de knooppunten die taken uitvoeren.
  
    Andere scenario's die kunnen profiteren van taaktoepassingspakketten zijn taken die een grote toepassing uitvoeren, maar voor slechts een paar taken. Een pre-processing fase of een samenvoegtaak, waarbij de pre-processing of merge-toepassing zwaar is, kan bijvoorbeeld profiteren van het gebruik van taaktoepassingspakketten.

> [!IMPORTANT]
> Er zijn beperkingen op het aantal toepassingen en toepassingspakketten binnen een Batch-account en op de maximale grootte van het toepassingspakket. Zie [Quota en limieten voor de Azure Batch-service](batch-quota-limit.md) voor meer informatie over deze limieten.
> 
> 

### <a name="benefits-of-application-packages"></a>Voordelen van toepassingspakketten
Toepassingspakketten kunnen de code in uw Batch-oplossing vereenvoudigen en de overhead verlagen die nodig is om de toepassingen te beheren die uw taken uitvoeren.

Met toepassingspakketten hoeft de starttaak van uw groep geen lange lijst met afzonderlijke bronbestanden op te geven die op de knooppunten moeten worden geïnstalleerd. U hoeft niet handmatig meerdere versies van uw toepassingsbestanden te beheren in Azure Storage of op uw knooppunten. En u hoeft zich geen zorgen te maken over het genereren van [SAS-URL's](../storage/common/storage-dotnet-shared-access-signature-part-1.md) om toegang te bieden tot de bestanden in uw opslagaccount. Batch werkt op de achtergrond met Azure Storage om toepassingspakketten op te slaan en te implementeren in compute nodes.

> [!NOTE] 
> De totale grootte van een begintaak moet kleiner zijn dan of gelijk zijn aan 32.768 tekens, inclusief bronbestanden en omgevingsvariabelen. Als uw starttaak deze limiet overschrijdt, is het gebruik van toepassingspakketten een andere optie. U ook een archief met rits sluiting met uw bronbestanden maken, het uploaden als een blob naar Azure Storage en het vervolgens uitderitsen van de opdrachtregel van uw starttaak. 
>
>

## <a name="upload-and-manage-applications"></a>Toepassingen uploaden en beheren
U de [Azure-portal][portal] of de API's voor batchbeheer gebruiken om de toepassingspakketten in uw Batch-account te beheren. In de volgende secties laten we eerst zien hoe je een Opslagaccount koppelen, vervolgens het toevoegen van applicaties en pakketten en het beheren ervan met de portal.

### <a name="link-a-storage-account"></a>Een opslagaccount koppelen
Als u toepassingspakketten wilt gebruiken, moet u eerst een [Azure Storage-account](batch-api-basics.md#azure-storage-account) koppelen aan uw Batch-account. Als u nog geen opslagaccount hebt geconfigureerd, wordt in de Azure-portal een waarschuwing weergegeven wanneer u voor het eerst op **Toepassingen** in uw Batch-account klikt.



![Waarschuwing 'Geen opslagaccount geconfigureerd' in Azure-portal][9]

De Batch-service gebruikt het bijbehorende opslagaccount om uw toepassingspakketten op te slaan. Nadat u de twee accounts hebt gekoppeld, kan Batch de pakketten die zijn opgeslagen in het gekoppelde opslagaccount automatisch implementeren naar uw compute nodes. Als u een opslagaccount wilt koppelen aan uw Batch-account, klikt u op **Opslagaccount** in het **waarschuwingsvenster** en klikt u vervolgens nogmaals op **Opslagaccount.**

![Het opslagaccountblad kiezen in Azure-portal][10]

We raden u aan een opslagaccount aan te maken *dat specifiek* voor gebruik met uw Batch-account is gebruikt en deze hier te selecteren. Nadat u een opslagaccount hebt gemaakt, u het vervolgens koppelen aan uw Batch-account met behulp van het venster **Opslagaccount.**

> [!IMPORTANT] 
> - Momenteel u geen toepassingspakketten gebruiken met een Azure Storage-account dat is geconfigureerd met [firewallregels.](../storage/common/storage-network-security.md)
> - Een Azure Storage-account met **hiërarchische naamruimte** ingesteld op **Ingeschakeld** kan niet worden gebruikt voor toepassingspakketten.

De Batch-service maakt gebruik van Azure Storage om uw toepassingspakketten op te slaan als blokblobs. Er worden [normaalkosten in rekening gebracht][storage_pricing] voor de blokblobgegevens en de grootte van elk pakket mag de maximale blokblobgrootte niet overschrijden. Zie [schaalbaarheid en prestatiedoelen voor Azure Storage voor opslagaccounts voor](../storage/blobs/scalability-targets.md)meer informatie. Zorg ervoor dat u rekening houdt met de grootte en het aantal van uw toepassingspakketten en verwijder periodiek afgeschafte pakketten om de kosten te minimaliseren.

### <a name="view-current-applications"></a>Huidige toepassingen weergeven
Als u de toepassingen in uw Batch-account wilt bekijken, klikt u op het **menuitem Toepassingen** in het linkermenu terwijl u uw **Batch-account**bekijkt.

![Tegel Toepassingen][2]

Als u deze menuoptie selecteert, wordt het venster **Toepassingen** geopend:

![Toepassingen weergeven][3]

In dit venster wordt de id van elke toepassing in uw account en de volgende eigenschappen weergegeven:

* **Pakketten:** het aantal versies dat aan deze toepassing is gekoppeld.
* **Standaardversie**: de toepassingsversie die is geïnstalleerd als u geen versie aangeeft wanneer u de toepassing voor een groep opgeeft. Deze instelling is optioneel.
* **Updates toestaan:** de waarde die aangeeft of pakketupdates, verwijderingen en toevoegingen zijn toegestaan. Als dit is ingesteld op **Nee,** worden pakketupdates en verwijderingen uitgeschakeld voor de toepassing. Alleen nieuwe versies van het toepassingspakket kunnen worden toegevoegd. De standaardinstelling is **Ja**.

Als u de bestandsstructuur van het toepassingspakket op uw compute-knooppunt wilt zien, navigeert u naar uw Batch-account in de portal. Navigeer vanuit uw Batch-account naar **Pools.** Selecteer de groep met de compute node(s) waarin u geïnteresseerd bent.

![Knooppunten in groep][13]

Nadat u uw groep hebt geselecteerd, navigeert u naar het compute-knooppunt waarop het toepassingspakket is geïnstalleerd. Van daaruit bevinden de details van het toepassingspakket zich in de **toepassingsmap.** Extra mappen op het compute node bevatten andere bestanden, zoals starttaken, uitvoerbestanden, foutuitvoer, enz.

![Bestanden op knooppunt][14]

### <a name="view-application-details"></a>Toepassingsgegevens weergeven
Als u de details voor een toepassing wilt bekijken, selecteert u de toepassing in het venster **Toepassingen.**

![Toepassingsgegevens][4]

In de toepassingsgegevens u de volgende instellingen voor uw toepassing configureren.

* **Updates toestaan:** geef op of de toepassingspakketten kunnen worden bijgewerkt of verwijderd. Zie 'Een toepassingspakket bijwerken of verwijderen' later in dit artikel.
* **Standaardversie**: Geef een standaardtoepassingspakket op dat moet worden geïmplementeerd voor compute-knooppunten.
* **Weergavenaam:** geef een vriendelijke naam op die uw Batch-oplossing kan gebruiken wanneer deze informatie over de toepassing weergeeft, bijvoorbeeld in de gebruikersinterface van een service die u via Batch aan uw klanten levert.

### <a name="add-a-new-application"></a>Een nieuwe toepassing toevoegen
Als u een nieuwe toepassing wilt maken, voegt u een toepassingspakket toe en geeft u een nieuwe, unieke toepassings-id op. Het eerste toepassingspakket dat u met de nieuwe toepassings-ID toevoegt, maakt ook de nieuwe toepassing.

Klik**op Toevoegen van** **toepassingen** > .

![Nieuw toepassingsblad in Azure-portal][5]

In **het venster Nieuwe toepassing** worden de volgende velden weergegeven om de instellingen van uw nieuwe toepassings- en toepassingspakket op te geven.

**Toepassings-id**

In dit veld wordt de id van uw nieuwe toepassing opgegeven, die onderworpen is aan de standaard azure batch-id-validatieregels. De regels voor het verstrekken van een aanvraag-id zijn als volgt:

* Op Windows-knooppunten kan de ID elke combinatie van alfanumerieke tekens, koppeltekens en underscores bevatten. Op Linux-knooppunten zijn alleen alfanumerieke tekens en underscores toegestaan.
* Kan niet meer dan 64 tekens bevatten.
* Moet uniek zijn binnen het Batch-account.
* Is case-preserving en case-ongevoelig.

**Versie**

In dit veld wordt de versie van het toepassingspakket opgegeven dat u uploadt. Voor versietekenreeksen gelden de volgende validatieregels:

* Op Windows-knooppunten kan de versietekenreeks elke combinatie van alfanumerieke tekens, koppeltekens, underscores en perioden bevatten. Op Linux-knooppunten kan de versietekenreeks alleen alfanumerieke tekens en underscores bevatten.
* Kan niet meer dan 64 tekens bevatten.
* Moet uniek zijn binnen de applicatie.
* Zijn case-preserving en case-ongevoelig.

**Toepassingspakket**

In dit veld wordt het .zip-bestand opgegeven dat de toepassingsbinaire bestanden bevat en ondersteunende bestanden die nodig zijn om de toepassing uit te voeren. Klik **op het vak Een bestand selecteren** of het mappictogram om naar te bladeren en selecteer een .zip-bestand met de bestanden van uw toepassing.

Nadat u een bestand hebt geselecteerd, klikt u op **OK** om de upload naar Azure Storage te starten. Wanneer de uploadbewerking is voltooid, wordt in de portal een melding weergegeven. Afhankelijk van de grootte van het bestand dat u uploadt en de snelheid van uw netwerkverbinding, kan deze bewerking enige tijd duren.

> [!WARNING]
> Sluit het **venster Nieuwe toepassing** niet voordat de uploadbewerking is voltooid. Hierdoor wordt het uploadproces gestopt.
> 
> 

### <a name="add-a-new-application-package"></a>Een nieuw toepassingspakket toevoegen
Als u een toepassingspakketversie voor een bestaande toepassing wilt toevoegen, selecteert u een toepassing in de vensters **Toepassingen** en klikt u op **Toevoegen** > **pakketten**.

![Het blad van het toepassingspakket toevoegen in Azure-portal][8]

Zoals u zien, komen de velden overeen met die van het **toepassingsvenster Nieuw,** maar is het vak **Toepassings-id** uitgeschakeld. Zoals u deed voor de nieuwe toepassing, geeft u de **versie** voor uw nieuwe pakket op, bladert u naar uw **toepassingspakket** .zip-bestand en klikt u op **OK** om het pakket te uploaden.

### <a name="update-or-delete-an-application-package"></a>Een toepassingspakket bijwerken of verwijderen
Als u een bestaand toepassingspakket wilt bijwerken of verwijderen, opent u de details voor de toepassing, klikt u op **Pakketten,** klikt u op de **ellips** in de rij van het toepassingspakket dat u wilt wijzigen en selecteert u de actie die u wilt uitvoeren.

![Pakket bijwerken of verwijderen in Azure-portal][7]

**Update**

Wanneer u op **Bijwerken**klikt, wordt het **updatepakket** weergegeven. Dit venster is vergelijkbaar met het venster **Nieuwe toepassingspakket,** maar alleen het selectieveld voor pakketten is ingeschakeld, zodat u een nieuw ZIP-bestand opgeven om te uploaden.

![Pakketblad bijwerken in Azure-portal][11]

**Verwijderen**

Wanneer u op **Verwijderen**klikt, wordt u gevraagd de verwijdering van de pakketversie te bevestigen en verwijdert Batch het pakket uit Azure Storage. Als u de standaardversie van een toepassing verwijdert, wordt de **standaardversieinstelling** voor de toepassing verwijderd.

![Toepassing verwijderen][12]

## <a name="install-applications-on-compute-nodes"></a>Toepassingen installeren op compute nodes
Nu u hebt geleerd hoe u toepassingspakketten beheren met de Azure-portal, kunnen we bespreken hoe u deze implementeren om knooppunten te berekenen en ze uit te voeren met Batch-taken.

### <a name="install-pool-application-packages"></a>Pakketten voor pooltoepassingen installeren
Als u een toepassingspakket wilt installeren op alle compute-knooppunten in een groep, geeft u een of meer *verwijzingen* naar het toepassingspakket voor de groep op. De toepassingspakketten die u opgeeft voor een groep, worden op elk compute-knooppunt geïnstalleerd wanneer dat knooppunt lid wordt van de groep en wanneer het knooppunt opnieuw wordt opgestart of opnieuw wordt weergegeven.

Geef in Batch .NET een of meer [CloudPool op][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] wanneer u een nieuwe groep maakt of voor een bestaande groep. De klasse [ApplicationPackageReference][net_pkgref] geeft een toepassings-id en versie op die moet worden geïnstalleerd op de compute-knooppunten van een groep.

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
> Als een implementatie van een toepassingspakket om welke reden dan ook mislukt, markeert de batchservice het knooppunt [onbruikbaar][net_nodestate]en worden er geen taken gepland voor uitvoering op dat knooppunt. Start in dit geval het knooppunt **opnieuw** om de implementatie van het pakket opnieuw te starten. Als u het knooppunt opnieuw start, u ook opnieuw taakplanning op het knooppunt uitvoeren.
> 
> 

### <a name="install-task-application-packages"></a>Takentoepassingspakketten installeren
Net als bij een groep geeft u referenties voor het toepassingspakket *voor* een taak op. Wanneer een taak is gepland om op een knooppunt uit te voeren, wordt het pakket gedownload en geëxtraheerd vlak voordat de opdrachtregel van de taak wordt uitgevoerd. Als een opgegeven pakket en versie al op het knooppunt is geïnstalleerd, wordt het pakket niet gedownload en wordt het bestaande pakket gebruikt.

Als u een taaktoepassingspakket wilt installeren, configureert u de [CloudTask][net_cloudtask]van de taak . [ApplicationPackageReferences,][net_cloudtask_pkgref] eigenschap:

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
De pakketten die u hebt opgegeven voor een groep of taak, worden `AZ_BATCH_ROOT_DIR` gedownload en geëxtraheerd naar een benoemde map binnen het knooppunt. Batch maakt ook een omgevingsvariabele die het pad naar de benoemde map bevat. Uw taakopdrachtregels gebruiken deze omgevingsvariabele wanneer u verwijst naar de toepassing op het knooppunt. 

Op Windows-knooppunten bevindt de variabele zich in de volgende indeling:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Op Linux-knooppunten is het formaat iets anders. Perioden (.), koppeltekens (-) en getaltekens (#) worden afgevlakt om te onderstrepen in de omgevingsvariabele. Houd er ook rekening mee dat het geval van de toepassings-ID behouden blijft. Bijvoorbeeld:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID`en `version` zijn waarden die overeenkomen met de toepassing en pakketversie die u hebt opgegeven voor implementatie. Als u bijvoorbeeld hebt opgegeven dat versie 2.7 van de *toepassingsblender* op Windows-knooppunten moet worden geïnstalleerd, gebruiken uw taakopdrachtregels deze omgevingsvariabele om toegang te krijgen tot de bestanden:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Geef op Linux-knooppunten de omgevingsvariabele in deze indeling op. De perioden (.), koppeltekens (-) en nummertekens (#) afvlakken om onderstreept te worden en het geval van de toepassings-id te behouden:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Wanneer u een toepassingspakket uploadt, u een standaardversie opgeven die moet worden geïmplementeerd op uw compute-knooppunten. Als u een standaardversie voor een toepassing hebt opgegeven, u het versieachtervoegsel weglaten wanneer u naar de toepassing verwijst. U de standaardtoepassingsversie opgeven in de Azure-portal in het venster **Toepassingen,** zoals weergegeven in [Toepassingen uploaden en beheren.](#upload-and-manage-applications)

Als u bijvoorbeeld '2.7' instelt als de standaardversie voor *toepassingsblender*en uw taken verwijzen naar de volgende omgevingsvariabele, worden versie 2.7 uitgevoerd door uw Windows-knooppunten:

`AZ_BATCH_APP_PACKAGE_BLENDER`

In het volgende codefragment wordt een opdrachtregel voor een voorbeeldtaak weergegeven waarmee de standaardversie van de *blendertoepassing* wordt gestart:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Zie [Omgevingsinstellingen voor taken](batch-api-basics.md#environment-settings-for-tasks) in het overzicht van de [batchfunctie](batch-api-basics.md) voor meer informatie over de omgevinginstellingen van compute node.
> 
> 

## <a name="update-a-pools-application-packages"></a>De toepassingspakketten van een groep bijwerken
Als een bestaande groep al is geconfigureerd met een toepassingspakket, u een nieuw pakket voor de groep opgeven. Als u een nieuwe pakketverwijzing voor een groep opgeeft, geldt het volgende:

* De batchservice installeert het nieuw opgegeven pakket op alle nieuwe knooppunten die lid worden van de groep en op bestaande knooppunten die opnieuw worden opgestart of opnieuw worden weergegeven.
* Compute-knooppunten die al in de groep zitten wanneer u de pakketverwijzingen bijwerkt, installeren het nieuwe toepassingspakket niet automatisch. Deze compute nodes moeten opnieuw worden opgestart of opnieuw worden weergegeven om het nieuwe pakket te ontvangen.
* Wanneer een nieuw pakket wordt geïmplementeerd, weerspiegelen de gemaakte omgevingsvariabelen de verwijzingen naar het nieuwe toepassingspakket.

In dit voorbeeld heeft de bestaande groep versie 2.7 van de *blendertoepassing* geconfigureerd als een van de [CloudPool.][net_cloudpool] [ApplicationPackageReferenties][net_cloudpool_pkgref]. Als u de knooppunten van de groep wilt bijwerken met versie 2.76b, geeft u een nieuwe [ApplicationPackageReference][net_pkgref] op met de nieuwe versie en verbindt u de wijziging.

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

Nu de nieuwe versie is geconfigureerd, installeert de Batch-service versie 2.76b op elk *nieuw* knooppunt dat bij de pool komt. Als u 2.76b wilt installeren op de knooppunten die *al* in de groep zijn, start u ze opnieuw op of opnieuw. Houd er rekening mee dat opnieuw opgestarte knooppunten de bestanden van eerdere pakketimplementaties behouden.

## <a name="list-the-applications-in-a-batch-account"></a>De toepassingen in een Batch-account weergeven
U de toepassingen en hun pakketten in een Batch-account vermelden met behulp van De [Toepassingsbewerkingen][net_appops]. [ListApplicationSummaries][net_appops_listappsummaries] methode.

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
Met toepassingspakketten u uw klanten helpen bij het selecteren van de toepassingen voor hun taken en de exacte versie opgeven die u moet gebruiken bij het verwerken van taken met uw batchservice. U uw klanten ook de mogelijkheid bieden om hun eigen toepassingen in uw service te uploaden en bij te houden.

## <a name="next-steps"></a>Volgende stappen
* De [Batch REST API][api_rest] biedt ook ondersteuning voor het werken met toepassingspakketten. Zie bijvoorbeeld het element [applicationPackageReferences][rest_add_pool_with_packages] in [Een groep toevoegen aan een account][rest_add_pool] voor informatie over het opgeven van pakketten die u wilt installeren met behulp van de REST API. Zie [Toepassingen][rest_applications] voor meer informatie over het verkrijgen van toepassingsgegevens met behulp van de Batch REST API.
* Meer informatie over het programmatisch [beheren van Azure Batch-accounts en quota met Batch Management .NET](batch-management-dotnet.md). De [Batch Management .NET-bibliotheek][api_net_mgmt] kan functies voor het maken en verwijderen van een account voor uw Batch-toepassing of -service inschakelen.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagram op hoog niveau"
[2]: ./media/batch-application-packages/app_pkg_02.png "Tegel toepassingen in Azure-portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Het blad van toepassingen in Azure-portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blade van toepassingsgegevens in Azure-portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nieuw toepassingsblad in Azure-portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Vervolgkeuzelijst pakketten bijwerken of verwijderen in Azure-portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nieuw toepassingspakketblad in Azure-portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Waarschuwing voor gekoppeld opslagaccount"
[10]: ./media/batch-application-packages/app_pkg_10.png "Het opslagaccountblad kiezen in Azure-portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Pakketblad bijwerken in Azure-portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Dialoogvenster pakketbevestiging verwijderen in Azure-portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Gegevens over het gegevensomen in Azure-portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Bestanden op het compute-knooppunt dat wordt weergegeven in Azure-portal"
