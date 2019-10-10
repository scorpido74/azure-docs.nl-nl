---
title: 'Snelstartgids: een toepassing starten met behulp van Maven-Azure lente-Cloud'
description: Een voorbeeld toepassing starten met behulp van Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166519"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Snelstartgids: een Azure lente-Cloud-app starten met de Maven-invoeg toepassing

Met de Maven-invoeg toepassing van Azure veer Cloud kunt u eenvoudig uw Azure lente-Cloud service toepassingen maken en bijwerken. Door vooraf een configuratie te definiëren, kunt u toepassingen implementeren in uw bestaande Azure lente-Cloud service. In dit artikel gebruiken we een voorbeeld toepassing met de naam PiggyMetrics om deze functie te demonstreren.

>[!Note]
> Zorg ervoor dat uw Azure-abonnement toegang heeft tot de Azure lente-Cloud voordat u begint met deze Snelstartgids.  Als preview-service vragen we klanten om aan ons te bellen zodat we uw abonnement kunnen toevoegen aan onze acceptatie lijst.  [Vul dit formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
)in als u de mogelijkheden van Azure lente-Cloud wilt verkennen.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren.  Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, JDK, maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u uw [Azure Cloud shell](https://shell.azure.com) vanuit shell.Azure.com.  [Lees onze documentatie](../cloud-shell/overview.md) voor meer informatie over Azure Cloud shell.

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

1. [Git installeren](https://git-scm.com/)
2. [JDK 8 installeren](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Maven 3,0 of hoger installeren](https://maven.apache.org/download.cgi)
4. [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registreren voor een Azure-abonnement](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten op het Azure Portal

1. Open in een webbrowser de [Azure Portal](https://portal.azure.com)en meld u aan bij uw account.

1. Zoek naar de **Azure lente-Cloud** en selecteer deze om naar de overzichts pagina te gaan. Selecteer de knop **maken** om aan de slag te gaan.

1. Vul het formulier in, waarbij u rekening moet houden met de volgende richt lijnen:
    - Service naam: Geef de naam van uw service-exemplaar op.  De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten.  Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.
    - Abonnement: Selecteer het abonnement dat u voor deze resource wilt factureren.  Zorg ervoor dat dit abonnement is toegevoegd aan onze acceptatie lijst voor Azure lente-Cloud.
    - Resource groep: het maken van nieuwe resource groepen voor nieuwe resources is een best practice.
    - Locatie: Selecteer de locatie voor uw service-exemplaar. Op dit moment worden de volgende locaties ondersteund: VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.
    
Het duurt ongeveer vijf minuten voordat de service wordt geïmplementeerd.  Zodra de app is geïmplementeerd, wordt de **overzichts** pagina voor het service-exemplaar weer gegeven.

## <a name="set-up-your-configuration-server"></a>De configuratie server instellen

1. Ga naar de **overzichts** pagina van de service en selecteer **Configuratie server**.
1. In de sectie **standaard opslagplaats** stelt u de **URI** in op ' https://github.com/Azure-Samples/piggymetrics ', stelt u een **Label** in op ' config ' en selecteert u **Toep assen** om uw wijzigingen op te slaan.

## <a name="clone-and-build-the-sample-application-repository"></a>De voor beeld-toepassings opslagplaats klonen en bouwen

1. Kloon de Git-opslag plaats door de volgende opdracht uit te voeren.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>De Azure lente-Cloud configuratie genereren en implementeren

1. Voeg het volgende toe aan uw `pom.xml` of `settings.xml` om Maven in te scha kelen voor gebruik met Azure veer Cloud.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Genereer een configuratie door de volgende opdracht uit te voeren.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Selecteer de modules `gateway`, `auth-service` en `account-service`.

    1. Selecteer uw abonnement en Azure lente-Cloud service cluster.

    1. Voer in de opgegeven lijst met projecten het nummer in dat overeenkomt met `gateway` om de toegang tot het publiek te geven.
    
    1. Bevestig de configuratie.

1. Implementeer de apps met behulp van de volgende opdracht:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. U kunt PiggyMetrics openen met behulp van de URL die u in de uitvoer van de vorige opdracht hebt gekregen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lente-Cloud toepassing geïmplementeerd vanuit een Maven-opslag plaats.  Ga verder met de zelf studie over het voorbereiden van uw app voor implementatie voor meer informatie over Azure veer Cloud.

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
