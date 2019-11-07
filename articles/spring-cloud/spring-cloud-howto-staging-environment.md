---
title: Een faserings omgeving instellen in azure lente-Cloud | Microsoft Docs
description: Meer informatie over het gebruik van een Blue-groene implementatie met Azure veer Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: jeconnoc
ms.openlocfilehash: 24ce4dee04e4daf3eaee4144f8dc56de5867bbca
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607214"
---
# <a name="how-to-set-up-a-staging-environment"></a>Een faseringsomgeving instellen

In dit artikel wordt uitgelegd hoe u een faserings implementatie kunt gebruiken met behulp van het Blue-groen implementatie patroon in azure lente-Cloud. Ook wordt uitgelegd hoe u de faserings implementatie in productie brengt zonder de productie-implementatie rechtstreeks te wijzigen.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u de PiggyMetrics-toepassing al hebt geïmplementeerd vanuit onze [zelf studie over het starten van een toepassing](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics bestaat uit drie toepassingen: ' gateway ', ' account-service ' en ' auth-service '.  

Als u een andere toepassing hebt die u voor dit voor beeld wilt gebruiken, moet u een eenvoudige wijziging aanbrengen in een openbaar gericht deel van de toepassing.  Deze wijziging is van invloed op uw staging-implementatie van productie.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

Om dit artikel te volt ooien:


## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Alle implementaties weer geven

Ga naar uw service-exemplaar in het Azure Portal en selecteer **implementatie beheer** om alle implementaties weer te geven. U kunt elke implementatie selecteren voor meer informatie.

## <a name="create-a-staging-deployment"></a>Een faserings implementatie maken

1. Breng in uw lokale ontwikkel omgeving een kleine wijziging aan in de gateway toepassing van de Piggy-metriek. Wijzig bijvoorbeeld de kleur in `gateway/src/main/resources/static/css/launch.css`. Op deze manier kunt u de twee implementaties eenvoudig onderscheiden. Voer de volgende opdracht uit om het jar-pakket te maken: 

    ```azurecli
    mvn clean package
    ```

1. Maak een nieuwe implementatie met Azure CLI, zodat deze de naam van faserings implementatie ' groen ' geeft.

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Zodra de implementatie is voltooid, opent u de pagina gateway vanuit het **toepassings dashboard** en ziet u alle exemplaren in het tabblad **app-exemplaren** aan de linkerkant.
  
> [!NOTE]
> De detectie status is "OUT_OF_SERVICE" zodat verkeer naar deze implementatie niet wordt doorgestuurd voordat de verificatie is voltooid.

## <a name="verify-the-staging-deployment"></a>De faserings implementatie verifiëren

1. Ga terug naar de pagina **implementatie beheer** en selecteer de nieuwe implementatie. De implementatie status moet worden **uitgevoerd**weer geven. De knop domein toewijzen/opheffen wordt uitgeschakeld omdat het een faserings omgeving is.

1. Op de pagina **overzicht** ziet u een **test eindpunt**. Kopieer en plak deze in een nieuwe browser pagina en Bekijk de pagina nieuwe metrische gegevens over Piggy.

>[!TIP]
> * Controleer of het eind punt van de test eindigt op '/' om ervoor te zorgen dat de CSS correct wordt geladen.  
> * Als uw browser vereist dat u aanmeldings referenties opgeeft om de pagina weer te geven, gebruikt u de [URL decoderen](https://www.urldecoder.org/) om uw test eindpunt te decoderen. Het decoderen van de URL retourneert een URL in de vorm "https://\<gebruikers naam >:\<wacht woord > @\<cluster naam >. test. azureapps. io/gateway/Green".  Gebruik dit om toegang te krijgen tot uw eind punt.

>[!NOTE]    
> De instellingen van de configuratie server zijn van toepassing op uw faserings omgeving en productie. Als u bijvoorbeeld het context pad (`server.servlet.context-path`) voor uw app-gateway in de configuratie server instelt als *somepath*, wordt het pad naar uw groene implementatie gewijzigd: ' https://\<gebruikers naam >:\<wacht woord > @\<cluster naam >. test.azureapps.io/gateway/green/somepath/... '
 
 Als u op dit moment naar uw open bare gateway voor apps gaat, ziet u de oude pagina zonder uw nieuwe wijziging.
    
## <a name="set-the-green-as-production-deployment"></a>De groen instellen als productie-implementatie

1. Nadat u uw wijziging in uw faserings omgeving hebt gecontroleerd, kunt u deze pushen naar productie. Ga terug naar **implementatie beheer** en selecteer het selectie vakje voor de "gateway"-toepassing.
2. Selecteer ' implementatie instellen '.
3. Selecteer ' groen ' in het menu ' productie-implementatie ' en selecteer **Toep assen**
4. Ga naar de **overzichts** pagina van uw gateway toepassing. Als u al een domein hebt toegewezen voor uw gateway toepassing, wordt de URL op de pagina **overzicht** weer gegeven. Ga naar de URL en u ziet de pagina aangepaste metrische gegevens over Piggy.

>[!NOTE]
> Zodra de groene implementatie is ingesteld op productie omgeving, wordt de vorige implementatie de faserings implementatie.

## <a name="modify-the-staging-deployment"></a>De faserings implementatie wijzigen

Als u niet tevreden bent met uw wijziging, kunt u de toepassings code wijzigen, een nieuw jar-pakket bouwen en dit uploaden naar uw groene implementatie met behulp van de Azure CLI.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-a-staging-deployment"></a>Een faserings implementatie verwijderen

Verwijder uw staging-implementatie uit de Azure-poort door te navigeren naar uw staging-implementatie pagina en de knop **verwijderen** te selecteren.

U kunt ook uw staging-implementatie verwijderen uit de Azure CLI met de volgende opdracht:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```
