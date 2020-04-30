---
title: Zelf studie-IntelliJ gebruiken voor het implementeren van Azure lente-Cloud toepassingen
description: Gebruik IntelliJ om toepassingen te implementeren in azure lente Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/26/2020
ms.openlocfilehash: 8e473a5692c3fa2be3b0f2d823d2a36ba768d661
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81731359"
---
# <a name="use-intellij-to-deploy-azure-spring-cloud-applications"></a>IntelliJ gebruiken om Azure Spring Cloud-toepassingen te implementeren
De IntelliJ-invoeg toepassing voor Azure lente-Cloud ondersteunt de implementatie van toepassingen vanuit het IntelliJ-idee.  

## <a name="prerequisites"></a>Vereisten
* [JDK 8 Azul Zulu](https://docs.microsoft.com/java/azure/jdk/java-jdk-install?view=azure-java-stable)
* [Maven 3.5.0 +](https://maven.apache.org/download.cgi)
* [IntelliJ idee, Community/Ultimate Edition, versie 2020.1/2019.3](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>De invoeg toepassing installeren
U kunt de Azure-toolkit voor IntelliJ idee 3.35.0 toevoegen vanuit de gebruikers interface van IntelliJ- **invoeg toepassingen** .

1. IntelliJ starten.  Als u eerder een project hebt geopend, sluit u het project om het dialoog venster Welkom weer te geven. Selecteer **configureren** vanuit koppeling rechtsonder en klik vervolgens op **invoeg toepassingen** om het dialoog venster configuratie van de invoeg toepassing te openen en selecteer **invoeg toepassingen vanaf schijf installeren**.

    ![Selecteer configureren](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Zoeken naar Azure-toolkit voor IntelliJ.  Klik op **installeren**.

    ![Invoeg toepassing installeren](media/spring-cloud-intellij-howto/install-plugin.png)

1. Klik op **IDE opnieuw starten**.

## <a name="tutorial-procedures"></a>Procedures voor zelf studie
Met de volgende procedures wordt een Hallo wereld-toepassing geïmplementeerd met behulp van het IntelliJ-idee.

* Open GS-veer-boot-project
* Implementeren in azure lente-Cloud
* Streaming-logboeken weer geven

## <a name="open-gs-spring-boot-project"></a>Open GS-veer-boot-project

1. De bron opslagplaats voor deze zelf studie downloaden en uitpakken, of deze klonen met git: git-kloonhttps://github.com/spring-guides/gs-spring-boot.git 
1. cd in GS-Spring-boot\complete.
1. Open IntelliJ **Welkom** dialoog venster, selecteer **project importeren** om de wizard Importeren te openen.
1. Selecteer `gs-spring-boot\complete` map.

    ![Project importeren](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>Implementeren in azure lente-Cloud
Als u naar Azure wilt implementeren, moet u zich aanmelden met uw Azure-account en uw abonnement kiezen.  Zie [installatie en aanmelding](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)voor meer informatie.

1. Klik met de rechter muisknop op uw project in IntelliJ project Verkenner en selecteer **Azure** -> **implementeren naar Azure lente Cloud**.

    ![Implementeren in azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Accepteer de naam voor de app in het veld **naam** . De **naam** verwijst naar de configuratie, niet de naam van de app. Gebruikers hoeven deze doorgaans niet te wijzigen.
1. Accepteer de id uit het project voor het **artefact**.
1. **App selecteren:** klik vervolgens op **app maken...**.

    ![Implementeren in azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Voer de naam van de **app**in en klik vervolgens op **OK**.

    ![Implementeren naar Azure OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Start de implementatie door te klikken op de knop **uitvoeren** . 

    ![Implementeren in azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. De invoeg toepassing voert de opdracht `mvn package` uit op het project, maakt de nieuwe app en implementeert het jar dat is gegenereerd door `package` de opdracht.

1. Als de app-URL niet wordt weer gegeven in het uitvoer venster, haalt u deze op uit het Azure Portal. Navigeer vanuit de resource groep naar het exemplaar van Azure lente Cloud.  Klik vervolgens op **apps**.  De app die wordt uitgevoerd wordt weer gegeven.

    ![Test-URL ophalen](media/spring-cloud-intellij-howto/get-test-url.png)

1. Ga in browser naar de URL.

    ![Navigeren in browser 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Streaming-logboeken weer geven
De logboeken ophalen:
1. Selecteer **Azure Explorer**en vervolgens een **lente-Cloud**.
1. Klik met de rechter muisknop op de app die wordt uitgevoerd.
1. Selecteer **streaming-logboeken** in de vervolg keuzelijst.

    ![Streaming-logboeken selecteren](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Selecteer exemplaar.

    ![Instantie selecteren](media/spring-cloud-intellij-howto/select-instance.png)

1. Het streaming-logboek wordt weer gegeven in het uitvoer venster.

    ![Uitvoer van streaming-logboek](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Volgende stappen
* [Lente toepassing voorbereiden voor Azure lente-Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-tutorial-prepare-app-deployment)
* [Meer informatie over Azure-toolkit voor IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/)
