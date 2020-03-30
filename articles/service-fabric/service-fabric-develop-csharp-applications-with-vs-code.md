---
title: Ontwikkel .NET Core-toepassingen met Visual Studio Code
description: In dit artikel ziet u hoe u .NET Core Service Fabric-toepassingen implementeren, implementeren en debuggen met behulp van Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 1d7478e6b81ef2c53ca6194197336e91d3ff250b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614520"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>C# Service Fabric-toepassingen ontwikkelen met Visual Studio Code

De [Service Fabric Reliable Services-extensie voor VS-code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) maakt het eenvoudig om .NET Core Service Fabric-toepassingen te bouwen op Windows-, Linux- en macOS-besturingssystemen.

In dit artikel ziet u hoe u een .NET Core Service Fabric-toepassing bouwen, implementeren en debuggen met behulp van Visual Studio Code.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u vs-code, de servicestructuur extensie reliable services voor VS-code en eventuele afhankelijkheden die nodig zijn voor uw ontwikkelomgeving, al hebt geïnstalleerd. Zie [Aan de slag gaan voor](./service-fabric-get-started-vs-code.md#prerequisites)meer informatie.

## <a name="download-the-sample"></a>Het voorbeeld downloaden
Dit artikel maakt gebruik van de CounterService-toepassing in de [Service Fabric .NET Core getting started samples GitHub repository](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Als u de opslagplaats naar uw ontwikkelingsmachine wilt klonen, voert u de volgende opdracht uit vanuit een terminalvenster (opdrachtvenster op Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>De toepassing openen in VS-code

### <a name="windows"></a>Windows
Klik met de rechtermuisknop op het vs-codepictogram in het menu Start en kies **Uitvoeren als beheerder**. Als u de foutopsporing aan uw services wilt koppelen, moet u VS-code als beheerder uitvoeren.

### <a name="linux"></a>Linux
Navigeer met de terminal naar het pad /service-fabric-dotnet-core-getting-started/Services/CounterService vanuit de map waar de toepassing lokaal in is gekloond.

Voer de volgende opdracht uit om VS-code als hoofdgebruiker te openen, zodat de foutopsporing aan uw services kan worden gekoppeld.
```
sudo code . --user-data-dir='.'
```

De toepassing moet nu worden weergegeven in de werkruimte VS-code.

![Servicetoepassing in - op- en uittellerservice](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>De toepassing bouwen
1. Druk op (Ctrl + Shift + p) om het **opdrachtpalet** in VS-code te openen.
2. Zoek naar en selecteer de opdracht **Servicefabric: Build Application.** De build output wordt naar de geïntegreerde terminal gestuurd.

   ![Opdracht Toepassing bouwen in VS-code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>De toepassing implementeren in het lokale cluster
Nadat u de toepassing hebt gebouwd, u deze implementeren in het lokale cluster. 

1. Selecteer in het **opdrachtpalet**de **opdracht Servicefabric: Deploy Application (Localhost).** De uitvoer van het installatieproces wordt naar de geïntegreerde terminal gestuurd.

   ![Opdracht Toepassing implementeren in VS-code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. Wanneer de implementatie is voltooid, start u een\/browser en opent u Service Fabric Explorer: http: /localhost:19080/Explorer. U moet zien dat de toepassing wordt uitgevoerd. Dit kan enige tijd duren, dus wees geduldig. 

   ![Counter Service-toepassing in Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. Nadat u hebt geverifieerd dat de toepassing wordt uitgevoerd, start\/u een browser en opent u deze pagina: http: /localhost:31002. Dit is de web front-end van de applicatie. Vernieuw de pagina om de huidige waarde van de teller te zien terwijl deze wordt verhoogd.

   ![Service-toepassing counteren in browser](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>De toepassing publiceren naar een Azure Service Fabric-cluster
Samen met het implementeren van de toepassing naar het lokale cluster, u de toepassing ook publiceren naar een extern Azure Service Fabric-cluster. 

1. Zorg ervoor dat u uw toepassing hebt gebouwd met behulp van de bovenstaande instructies. Werk het gegenereerde configuratiebestand bij `Cloud.json` met de details van het externe cluster waarnaar u wilt publiceren.

2. Selecteer in het **opdrachtpalet**de **opdracht Servicefabric: Publish Application**. De uitvoer van het installatieproces wordt naar de geïntegreerde terminal gestuurd.

   ![Opdracht Toepassing publiceren in VS-code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. Wanneer de implementatie is voltooid, start u `https:<clusterurl>:19080/Explorer`een browser en opent u Service Fabric Explorer: . U moet zien dat de toepassing wordt uitgevoerd. Dit kan enige tijd duren, dus wees geduldig. 

## <a name="debug-the-application"></a>Fouten in de toepassing opsporen
Wanneer u toepassingen in VS-code debugadt, moet de toepassing worden uitgevoerd op een lokaal cluster. Breekpunten kunnen vervolgens aan de code worden toegevoegd.

Voer de volgende stappen uit om een breekpunt en foutopsporing in te stellen:
1. Open in Explorer het bestand */src/CounterServiceApplication/CounterService/CounterService.cs* en stel een `RunAsync` breekpunt in op regel 62 in de methode.
3. Klik op het pictogram Foutopsporing in de **activiteitsbalk** om de foutopsporingsweergave in VS-code te openen. Klik op het tandwielpictogram boven aan de foutopsporingsweergave en selecteer **.NET Core** in het vervolgkeuzemenu. Het launch.json-bestand wordt geopend. U dit bestand sluiten. Nu moet u configuratiekeuzes zien in het configuratiemenu voor foutopsporing naast de run-knop (groene pijl).

   ![Pictogram Foutopsporing in VS-codewerkruimte](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Selecteer **.NET Core Attach** in het menu foutopsporingsconfiguratie.

   ![Pictogram Foutopsporing in VS-codewerkruimte](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Open Service Fabric Explorer in\/een browser: http: /localhost:19080/Explorer. Klik op **Toepassingen** en zoom in om het primaire knooppunt te bepalen waarop de CounterService wordt uitgevoerd. In de afbeelding onder het primaire knooppunt voor de CounterService is Knooppunt 0.

   ![Primair knooppunt voor CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. Klik in VS-code op het pictogram Uitvoeren (groene pijl) naast de **foutopsporingsconfiguratie .NET Core Attach.** Selecteer in het dialoogvenster processelectie het CounterService-proces dat wordt uitgevoerd op het primaire knooppunt dat u in stap 4 hebt geïdentificeerd.

   ![Primair proces](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Het breekpunt in het *CounterService.cs* bestand zal zeer snel worden geraakt. U vervolgens de waarden van de lokale variabelen verkennen. Gebruik de werkbalk Foutopsporing boven aan VS-code om de uitvoering voort te zetten, regels te omzeilen, in methoden te stappen of uit de huidige methode te stappen. 

   ![Foutopsporingswaarden](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Als u de foutopsporingssessie wilt beëindigen, klikt u op het stekkerpictogram op de werkbalk Foutopsporing boven aan VS-code.
   
   ![Verbinding verbreken van foutopsporing](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. Wanneer u klaar bent met foutopsporing, u de opdracht **Servicefabric: Toepassing verwijderen** gebruiken om de CounterService-toepassing uit uw lokale cluster te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en debuggen van Java Service Fabric-toepassingen met VS-code](./service-fabric-develop-java-applications-with-vs-code.md).



