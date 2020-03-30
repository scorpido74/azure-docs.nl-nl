---
title: Java-toepassingen ontwikkelen met Visual Studio Code
description: In dit artikel ziet u hoe u Java Service Fabric-toepassingen bouwen, implementeren en debuggen met behulp van Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 999dbb8c36c4e0413f287b2a73cf39ab4acd15f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610043"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Java Service Fabric-toepassingen ontwikkelen met Visual Studio Code

De [Service Fabric Reliable Services-extensie voor VS-code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) maakt het eenvoudig om Java Service Fabric-toepassingen te bouwen op Windows-, Linux- en macOS-besturingssystemen.

In dit artikel ziet u hoe u een Java Service Fabric-toepassing bouwen, implementeren en debuggen met behulp van Visual Studio Code.

> [!IMPORTANT]
> Service Fabric Java-toepassingen kunnen worden ontwikkeld op Windows-machines, maar kunnen alleen worden geïmplementeerd op Azure Linux-clusters. Foutopsporing java-toepassingen wordt niet ondersteund op Windows.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u vs-code, de servicestructuur extensie reliable services voor VS-code en eventuele afhankelijkheden die nodig zijn voor uw ontwikkelomgeving, al hebt geïnstalleerd. Zie [Aan de slag gaan voor](./service-fabric-get-started-vs-code.md#prerequisites)meer informatie.

## <a name="download-the-sample"></a>Het voorbeeld downloaden
Dit artikel maakt gebruik van de toepassing Stemmen in de [Service Fabric Java-toepassing snelstart voorbeeld GitHub repository](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Als u de opslagplaats naar uw ontwikkelingsmachine wilt klonen, voert u de volgende opdracht uit vanuit een terminalvenster (opdrachtvenster op Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>De toepassing openen in VS-code

Open VS-code.  Klik op het Explorer-pictogram in de **activiteitenbalk** en klik op **Map openen**of klik op Map bestand **> openen**. Navigeer naar de *map ./service-fabric-java-quickstart/Voting* in de map waar u de opslagplaats hebt gekloond en klik op **OK**. De werkruimte moet dezelfde bestanden bevatten die in de onderstaande schermafbeelding worden weergegeven.

![Java-stemtoepassing in werkruimte](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>De toepassing bouwen

1. Druk op (Ctrl + Shift + p) om het **opdrachtpalet** in VS-code te openen.
2. Zoek naar en selecteer de opdracht **Servicefabric: Build Application.** De build output wordt naar de geïntegreerde terminal gestuurd.

   ![Opdracht Toepassing bouwen in VS-code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>De toepassing implementeren in het lokale cluster
Nadat u de toepassing hebt gebouwd, u deze implementeren in het lokale cluster. 

> [!IMPORTANT]
> Java-toepassingen implementeren in het lokale cluster wordt niet ondersteund op Windows-machines.

1. Selecteer in het **opdrachtpalet**de **opdracht Servicefabric: Deploy Application (Localhost).** De uitvoer van het installatieproces wordt naar de geïntegreerde terminal gestuurd.

   ![Opdracht Toepassing implementeren in VS-code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. Wanneer de implementatie is voltooid, start u `http://localhost:19080/Explorer`een browser en opent u Service Fabric Explorer: . U moet zien dat de toepassing wordt uitgevoerd. Dit kan enige tijd duren, dus wees geduldig. 

   ![Stemtoepassing in Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. Nadat u hebt geverifieerd dat de toepassing wordt uitgevoerd, start `http://localhost:8080`u een browser en opent u deze pagina: . Dit is de web front-end van de applicatie. Je items toevoegen en erop klikken om te stemmen.

   ![Stemtoepassing in browser](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Als u de toepassing uit het cluster wilt verwijderen, selecteert u de opdracht **Servicestructuur: Toepassing verwijderen** uit het **opdrachtpalet**. De uitvoer van het verwijderproces wordt naar de geïntegreerde terminal verzonden. U Service Fabric Explorer gebruiken om te controleren of de toepassing is verwijderd uit het lokale cluster.

## <a name="debug-the-application"></a>Fouten in de toepassing opsporen
Wanneer u toepassingen in VS-code debugadt, moet de toepassing worden uitgevoerd op een lokaal cluster. Breekpunten kunnen vervolgens aan de code worden toegevoegd.

> [!IMPORTANT]
> Foutopsporing van Java-toepassingen wordt niet ondersteund op Windows-machines.

Voer de volgende stappen uit om de VotingDataService en de stemaanvraag voor foutopsporing voor te bereiden:

1. Update het *bestand Voting/VotingApplication/VotingDataServicePkg/Code/entryPoint.sh* bij.
Reageer op regel 6 (gebruik '#') en voeg de volgende opdracht toe aan de onderkant van het bestand:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Update het bestand *Voting/VotingApplication/ApplicationManifest.xml.* Stel de **kenmerken MinReplicaSetSize** en **TargetReplicaSetSize** in op '1' in het element **StatefulService:**
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klik op het pictogram Foutopsporing in de **activiteitsbalk** om de foutopsporingsweergave in VS-code te openen. Klik op het tandwielpictogram boven aan de foutopsporingsweergave en selecteer **Java** in het vervolgkeuzemenu. Het launch.json-bestand wordt geopend. 

   ![Pictogram Foutopsporing in VS-codewerkruimte](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. Stel in het bestand launch.json de poortwaarde in in de configuratie met de naam **Foutopsporing (Koppelen)** op **8001**. Sla het bestand op.

   ![Foutopsporingsconfiguratie voor de launch.json](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Implementeer de toepassing in het lokale cluster met de opdracht **Servicefabric: Deploy Application (Localhost).** Controleer of de toepassing wordt uitgevoerd in Service Fabric Explorer. Uw aanvraag is nu klaar om te worden gedebugged.

Voer de volgende stappen uit om een breekpunt in te stellen:

1. Open in Explorer het bestand */Voting/VotingDataService/src/statefulservice/VotingDataService.java.* Stel een breekpunt in op `try` de `addItem` eerste regel code in het blok in de methode (regel 80).
   
   ![Breekpunt instellen in de stemgegevensservice](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Zorg ervoor dat u breekpunten instelt op uitvoerbare coderegels. Bijvoorbeeld breekpunten die zijn ingesteld `try` op `catch` methodedeclaratie, instructies of instructies worden gemist door de foutopsporing.
2. Als u wilt beginnen met foutopsporing, klikt u op het pictogram Foutopsporing in de **activiteitsbalk,** selecteert u de configuratie **Foutopsporing (Bijvoegen)** in het menu Foutopsporing en klikt u op de knop Uitvoeren (groene pijl).

   ![Configuratie van foutopsporing (koppelen)](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Ga in een webbrowser naar `http://localhost:8080`. Typ een nieuw item in het tekstvak en klik op **+ Toevoegen**. Je breekpunt moet geraakt worden. U de werkbalk Foutopsporing boven aan VS-code gebruiken om de uitvoering voort te zetten, regels te omzeilen, in methoden te stappen of uit de huidige methode te stappen. 
   
   ![Hit breakpoint](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Als u de foutopsporingssessie wilt beëindigen, klikt u op het stekkerpictogram op de werkbalk Foutopsporing boven aan VS-code.
   
   ![Verbinding verbreken van foutopsporing](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. Wanneer u klaar bent met foutopsporing, u de opdracht **Servicefabric: Toepassing verwijderen** gebruiken om de stemtoepassing uit uw lokale cluster te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [ontwikkelen en debuggen van C# Service Fabric-toepassingen met VS-code](./service-fabric-develop-csharp-applications-with-vs-code.md).
