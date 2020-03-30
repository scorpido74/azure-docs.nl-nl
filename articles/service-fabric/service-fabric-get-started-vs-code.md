---
title: Azure-servicefabric met VS-code aan de slag
description: Dit artikel is een overzicht van het maken van Service Fabric-toepassingen met behulp van Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 17bff459a0399d39c6bfdd772ad16d0b5b2f6771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258484"
---
# <a name="service-fabric-for-visual-studio-code"></a>Servicefabric voor Visual Studio-code

De [Service Fabric Reliable Services-extensie voor VS-code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) biedt de tools die nodig zijn om Service Fabric-toepassingen voor Windows, Linux en macOS te maken, te bouwen en te debuggen.

Dit artikel geeft een overzicht van de vereisten en de instelling van de extensie, evenals het gebruik van de verschillende opdrachten die worden geleverd door de extensie. 

> [!IMPORTANT]
> Service Fabric Java-toepassingen kunnen worden ontwikkeld op Windows-machines, maar kunnen alleen worden geïmplementeerd op Azure Linux-clusters. Foutopsporing java-toepassingen wordt niet ondersteund op Windows.

## <a name="prerequisites"></a>Vereisten

De volgende vereisten moeten op alle omgevingen worden geïnstalleerd.

* [Visual Studio-code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman Generators -- installeer de juiste generatoren voor uw toepassing

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

De volgende voorwaarden moeten worden geïnstalleerd voor Java-ontwikkeling:

* [Java SDK](https://aka.ms/azure-jdks) (versie 1.8)
* [Gradle](https://gradle.org/install/)
* [Foutopsporing voor Java VS Code-extensie](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Nodig om Java-services te debuggen. Debugging Java-services worden alleen op Linux ondersteund. U het installeren door te klikken op het pictogram Extensies in de **activiteitsbalk** in VS-code en te zoeken naar de extensie, of via de VS Code Marketplace.

De volgende voorwaarden moeten worden geïnstalleerd voor de ontwikkeling van .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (versie 2.0.0 of hoger)
* [C# voor Visual Studio Code (powered by OmniSharp) VS Code extension C# for Visual Studio Code (powered by OmniSharp) VS Code extension C# for Visual Studio Code (powered by OmniSharp) VS Code extension C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Nodig om C# services te debuggen. U het installeren door te klikken op het pictogram Extensies in de **activiteitsbalk** in VS-code en te zoeken naar de extensie, of via de VS Code Marketplace.

## <a name="setup"></a>Instellen

1. Open VS-code.
2. Klik op het pictogram Extensies in de **activiteitsbalk** aan de linkerkant van VS-code. Zoek naar "Service Fabric". Klik **op Installeren** voor de extensie Betrouwbare Services van servicefabric.

## <a name="commands"></a>Opdrachten
De Service Fabric Reliable Services-extensie voor VS-code biedt veel opdrachten om ontwikkelaars te helpen bij het maken en implementeren van Service Fabric-projecten. U opdrachten uit het **opdrachtpalet** aanroepen door op de opdrachtnaam in de invoerbalk te drukken `(Ctrl + Shift + p)`en de gewenste opdracht te selecteren in de snellijst. 

* Servicefabric: Toepassing maken 
* Servicefabric: Toepassing publiceren 
* Servicefabric: Implementatietoepassing 
* Servicefabric: Toepassing verwijderen  
* Servicefabric: Build-toepassing 
* Servicefabric: Schone toepassing 

### <a name="service-fabric-create-application"></a>Servicefabric: Toepassing maken

De opdracht **Servicefabric: Toepassing maken** maakt een nieuwe Service Fabric-toepassing in uw huidige werkruimte. Afhankelijk van welke yeoman generatoren zijn geïnstalleerd op uw ontwikkeling machine, u verschillende soorten Service Fabric applicatie, waaronder Java, C #, Container, en Gast projecten. 

1.  De **servicestructuur selecteren: de** opdracht Toepassing maken
2.  Selecteer het type voor uw nieuwe Service Fabric-toepassing. 
3.  Voer de naam in van de toepassing die u wilt maken
3.  Selecteer het type service dat u aan uw Service Fabric-toepassing wilt toevoegen. 
4.  Volg de aanwijzingen om de service een naam te geven. 
5.  De nieuwe Service Fabric-toepassing wordt weergegeven in de werkruimte.
6.  Open de nieuwe toepassingsmap zodat deze de hoofdmap in de werkruimte wordt. Vanaf hier u opdrachten blijven uitvoeren.

### <a name="service-fabric-add-service"></a>Servicefabric: Service toevoegen
De **opdracht Servicefabric: Service toevoegen** voegt een nieuwe service toe aan een bestaande Service Fabric-toepassing. De toepassing waaraan de service wordt toegevoegd, moet de hoofdmap van de werkruimte zijn. 

1.  Selecteer de **opdracht Servicefabric: Service toevoegen.**
2.  Selecteer het type van uw huidige Service Fabric-toepassing. 
3.  Selecteer het type service dat u aan uw Service Fabric-toepassing wilt toevoegen. 
4.  Volg de aanwijzingen om de service een naam te geven. 
5.  De nieuwe service wordt weergegeven in uw projectmap. 

### <a name="service-fabric-publish-application"></a>Servicefabric: Toepassing publiceren
De opdracht **Servicefabric: Publicatie-toepassing** implementeert uw Service Fabric-toepassing op een extern cluster. Het doelcluster kan een beveiligd of onveilig cluster zijn. Als parameters niet zijn ingesteld in Cloud.json, wordt de toepassing geïmplementeerd in het lokale cluster.

1.  De eerste keer dat de toepassing wordt gebouwd, wordt een Cloud.json-bestand gegenereerd in de projectmap.
2.  Voer de waarden in voor het cluster waarmee u verbinding wilt maken in het cloud.json-bestand.
3.  Selecteer de opdracht **Servicefabric: Publicatie van toepassing.**
4.  Bekijk het doelcluster met Service Fabric Explorer om te controleren of de toepassing is geïnstalleerd. 

### <a name="service-fabric-deploy-application-localhost"></a>Servicefabric: Implementatietoepassing (Localhost)
De opdracht **Servicefabric: Deploy Application** implementeert uw Service Fabric-toepassing in uw lokale cluster. Controleer of uw lokale cluster wordt uitgevoerd voordat u de opdracht gebruikt. 

1. De **servicestructuur selecteren: de** opdracht Toepassing implementeren
2. Bekijk het lokale cluster met Service\/Fabric Explorer (http: /localhost:19080/Explorer) om te controleren of de toepassing is geïnstalleerd. Dit kan enige tijd duren, dus wees geduldig.
3. U ook **Service Fabric: de** opdracht Toepassing publiceren zonder parameters die zijn ingesteld in het Cloud.json-bestand gebruiken om te implementeren in een lokaal cluster.

> [!NOTE]
> Java-toepassingen implementeren in het lokale cluster wordt niet ondersteund op Windows-machines.

### <a name="service-fabric-remove-application"></a>Servicefabric: Toepassing verwijderen
De opdracht **Servicefabric: Verwijderen van toepassing** verwijdert een Service Fabric-toepassing uit het cluster dat eerder is geïmplementeerd bij het gebruik van de VS-code-extensie. 

1.  Selecteer de **opdracht Servicefabric: Toepassing verwijderen.**
2.  Bekijk het cluster met Service Fabric Explorer om te controleren of de toepassing is verwijderd. Dit kan enige tijd duren, dus wees geduldig.

### <a name="service-fabric-build-application"></a>Servicefabric: Build-toepassing
De **opdracht Service Fabric: Build Application** kan java- of C#-servicefabric-toepassingen bouwen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing bevindt voordat u deze opdracht uitvoert. De opdracht identificeert het type toepassing (C# of Java) en bouwt uw toepassing dienovereenkomstig.
2.  Selecteer de opdracht **Servicefabric: Build Application.**
3.  De output van het bouwproces wordt naar de geïntegreerde terminal geschreven.

### <a name="service-fabric-clean-application"></a>Servicefabric: Schone toepassing
De opdracht **Servicefabric: Clean Application** verwijdert alle jarbestanden en native libraries die door de build zijn gegenereerd. Alleen geldig voor Java-toepassingen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing bevindt voordat u deze opdracht uitvoert. 
2.  Selecteer de opdracht **Servicefabric: Clean Application.**
3.  De output van het schone proces wordt naar de geïntegreerde terminal geschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en debuggen van C# Service Fabric-toepassingen met VS-code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Meer informatie over het [ontwikkelen en debuggen van Java Service Fabric-toepassingen met VS-code](./service-fabric-develop-java-applications-with-vs-code.md).
