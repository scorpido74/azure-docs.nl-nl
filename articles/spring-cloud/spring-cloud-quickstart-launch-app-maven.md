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
ms.openlocfilehash: ce07d43a289cf527664b120dd832cf832fb2b05e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161414"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Snelstartgids: een Azure lente-Cloud-app starten met behulp van de Maven-invoeg toepassing

Met de Azure veer Cloud maven-invoeg toepassing kunt u eenvoudig uw Azure lente-Cloud service toepassingen maken en bijwerken. Door een configuratie vooraf te definiëren, kunt u toepassingen implementeren in uw bestaande Azure lente-Cloud service. In dit artikel gebruikt u een voorbeeld toepassing met de naam PiggyMetrics om deze functie te demonstreren.

>[!Note]
> Voordat u met deze Snelstartgids begint, moet u ervoor zorgen dat uw Azure-abonnement toegang heeft tot de Azure lente-Cloud. Als preview-service nodigen we u uit om contact met ons op te nemen zodat we uw abonnement kunnen toevoegen aan onze acceptatie lijst. Als u de mogelijkheden van Azure lente Cloud wilt verkennen, vult u de [Azure lente-Cloud (private preview)-rente formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u)in en verzendt u deze. Hoewel Azure lente-Cloud in preview is, biedt micro soft beperkte ondersteuning zonder een SLA.  Raadpleeg deze [Veelgestelde vragen](https://azure.microsoft.com/support/faq/)over ondersteuning voor meer informatie over ondersteuning tijdens previews.

>[!TIP]
> Azure Cloud Shell is een gratis interactieve shell die u kunt gebruiken om de opdrachten in dit artikel uit te voeren. Het heeft algemene Azure-hulpprogram ma's die vooraf zijn geïnstalleerd, met inbegrip van de nieuwste versies van Git, de Java Development Kit (JDK), maven en de Azure CLI. Als u bent aangemeld bij uw Azure-abonnement, start u [Azure Cloud shell](https://shell.azure.com). Zie [overzicht van Azure Cloud shell](../cloud-shell/overview.md)voor meer informatie.

Dit zijn de vereisten voor het voltooien van deze snelstartgids:

1. [Installeer Git](https://git-scm.com/).
2. [Installeer jdk 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Installeer Maven 3,0 of hoger](https://maven.apache.org/download.cgi).
4. [Installeer de Azure CLI-versie 2.0.67 of hoger](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Meld u aan voor een gratis Azure-abonnement](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>De Azure CLI-extensie installeren

Installeer de Azure veer Cloud-extensie voor de Azure CLI met behulp van de volgende opdracht:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Een service-exemplaar inrichten op het Azure Portal

1. Open in een webbrowser de [Azure Portal](https://portal.azure.com)en meld u aan bij uw account.

1. Zoek en selecteer **Azure lente-Cloud**. 
1. Selecteer op de pagina overzicht de optie **maken**en ga vervolgens als volgt te werk:  

    a. Geef in het vak **service naam** de naam van uw service-exemplaar op. De naam moet tussen 4 en 32 tekens lang zijn en mag alleen kleine letters, cijfers en afbreek streepjes bevatten. Het eerste teken van de service naam moet een letter zijn en het laatste teken moet een letter of een cijfer zijn.  

    b. Selecteer in de vervolg keuzelijst **abonnement** het abonnement dat u voor deze resource wilt factureren. Zorg ervoor dat dit abonnement is toegevoegd aan onze acceptatie lijst voor Azure lente-Cloud.  

    c. Maak een nieuwe resource groep in het vak **resource groep** . Het maken van resource groepen voor nieuwe resources is een best practice.  

    d. Selecteer in de vervolg keuzelijst **locatie** de locatie voor uw service-exemplaar. Op dit moment worden de volgende locaties ondersteund: VS-Oost, VS-West 2, Europa-west en Zuidoost-Azië.
    
Het duurt ongeveer vijf minuten voordat de service wordt geïmplementeerd. Nadat de service is geïmplementeerd, wordt de **overzichts** pagina voor het service-exemplaar weer gegeven.

## <a name="set-up-your-configuration-server"></a>De configuratie server instellen

1. Selecteer op de pagina service **overzicht** de optie **Configuratie server**.
1. Stel in de sectie **standaard opslagplaats** de optie **URI** in op **https://github.com/Azure-Samples/piggymetrics** , stel **Label** in op **configuratie**en selecteer vervolgens **Toep assen** om uw wijzigingen op te slaan.

## <a name="clone-and-build-the-sample-application-repository"></a>De voor beeld-toepassings opslagplaats klonen en bouwen

1. Kloon de Git-opslag plaats door de volgende opdracht uit te voeren:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Wijzig de map en bouw het project door de volgende opdracht uit te voeren:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>De Azure lente-Cloud configuratie genereren en implementeren

1. Om Maven in te scha kelen voor gebruik met Azure lente-Cloud, voegt u de volgende code toe aan uw *pom. XML-* of *Settings. XML-* bestand.

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

1. Genereer een configuratie door de volgende opdracht uit te voeren:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Selecteer de modules `gateway`, `auth-service` en `account-service`.

    b. Selecteer uw abonnement en Azure lente-Cloud service cluster.

    c. Voer in de lijst met opgegeven projecten het nummer in dat overeenkomt met `gateway` om het open bare toegang te geven.
    
    d. Bevestig de configuratie.

1. Implementeer de apps met behulp van de volgende opdracht:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. U kunt toegang krijgen tot PiggyMetrics met behulp van de URL die wordt vermeld in de uitvoer van de voor gaande opdracht.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lente-Cloud toepassing geïmplementeerd vanuit een Maven-opslag plaats. Voor meer informatie over Azure lente-Cloud gaat u verder met de zelf studie over het voorbereiden van uw app voor implementatie.

> [!div class="nextstepaction"]
> [De Azure lente-Cloud toepassing voorbereiden voor implementatie](spring-cloud-tutorial-prepare-app-deployment.md)
