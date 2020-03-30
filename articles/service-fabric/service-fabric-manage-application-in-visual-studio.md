---
title: Toepassingen beheren in Visual Studio
description: Gebruik Visual Studio om uw Azure Service Fabric-toepassingen en -services te maken, te ontwikkelen, te verpakken, te implementeren en te debuggen.
author: mikkelhegn
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: mikhegn
ms.openlocfilehash: d6734f5da0fb7e5c9052b26b55b2d90b068bdbbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614329"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Visual Studio gebruiken om het schrijven en beheren van uw Service Fabric-toepassingen te vereenvoudigen
U uw Azure Service Fabric-toepassingen en -services beheren via Visual Studio. Zodra u [uw ontwikkelomgeving](service-fabric-get-started.md)hebt ingesteld, u Visual Studio gebruiken om Service Fabric-toepassingen te maken, services toe te voegen of toepassingen in uw lokale ontwikkelingscluster te verpakken, te registreren en te implementeren.

## <a name="deploy-your-service-fabric-application"></a>Uw Service Fabric-toepassing implementeren
Standaard combineert het implementeren van een toepassing de volgende stappen in één eenvoudige bewerking:

1. Het toepassingspakket maken
2. Het toepassingspakket uploaden naar het afbeeldingsarchief
3. Het toepassingstype registreren
4. Alle lopende toepassingsinstanties verwijderen
5. Een toepassingsinstantie maken

In Visual Studio wordt op **F5** gedrukt uw toepassing geïmplementeerd en wordt de foutopsporing gekoppeld aan alle toepassingsinstanties. U **Ctrl+F5** gebruiken om een toepassing te implementeren zonder foutopsporing, of u publiceren naar een lokaal of extern cluster met behulp van het publicatieprofiel.

### <a name="application-debug-mode"></a>Foutopsporingsmodus voor toepassingen
Visual Studio biedt een eigenschap genaamd **Application Debug Mode**, die bepaalt hoe u visual studios wilt dat de implementatie van toepassingen te behandelen als onderdeel van foutopsporing.

#### <a name="to-set-the-application-debug-mode-property"></a>De eigenschap Foutopsporingsmodus voor toepassingen instellen
1. Kies **Eigenschappen** (of druk op de **F4-toets)** in het snelmenu van het servicefabric-toepassingsproject (*.sfproj).
2. Stel in het venster **Eigenschappen** de eigenschap **Foutopsporingsmodus voor toepassingen** in.

![Eigenschap Foutopsporingsmodus instellen][debugmodeproperty]

#### <a name="application-debug-modes"></a>Foutopsporingsmodi voor toepassingen

1. **Toepassing vernieuwen** Met deze modus u uw code snel wijzigen en debuggen en ondersteunt u het bewerken van statische webbestanden tijdens het foutopsporing. Deze modus werkt alleen als uw lokale ontwikkelingscluster zich in de modus 1-knooppunt bevindt. Dit is de standaardfoutfoutmodus voor toepassingen.
2. **Als u de toepassing verwijdert** wanneer de foutopsporingssessie is beëindigd, wordt de toepassing verwijderd.
3. **Automatische upgrade** De toepassing blijft worden uitgevoerd wanneer de foutopsporingssessie is afgelopen. De volgende foutopsporingssessie behandelt de implementatie als een upgrade. Het upgradeproces bewaart alle gegevens die u in een vorige foutopsporingssessie hebt ingevoerd.
4. **Toepassing behouden** De toepassing blijft in het cluster worden uitgevoerd wanneer de foutopsporingssessie is afgelopen. Aan het begin van de volgende foutopsporingssessie wordt de toepassing verwijderd.

Voor **auto-upgrade** worden gegevens bewaard door de toepassingsupgrademogelijkheden van Service Fabric toe te passen. Zie [Service Fabric-toepassingsupgrade](service-fabric-application-upgrade.md)voor meer informatie over het upgraden van toepassingen en hoe u een upgrade in een echte omgeving uitvoeren.

## <a name="add-a-service-to-your-service-fabric-application"></a>Een service toevoegen aan uw Service Fabric-toepassing
U nieuwe services aan uw toepassing toevoegen om de functionaliteit uit te breiden. Om ervoor te zorgen dat de service is opgenomen in uw aanvraagpakket, voegt u de service toe via het **menu-item New Fabric Service...**

![Een nieuwe Service Fabric-service toevoegen][newservice]

Selecteer een projecttype ServiceFabric dat u aan uw toepassing wilt toevoegen en geef een naam voor de service op.  Zie [Een framework voor uw service kiezen](service-fabric-choose-framework.md) om u te helpen beslissen welk servicetype u wilt gebruiken.

![Selecteer een projecttype servicefabric-service dat aan uw toepassing moet worden toegevoegd][addserviceproject]

De nieuwe service wordt toegevoegd aan uw oplossing en bestaand toepassingspakket. De serviceverwijzingen en een standaardservice-instantie worden toegevoegd aan het toepassingsmanifest, waardoor de service wordt gemaakt en gestart wanneer u de toepassing de volgende keer implementeert.

![De nieuwe service wordt toegevoegd aan uw sollicitatiemanifest][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Uw Service Fabric-toepassing verpakken
Als u de toepassing en de bijbehorende services wilt implementeren in een cluster, moet u een toepassingspakket maken.  Het pakket organiseert het toepassingsmanifest, servicemanifesten en andere benodigde bestanden in een specifieke lay-out.  Visual Studio stelt het pakket in en beheert het in de map van het toepassingsproject, in de 'pkg'-map.  Als u **op Pakket** klikt in het contextmenu **Toepassing,** wordt het toepassingspakket gemaakt of bijgewerkt.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Toepassingen en toepassingstypen verwijderen met Behulp van Cloud Explorer
U basisclusterbeheerbewerkingen uitvoeren vanuit Visual Studio met Behulp van Cloud Explorer, die u starten in het menu **Weergave.** U bijvoorbeeld toepassingen verwijderen en toepassingstypen voor lokale of externe clusters verwijderen.

![Een toepassing verwijderen][removeapplication]

> [!TIP]
> Zie Uw cluster visualiseren met [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)voor een uitgebreidere clusterbeheerfunctionaliteit.
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Volgende stappen
* [Service Fabric-toepassingsmodel](service-fabric-application-model.md)
* [Implementatie van servicefabric-toepassingen](service-fabric-deploy-remove-applications.md)
* [Toepassingsparameters voor meerdere omgevingen beheren](service-fabric-manage-multiple-environment-app-configuration.md)
* [Debugging van uw Service Fabric-toepassing](service-fabric-debugging-your-application.md)
* [Uw cluster visualiseren met Behulp van Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
