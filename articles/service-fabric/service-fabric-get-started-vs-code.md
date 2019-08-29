---
title: Aan de slag met Azure Service Fabric met VS code | Microsoft Docs
description: Dit artikel bevat een overzicht van het maken van Service Fabric-toepassingen met Visual Studio code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 9662ebd26a263fa006c8fccf877fdc950e9014c0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102963"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric voor Visual Studio code

De [uitbrei ding Service Fabric reliable Services voor VS code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) biedt de hulpprogram ma's die nodig zijn voor het maken, bouwen en fouten opsporen van service Fabric toepassingen op Windows-, Linux-en macOS-besturings systemen.

Dit artikel bevat een overzicht van de vereisten en instellingen van de uitbrei ding, evenals het gebruik van de verschillende opdrachten die worden geleverd door de uitbrei ding. 

> [!IMPORTANT]
> Service Fabric Java-toepassingen kunnen worden ontwikkeld op Windows-computers, maar kunnen alleen worden geïmplementeerd op Azure Linux-clusters. Fout opsporing van Java-toepassingen wordt niet ondersteund in Windows.

## <a name="prerequisites"></a>Vereisten

De volgende vereiste onderdelen moeten worden geïnstalleerd in alle omgevingen.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Yeoman-generatoren: Installeer de juiste Generators voor uw toepassing

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

De volgende vereiste onderdelen moeten worden geïnstalleerd voor Java-ontwikkeling:

