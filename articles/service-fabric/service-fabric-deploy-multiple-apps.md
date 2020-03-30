---
title: Een Node.js-toepassing implementeren die MongoDB gebruikt
description: Walkthrough over het verpakken van meerdere gastuitvoerables om te implementeren in een Azure Service Fabric-cluster
author: mikkelhegn
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mikhegn
ms.openlocfilehash: 4538efc8a2426fc20dd20d1a85edaf6f76bfc649
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614465"
---
# <a name="deploy-multiple-guest-executables"></a>Meerdere toepassingen implementeren die door gasten kunnen worden uitgevoerd
In dit artikel ziet u hoe u meerdere uitvoerbare gasten verpakken en implementeren in Azure Service Fabric. Voor het bouwen en implementeren van één Service Fabric-pakket lees je hoe je [een gast implementeert die uitvoerbaar is voor Service Fabric.](service-fabric-deploy-existing-app.md)

Hoewel deze walkthrough laat zien hoe u een toepassing implementeert met een Node.js-front-end die MongoDB als gegevensarchief gebruikt, u de stappen toepassen op elke toepassing die afhankelijk is van een andere toepassing.   

U Visual Studio gebruiken om het toepassingspakket te produceren dat meerdere gastuitvoerbare bestanden bevat. Zie [Visual Studio gebruiken om een bestaande toepassing te verpakken.](service-fabric-deploy-existing-app.md) Nadat u de eerste gast uitvoerbaar hebt toegevoegd, klikt u met de rechtermuisknop op het toepassingsproject en selecteert u de **Add->New Service Fabric-service** om het tweede gastuitvoerproject aan de oplossing toe te voegen. Opmerking: Als u ervoor kiest om de bron in het Visual Studio-project te koppelen, zorgt het bouwen van de Visual Studio-oplossing ervoor dat uw toepassingspakket up-to-date is met wijzigingen in de bron. 

