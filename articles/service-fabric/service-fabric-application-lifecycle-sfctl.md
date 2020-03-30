---
title: Azure Service Fabric-toepassingen beheren met sfctl
description: Meer informatie over het implementeren en verwijderen van toepassingen uit een Azure Service Fabric-cluster met Azure Service Fabric CLI
author: Christina-Kang
ms.topic: conceptual
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 7d361d44c349bc7a6e3c041f78d00ad66182fa15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259069"
---
# <a name="manage-an-azure-service-fabric-application-by-using-azure-service-fabric-cli-sfctl"></a>Een Azure Service Fabric-toepassing beheren met Azure Service Fabric CLI (sfctl)

Meer informatie over het maken en verwijderen van toepassingen die worden uitgevoerd in een Azure Service Fabric-cluster.

## <a name="prerequisites"></a>Vereisten

* Installatie Service Fabric CLI. Selecteer vervolgens het cluster Service Fabric. Zie [Aan de slag met Service Fabric CLI](service-fabric-cli.md)voor meer informatie.

* Zorg ervoor dat een Service Fabric-toepassingspakket klaar staat om te worden geïmplementeerd. Lees meer informatie over het schrijven en verpakken van een toepassing over het [servicefabric-toepassingsmodel.](service-fabric-application-model.md)

## <a name="overview"></a>Overzicht

Voer de volgende stappen uit om een nieuwe toepassing te implementeren:

1. Upload een toepassingspakket naar de Afbeeldingenwinkel Service Fabric.
2. Een toepassingstype inrichten.
3. Verwijder de inhoud van het afbeeldingsarchief.
4. Een toepassing opgeven en maken.
5. Services opgeven en maken.

Voer de volgende stappen uit om een bestaande toepassing te verwijderen:

1. Verwijder de toepassing.
2. De inschrijftoepassing van het gekoppelde toepassingstype ongedaan maken.

## <a name="deploy-a-new-application"></a>Een nieuwe toepassing implementeren

Als u een nieuwe toepassing wilt implementeren, voert u de volgende taken uit:

### <a name="upload-a-new-application-package-to-the-image-store"></a>Een nieuw toepassingspakket uploaden naar de afbeeldingswinkel

Upload het toepassingspakket naar de servicefabric-afbeeldingswinkel voordat u een toepassing maakt.

Als uw toepassingspakket zich bijvoorbeeld `app_package_dir` in de map bevindt, gebruikt u de volgende opdrachten om de map te uploaden:

```shell
sfctl application upload --path ~/app_package_dir
```

Voor grote toepassingspakketten `--show-progress` u de optie opgeven om de voortgang van de upload weer te geven.

### <a name="provision-the-application-type"></a>Bepaling van het toepassingstype

Wanneer de upload is voltooid, dient u de toepassing in. Als u de toepassing wilt inrichten, gebruikt u de volgende opdracht:

```shell
sfctl application provision --application-type-build-path app_package_dir
```

De waarde `application-type-build-path` voor is de naam van de map waar u uw aanvraagpakket hebt geüpload.

### <a name="delete-the-application-package"></a>Het toepassingspakket verwijderen

Het wordt aanbevolen om het aanvraagpakket te verwijderen nadat de toepassing is geregistreerd.  Als u toepassingspakketten uit de afbeeldingswinkel verwijderde, worden systeembronnen vrijgemaakt.  Het bijhouden van ongebruikte toepassingspakketten verbruikt schijfopslag en leidt tot problemen met de prestaties van toepassingen. 

Als u het toepassingspakket uit het afbeeldingsarchief wilt verwijderen, gebruikt u de volgende opdracht:

```shell
sfctl store delete --content-path app_package_dir
```

`content-path`moet de naam zijn van de map die u hebt geüpload toen u de toepassing maakte.

### <a name="create-an-application-from-an-application-type"></a>Een toepassing maken op basis van een toepassingstype

Nadat u de toepassing hebt ingemaakt, gebruikt u de volgende opdracht om de naam en het maken van uw toepassing:

```shell
sfctl application create --app-name fabric:/TestApp --app-type TestAppType --app-version 1.0
```

`app-name`is de naam die u wilt gebruiken voor de toepassingsinstantie. U aanvullende parameters ophalen uit het eerder ingerichte toepassingsmanifest.

