---
title: Een faseringsomgeving instellen in Azure Spring Cloud | Microsoft Documenten
description: Meer informatie over het gebruik van blauwgroene implementatie met Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.openlocfilehash: 2e29f6a75b303518ac34ecf9b570bd7638cf0c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471027"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Een faseringsomgeving instellen in Azure Spring Cloud

In dit artikel wordt besproken hoe u een implementatie met tijdelijke bestanden instellen met behulp van het blauwgroene implementatiepatroon in Azure Spring Cloud. Blauw/groen-implementatie is een Azure DevOps-patroon voor continue levering dat erop vertrouwt een bestaande (blauwe) versie live te houden terwijl een nieuwe (groene) wordt geïmplementeerd. In dit artikel ziet u hoe u die implementatie met fasering in productie nemen zonder de productie-implementatie rechtstreeks te wijzigen.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de PiggyMetrics-toepassing al hebt geïmplementeerd in onze [zelfstudie over het starten van een Azure Spring Cloud-toepassing.](spring-cloud-quickstart-launch-app-portal.md) PiggyMetrics bestaat uit drie toepassingen: 'gateway', 'accountservice' en 'auth-service'.  

Als u een andere toepassing voor dit voorbeeld wilt gebruiken, moet u een eenvoudige wijziging aanbrengen in een openbaar gedeelte van de toepassing.  Deze wijziging onderscheidt uw faseringsimplementatie van de productie.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u gebruiken om de instructies in dit artikel uit te voeren.  Het heeft gemeenschappelijke, vooraf geïnstalleerde Azure-hulpprogramma's, waaronder de nieuwste versies van Git, JDK, Maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud Shell](https://shell.azure.com).  Zie [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md)voor meer informatie.

Als u een faseringsomgeving wilt instellen in Azure Spring Cloud, volgt u de instructies in de volgende secties.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure Spring Cloud-extensie voor de Azure CLI met de volgende opdracht:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Alle implementaties weergeven

Ga naar uw service-instantie in de Azure-portal en selecteer **Implementatiebeheer** om alle implementaties weer te geven. Als u meer details wilt weergeven, u elke implementatie selecteren.

## <a name="create-a-staging-deployment"></a>Een faseringsimplementatie maken

1. Breng in uw lokale ontwikkelingsomgeving een kleine wijziging aan in de PiggyMetrics-gatewaytoepassing. Wijzig bijvoorbeeld de kleur in het bestand *gateway/src/main/resources/static/css/launch.css.* Hierdoor u eenvoudig onderscheid maken tussen de twee implementaties. Voer de volgende opdracht uit om het potpakket te bouwen: 

    ```console
    mvn clean package
    ```

1. Maak in Azure CLI een nieuwe implementatie en geef deze de naam 'groen' voor de implementatie van de fasering.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Nadat de implementatie is voltooid, opent u de gatewaypagina vanuit het **toepassingsdashboard**en bekijkt u al uw exemplaren op het tabblad **App-instanties** aan de linkerkant.
  
> [!NOTE]
> De detectiestatus is *OUT_OF_SERVICE* zodat het verkeer niet naar deze implementatie wordt doorgestuurd voordat de verificatie is voltooid.

## <a name="verify-the-staging-deployment"></a>De implementatie van de fasering verifiëren

1. Ga terug naar de pagina **Implementatiebeheer** en selecteer uw nieuwe implementatie. De implementatiestatus moet *Uitvoeren weergeven*. De knop **Domein toewijzen/toewijzen** moet grijs worden weergegeven, omdat de omgeving een faseringsomgeving is.

1. In het deelvenster **Overzicht** ziet u een **eindpunt voor de test**. Kopieer en plak het in een nieuw browservenster en de nieuwe PiggyMetrics-pagina moet worden weergegeven.

>[!TIP]
> * Controleer of uw testeindpunt eindigt met een slash (/) om ervoor te zorgen dat het CSS-bestand correct is geladen.  
> * Als u inloggegevens moet invoeren om de pagina te bekijken, gebruikt u [URL-decode](https://www.urldecoder.org/) om uw testeindpunt te decoderen. URL-decode retourneert een\<URL in\<het\<formulier "https:// gebruikersnaam>: wachtwoord>@ clusternaam>.test.azureapps.io/gateway/green".  Gebruik dit formulier om toegang te krijgen tot uw eindpunt.

>[!NOTE]    
> Config-serverinstellingen zijn van toepassing op zowel uw faseringsomgeving als productie. Als u bijvoorbeeld het contextpad`server.servlet.context-path`( ) voor uw app-gateway in config-server instelt als *somepath,* wordt het pad naar uw groene implementatie gewijzigd in "https://\<gebruikersnaam>:\<wachtwoord>@\<clusternaam>.test.azureapps.io/gateway/green/somepath/...".
 
 Als u op dit moment uw openbare app-gateway bezoekt, ziet u de oude pagina zonder uw nieuwe wijziging.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>De groene implementatie instellen als de productieomgeving

1. Nadat u uw wijziging in uw faseringsomgeving hebt geverifieerd, u deze naar productie pushen. Ga terug naar **Implementatiebeheer**en schakel het selectievakje **gatewaytoepassing** in.

2. Selecteer **Implementatie instellen**.
3. Selecteer In de lijst **Productie-implementatie** de optie **Groen**en selecteer **Vervolgens Toepassen**.
4. Ga naar de **overzichtspagina** van uw gatewaytoepassing. Als u al een domein voor uw gatewaytoepassing hebt toegewezen, wordt de URL weergegeven in het deelvenster **Overzicht.** Als u de gewijzigde PiggyMetrics-pagina wilt weergeven, selecteert u de URL en gaat u naar de site.

>[!NOTE]
> Nadat u de groene implementatie hebt ingesteld als de productieomgeving, wordt de vorige implementatie de implementatie van de fasering.

## <a name="modify-the-staging-deployment"></a>De implementatie van tijdelijke bestanden wijzigen

Als u niet tevreden bent met uw wijziging, u uw toepassingscode wijzigen, een nieuw potpakket maken en deze uploaden naar uw groene implementatie met behulp van de Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>De implementatie van tijdelijke bestanden verwijderen

Als u uw implementatie met tijdelijke bestanden wilt verwijderen uit de Azure-poort, gaat u naar de pagina met de implementatie van tijdelijke implementatie en selecteert u de knop **Verwijderen.**

U ook uw tijdelijke implementatie verwijderen uit de Azure CLI door de volgende opdracht uit te voeren:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
