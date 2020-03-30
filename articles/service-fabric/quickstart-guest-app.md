---
title: Een bestaande app snel implementeren in een cluster
description: Een Azure Service Fabric-cluster gebruiken voor het hosten van een bestaande Node.js-toepassing met Visual Studio.
ms.topic: conceptual
ms.date: 12/06/2017
ms.openlocfilehash: 9153fc4cd60cb892532db49bf4339b517320b1a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614839"
---
# <a name="host-a-nodejs-application-on-azure-service-fabric"></a>Een Node.js-toepassing hosten in Azure Service Fabric

In deze snelstartgids wordt uitgelegd hoe u een bestaande toepassing (Node.js in dit voorbeeld) kunt implementeren in een Service Fabric-cluster in Azure.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorgt u ervoor dat u [uw ontwikkelingsomgeving hebt ingesteld](service-fabric-get-started.md). Inclusief het installeren van de Service Fabric SDK en Visual Studio 2019 of 2015.

U moet ook beschikken over een bestaande Node.js-toepassing voor implementatie. Deze snelstartgids maakt gebruik van een eenvoudige Node.js-website die [hier][download-sample] kan worden gedownload. Pak dit bestand uit in de map `<path-to-project>\ApplicationPackageRoot\<package-name>\Code\` nadat u het project in de volgende stap hebt gemaakt.

Als u geen Azure-abonnement hebt, maakt u een [gratis account][create-account]aan.

## <a name="create-the-service"></a>De service maken

Visual Studio starten als **beheerder**.

Maak een project met `CTRL`+`SHIFT`+`N`

Kies in het dialoogvenster **Nieuw Project** de optie **Cloud > Service Fabric-toepassing**.

Noem de toepassing **MyGuestApp** en druk op **OK**.

>[!IMPORTANT]
>Node.js kan gemakkelijk de limiet van 260 tekens overschrijden voor paden in Windows. Gebruik een kort pad voor het project zelf, zoals **c:\code\svc1**. Optioneel u **[deze instructies](https://stackoverflow.com/a/41687101/1664231)** volgen om lange bestandspaden in Windows 10 in te schakelen.
   
![Dialoogvenster voor nieuw project in Visual Studio][new-project]

In het volgende dialoogvenster kunt u elk type Service Fabric-service maken. Kies voor deze snelstartgids **Door gast uitvoerbaar bestand**.

Noem de service **MyGuestService** en stel de opties aan de rechterkant in op de volgende waarden:

| Instelling                   | Waarde |
| ------------------------- | ------ |
| Map met codepakket       | _&lt;de map met uw Node.js-app&gt;_ |
| Gedrag codepakket     | Mapinhoud naar project kopiëren |
| Programma                   | node.exe |
| Argumenten                 | server.js |
| Werkmap            | CodePackage |

Druk op **OK**.

![Dialoogvenster voor nieuwe service in Visual Studio][new-service]

Visual Studio maakt het toepassingsproject en het actorserviceproject en geeft deze weer in Solution Explorer.

Het applicatieproject (**MyGuestApp**) bevat niet direct code. Het project verwijst naar een reeks serviceprojecten. Het bevat ook drie andere soorten inhoud:

* **Profielen publiceren**  
Hulpprogrammavoorkeuren voor verschillende omgevingen.

* **Scripts**  
PowerShell-script voor het implementeren/bijwerken van uw toepassing.

* **Toepassingsdefinitie**  
Bevat het manifest van de toepassing onder *ApplicationPackageRoot*. Gekoppelde bestanden met toepassingsparameters vindt u onder *ApplicationParameters*, die de toepassing definiëren en waarmee u deze specifiek voor een bepaalde omgeving kunt configureren.
    
Zie [Aan de slag met Reliable Services](service-fabric-reliable-services-quick-start.md) voor een overzicht van de inhoud van het serviceproject.

## <a name="set-up-networking"></a>Netwerkservice instellen

De voorbeeld-Node.js-app die we implementeren gebruikt poort **80** en we moeten Service Fabric doorgeven dat deze poort open moet zijn.

Open het bestand **ServiceManifest.xml** in het project. Aan de onderkant van het manifest, is er een `<Resources> \ <Endpoints>` met een vermelding al gedefinieerd. Wijzig deze vermelding om `Port`, `Protocol` en `Type` toe te voegen. 

```xml
  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="MyGuestAppServiceTypeEndpoint" Port="80" Protocol="http" Type="Input" />
    </Endpoints>
  </Resources>