De naam van de toepassing `fabric:/`moet beginnen met het voorvoegsel .

### <a name="create-services-for-the-new-application"></a>Services maken voor de nieuwe toepassing

Nadat u een toepassing hebt gemaakt, maakt u services van de toepassing. In het volgende voorbeeld maken we een nieuwe stateless service van onze applicatie. De services die u maken met een toepassing, worden gedefinieerd in een servicemanifest in het eerder ingerichte toepassingspakket.

```shell
sfctl service create --app-id TestApp --name fabric:/TestApp/TestSvc --service-type TestServiceType \
--stateless --instance-count 1 --singleton-scheme
```

## <a name="verify-application-deployment-and-health"></a>Implementatie en status van toepassingen verifiëren

Gebruik de volgende statusopdrachten om te controleren of alles in orde is:

```shell
sfctl application list
sfctl service list --application-id TestApp
```

Als u wilt controleren of de service in orde is, gebruikt u vergelijkbare opdrachten om de status van zowel de service als de toepassing op te halen:

```shell
sfctl application health --application-id TestApp
sfctl service health --service-id TestApp/TestSvc
```

Gezonde diensten en `HealthState` toepassingen `Ok`hebben een waarde van .

## <a name="remove-an-existing-application"></a>Een bestaande toepassing verwijderen

Als u een toepassing wilt verwijderen, voert u de volgende taken uit:

### <a name="delete-the-application"></a>De toepassing verwijderen

Als u de toepassing wilt verwijderen, gebruikt u de volgende opdracht:

```shell
sfctl application delete --application-id TestEdApp
```

### <a name="unprovision-the-application-type"></a>De inschrijftijd van het toepassingstype ongedaan maken

Nadat u de toepassing hebt verwijderd, u het toepassingstype ontleden als u deze niet meer nodig hebt. Als u het toepassingstype wilt ontleden, gebruikt u de volgende opdracht:

```shell
sfctl application unprovision --application-type-name TestAppType --application-type-version 1.0
```

De typenaam en de typeversie moeten overeenkomen met de naam en versie in het eerder ingerichte toepassingsmanifest.

## <a name="upgrade-application"></a>Toepassing bijwerken

Nadat u uw toepassing hebt gemaakt, u dezelfde reeks stappen herhalen om een tweede versie van uw toepassing in te richten. Vervolgens u met een upgrade van de Service Fabric-toepassing overschakelen naar het uitvoeren van de tweede versie van de toepassing. Zie voor meer informatie de documentatie over [upgrades van servicefabric-toepassingen](service-fabric-application-upgrade.md).

Als u een upgrade wilt uitvoeren, moet u eerst de volgende versie van de toepassing inrichten met dezelfde opdrachten als voorheen:

```shell
sfctl application upload --path ~/app_package_dir_2
sfctl application provision --application-type-build-path app_package_dir_2
sfctl store delete --content-path app_package_dir_2
```

Het wordt aanbevolen om vervolgens een bewaakte automatische upgrade uit te voeren, de upgrade te starten door de volgende opdracht uit te voeren:

```shell
sfctl application upgrade --app-id TestApp --app-version 2.0.0 --parameters "{\"test\":\"value\"}" --mode Monitored
```

Upgrades overschrijven bestaande parameters met welke set is opgegeven. Toepassingsparameters moeten zo nodig worden doorgegeven als argumenten aan de upgradeopdracht. Toepassingsparameters moeten worden gecodeerd als een JSON-object.

Als u eerder opgegeven parameters wilt `sfctl application info` ophalen, u de opdracht gebruiken.

Wanneer een upgrade van een toepassing wordt uitgevoerd, `sfctl application upgrade-status` kan de status worden opgehaald met behulp van de opdracht.

Ten slotte u, als een upgrade aan de `sfctl application upgrade-rollback` gang is en moet worden geannuleerd, de upgrade terugdraaien.

## <a name="next-steps"></a>Volgende stappen

* [Basics van Service Fabric CLI](service-fabric-cli.md)
* [Aan de slag met Service Fabric op Linux](service-fabric-get-started-linux.md)
* [Een upgrade van een Service Fabric-toepassing starten](service-fabric-application-upgrade.md)
