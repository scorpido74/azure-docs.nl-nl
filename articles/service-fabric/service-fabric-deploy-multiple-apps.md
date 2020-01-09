---
title: Een node. js-toepassing implementeren die gebruikmaakt van MongoDB
description: Overzicht van het inpakken van meerdere gast uitvoer bare bestanden om te implementeren in een Azure Service Fabric-cluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614465"
---
# <a name="deploy-multiple-guest-executables"></a>Meerdere bestanden implementeren die door gasten kunnen worden uitgevoerd
In dit artikel wordt beschreven hoe u meerdere uitvoer bare gast bestanden verpakken en implementeert naar Azure Service Fabric. Voor het bouwen en implementeren van een enkel Service Fabric-pakket leest u hoe u [een uitvoerbaar gast bestand implementeert in service Fabric](service-fabric-deploy-existing-app.md).

In dit scenario ziet u hoe u een toepassing implementeert met een node. js-front-end die gebruikmaakt van MongoDB als gegevens opslag, kunt u de stappen Toep assen op elke toepassing die afhankelijkheden voor een andere toepassing bevat.   

U kunt Visual Studio gebruiken om het toepassings pakket te maken dat meerdere uitvoer bare bestanden voor gasten bevat. Zie [Visual Studio gebruiken voor het inpakken van een bestaande toepassing](service-fabric-deploy-existing-app.md). Nadat u het eerste uitvoer bare gast bestand hebt toegevoegd, klikt u met de rechter muisknop op het toepassings project en selecteert u de **> nieuwe service Fabric-service** om het tweede uitvoer bare gast project toe te voegen aan de oplossing. Opmerking: als u ervoor kiest om de bron te koppelen in het Visual Studio-project en de Visual Studio-oplossing te bouwen, zorgt u ervoor dat uw toepassings pakket up-to-date is met de wijzigingen in de bron. 