* [Java-SDK](https://aka.ms/azure-jdks) (versie 1,8)
* [Gradle](https://gradle.org/install/)
* [Fout opsporing voor Java versus code-uitbrei ding](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Nodig voor fout opsporing van Java-Services. Fout opsporing van Java-Services wordt alleen ondersteund in Linux. U kunt dit doen door te klikken op het pictogram extensies in de **activiteiten balk** in VS code en te zoeken naar de uitbrei ding of via de VS code Marketplace.

De volgende vereiste onderdelen moeten worden geïnstalleerd voor .NET core/C# Development:

* [.Net core](https://www.microsoft.com/net/learn/get-started) (versie 2.0.0 of hoger)
* [voor Visual Studio code (aangedreven door OmniSharp) versus code-uitbrei ding C# ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) Nodig voor het C# opsporen van services. U kunt dit doen door te klikken op het pictogram extensies in de **activiteiten balk** in VS code en te zoeken naar de uitbrei ding of via de VS code Marketplace.

## <a name="setup"></a>Instellen

1. Open VS code.
2. Klik op het pictogram uitbrei dingen in de **activiteiten balk** aan de linkerkant van VS code. Zoek naar "Service Fabric". Klik op **installeren** voor de uitbrei ding service Fabric reliable Services.

## <a name="commands"></a>Opdrachten
De uitbrei ding Service Fabric Reliable Services voor VS code biedt een groot aantal opdrachten waarmee ontwikkel aars Service Fabric projecten kunnen maken en implementeren. U kunt opdrachten vanuit het **opdracht palet** aanroepen door `(Ctrl + Shift + p)`op, op de opdracht naam in de invoer balk te drukken en de gewenste opdracht te selecteren in de lijst prompt. 

* Service Fabric: Toepassing maken 
* Service Fabric: Toepassing publiceren 
* Service Fabric: Toepassing implementeren 
* Service Fabric: Toepassing verwijderen  
* Service Fabric: Toepassing bouwen 
* Service Fabric: Toepassing opschonen 

### <a name="service-fabric-create-application"></a>Service Fabric: Toepassing maken

De **service Fabric: Met de** opdracht toepassing maken maakt u een nieuwe service Fabric-toepassing in uw huidige werk ruimte. Afhankelijk van welke Yeoman-generators zijn geïnstalleerd op uw ontwikkel computer, kunt u verschillende soorten Service Fabric toepassing maken, met inbegrip van C#Java-,, container-en gast projecten. 

1.  Selecteer de **service Fabric: Service** opdracht toevoegen
2.  Selecteer het type voor uw nieuwe Service Fabric-toepassing. 
3.  Voer de naam in van de toepassing die u wilt maken
3.  Selecteer het type service dat u wilt toevoegen aan uw Service Fabric-toepassing. 
4.  Volg de aanwijzingen om de naam van de service te wijzigen. 
5.  De nieuwe Service Fabric-toepassing wordt weer gegeven in de werk ruimte.
6.  Open de map nieuwe toepassing, zodat deze de hoofdmap wordt in de werk ruimte. U kunt nu door gaan met het uitvoeren van opdrachten.

### <a name="service-fabric-add-service"></a>Service Fabric: Service toevoegen
De **service Fabric: Met service** opdracht toevoegen voegt u een nieuwe service toe aan een bestaande service Fabric-toepassing. De toepassing waaraan de service wordt toegevoegd, moet de hoofd directory van de werk ruimte zijn. 

1.  Selecteer de **service Fabric: Opdracht service** toevoegen.
2.  Selecteer het type van uw huidige Service Fabric-toepassing. 
3.  Selecteer het type service dat u wilt toevoegen aan uw Service Fabric-toepassing. 
4.  Volg de aanwijzingen om de naam van de service te wijzigen. 
5.  De nieuwe service wordt weer gegeven in de projectmap. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Toepassing publiceren
De **service Fabric: Opdracht toepassing** publiceren implementeert uw service Fabric-toepassing op een extern cluster. Het doel cluster kan een beveiligd of een niet-beveiligd cluster zijn. Als er geen para meters zijn ingesteld in Cloud. json, wordt de toepassing geïmplementeerd naar het lokale cluster.

1.  De eerste keer dat de toepassing is gebouwd, wordt er een Cloud. JSON-bestand gegenereerd in de projectmap.
2.  Voer de waarden in voor het cluster waarmee u verbinding wilt maken in het bestand Cloud. json.
3.  Selecteer de **service Fabric: De opdracht** toepassing publiceren.
4.  Bekijk het doel cluster met Service Fabric Explorer om te bevestigen dat de toepassing is geïnstalleerd. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Toepassing implementeren (localhost)
De **service Fabric: Met de** opdracht toepassing implementeren worden uw service Fabric-toepassing geïmplementeerd naar uw lokale cluster. Zorg ervoor dat het lokale cluster wordt uitgevoerd voordat u de opdracht gebruikt. 

1. Selecteer de **service Fabric: Toepassings** opdracht implementeren
2. Bekijk het lokale cluster met Service Fabric Explorer (http:\//localhost: 19080/Explorer) om te bevestigen dat de toepassing is geïnstalleerd. Dit kan enige tijd duren. het is dus een ogen blik geduld.
3. U kunt ook service Fabric **gebruiken: Opdracht toepassing** publiceren zonder para meters in het bestand Cloud. json dat moet worden geïmplementeerd op een lokaal cluster.

> [!NOTE]
> Het implementeren van Java-toepassingen voor het lokale cluster wordt niet ondersteund op Windows-computers.

### <a name="service-fabric-remove-application"></a>Service Fabric: Toepassing verwijderen
De **service Fabric: Met de** opdracht toepassing verwijderen wordt een service Fabric-toepassing verwijderd uit het cluster waarop deze eerder is geïmplementeerd, met behulp van de VS code-extensie. 

1.  Selecteer de **service Fabric: Opdracht van** toepassing verwijderen.
2.  Bekijk het cluster met Service Fabric Explorer om te bevestigen dat de toepassing is verwijderd. Dit kan enige tijd duren. het is dus een ogen blik geduld.

### <a name="service-fabric-build-application"></a>Service Fabric: Toepassing bouwen
De **service Fabric: De opdracht** toepassings toepassing maken kan Java- C# of service Fabric-toepassingen bouwen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing bevindt voordat u deze opdracht uitvoert. De opdracht geeft het type toepassing (C# of Java) aan en bouwt uw toepassing dienovereenkomstig.
2.  Selecteer de **service Fabric: De opdracht** toepassing bouwen.
3.  De uitvoer van het bouw proces wordt geschreven naar de geïntegreerde Terminal.

### <a name="service-fabric-clean-application"></a>Service Fabric: Toepassing opschonen
De **service Fabric: De opdracht** toepassing opschonen verwijdert alle JAR-bestanden en systeem eigen bibliotheken die zijn gegenereerd door de build. Alleen geldig voor Java-toepassingen. 

1.  Zorg ervoor dat u zich in de hoofdmap van de toepassing bevindt voordat u deze opdracht uitvoert. 
2.  Selecteer de **service Fabric: De opdracht** toepassing opschonen.
3.  De uitvoer van het schone proces wordt naar de geïntegreerde terminal geschreven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en C# opsporen van fouten in service Fabric toepassingen met VS code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Meer informatie over het [ontwikkelen en opsporen van fouten in Java service Fabric-toepassingen met VS code](./service-fabric-develop-java-applications-with-vs-code.md).
