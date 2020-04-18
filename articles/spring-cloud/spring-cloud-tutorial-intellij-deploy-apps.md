---
title: Zelfstudie - IntelliJ gebruiken om Azure Spring Cloud-toepassingen te implementeren
description: Gebruik IntelliJ om toepassingen te implementeren in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: dbd0313c3edca8c5a875572fe52d7ec751ba65e8
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641987"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>IntelliJ gebruiken om Azure Spring Cloud-toepassingen te implementeren
De IntelliJ-plug-in voor Azure Spring Cloud ondersteunt toepassingsimplementatie vanuit het IntelliJ IDEA.  

## <a name="prerequisites"></a>Vereisten
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0+](https://maven.apache.org/download.cgi)
* [IntelliJ IDEA Community Edition, versie [2020.1]](https://www.jetbrains.com/idea/download/#section=windows)
* Of: IntelliJ IDEA Ultimate Edition, versie [2020.1]

## <a name="install-the-plug-in"></a>De plug-in installeren
U Azure Toolkit voor IntelliJ IDEA 3.35.0 gebruiken via de Gebruikersinterface van IntelliJ **Plugins.**

1. Start IntelliJ.  Als u eerder een project hebt geopend, sluit u het project om het welkomstdialoogvenster weer te geven. Selecteer **Configureren** vanaf koppeling rechtsonder en klik op **Plug-ins** om het dialoogvenster invoegconfiguratie te openen en selecteer **Plug-ins installeren vanaf de schijf**.

    ![Selecteer Configureren](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Zoek naar Azure Toolkit voor IntelliJ.  Klik **op Installeren**.

    ![Plug-in installeren](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klik **op IDE opnieuw starten**.

## <a name="tutorial-procedures"></a>Zelfstudieprocedures
De volgende procedures implementeren een Hello World-toepassing met behulp van het IntelliJ IDEA.

* Open gs-spring-boot project
* Implementeren in Azure Spring Cloud
* Streaminglogboeken weergeven

## <a name="open-gs-spring-boot-project"></a>Open gs-spring-boot project

1. Download en rits de bronrepository voor deze zelfstudie uit, of kloon deze met Git: git clonehttps://github.com/spring-guides/gs-spring-boot.git 
1. cd in gs-spring-boot\compleet.
1. Open **het** dialoogvenster Welkom inTelliJ en selecteer **Project importeren** om de wizard Importeren te openen.
1. Selecteer `gs-spring-boot\complete` map.

    ![Project importeren](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Implementeren in Azure Spring Cloud
Als u wilt implementeren in Azure, moet u zich aanmelden met uw Azure-account en uw abonnement kiezen.  Zie [Installatie en aanmelden](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-create-hello-world-web-app#installation-and-sign-in)voor aanmeldingsgegevens.

1. Klik met de rechtermuisknop op uw project in IntelliJ project explorer en selecteer **Azure** -> **Deploy in Azure Spring Cloud**.

    ![Implementeren in Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Accepteer de naam voor app in het veld **Naam.** **Naam** verwijst naar de configuratie, niet app naam. Gebruikers hoeven het meestal niet te wijzigen.
1. Accepteer de id van het project voor het **artefact**.
1. Selecteer **App:** klik vervolgens op **App maken...**.

    ![Implementeren naar Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Voer **de naam van app**in en klik op **OK.**

    ![Implementeren naar Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Start de implementatie door op de knop **Uitvoeren** te klikken. 

    ![Implementeren naar Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. De plug-in voert `mvn package` de opdracht uit voor het project en maakt `package` vervolgens de nieuwe app en implementeert de pot die door de opdracht wordt gegenereerd.

1. Als de URL van de app niet wordt weergegeven in het uitvoervenster, haalt u deze op van de Azure-portal. Navigeer vanuit uw brongroep naar het exemplaar van Azure Spring Cloud.  Klik vervolgens op **Apps**.  De lopende app wordt weergegeven.

    ![Download test-URL](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navigeer naar de URL in de browser.

    ![Navigeren in browser 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Streaminglogboeken weergeven
Ga als eerste over de slag met de tekst:
1. Selecteer **Azure Explorer**en vervolgens Spring **Cloud**.
1. Klik met de rechtermuisknop op de hardloopapp.
1. Selecteer **Streaming logs** in de vervolgkeuzelijst.

    ![Streaminglogboeken selecteren](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Instantie selecteren.

    ![Instantie selecteren](media/spring-cloud-intellij-howto/select-instance.png)

1. Het streaminglogboek is zichtbaar in het uitvoervenster.

    ![Streaming logboekuitvoer](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="see-also"></a>Zie ook
* [Een Hallo wereld-web-app maken voor Azure App Service met behulp van IntelliJ](https://docs.microsoft.com/azure/java/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)
* [Een Java Spring-toepassing voorbereiden voor implementatie in Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment) 