## <a name="samples"></a>Voorbeelden
* [Monster voor het verpakken en implementeren van een gast uitvoerbaar](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Voorbeeld van twee gastexecutables (C# en nodejs) die communiceren via de naamgevingsservice via REST](https://github.com/Azure-Samples/service-fabric-containers)

## <a name="manually-package-the-multiple-guest-executable-application"></a>De uitvoerbare toepassing voor meerdere gasten handmatig verpakken
U de gast ook handmatig uitvoerbaar verpakken. Zie Voor meer informatie [handmatig een bestaand uitvoerbaar verpakken en implementeren.](service-fabric-deploy-existing-app.md#manually-package-and-deploy-an-existing-executable)

### <a name="packaging-the-nodejs-application"></a>Verpakking van de Node.js applicatie
In dit artikel wordt ervan uitgegaan dat Node.js niet is geïnstalleerd op de knooppunten in het cluster Servicefabric. Als gevolg hiervan moet u Node.exe toevoegen aan de hoofdmap van uw knooppunttoepassing voordat u deze verpakt. De directorystructuur van de Node.js-toepassing (met Express-webframework en Jade-sjabloonengine) moet er hetzelfde uitzien als de onderstaande:

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

Als volgende stap maakt u een toepassingspakket voor de Node.js-toepassing. De onderstaande code maakt een Service Fabric-toepassingspakket dat de toepassing Node.js bevat.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Hieronder vindt u een beschrijving van de parameters die worden gebruikt:

* **/bron** verwijst naar de map van de toepassing die moet worden verpakt.
* **/target** definieert de map waarin het pakket moet worden gemaakt. Deze map moet anders zijn dan de bronmap.
* **/appname** definieert de toepassingsnaam van de bestaande toepassing. Het is belangrijk om te begrijpen dat dit zich vertaalt naar de servicenaam in het manifest en niet naar de naam van de Service Fabric-toepassing.
* **/exe** definieert de uitvoerbare die Service Fabric `node.exe`wordt verondersteld te lanceren, in dit geval .
* **/ma** definieert het argument dat wordt gebruikt om het uitvoerbare te starten. Aangezien Node.js niet is geïnstalleerd, moet Service Fabric de Node.js-webserver starten door . `node.exe bin/www`  `/ma:'bin/www'`vertelt het verpakkingsgereedschap `bin/www` te gebruiken als argument voor node.exe.
* **/AppType** definieert de typenaam servicefabric-toepassing.

Als u naar de map bladert die is opgegeven in de parameter /target, u zien dat het hulpprogramma een volledig functionerend Service Fabric-pakket heeft gemaakt, zoals hieronder wordt weergegeven:

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
De gegenereerde ServiceManifest.xml heeft nu een sectie die beschrijft hoe de Node.js-webserver moet worden gestart, zoals in het onderstaande codefragment wordt weergegeven:

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
In dit voorbeeld luistert de Node.js-webserver naar poort 3000, dus u moet de eindpuntgegevens bijwerken in het bestand ServiceManifest.xml zoals hieronder weergegeven.   

```xml
<Resources>
      <Endpoints>
         <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
### <a name="packaging-the-mongodb-application"></a>Verpakking van de MongoDB applicatie
Nu u de Node.js applicatie hebt verpakt, u doorgaan en MongoDB verpakken. Zoals eerder vermeld, zijn de stappen die je nu doormaakt niet specifiek voor Node.js en MongoDB. In feite zijn ze van toepassing op alle toepassingen die zijn bedoeld om samen te worden verpakt als een Service Fabric applicatie.  

Om MongoDB te verpakken, wilt u ervoor zorgen dat u Mongod.exe en Mongo.exe verpakt. Beide binaire bestanden bevinden `bin` zich in de directory van uw MongoDB-installatiemap. De mapstructuur lijkt op de onderstaande.

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- anybinary.exe
```
Service Fabric moet MongoDB starten met een opdracht die vergelijkbaar is `/ma` met de onderstaande, dus je moet de parameter gebruiken bij het verpakken van MongoDB.

```
mongod.exe --dbpath [path to data]
```
> [!NOTE]
> De gegevens worden niet bewaard in het geval van een knooppuntstoring als u de MongoDB-gegevensmap op de lokale map van het knooppunt plaatst. U moet duurzame opslag gebruiken of een MongoDB-replicaset implementeren om gegevensverlies te voorkomen.  
>
>

In PowerShell of de opdrachtschaal draaien we het verpakkingsgereedschap met de volgende parameters:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Om MongoDB toe te voegen aan uw Service Fabric-toepassingspakket, moet u ervoor zorgen dat de /target parameter verwijst naar dezelfde directory die het toepassingsmanifest al bevat, samen met de Node.js-toepassing. U moet er ook voor zorgen dat u dezelfde ApplicationType-naam gebruikt.

Laten we naar de map bladeren en onderzoeken wat het hulpprogramma heeft gemaakt.

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
Zoals u zien, heeft het hulpprogramma een nieuwe map, MongoDB, toegevoegd aan de map met de MongoDB-binaries. Als u `ApplicationManifest.xml` het bestand opent, u zien dat het pakket nu zowel de Node.js-toepassing als MongoDB bevat. De onderstaande code toont de inhoud van het sollicitatiemanifest.

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
De laatste stap is het publiceren van de toepassing naar het lokale cluster Service Fabric met behulp van de PowerShell-scripts hieronder:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Zodra de toepassing is gepubliceerd naar het lokale cluster, hebt u toegang tot de Node.js-toepassing op de poort die\/we hebben ingevoerd in het servicemanifest van de node.js-toepassing- bijvoorbeeld http: /localhost:3000.

In deze zelfstudie hebt u gezien hoe u eenvoudig twee bestaande toepassingen verpakken als één Service Fabric-toepassing. U hebt ook geleerd hoe u het implementeren in Service Fabric, zodat het kan profiteren van een aantal van de Service Fabric-functies, zoals hoge beschikbaarheid en integratie van het gezondheidssysteem.

## <a name="adding-more-guest-executables-to-an-existing-application-using-yeoman-on-linux"></a>Meer gastexecutables toevoegen aan een bestaande toepassing met Yeoman op Linux

Voer de volgende stappen uit als u nog een service wilt toevoegen aan een toepassing die al is gemaakt met `yo`: 
1. Stel de directory in op de hoofdmap van de bestaande toepassing.  Bijvoorbeeld `cd ~/YeomanSamples/MyApplication` als `MyApplication` de toepassing is die is gemaakt door Yeoman.
2. Voer `yo azuresfguest:AddService` uit en geef de nodige details.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van containers met [servicestructuur en containersoverzicht](service-fabric-containers-overview.md)
* [Monster voor het verpakken en implementeren van een gast uitvoerbaar](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Voorbeeld van twee gastexecutables (C# en nodejs) die communiceren via de naamgevingsservice via REST](https://github.com/Azure-Samples/service-fabric-containers)
