---
title: 'Quick Start: een Java-container installatie kopie bouwen en pushen naar Azure Container Registry met behulp van Maven en giek'
description: Bouw een in een container geplaatste java-app en push deze naar Azure Container Registry met behulp van de Maven giek-invoeg toepassing.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 62d63b24baab204cb029565b109ea2de768e1d80
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165444"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Snelstartgids: Java-container installatie kopieën bouwen en pushen naar Azure Container Registry

In deze Quick start ziet u hoe u een in een container geplaatste java-app maakt en deze naar Azure Container Registry pusht met behulp van de invoeg toepassing maven giek. Het gebruik van Maven en giek is een voor beeld van het gebruik van het hulp programma voor ontwikkel aars om te communiceren met een Azure container Registry.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement; als u nog geen Azure-abonnement hebt, kunt u uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) activeren of u aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/pricing/free-trial).
* De [Azure-opdrachtregelinterface (CLI)](/cli/azure/overview).
* Een ondersteunde JDK (Java Development Kit). Zie <https://aka.ms/azure-jdks> voor meer informatie over de JDK's die beschikbaar zijn voor gebruik bij het ontwikkelen op Azure.
* [Maven](http://maven.apache.org) Build Tool van Apache (versie 3 of hoger).
* Een [Git](https://git-scm.com)-client.
* Een [Docker](https://www.docker.com)-client.
* Het [hulp programma ACR docker-referentie](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>De web-app Aan de slag met Spring Boot in Docker maken

De volgende stappen helpen u bij het bouwen van een Spring Boot-web-app en bij het lokaal testen van de web-app.

1. Gebruik vanaf de opdracht prompt de volgende opdracht om het voor beeld [van de eerste keer opstarten op docker](https://github.com/spring-guides/gs-spring-boot-docker) -voorbeeld project te klonen.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Ga naar de map met het voltooide project.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Gebruik Maven om de voorbeeld-app te bouwen en uit te voeren.

   ```bash
   mvn package spring-boot:run
   ```

1. Test de web-app door naar `http://localhost:8080` te bladeren of met de volgende opdracht `curl`:

   ```bash
   curl http://localhost:8080
   ```

Het volgende bericht wordt weer gegeven: **Hello docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Een Azure Container Registry maken met Azure CLI

Vervolgens maakt u een Azure-resource groep en uw ACR aan de hand van de volgende stappen:

1. Meld u aan bij uw Azure-account met behulp van de volgende opdracht:

   ```azurecli
   az login
   ```

1. Geef het Azure-abonnement op dat moet worden gebruikt:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Maak een resourcegroep voor de Azure-resources die in deze zelfstudie worden gebruikt. Vervang in de volgende opdracht de tijdelijke aanduidingen door de naam van uw eigen resource en een locatie zoals `eastus`.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Maak een persoonlijk Azure-container register in de resource groep met behulp van de volgende opdracht. Zorg ervoor dat u de tijdelijke aanduidingen vervangt door de werkelijke waarden. In de zelfstudie wordt in latere stappen de voorbeeld-app als een Docker-installatiekopie naar dit register gepusht.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Uw app via Jib naar het containerregister pushen

Ten slotte werkt u de project configuratie bij en gebruikt u de opdracht prompt om uw installatie kopie te bouwen en te implementeren.

1. Meld u met de volgende opdracht aan bij uw Azure Container Registry vanuit de Azure CLI. Zorg ervoor dat u de tijdelijke aanduiding vervangt door uw eigen register naam.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Met de `az configure` opdracht wordt de standaard naam van het REGI ster ingesteld voor gebruik met `az acr`-opdrachten.

1. Navigeer naar de voltooide projectmap voor uw SpringBoot-app (bijvoorbeeld *C:\SpringBoot\gs-spring-boot-docker\complete* of */users/robert/SpringBoot/gs-spring-boot-docker/complete*) en open het bestand *pom.xml* met een teksteditor.

1. Werk de verzameling `<properties>` in het bestand *pom. XML* bij met de volgende XML-code. Vervang de tijdelijke aanduiding door de naam van uw REGI ster en werk de `<jib-maven-plugin.version>` waarde bij met de nieuwste versie van de [maven-invoeg toepassing](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Werk de `<plugins>` verzameling in het bestand *pom. XML* zodanig bij dat het `<plugin>`-element de `jib-maven-plugin`bevat en vermeld, zoals in het volgende voor beeld wordt weer gegeven. Houd er rekening mee dat we een basis installatie kopie gebruiken van micro soft Container Registry (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine`, dat een officieel ondersteund JDK voor Azure bevat. Voor andere MCR-basis installatie kopieën met officieel ondersteunde JDKs raadpleegt u [Java SE jdk](https://hub.docker.com/_/microsoft-java-jdk), [Java SE jre](https://hub.docker.com/_/microsoft-java-jre), [Java SE HEADLESS jre](https://hub.docker.com/_/microsoft-java-jre-headless)en [Java SE jdk en Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Ga naar de map met het voltooide project voor uw Spring Boot-toepassing en voer de volgende opdracht uit om de installatiekopie te bouwen en deze naar het register te pushen:

   ```bash
   mvn compile jib:build
   ```

> [!NOTE]
>
> Uit veiligheids overwegingen is de referentie die is gemaakt door `az acr login` slechts één uur geldig. Als u een niet- *geautoriseerde 401* -fout ontvangt, kunt u de `az acr login -n <your registry name>` opdracht opnieuw uitvoeren om opnieuw te verifiëren.

## <a name="verify-your-container-image"></a>De container installatie kopie controleren

Gefeliciteerd! U beschikt nu over de door Azure ondersteunde JDK voor de Java-app die is gepusht naar uw ACR. U kunt de installatie kopie nu testen door deze te implementeren op Azure App Service, of deze naar een lokale locatie te halen met de opdracht (vervangen van de tijdelijke aanduiding):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Volgende stappen

Zie voor andere versies van de officiële door micro soft ondersteunde Java base-installatie kopieën:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java-SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java-SE headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK en Maven](https://hub.docker.com/_/microsoft-java-maven)

Voor meer informatie over Spring en Azure gaat u door naar het documentatiecentrum van Spring op Azure.

> [!div class="nextstepaction"]
> [Spring in Azure](/azure/java/spring-framework)

### <a name="additional-resources"></a>Aanvullende bronnen

Zie de volgende bronnen voor meer informatie:

* [Azure for Java Developers](/azure/java) (Azure voor Java-ontwikkelaars)
* [Werken met Azure DevOps en Java](/azure/devops/java)
* [Aan de slag met Spring Boot in Docker](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Een Spring Boot-toepassing implementeren in Azure App Service](/azure/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Een aangepaste Docker-installatiekopie gebruiken voor Azure-web-apps in Linux](/azure/app-service-web/app-service-linux-using-custom-docker-image)
