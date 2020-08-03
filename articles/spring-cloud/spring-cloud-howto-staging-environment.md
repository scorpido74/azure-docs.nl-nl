---
title: Een faserings omgeving instellen in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het gebruik van een Blue-groene implementatie met Azure veer Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 5333dd45cee58821d89cd49c44d3c5bba4cd9115
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500399"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Een faserings omgeving instellen in azure lente-Cloud

In dit artikel wordt beschreven hoe u een faserings implementatie instelt met behulp van het patroon voor blauw-groen-implementatie in azure lente-Cloud. Blauw/groen-implementatie is een Azure DevOps-patroon voor continue levering dat erop vertrouwt een bestaande (blauwe) versie live te houden terwijl een nieuwe (groene) wordt geïmplementeerd. In dit artikel wordt beschreven hoe u die fase ring implementeert in productie zonder de productie-implementatie rechtstreeks te wijzigen.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de PiggyMetrics-toepassing al hebt geïmplementeerd vanuit onze [zelf studie over het starten van een Azure lente-Cloud toepassing](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics bestaat uit drie toepassingen: "gateway," "account-service" en "auth-service".  

Als u een andere toepassing voor dit voor beeld wilt gebruiken, moet u een eenvoudige wijziging aanbrengen in een openbaar gedeelte van de toepassing.  Deze wijziging is van invloed op uw staging-implementatie van productie.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u kunt gebruiken om de instructies in dit artikel uit te voeren.  Het heeft gemeen schappelijke, vooraf geïnstalleerde Azure-hulpprogram ma's, waaronder de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com).  Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md)voor meer informatie.

Volg de instructies in de volgende secties om een faserings omgeving in te stellen in azure lente Cloud.

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Alle implementaties weer geven

Ga naar uw service-exemplaar in het Azure Portal en selecteer **implementatie beheer** om alle implementaties weer te geven. Als u meer details wilt weer geven, kunt u elke implementatie selecteren.

## <a name="create-a-staging-deployment"></a>Een faserings implementatie maken

1. Breng in uw lokale ontwikkel omgeving een kleine wijziging aan in de PiggyMetrics-gateway toepassing. Wijzig bijvoorbeeld de kleur in het bestand *Gateway/src/main/resources/static/CSS/start. CSS* . Zo kunt u de twee implementaties eenvoudig onderscheiden. Voer de volgende opdracht uit om het jar-pakket te bouwen: 

    ```console
    mvn clean package
    ```

1. Maak in de Azure CLI een nieuwe implementatie en geef deze de naam ' groen ' van de faserings implementatie.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Nadat de implementatie is voltooid, opent u de pagina gateway vanuit het **toepassings dashboard**en bekijkt u alle exemplaren op het tabblad **app-exemplaren** aan de linkerkant.
  
> [!NOTE]
> De detectie status is *OUT_OF_SERVICE* zodat verkeer naar deze implementatie niet wordt doorgestuurd voordat de verificatie is voltooid.

## <a name="verify-the-staging-deployment"></a>De faserings implementatie verifiëren

1. Ga terug naar de pagina **implementatie beheer** en selecteer uw nieuwe implementatie. De implementatie status moet worden *uitgevoerd*weer geven. De knop **domein toewijzen/opheffen** moet grijs worden weer gegeven, omdat de omgeving een faserings omgeving is.

1. In het deel venster **overzicht** ziet u een **test eindpunt**. Kopieer en plak deze in een nieuw browser venster en de nieuwe PiggyMetrics-pagina moet worden weer gegeven.

>[!TIP]
> * Controleer of het eind punt van de test eindigt met een slash (/) om ervoor te zorgen dat het CSS-bestand correct wordt geladen.  
> * Als uw browser vereist dat u aanmeldings referenties opgeeft om de pagina weer te geven, gebruikt u de [URL decoderen](https://www.urldecoder.org/) om uw test eindpunt te decoderen. Het decoderen van de URL retourneert een URL in de vorm "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green".  Gebruik dit formulier om toegang te krijgen tot uw eind punt.

>[!NOTE]    
> Instellingen voor de configuratie server zijn van toepassing op zowel uw faserings omgeving als productie. Als u bijvoorbeeld het context pad ( `server.servlet.context-path` ) voor uw app-gateway in de configuratie server instelt als *somepath*, wordt het pad naar uw groene implementatie gewijzigd in ' https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.io/gateway/Green/somepath/... '.
 
 Als u op dit punt naar de open bare app-gateway gaat, ziet u de oude pagina zonder uw nieuwe wijziging.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>De groene implementatie instellen als de productie omgeving

1. Nadat u uw wijziging in uw faserings omgeving hebt gecontroleerd, kunt u deze pushen naar productie. Ga terug naar **implementatie beheer**en selecteer het selectie vakje **Gateway** toepassing.

2. Selecteer **implementatie instellen**.
3. Selecteer in de lijst **productie-implementatie** de optie **groen**en selecteer vervolgens **Toep assen**.
4. Ga naar de **overzichts** pagina van uw gateway toepassing. Als u al een domein hebt toegewezen voor uw gateway toepassing, wordt de URL weer gegeven in het deel venster **overzicht** . Als u de aangepaste pagina PiggyMetrics wilt weer geven, selecteert u de URL en gaat u naar de site.

>[!NOTE]
> Nadat u de groene implementatie hebt ingesteld als de productie omgeving, wordt de vorige implementatie de faserings implementatie.

## <a name="modify-the-staging-deployment"></a>De faserings implementatie wijzigen

Als u niet tevreden bent met uw wijziging, kunt u de toepassings code wijzigen, een nieuw jar-pakket bouwen en dit uploaden naar uw groene implementatie met behulp van de Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>De faserings implementatie verwijderen

Als u uw staging-implementatie wilt verwijderen uit de Azure-poort, gaat u naar de pagina faserings implementatie en selecteert u vervolgens de knop **verwijderen** .

U kunt ook uw staging-implementatie verwijderen uit de Azure CLI door de volgende opdracht uit te voeren:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