## <a name="samples"></a>Voorbeelden
* [Voor beeld voor het verpakken en implementeren van een uitvoerbaar gast bestand](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Voor beeld van twee gast uitvoerC# bare bestanden (en nodejs) die communiceren via de naamgevings service met rest](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>De uitvoer bare toepassing met meerdere Gasts hand matig inpakken
U kunt het uitvoer bare gast bestand ook hand matig inpakken. Zie [hand matig een bestaand uitvoerbaar pakket en implementeren](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable)voor meer informatie.

### <a name="packaging-the-nodejs-application"></a>De node. js-toepassing inpakken
In dit artikel wordt ervan uitgegaan dat node. js niet is geïnstalleerd op de knoop punten in het Service Fabric cluster. Als gevolg hiervan moet u node. exe toevoegen aan de hoofdmap van uw knooppunt toepassing vóór de verpakking. De mapstructuur van de node. js-toepassing (met behulp van Express web framework en Jade-sjabloon Engine) moet er ongeveer als volgt uitzien:

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Als volgende stap maakt u een toepassings pakket voor de node. js-toepassing. Met de code hieronder maakt u een Service Fabric toepassings pakket met de node. js-toepassing.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Hieronder volgt een beschrijving van de para meters die worden gebruikt:

* **/Source** verwijst naar de map van de toepassing die moet worden verpakt.
* **/target** definieert de directory waarin het pakket moet worden gemaakt. Deze map moet afwijken van de bronmap.
* **/AppName** definieert de toepassings naam van de bestaande toepassing. Het is belang rijk om te begrijpen dat dit wordt omgezet in de naam van de service in het manifest en niet op de naam van de Service Fabric toepassing.
* **/exe** definieert het uitvoer bare bestand dat service Fabric moet starten, in dit geval `node.exe`.
* **/ma** definieert het argument dat wordt gebruikt om het uitvoer bare bestand te starten. Als node. js niet is geïnstalleerd, moet Service Fabric de node. js-webserver starten door `node.exe bin/www`uit te voeren.  `/ma:'bin/www'` vertelt het verpakkings programma `bin/www` wordt gebruikt als argument voor node. exe.
* **/AppType** definieert de naam van het toepassings type service Fabric.

Als u bladert naar de map die is opgegeven in de para meter/target, ziet u dat het hulp programma een volledig functionerend Service Fabric-pakket heeft gemaakt, zoals hieronder wordt weer gegeven:

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
De gegenereerde ServiceManifest. XML bevat nu een sectie waarin wordt beschreven hoe de node. js-webserver moet worden gestart, zoals wordt weer gegeven in het volgende code fragment:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
In dit voor beeld luistert de node. js-webserver naar poort 3000, dus moet u de eindpunt gegevens bijwerken in het bestand ServiceManifest. XML, zoals hieronder wordt weer gegeven.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>De MongoDB-toepassing inpakken
Nu u de node. js-toepassing hebt ingepakt, kunt u door gaan en pakket MongoDB. Zoals eerder vermeld, zijn de stappen die u nu doorloopt niet specifiek voor node. js en MongoDB. Ze zijn in feite van toepassing op alle toepassingen die als een Service Fabric toepassing moeten worden verpakt.  

Als u de MongoDB wilt verpakken, moet u ervoor zorgen dat u Mongod. exe en Mongo. exe inpakt. Beide binaire bestanden bevinden zich in de map `bin` van uw MongoDB-installatiemap. De mapstructuur ziet er ongeveer als volgt uit.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric moet MongoDB starten met een opdracht zoals hieronder, dus u moet de `/ma`-para meter gebruiken wanneer MongoDB wordt ingepakt.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> De gegevens blijven niet behouden in het geval van een storing in een knoop punt als u de MongoDB-gegevens map in de lokale map van het knoop punt plaatst. U moet een duurzame opslag gebruiken of een MongoDB replicaset implementeren om gegevens verlies te voor komen.  
>
>

In Power shell of de opdracht shell voeren we het pakket met de volgende para meters uit:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Als u MongoDB wilt toevoegen aan uw Service Fabric toepassings pakket, moet u ervoor zorgen dat de/target-para meter verwijst naar dezelfde map die al het toepassings manifest bevat, samen met de node. js-toepassing. U moet er ook voor zorgen dat u dezelfde Application type-naam gebruikt.

Ga naar de map en controleer wat het hulp programma heeft gemaakt.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Zoals u ziet, voegt het hulp programma een nieuwe map, MongoDB, toe aan de map die de binaire bestanden van MongoDB bevat. Als u het `ApplicationManifest.xml` bestand opent, ziet u dat het pakket nu zowel de node. js-toepassing als de MongoDB bevat. De volgende code toont de inhoud van het toepassings manifest.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

### <a name="publishing-the-application"></a>De toepassing publiceren
De laatste stap bestaat uit het publiceren van de toepassing naar het lokale Service Fabric-cluster met behulp van de onderstaande Power shell-scripts:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Zodra de toepassing is gepubliceerd naar het lokale cluster, hebt u toegang tot de node. js-toepassing op de poort die u hebt ingevoerd in het service manifest van de node. js-toepassing, bijvoorbeeld http:\//localhost: 3000.

In deze zelf studie hebt u gezien hoe u eenvoudig twee bestaande toepassingen kunt inpakken als een Service Fabric-toepassing. U hebt ook geleerd hoe u deze kunt implementeren op Service Fabric zodat het kan profiteren van sommige van de Service Fabric functies, zoals hoge Beschik baarheid en status systeem integratie.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Meer uitvoer bare bestanden voor gasten toevoegen aan een bestaande toepassing met behulp van Yeoman in Linux

Voer de volgende stappen uit als u nog een service wilt toevoegen aan een toepassing die al is gemaakt met `yo`: 
1. Stel de directory in op de hoofdmap van de bestaande toepassing.  Bijvoorbeeld `cd ~/YeomanSamples/MyApplication` als `MyApplication` de toepassing is die is gemaakt door Yeoman.
2. Voer `yo azuresfguest:AddService` uit en geef de benodigde details op.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van containers met [service Fabric en containers-overzicht](service-fabric-containers-overview.md)
* [Voor beeld voor het verpakken en implementeren van een uitvoerbaar gast bestand](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Voor beeld van twee gast uitvoerC# bare bestanden (en nodejs) die communiceren via de naamgevings service met rest](https://github.com/Azure-Samples/service-fabric-containers)
