---
title: Een bestaande uitvoerbare implementeren voor Azure Service Fabric
description: Meer informatie over het verpakken van een bestaande toepassing als een gast die uitvoerbaar is, zodat deze kan worden geïmplementeerd in een cluster van Servicefabric.
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c6c6bc0369593c177b74261da1fd8c15dd73fcb3
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520488"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>Een bestaande uitvoerbare fabric verpakken en implementeren

Wanneer u een bestaand uitvoerbaar als [gast uitvoerbaar](service-fabric-guest-executables-introduction.md)verpakt, u ervoor kiezen om een Visual Studio-projectsjabloon te gebruiken of het toepassingspakket handmatig te [maken.](#manually) Met Behulp van Visual Studio worden de structuur van het toepassingspakket en de manifestbestanden gemaakt door de nieuwe projectsjabloon voor u.

> [!TIP]
> De eenvoudigste manier om een bestaande Windows-uitvoerbare pakket in een dienst is het gebruik van Visual Studio en op Linux om Yeoman te gebruiken
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Visual Studio gebruiken om een bestaande uitvoerbare te verpakken en te implementeren

Visual Studio biedt een servicesjabloon service voor Service Fabric waarmee u een gast implementeren die kan worden uitgevoerd in een cluster van ServiceFabric.

1. Kies **Nieuw project** > **bestand**en maak een Service Fabric-toepassing.
2. Kies **Gast uitvoerbaar** als servicesjabloon.
3. Klik **op Bladeren** om de map met uw uitvoerbare map te selecteren en vul de rest van de parameters in om de service te maken.
   * *Codepakketgedrag*. Kan worden ingesteld om alle inhoud van uw map te kopiëren naar het Visual Studio Project, wat handig is als het uitvoerbare niet verandert. Als u verwacht dat de uitvoerbare te veranderen en wilt de mogelijkheid om op te halen nieuwe builds dynamisch, u ervoor kiezen om een koppeling naar de map plaats. U gekoppelde mappen gebruiken bij het maken van het toepassingsproject in Visual Studio. Hiermee wordt vanuit het project naar de bronlocatie gekoppeld, waardoor u de gast uitvoerbaar bijwerken in de bronbestemming. Deze updates worden onderdeel van het applicatiepakket op build.
   * *Het programma* geeft de uitvoerbare op die moet worden uitgevoerd om de service te starten.
   * *Argumenten* geeft de argumenten op die moeten worden doorgegeven aan de uitvoerbare. Het kan een lijst met parameters met argumenten zijn.
   * *WorkingFolder* geeft de werkmap op voor het proces dat wordt gestart. U drie waarden opgeven:
     * `CodeBase`hiermee wordt opgegeven dat de werkmap wordt ingesteld op`Code` de codemap in het toepassingspakket (map weergegeven in de voorgaande bestandsstructuur).
     * `CodePackage`hiermee wordt opgegeven dat de werkmap wordt ingesteld op`GuestService1Pkg` de hoofdmap van het toepassingspakket (weergegeven in de voorgaande bestandsstructuur).
     * `Work`hiermee wordt opgegeven dat de bestanden in een submap worden geplaatst die werk wordt genoemd.
4. Geef de service een naam en klik op **OK**.
5. Als uw service een eindpunt voor communicatie nodig heeft, u nu het protocol, de poort en het type toevoegen aan het bestand ServiceManifest.xml. Bijvoorbeeld: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`.
6. U het pakket nu gebruiken en actie publiceren tegen uw lokale cluster door de oplossing te debuggen in Visual Studio. Wanneer u klaar bent, u de toepassing publiceren naar een extern cluster of de oplossing controleren om de bronbesturingselement te beheren.
7. Lees [de hardlooptoepassing](#check-your-running-application) om te zien hoe u de uitvoerbare service van uw gast weergeven die wordt uitgevoerd in Service Fabric Explorer.

Zie [Uw eerste gastuitvoerbare toepassing maken met Visual Studio](quickstart-guest-app.md)voor een voorbeeldvan walkthrough.

### <a name="packaging-multiple-executables-with-visual-studio"></a>Meerdere executables verpakken met Visual Studio

U Visual Studio gebruiken om een toepassingspakket te maken dat meerdere gastuitvoerbare bestanden bevat. Nadat u de eerste gast hebt toegevoegd die uitvoerbaar is, klikt u met de rechtermuisknop op het toepassingsproject en selecteert u de **add->New Service Fabric-service** om het tweede gastuitvoerproject aan de oplossing toe te voegen.

> [!NOTE]
> Als u ervoor kiest om de bron in het Visual Studio-project te koppelen, zal het bouwen van de Visual Studio-oplossing ervoor zorgen dat uw toepassingspakket up-to-date is met wijzigingen in de bron.

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Yeoman gebruiken om een bestaande uitvoerbare op Linux te verpakken en te implementeren

De procedure voor het maken en implementeren van een gast uitvoerbaar op Linux is hetzelfde als het implementeren van een csharp of java applicatie.

1. Typ in een terminal `yo azuresfguest`.
2. Geef uw toepassing een naam.
3. Geef uw service een naam en geef de details, inclusief het pad van de uitvoerbare en de parameters waarmee deze moet worden aangeroepen.

Yeoman maakt een applicatiepakket met de juiste toepassings- en manifestbestanden samen met het installeren en verwijderen van scripts.

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Meerdere executables verpakken met Yeoman op Linux

Voer de volgende stappen uit als u nog een service wilt toevoegen aan een toepassing die al is gemaakt met `yo`:

1. Stel de directory in op de hoofdmap van de bestaande toepassing.  Bijvoorbeeld `cd ~/YeomanSamples/MyApplication` als `MyApplication` de toepassing is die is gemaakt door Yeoman.
2. Voer `yo azuresfguest:AddService` uit en geef de nodige details.

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>Handmatig een bestaande uitvoerbare pakket en implementeren

Het handmatig verpakken van een gast uitvoerbaar is gebaseerd op de volgende algemene stappen:

1. Maak de pakketmapstructuur.
2. Voeg de code- en configuratiebestanden van de toepassing toe.
3. Bewerk het servicemanifestbestand.
4. Bewerk het manifestbestand van de toepassing.

### <a name="create-the-package-directory-structure"></a>De pakketmapstructuur maken

U beginnen met het maken van de directorystructuur, zoals beschreven in [Package an Azure Service Fabric App](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps).

### <a name="add-the-applications-code-and-configuration-files"></a>Code- en configuratiebestanden van de toepassing toevoegen

Nadat u de directorystructuur hebt gemaakt, u de code- en configuratiebestanden van de toepassing toevoegen onder de code en config-mappen. U ook extra mappen of submappen maken onder de code of config-mappen.

Service Fabric `xcopy` doet een van de inhoud van de hoofdmap van de toepassing, dus er is geen vooraf gedefinieerde structuur om anders te gebruiken dan het maken van twee topmappen, code en instellingen. (U verschillende namen kiezen als u dat wilt. Meer details zijn in de volgende sectie.)

> [!NOTE]
> Zorg ervoor dat u alle bestanden en afhankelijkheden opneemt die de toepassing nodig heeft. Service Fabric kopieert de inhoud van het toepassingspakket op alle knooppunten in het cluster waar de services van de toepassing worden geïmplementeerd. Het pakket moet alle code bevatten die de toepassing moet uitvoeren. Ga er niet van uit dat de afhankelijkheden al zijn geïnstalleerd.
>
>

### <a name="edit-the-service-manifest-file"></a>Het servicemanifestbestand bewerken

De volgende stap is het bewerken van het servicemanifestbestand om de volgende informatie op te nemen:

* De naam van het servicetype. Dit is een ID die Service Fabric gebruikt om een service te identificeren.
* De opdracht die moet worden gebruikt om de toepassing te starten (ExeHost).
* Elk script dat moet worden uitgevoerd om de toepassing in te stellen (SetupEntrypoint).

Het volgende is een `ServiceManifest.xml` voorbeeld van een bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

De volgende secties gaan over de verschillende delen van het bestand die u moet bijwerken.

#### <a name="update-servicetypes"></a>Servicetypen bijwerken

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* Je elke naam kiezen `ServiceTypeName`die je wilt. De waarde wordt `ApplicationManifest.xml` in het bestand gebruikt om de service te identificeren.
* Geef `UseImplicitHost="true"` op. Dit kenmerk vertelt Service Fabric dat de service is gebaseerd op een zelfstandige app, dus alles wat Service Fabric hoeft te doen is om het te starten als een proces en de gezondheid te controleren.

#### <a name="update-codepackage"></a>CodePakket bijwerken
Het element CodePackage geeft de locatie (en versie) van de code van de service aan.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Het `Name` element wordt gebruikt om de naam van de map op te geven in het toepassingspakket dat de code van de service bevat. `CodePackage`heeft ook `version` het attribuut. Dit kan worden gebruikt om de versie van de code op te geven en kan ook mogelijk worden gebruikt om de code van de service te upgraden met behulp van de infrastructuur voor het beheer van de levenscyclus van toepassingen in Service Fabric.

#### <a name="optional-update-setupentrypoint"></a>Optioneel: SetupEntrypoint bijwerken

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Het Element SetupEntryPoint wordt gebruikt om elk uitvoerbaar of batchbestand op te geven dat moet worden uitgevoerd voordat de code van de service wordt gestart. Het is een optionele stap, dus het hoeft niet te worden opgenomen als er geen initialisatie nodig is. De SetupEntryPoint wordt uitgevoerd elke keer dat de service opnieuw wordt gestart.

Er is slechts één SetupEntryPoint, dus setupscripts moeten worden gegroepeerd in één batchbestand als de installatie van de toepassing meerdere scripts vereist. De SetupEntryPoint kan elk type bestand uitvoeren: uitvoerbare bestanden, batchbestanden en PowerShell-cmdlets. Zie [SetupEntryPoint configureren](service-fabric-application-runas-security.md)voor meer informatie.

In het voorgaande voorbeeld wordt in SetupEntryPoint een batchbestand uitgevoerd dat is aangeroepen `LaunchConfig.cmd` in de `scripts` submap van de codemap (ervan uitgaande dat het element WorkingFolder is ingesteld op CodeBase).

#### <a name="update-entrypoint"></a>EntryPoint bijwerken

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Het `EntryPoint` element in het servicemanifestbestand wordt gebruikt om aan te geven hoe de service moet worden gestart.

Het `ExeHost` element geeft de uitvoerbare (en argumenten) op die moeten worden gebruikt om de service te starten. U het `IsExternalExecutable="true"` kenmerk `ExeHost` optioneel toevoegen om aan te geven dat het programma een extern uitvoerbaar is buiten het codepakket. Bijvoorbeeld `<ExeHost IsExternalExecutable="true">`.

* `Program`hiermee wordt de naam van de uitvoerbare opgegeven die de service moet starten.
* `Arguments`hiermee worden de argumenten opgegeven die aan de uitvoerbare moeten worden doorgegeven. Het kan een lijst met parameters met argumenten zijn.
* `WorkingFolder`hiermee wordt de werkmap opgegeven voor het proces dat wordt gestart. U drie waarden opgeven:
  * `CodeBase`hiermee wordt opgegeven dat de werkmap wordt ingesteld op`Code` de codemap in het toepassingspakket (map in de voorgaande bestandsstructuur).
  * `CodePackage`hiermee wordt opgegeven dat de werkmap wordt ingesteld op`GuestService1Pkg` de hoofdmap van het toepassingspakket (in de voorgaande bestandsstructuur).
    * `Work`hiermee wordt opgegeven dat de bestanden in een submap worden geplaatst die werk wordt genoemd.

De WorkingFolder is handig om de juiste werkmap in te stellen, zodat relatieve paden kunnen worden gebruikt door de toepassings- of initialisatiescripts.

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>Eindpunten bijwerken en registreren bij naamgevingsservice voor communicatie

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```

In het voorgaande `Endpoint` voorbeeld geeft het element de eindpunten op waarop de toepassing kan luisteren. In dit voorbeeld luistert de toepassing Node.js op http op poort 3000.

Bovendien u Service Fabric vragen om dit eindpunt te publiceren naar de naamgevingsservice, zodat andere services het eindpunt adres van deze service kunnen ontdekken. Dit stelt u in staat om te communiceren tussen diensten die gast executables zijn.
Het gepubliceerde eindpuntadres is `UriScheme://IPAddressOrFQDN:Port/PathSuffix`van het formulier . `UriScheme`en `PathSuffix` zijn optionele kenmerken. `IPAddressOrFQDN`is het IP-adres of de volledig gekwalificeerde domeinnaam van het knooppunt waarop dit uitvoerbare wordt geplaatst en wordt voor u berekend.

In het volgende voorbeeld ziet u in Service Fabric Explorer een eindpunt `http://10.1.4.92:3000/myapp/` dat vergelijkbaar is met gepubliceerd voor de serviceinstantie. Of als dit een lokale `http://localhost:3000/myapp/`machine is, zie je.

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```

U deze adressen met [omgekeerde proxy](service-fabric-reverseproxy.md) gebruiken om te communiceren tussen services.

### <a name="edit-the-application-manifest-file"></a>Het manifestbestand van de toepassing bewerken

Zodra u het `Servicemanifest.xml` bestand hebt geconfigureerd, moet u `ApplicationManifest.xml` enkele wijzigingen aanbrengen in het bestand om ervoor te zorgen dat het juiste servicetype en de juiste naam worden gebruikt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport

In `ServiceManifestImport` het element u een of meer services opgeven die u in de app wilt opnemen. Er wordt verwezen `ServiceManifestName`naar services, waarmee de `ServiceManifest.xml` naam van de map waar het bestand zich bevindt, wordt opgegeven.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>Logboekregistratie instellen

Voor gastuitvoerbare gegevens is het handig om consolelogboeken te kunnen zien om erachter te komen of de toepassings- en configuratiescripts fouten laten zien.
De omleiding van de console `ServiceManifest.xml` kan `ConsoleRedirection` in het bestand worden geconfigureerd met behulp van het element.

> [!WARNING]
> Gebruik nooit het omleidingsbeleid voor de console in een toepassing die in productie wordt geïmplementeerd, omdat dit van invloed kan zijn op de failover van de toepassing. *Gebruik* dit alleen voor lokale ontwikkelings- en debuggingdoeleinden.  
>
>

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection`kan worden gebruikt om de uitvoer van de console (zowel stdout als stderr) om te leiden naar een werkmap. Dit biedt de mogelijkheid om te controleren of er geen fouten zijn tijdens het instellen of uitvoeren van de toepassing in het cluster Servicefabric.

`FileRetentionCount`hiermee wordt bepaald hoeveel bestanden in de werkmap worden opgeslagen. Een waarde van 5 betekent bijvoorbeeld dat de logbestanden voor de vorige vijf uitvoeringen worden opgeslagen in de werkmap.

`FileMaxSizeInKb`geeft de maximale grootte van de logboekbestanden op.

Logbestanden worden opgeslagen in een van de werkmappen van de service. Als u wilt bepalen waar de bestanden zich bevinden, gebruikt u Service Fabric Explorer om te bepalen op welk knooppunt de service wordt uitgevoerd en op welk werkmap wordt gebruikt. Dit proces wordt later in dit artikel behandeld.

## <a name="deployment"></a>Implementatie

De laatste stap is het [implementeren van uw toepassing.](service-fabric-deploy-remove-applications.md) In het volgende PowerShell-script ziet u hoe u uw toepassing implementeert in het cluster lokale ontwikkeling en een nieuwe Service Fabric-service start.

```powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> [Comprimeer het pakket](service-fabric-package-apps.md#compress-a-package) voordat u naar het afbeeldingsarchief kopieert als het pakket groot is of veel bestanden heeft. Lees [hier](service-fabric-deploy-remove-applications.md#upload-the-application-package)meer .
>

Een Service Fabric-service kan worden geïmplementeerd in verschillende 'configuraties'. Het kan bijvoorbeeld worden geïmplementeerd als één of meerdere exemplaren, of het kan zodanig worden geïmplementeerd dat er één instantie van de service op elk knooppunt van het cluster ServiceFabric is.

De `InstanceCount` parameter `New-ServiceFabricService` van de cmdlet wordt gebruikt om aan te geven hoeveel exemplaren van de service moeten worden gestart in het cluster Servicefabric. U `InstanceCount` de waarde instellen, afhankelijk van het type toepassing dat u implementeert. De twee meest voorkomende scenario's zijn:

* `InstanceCount = "1"`. In dit geval wordt slechts één exemplaar van de service geïmplementeerd in het cluster. De planner van Service Fabric bepaalt op welk knooppunt de service wordt geïmplementeerd.
* `InstanceCount ="-1"`. In dit geval wordt één exemplaar van de service geïmplementeerd op elk knooppunt in het cluster Servicefabric. Het resultaat is het hebben van één (en slechts één) instantie van de service voor elk knooppunt in het cluster.

Dit is een handige configuratie voor front-end toepassingen (bijvoorbeeld een REST-eindpunt), omdat clienttoepassingen moeten "verbinding" maken met een van de knooppunten in het cluster om het eindpunt te gebruiken. Deze configuratie kan ook worden gebruikt wanneer bijvoorbeeld alle knooppunten van het cluster Service Fabric zijn verbonden met een load balancer. Clientverkeer kan vervolgens worden verdeeld over de service die wordt uitgevoerd op alle knooppunten in het cluster.

## <a name="check-your-running-application"></a>Uw lopende toepassing controleren
Identificeer in Service Fabric Explorer het knooppunt waar de service wordt uitgevoerd. In dit voorbeeld wordt het uitgevoerd op Node1:

![Knooppunt waar de service wordt uitgevoerd](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

Als u naar het knooppunt navigeert en naar de toepassing bladert, ziet u de essentiële knooppuntgegevens, inclusief de locatie op de schijf.

![Locatie op schijf](./media/service-fabric-deploy-existing-app/locationondisk2.png)

Als u naar de map bladert met Server Explorer, u de werkmap en de logboekmap van de service vinden, zoals in de volgende schermafbeelding wordt weergegeven:

![Locatie van het logboek](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een gast uitvoerbaar verpakken en implementeren in Service Fabric. Zie de volgende artikelen voor gerelateerde informatie en taken.

* [Monster voor het verpakken en implementeren van een gast uitvoerbaar,](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)inclusief een link naar de prerelease van de verpakkingstool
* [Voorbeeld van twee gastexecutables (C# en nodejs) die communiceren via de naamgevingsservice via REST](https://github.com/Azure-Samples/service-fabric-containers)
* [Uw eerste Service Fabric-toepassing maken met Visual Studio](service-fabric-tutorial-create-dotnet-app.md)
