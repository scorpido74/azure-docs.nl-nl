---
title: Uw cluster visualiseren met Azure Service Fabric Explorer
description: Service Fabric Explorer is een toepassing voor het inspecteren en beheren van cloudtoepassingen en -knooppunten in een Microsoft Azure Service Fabric-cluster.
author: mikkelhegn
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: mikhegn
ms.openlocfilehash: 80e3d990b6e8026c57ffff0048d0447a95529564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258185"
---
# <a name="visualize-your-cluster-with-service-fabric-explorer"></a>Visualisern van uw cluster met Service Fabric Explorer

Service Fabric Explorer (SFX) is een open-source tool voor het inspecteren en beheren van Azure Service Fabric clusters. Service Fabric Explorer is een desktopapplicatie voor Windows, macOS en Linux.

## <a name="service-fabric-explorer-download"></a>Download Service Fabric Explorer

Gebruik de volgende koppelingen om Service Fabric Explorer als bureaubladtoepassing te downloaden:

- Windows
  - https://aka.ms/sfx-windows

- Linux
  - https://aka.ms/sfx-linux-x86
  - https://aka.ms/sfx-linux-x64

- macOS
  - https://aka.ms/sfx-macos

> [!NOTE]
> De desktopversie van Service Fabric Explorer kan meer of minder functies bevatten dan de clusterondersteuning. U teruggaan naar de Service Fabric Explorer-versie die is geïmplementeerd in het cluster om volledige compatibiliteit met functies te garanderen.
>
>

### <a name="running-service-fabric-explorer-from-the-cluster"></a>ServiceFabric Explorer uitvoeren vanuit het cluster

Service Fabric Explorer wordt ook gehost in het HTTP-beheereindpunt van een Service Fabric-cluster. Als u SFX in een webbrowser wilt starten, bladert u vanuit elke\/browser naar het HTTP-beheereindpunt van het cluster , bijvoorbeeld https: /clusterFQDN:19080.

Voor het instellen van een ontwikkelaarswerkstation u Service https://localhost:19080/ExplorerFabric Explorer op uw lokale cluster starten door te navigeren naar. Kijk naar dit artikel om [uw ontwikkelingsomgeving voor](service-fabric-get-started.md)te bereiden.

> [!NOTE]
> Als uw cluster is beveiligd door een zelfondertekend certificaat, ontvangt u een foutmelding van de webbrowser "Deze site is niet veilig". U gewoon doorgaan via de meeste moderne webbrowsers door het overschrijven van de waarschuwing. In een productieomgeving moet uw cluster worden beveiligd met de gemeenschappelijke naam en een certificaat van de certificaatautoriteit. 
>
>

## <a name="connect-to-a-service-fabric-cluster"></a>Verbinding maken met een Service Fabric-cluster
Als u verbinding wilt maken met een cluster servicestructuur, hebt u standaard het eindpunt van clustersbeheer (FQDN/IP) en de HTTP-beheereindpuntpoort (standaard 19080) nodig. Bijvoorbeeld https\:-mysfcluster.westus.cloudapp.azure.com:19080. Gebruik het selectievakje Verbinding maken met localhost om verbinding te maken met een lokaal cluster op uw werkstation.

### <a name="connect-to-a-secure-cluster"></a>Verbinding maken met een beveiligd cluster
U de toegang van clients tot uw Service Fabric-cluster beheren met certificaten of met Azure Active Directory (AAD).

Als u probeert verbinding te maken met een beveiligd cluster, moet u, afhankelijk van de configuratie van het cluster, een clientcertificaat tonen of u aanmelden met AAD.

## <a name="understand-the-service-fabric-explorer-layout"></a>De indeling Van De Verkenner seis
U door Service Fabric Explorer navigeren met de structuur aan de linkerkant. Aan de basis van de structuur biedt het clusterdashboard een overzicht van uw cluster, inclusief een overzicht van de status van toepassing en knooppunt.

![Clusterdashboard servicefabric Explorer][sfx-cluster-dashboard]

### <a name="view-the-clusters-layout"></a>De indeling van het cluster weergeven
Knooppunten in een cluster van Servicefabric worden geplaatst in een tweedimensionaal raster van foutdomeinen en upgradedomeinen. Deze plaatsing zorgt ervoor dat uw toepassingen beschikbaar blijven in aanwezigheid van hardwarefouten en toepassingsupgrades. U met behulp van de clusterkaart bekijken hoe het huidige cluster is ingedeeld.

![Clusterkaart van Service Fabric Explorer][sfx-cluster-map]

### <a name="view-applications-and-services"></a>Toepassingen en services weergeven
Het cluster bevat twee subtrees: een voor toepassingen en een voor knooppunten.

U de toepassingsweergave gebruiken om door de logische hiërarchie van Service Fabric te navigeren: toepassingen, services, partities en replica's.

In het onderstaande voorbeeld bestaat de applicatie **MyApp** uit twee diensten, **MyStatefulService** en **WebService.** Aangezien **MyStatefulService** stateful is, bevat het een partitie met één primaire en twee secundaire replica's. WebSvcService is daarentegen stateloos en bevat één exemplaar.

![Toepassingsweergave van Service Fabric Explorer][sfx-application-tree]

Op elk niveau van de boom, het hoofdvenster toont relevante informatie over het item. U bijvoorbeeld de status en versie voor een bepaalde service zien.

