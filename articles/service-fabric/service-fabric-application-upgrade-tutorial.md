---
title: Zelfstudie voor upgrade van de Service Fabric-app
description: In dit artikel wordt de ervaring van het implementeren van een Service Fabric-toepassing, het wijzigen van de code en het uitrollen van een upgrade met Behulp van Visual Studio doorgenomen.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: db814b972db1aee56be0858c9ff5d1c382640642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464822"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Zelfstudie voor upgrade van servicefabric-toepassingen met Visual Studio
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric vereenvoudigt het proces van het upgraden van cloudtoepassingen door ervoor te zorgen dat alleen gewijzigde services worden bijgewerkt en dat de status van de toepassing gedurende het hele upgradeproces wordt gecontroleerd. Het rolt ook automatisch terug de toepassing naar de vorige versie bij het tegenkomen van problemen. Service Fabric applicatie upgrades zijn *Zero Downtime,* omdat de applicatie kan worden opgewaardeerd zonder downtime. Deze zelfstudie gaat over het voltooien van een rolling upgrade van Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Stap 1: Het voorbeeld visuele objecten bouwen en publiceren
Download eerst de toepassing [Visuele objecten](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) van GitHub. Bouw en publiceer de toepassing vervolgens door met de rechtermuisknop op het toepassingsproject **VisualObjects**te klikken en de opdracht **Publiceren** te selecteren in het menu-item ServiceFabric.

![Contextmenu voor een Service Fabric-toepassing][image1]

Als **u Publiceren selecteert,** wordt een pop-up weergegeven en u het **doelprofiel** instellen **op Publicatieprofielen\Local.xml**. Het venster moet er als volgt uitzien voordat u op **Publiceren**klikt.

![Een Service Fabric-toepassing publiceren][image2]

U nu in het dialoogvenster op **Publiceren** klikken. U Service Fabric Explorer gebruiken [om het cluster en de toepassing weer te geven.](service-fabric-visualizing-your-cluster.md) De toepassing Visuele objecten heeft een webservice [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) waar u naartoe gaan door de adresbalk van uw browser in te typen.  U ziet 10 zwevende visuele objecten bewegen op het scherm.

**LET OP:** Als u `Cloud.xml` wordt geïmplementeerd op profiel (Azure Service Fabric), moet de toepassing vervolgens beschikbaar zijn op **http://{ServiceFabricName}.{ Regio}.cloudapp.azure.com:8081/visualobjects/**. Zorg ervoor dat `8081/TCP` u hebt geconfigureerd in de load balancer (zoek de Load Balancer in dezelfde resourcegroep als de instantie ServiceFabric).

## <a name="step-2-update-the-visual-objects-sample"></a>Stap 2: Voorbeeld van visuele objecten bijwerken
U zult merken dat met de versie die in stap 1 is geïmplementeerd, de visuele objecten niet roteren. Laten we deze toepassing upgraden naar een toepassing waar de visuele objecten ook draaien.

Selecteer het project VisualObjects.ActorService in de oplossing VisualObjects en open het **VisualObjectActor.cs** bestand. Ga in dat bestand `MoveObject`naar de `visualObject.Move(false)`methode , `visualObject.Move(true)`reageer uit en maak geen commentaar . Met deze codewijziging worden de objecten gedraaid nadat de service is bijgewerkt.  **Nu u bouwen (niet opnieuw) de oplossing,** die de gewijzigde projecten bouwt. Als u *Alle opnieuw opbouwen*selecteert, moet u de versies voor alle projecten bijwerken.

We moeten ook onze applicatie versie. Als u de versiewijzigingen wilt aanbrengen nadat u met de rechtermuisknop op het **VisualObjects-project** hebt geklikt, u de optie **Manifestversies van** Visual Studio bewerken gebruiken. Als u deze optie selecteert, wordt het dialoogvenster voor editieversies als volgt weergegeven:

![Dialoogvenster Versiebeheer][image3]

Werk de versies voor de gewijzigde projecten en hun codepakketten bij, samen met de toepassing naar versie 2.0.0. Nadat de wijzigingen zijn aangebracht, moet het manifest er als volgt uitzien (vette gedeelten tonen de wijzigingen):

![Versies bijwerken][image4]

De hulpprogramma's van Visual Studio kunnen automatische rollups van versies uitvoeren bij het selecteren **van toepassings- en serviceversies automatisch bijwerken.** Als u [SemVer](http://www.semver.org)gebruikt, moet u de versie van het code- en/of configuratiepakket alleen bijwerken als die optie is geselecteerd.

Sla de wijzigingen op en schakel nu het selectievakje **Toepassing bijwerken** in.

## <a name="step-3--upgrade-your-application"></a>Stap 3: Uw toepassing upgraden
Maak uzelf vertrouwd met de [upgradeparameters van](service-fabric-application-upgrade-parameters.md) de toepassing en het [upgradeproces](service-fabric-application-upgrade.md) om een goed inzicht te krijgen in de verschillende upgradeparameters, time-outs en gezondheidscriteria die kunnen worden toegepast. Voor deze walkthrough is het beoordelingscriterium voor de servicestatus ingesteld op de standaardmodus (modus niet-gecontroleerd). U deze instellingen configureren door **Upgrade-instellingen configureren** te selecteren en vervolgens de gewenste parameters aan te passen.

Nu zijn we helemaal klaar om de upgrade van de toepassing te starten door **Publiceren te selecteren.** Met deze optie wordt uw toepassing gesanerd naar versie 2.0.0, waarin de objecten roteren. Service Fabric-upgrades één updatedomein tegelijk (sommige objecten worden eerst bijgewerkt, gevolgd door anderen) en de service blijft toegankelijk tijdens de upgrade. Toegang tot de service kan worden gecontroleerd via uw client (browser).  

Nu, als de applicatie-upgrade vordert, u deze controleren met Service Fabric Explorer, met behulp van het tabblad **Upgrades in uitvoering** onder de toepassingen.

In een paar minuten moeten alle updatedomeinen worden bijgewerkt (voltooid) en in het uitvoervenster Van Visual Studio moet ook worden vermeld dat de upgrade is voltooid. En je moet vinden dat *alle* visuele objecten in uw browser venster zijn nu roterende!

U proberen de versies te wijzigen en van versie 2.0.0 naar versie 3.0.0 te gaan als oefening, of zelfs van versie 2.0.0 terug naar versie 1.0.0. Speel met time-outs en gezondheidsbeleid om jezelf ermee vertrouwd te maken. Bij het implementeren naar een Azure-cluster in plaats van een lokaal cluster, moeten de gebruikte parameters mogelijk verschillen. We raden u aan de time-outs conservatief in te stellen.

## <a name="next-steps"></a>Volgende stappen
[Het upgraden van uw toepassing met PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) leidt u door een applicatie-upgrade met PowerShell.

Bepaal hoe uw toepassing wordt bijgewerkt met behulp van [upgradeparameters.](service-fabric-application-upgrade-parameters.md)

Maak uw toepassingsupgrades compatibel door te leren hoe [u gegevensserialisatie kunt](service-fabric-application-upgrade-data-serialization.md)gebruiken.

Meer informatie over het gebruik van geavanceerde functionaliteit tijdens het upgraden van uw toepassing door te verwijzen naar [geavanceerde onderwerpen.](service-fabric-application-upgrade-advanced.md)

Los veelvoorkomende problemen op in toepassingsupgrades door te verwijzen naar de stappen in [het oplossen van toepassingsupgrades.](service-fabric-application-upgrade-troubleshooting.md)

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
