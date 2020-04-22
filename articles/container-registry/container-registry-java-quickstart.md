---
title: Quickstart - Java-containerafbeeldingen bouwen en pushen naar Azure Container Registry met Maven en Jib
description: Bouw een gecontaineriseerde Java-app en duw deze naar Azure Container Registry met de Maven Jib-plug-in.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: ef933ae9a6b0a34529c7ec145b13c023728a3457
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731860"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Snelstart: Java-containerafbeeldingen bouwen en pushen naar Azure Container Registry

Deze quickstart laat u zien hoe u een gecontaineriseerde Java-app bouwt en deze naar Azure Container Registry pusht met de Maven Jib-plug-in. Het gebruik van Maven en Jib is een voorbeeld van het gebruik van tooling voor ontwikkelaars om te communiceren met een Azure-containerregister.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement; als u nog geen Azure-abonnement hebt, kunt u uw [voordelen als MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) activeren of u aanmelden voor een [gratis Azure-account](https://azure.microsoft.com/pricing/free-trial).
* De [Azure-opdrachtregelinterface (CLI)](/cli/azure/overview).
* Een ondersteunde JDK (Java Development Kit). Zie <https://aka.ms/azure-jdks> voor meer informatie over de JDK's die beschikbaar zijn voor gebruik bij het ontwikkelen op Azure.
* Apache's [Maven](http://maven.apache.org) build tool (versie 3 of hoger).
* Een [Git](https://git-scm.com)-client.
* Een [Docker](https://www.docker.com)-client.
* De [ACR Docker-referentiehelper](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>De web-app Aan de slag met Spring Boot in Docker maken

De volgende stappen helpen u bij het bouwen van een Spring Boot-web-app en bij het lokaal testen van de web-app.

1. Gebruik in de opdrachtprompt de volgende opdracht om het voorbeeldproject [Spring Boot op Docker Getting Started](https://github.com/spring-guides/gs-spring-boot-docker) te klonen.

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

U ziet het volgende bericht weergegeven: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Een Azure Container Registry maken met Azure CLI

Vervolgens maakt u een Azure-brongroep en uw ACR met de volgende stappen:

1. Meld u aan bij uw Azure-account met de volgende opdracht:

   ```azurecli
   az login
   ```

1. Geef het Azure-abonnement op dat moet worden gebruikt:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Maak een resourcegroep voor de Azure-resources die in deze zelfstudie worden gebruikt. Zorg er in de volgende opdracht voor dat u de tijdelijke `eastus`aanduidingen vervangt door uw eigen resourcenaam en een locatie zoals .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Maak een privé-Azure-containerregister in de resourcegroep met de volgende opdracht. Zorg ervoor dat u de tijdelijke aanduidingen vervangt door werkelijke waarden. In de zelfstudie wordt in latere stappen de voorbeeld-app als een Docker-installatiekopie naar dit register gepusht.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Uw app via Jib naar het containerregister pushen

Ten slotte werkt u uw projectconfiguratie bij en gebruikt u de opdrachtprompt om uw afbeelding te bouwen en te implementeren.

1. Meld u met de volgende opdracht aan bij uw Azure Container Registry vanuit de Azure CLI. Zorg ervoor dat u de tijdelijke aanduiding vervangt door uw eigen registernaam.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Met `az configure` de opdracht wordt de `az acr` standaardregisternaam ingesteld die met opdrachten moet worden gebruikt.

1. Navigeer naar de voltooide projectmap voor uw SpringBoot-app (bijvoorbeeld *C:\SpringBoot\gs-spring-boot-docker\complete* of */users/robert/SpringBoot/gs-spring-boot-docker/complete*) en open het bestand *pom.xml* met een teksteditor.

1. Werk `<properties>` de verzameling in het *pom.xml-bestand* bij met de volgende XML. Vervang de tijdelijke aanduiding door uw `<jib-maven-plugin.version>` registernaam en werk de waarde bij met de nieuwste versie van de [jib-maven-plugin.](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Werk `<plugins>` de verzameling in het *pom.xml-bestand bij,* zodat het `<plugin>` element bevat en invoer voor het `jib-maven-plugin`element , zoals in het volgende voorbeeld wordt weergegeven. Houd er rekening mee dat we een basisafbeelding `mcr.microsoft.com/java/jdk:8-zulu-alpine`uit het Microsoft Container Registry (MCR) gebruiken, die een officieel ondersteunde JDK voor Azure bevat. Voor andere MCR basisbeelden met officieel ondersteunde JDKs, zie [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless), en [Java SE JDK en Maven](https://hub.docker.com/_/microsoft-java-maven).

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
> Om veiligheidsredenen is de `az acr login` referentie die is gemaakt door slechts 1 uur geldig. Als u een *fout van 401 ongeautoriseerde* fout ontvangt, u de `az acr login -n <your registry name>` opdracht opnieuw uitvoeren om opnieuw te verifiëren.

## <a name="verify-your-container-image"></a>Uw containerafbeelding verifiëren

Gefeliciteerd! Nu heb je je gecontaineriseerde Java App build op Azure ondersteund JDK geduwd om uw ACR. U de afbeelding nu testen door deze te implementeren in Azure App Service of deze naar lokaal te trekken met opdracht (de tijdelijke aanduiding vervangen):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Volgende stappen

Zie voor andere versies van de officiële door Microsoft ondersteunde Java-basisafbeeldingen:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK en Maven](https://hub.docker.com/_/microsoft-java-maven)

Voor meer informatie over Spring en Azure gaat u door naar het documentatiecentrum van Spring op Azure.

> [!div class="nextstepaction"]
> [Spring in Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Aanvullende resources

Zie de volgende bronnen voor meer informatie:

* [Azure voor Java-ontwikkelaars](/azure/java)
* [Werken met Azure DevOps en Java](/azure/devops/java)
* [Aan de slag met Spring Boot in Docker](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Een Spring Boot-toepassing implementeren in Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin)
* [Een aangepaste Docker-installatiekopie gebruiken voor Azure-web-apps in Linux](/azure/app-service-web/app-service-linux-using-custom-docker-image)