![Deelvenster Essentials van Service Fabric Explorer][sfx-service-essentials]

### <a name="view-the-clusters-nodes"></a>De knooppunten van het cluster weergeven
In de knooppuntweergave ziet u de fysieke indeling van het cluster. Voor elk knooppunt kunt u controleren voor welke toepassingen er op het knooppunt code is geïmplementeerd. Meer specifiek u zien welke replica's er momenteel worden uitgevoerd.

## <a name="actions"></a>Acties
Service Fabric Explorer biedt een snelle manier om acties op knooppunten, toepassingen en services binnen uw cluster aan te roepen.

Als u bijvoorbeeld een toepassingsinstantie wilt verwijderen, kiest u de toepassing in de structuur aan de linkerkant en kiest u**Toepassing Voor verwijderen** **van acties** > .

![Een toepassing verwijderen in Service Fabric Explorer][sfx-delete-application]

> [!TIP]
> U dezelfde acties uitvoeren door op de ellips naast elk element te klikken.
>
> Elke actie die kan worden uitgevoerd via Service Fabric Explorer kan ook worden uitgevoerd via PowerShell of een REST API, om automatisering mogelijk te maken.
>
>

U Service Fabric Explorer ook gebruiken om toepassingsinstanties voor een bepaald toepassingstype en -versie te maken. Kies het toepassingstype in de structuurweergave en klik vervolgens op de koppeling **App-instantie maken** naast de versie die u in het rechterdeelvenster wilt gebruiken.

![Een toepassingsinstantie maken in Service Fabric Explorer][sfx-create-app-instance]

> [!NOTE]
> Service Fabric Explorer ondersteunt geen parameters bij het maken van toepassingsinstanties. Toepassingsinstanties gebruiken standaardparameterwaarden.
>
>

## <a name="event-store"></a>Evenementenwinkel
EventStore is een functie die wordt aangeboden door het platform dat Service Fabric-platformgebeurtenissen biedt die beschikbaar zijn in de Service Fabric Explorer en via REST API. U een momentopnameweergave zien van wat er in uw cluster aan de hand is voor elke entiteit, bijvoorbeeld knooppunt, service, toepassing en query op basis van het tijdstip van de gebeurtenis. U ook meer lezen over de EventStore in het [EventStore-overzicht.](service-fabric-diagnostics-eventstore.md)   

![EventStore (EventStore)][sfx-eventstore]

>[!NOTE]
>Vanaf Service Fabric versie 6.4. EventStore is standaard niet ingeschakeld en moet zijn ingeschakeld in de sjabloon resourcemanager

>[!NOTE]
>Vanaf Service Fabric versie 6.4. De EventStore-API's zijn alleen beschikbaar voor Windows-clusters die alleen op Azure worden uitgevoerd. We werken aan het porten van deze functionaliteit naar Linux en onze Standalone clusters.

## <a name="image-store-viewer"></a>Afbeelding smaken van archiefviewer
Image store viewer is een functie die wordt aangeboden als u de Native Image Store gebruikt waarmee de huidige inhoud van het Image Store kan worden bekeken en bestands- en mapgegevens kunnen worden opgevraagd, samen met het verwijderen van bestanden/mappen.

![Clusterkaart van Service Fabric Explorer][sfx-imagestore]

## <a name="backup-and-restore"></a>Back-up en herstel
Service Fabric Explorer biedt de mogelijkheid om te communiceren met [back-up en herstel.](./service-fabric-reliable-services-backup-restore.md) Om back-up- en herstelfuncties in SFX te kunnen zien, moet de geavanceerde modus zijn ingeschakeld.

![Geavanceerde modus inschakelen][0]
 
De volgende bewerkingen zijn mogelijk:

* Een back-upbeleid maken, bewerken en verwijderen.
* Back-up voor een toepassing, service of partitie in- en uitschakelen.
* Back-up voor een toepassing, service of partitie onderbreken en hervatten.
* Back-up van een partitie activeren en bijhouden.
* Herstel activeren en bijhouden voor een partitie.

Zie de [VERWIJZING REST API](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)voor meer informatie over de service Back-up en Herstel.
## <a name="next-steps"></a>Volgende stappen
* [Uw Service Fabric-toepassingen beheren in Visual Studio](service-fabric-manage-application-in-visual-studio.md)
* [Implementatie van Service Fabric-toepassingen met PowerShell](service-fabric-deploy-remove-applications.md)

<!--Image references-->
[sfx-cluster-dashboard]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-dashboard.png
[sfx-cluster-map]: ./media/service-fabric-visualizing-your-cluster/sfx-cluster-map.png
[sfx-application-tree]: ./media/service-fabric-visualizing-your-cluster/sfx-application-tree.png
[sfx-service-essentials]: ./media/service-fabric-visualizing-your-cluster/sfx-service-essentials.png
[sfx-delete-application]: ./media/service-fabric-visualizing-your-cluster/sfx-delete-application.png
[sfx-create-app-instance]: ./media/service-fabric-visualizing-your-cluster/sfx-create-app-instance.png
[sfx-eventstore]: ./media/service-fabric-diagnostics-eventstore/eventstore.png
[sfx-imagestore]: ./media/service-fabric-visualizing-your-cluster/sfx-image-store.png
[0]: ./media/service-fabric-backuprestoreservice/advanced-mode.png