---
title: Azure Service Fabric Docker Stel implementatievoorbeeld samen
description: Azure Service Fabric accepteert docker compose-indeling om het eenvoudiger te maken om bestaande containers te orkestreren met Service Fabric. Deze ondersteuning is momenteel in preview.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282456"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Ondersteuning voor implementatie van docker samenstellen in Azure Service Fabric (Voorbeeld)

Docker gebruikt het [docker-compose.yml-bestand](https://docs.docker.com/compose) voor het definiëren van multicontainertoepassingen. Om het klanten die bekend zijn met Docker gemakkelijk te maken om bestaande containertoepassingen op Azure Service Fabric te orkestreren, hebben we preview-ondersteuning voor Docker Compose-implementatie native in het platform opgenomen. Service Fabric kan versie 3 `docker-compose.yml` en hoger van bestanden accepteren. 

Omdat deze ondersteuning in preview is, wordt alleen een subset van Compos-richtlijnen ondersteund.

Als u deze voorbeeld wilt gebruiken, maakt u uw cluster met versie 5.7 of meer van de runtime van Service Fabric via de Azure-portal, samen met de bijbehorende SDK. 

> [!NOTE]
> Deze functie is in preview en wordt niet ondersteund in de productie.
> De onderstaande voorbeelden zijn gebaseerd op runtime versie 6.0 en SDK versie 2.8.

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Een Docker Compose-bestand implementeren op servicestructuur

Met de volgende opdrachten wordt een `fabric:/TestContainerApp`Service Fabric-toepassing gemaakt (met de naam ), die u controleren en beheren zoals elke andere Service Fabric-toepassing. U de opgegeven toepassingsnaam gebruiken voor statusquery's.
ServiceFabric herkent 'DeploymentName' als de id van de groep Compose.

### <a name="use-powershell"></a>PowerShell gebruiken

Maak een implementatie van Service Fabric Compose vanuit een docker-compose.yml-bestand door de volgende opdracht in PowerShell uit te voeren:

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName`en `RegistryPassword` verwijzen naar de gebruikersnaam en het wachtwoord van het containerregister. Nadat u de implementatie hebt voltooid, u de status ervan controleren met behulp van de volgende opdracht:

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

Als u de implementatie van Compose via PowerShell wilt verwijderen, gebruikt u de volgende opdracht:

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

Als u een upgrade voor een implementatie samenstellen via PowerShell wilt starten, gebruikt u de volgende opdracht:

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

Als u de upgrade van de implementatie opstellen via PowerShell wilt terugdraaien, gebruikt u de volgende opdracht:

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

Nadat de upgrade is geaccepteerd, kan de voortgang van de upgrade worden bijgehouden met de volgende opdracht:

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI (sfctl) gebruiken

U ook de volgende opdracht Service Fabric CLI gebruiken:

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

Nadat u de implementatie hebt gemaakt, u de status ervan controleren met behulp van de volgende opdracht:

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Als u de implementatie Van Samengesteld wilt verwijderen, gebruikt u de volgende opdracht:

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Als u een upgrade voor een implementatie samenstellen wilt starten, gebruikt u de volgende opdracht:

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Als u de upgrade van de implementatie van Samengesteld wilt terugdraaien, gebruikt u de volgende opdracht:

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

Nadat de upgrade is geaccepteerd, kan de voortgang van de upgrade worden bijgehouden met de volgende opdracht:

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>Ondersteunde richtlijnen opstellen

Deze voorbeeld ondersteunt een subset van de configuratieopties uit de indeling Versie 3 opstellen, inclusief de volgende primitieven:

* Services > implementeren > replica's
* Services > > beperkingen voor plaatsing implementeren >
* Services > > limieten voor > resources implementeren
    * -cpu-aandelen
    * -geheugen
    * -geheugen-swap
* Services > Opdrachten
* Diensten > milieu
* Services > havens
* Services > Afbeelding
* Services > Isolatie (alleen voor Windows)
* Services > logging > stuurprogramma
* Services > logboekregistratie > >-opties voor stuurprogramma's
* Volume & implementeren > volume

Stel het cluster in voor het afdwingen van resourcelimieten, zoals beschreven in [Resource governance servicefabric](service-fabric-resource-governance.md). Alle andere Docker Compose-richtlijnen worden niet ondersteund voor deze preview.

### <a name="ports-section"></a>Sectie Poorten

Geef het http- of https-protocol op in de sectie Poorten die wordt gebruikt door de servicelistener van de Service Fabric.Specific of the http or https protocol in the Ports section that will be used by the Service Fabric service listener. Dit zorgt ervoor dat het eindpuntprotocol correct wordt gepubliceerd met de naamgevingsservice om omgekeerde proxy toe te staan om de aanvragen door te sturen:
* Als u naar onveilige services voor Service Fabric Compose wilt routeren, geeft u **/http**op . Bijvoorbeeld , - **"80:80/http"**.
* Als u wilt routeren naar beveiligde Services Fabric Compose-services, geeft u **/https**op . Bijvoorbeeld - **"443:443/https"**.

> [!NOTE]
> De syntaxis van de sectie /http- en /https-poorten is specifiek voor Servicefabric om de juiste URL van de listener van de servicefabric te registreren.  Als de syntaxis van het docker samenstellen-bestand programmatisch is gevalideerd, kan dit een validatiefout veroorzaken.

## <a name="servicednsname-computation"></a>ServiceDnsName-berekening

Als de servicenaam die u opgeeft in een Bestand Samenstellen een volledig gekwalificeerde domeinnaam is (dat wil zeggen, het bevat een stip [.]), is de DNS-naam die door Service Fabric is `<ServiceName>` geregistreerd (inclusief de stip). Als dit niet het gevolg is, wordt elk padsegment in de toepassingsnaam een domeinlabel in de DNS-naam van de service, waarbij het eerste padsegment het domeinlabel van het hoogste niveau wordt.

Als de opgegeven toepassingsnaam `fabric:/SampleApp/MyComposeApp`bijvoorbeeld `<ServiceName>.MyComposeApp.SampleApp` de geregistreerde DNS-naam is.

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Implementatie (instantiedefinitie) opstellen versus servicefabric-appmodel (typedefinitie)

Een docker-compose.yml-bestand beschrijft een inzetbare set containers, inclusief hun eigenschappen en configuraties.
Het bestand kan bijvoorbeeld omgevingsvariabelen en poorten bevatten. U ook implementatieparameters, zoals plaatsingsbeperkingen, resourcelimieten en DNS-namen, opgeven in het docker-compose.yml-bestand.

Het [toepassingsmodel Service Fabric](service-fabric-application-model.md) maakt gebruik van servicetypen en toepassingstypen, waarbij u veel toepassingsexemplaren van hetzelfde type hebben. U bijvoorbeeld één toepassingsinstantie per klant hebben. Dit op typen gebaseerde model ondersteunt meerdere versies van hetzelfde toepassingstype dat is geregistreerd bij de runtime.

Klant A kan bijvoorbeeld een toepassing hebben die is geinstantieerd met type 1.0 van AppTypeA en klant B kan een andere toepassing hebben die wordt geinstantieerd met hetzelfde type en dezelfde versie. U definieert de toepassingstypen in de toepassingsmanifesten en u geeft de toepassingsnaam en implementatieparameters op wanneer u de toepassing maakt.

Hoewel dit model flexibiliteit biedt, zijn we ook van plan om een eenvoudiger, op instanties gebaseerd implementatiemodel te ondersteunen waarbij typen impliciet zijn uit het manifestbestand. In dit model krijgt elke toepassing zijn eigen onafhankelijke manifest. We bekijken deze inspanning door ondersteuning toe te voegen voor docker-compose.yml, een op instance gebaseerde implementatieindeling.

## <a name="next-steps"></a>Volgende stappen

* Lees meer over het [servicefabric-toepassingsmodel](service-fabric-application-model.md)
* [Aan de slag met Service Fabric-CLI](service-fabric-cli.md)