```

## <a name="deploy-to-azure"></a>Implementeren in Azure

Als u op **F5** drukt en het project uitvoert, wordt het geïmplementeerd in het lokale cluster. Echter, we gaan in plaats daarvan implementeren in Azure.

Klik met de rechtermuisknop op het project en kies **Publiceren...** Er wordt een dialoogvenster geopend waarin u kunt aangeven dat u naar Azure wilt publiceren.

![Dialoogvenster Publiceren naar Azure voor een Service Fabric-service][publish]

Selecteer het doelprofiel **PublishProfiles\Cloud.xml**.

Kies een Azure-account waarnaar u wilt implementeren als u dit nog niet hebt gedaan. Als u er nog geen hebt, [meldt u zich er voor een aan][create-account].

Selecteer onder **Verbindingseindpunt** het Service Fabric-cluster waarin u wilt implementeren. Als u er geen hebt, selecteert u ** &lt;Nieuw cluster maken... &gt; ** die het browservenster opent voor de Azure-portal. Zie [Een cluster maken in de portal](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal) voor meer informatie. 

Wanneer u de Service Fabric-cluster maakt, zorg er dan voor dat u de **Aangepaste eindpunten** instelt op **80**.

![Service Fabric-knooppuntconfiguratie met aangepast eindpunt][custom-endpoint]

Het maken van een nieuw Service Fabric-cluster neemt enige tijd in beslag. Zodra deze is gemaakt, gaat u terug ** &lt;&gt;** naar het dialoogvenster Publiceren en selecteert u Vernieuwen . Het nieuwe cluster wordt vermeld in de vervolgkeuzelijst; selecteer het.

Druk op **Publiceren** en wacht tot de implementatie is voltooid.

Dit kan enkele minuten duren. Nadat deze is voltooid, kan het nog enkele minuten duren voordat de toepassing volledig beschikbaar is.

## <a name="test-the-website"></a>De website testen

Test uw service na publicatie in een webbrowser. 

Open eerst Azure Portal en zoek uw Service Fabric-service op.

Controleer de overzichtsblade van het serviceadres. Gebruik de domeinnaam van de eigenschap _Eindpunt van de clientverbinding_. Bijvoorbeeld `http://mysvcfab1.westus2.cloudapp.azure.com`.

![Overzichtsblade van Service Fabric in Azure Portal][overview]

Navigeer naar dit adres waar `HELLO WORLD` u het antwoord ziet.

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Vergeet niet om alle bronnen die u hebt gemaakt voor deze quickstart te verwijderen, omdat er kosten in rekening worden gebracht voor deze bronnen.

## <a name="next-steps"></a>Volgende stappen
Lees meer over [door gast uitvoerbare bestanden](service-fabric-guest-executables-introduction.md).

<!-- Image References -->

[new-project]: ./media/quickstart-guest-app/new-project.png
[new-service]: ./media/quickstart-guest-app/template.png
[solution-exp]: ./media/quickstart-guest-app/solution-explorer.png
[publish]: ./media/quickstart-guest-app/publish.png
[overview]: ./media/quickstart-guest-app/overview.png
[custom-endpoint]: ./media/quickstart-guest-app/custom-endpoint.png

[download-sample]: https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/service-fabric-node-website.zip
[create-account]: https://azure.microsoft.com/free/?WT.mc_id=A261C142F
