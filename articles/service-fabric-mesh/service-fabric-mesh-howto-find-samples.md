---
title: Azure Service Fabric mesh-voor beelden zoeken
description: Hier volgt een index van beschik bare Service Fabric mesh-voorbeeld toepassingen. De broncode in die voorbeelden laten zien hoe u een bepaald scenario kunt uitvoeren met behulp van het Service Fabric-resourcemodel.
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75461904"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric Mesh-voorbeelden zoeken

Deze tabel biedt een overzicht van de beschikbare Service Fabric Mesh-voorbeeldtoepassingen. De broncode in die voorbeelden laten zien hoe u een bepaald scenario kunt uitvoeren met behulp van het Service Fabric-resourcemodel.

Zie de [GitHub-pagina met voorbeeldsjablonen](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md) voor meer informatie over het direct implementeren van sjablonen naar Azure.

|Voorbeeldsjabloon|Scenariobeschrijving|Broncode|Ontwikkelhulpprogramma's|
|------------|--------------------|----------|----------------------|
| [Hallo wereld-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | Statische webpagina die wordt gehost in een container. Voor Linux wordt nginx gebruikt, voor Windows wordt IIS gebruikt. | [Bron code](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | Geen vereisten |
| [Teller-app voor Azure Files-volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Sla de status op door een koppeling aan te brengen met op Azure Files gebaseerde volumes in de container. <br><br> **Opmerking:** voor deze sjabloon is vereist dat er al een Azure Files-bestandsshare is ingericht. Zie hiervoor de [instructies](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share). | [Bron code](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | Maak een toepassing met een front-end- en back-endservice die gebruikmaken van DNS-omzetting. Als zelfstudie [hier](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore) te vinden. | [Bron code](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh Tooling |
| [Visuele objecten-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | Microservices upgraden en de schaal ervan in een toepassing aanpassen. | [Bron code](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh Tooling |
| [Stem-app](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | Maak een toepassing met een front-end- en back-endservice die gebruikmaken van DNS-omzetting. | [Bron code](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Visual Studio net-Hulpprogram Ma's voor de Windows-versie VS code/DotNet CLI kunnen worden gebruikt voor de Linux-versie |
| [Teller-app voor betrouwbare Service Fabric-volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Sla de status op door een koppeling aan te brengen met op Service Fabric Reliable Disk gebaseerde volumes in de container.| [Bron code](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
