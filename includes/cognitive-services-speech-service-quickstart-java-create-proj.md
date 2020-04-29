---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: e0bc7eeff276d44f3cd23e30eb54c9bd1ca94efd
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80658312"
---
1. Start Eclipse.

1. Voer in de Eclipse Launcher, in het veld **Workspace**, de naam van een nieuwe werkruimtemap in. Selecteer vervolgens **Starten**.

   ![Schermopname van Eclipse Launcher](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-01-create-new-eclipse-workspace.png)

1. Binnen enkele ogenblikken verschijnt het hoofdvenster van de Eclipse-IDE. Sluit het **welkomst** scherm als er een aanwezig is.

1. Maak in de menu balk voor eclips een nieuw project door **bestand** > **Nieuw** > **project**te kiezen.

1. Het dialoogvenster **Nieuw project** wordt weergegeven. Selecteer **Java-project** en vervolgens **Volgende**.

   ![Schermopname van het dialoogvenster Nieuw project, met Java-project gemarkeerd](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-02-select-wizard.png)

1. De wizard **nieuw Java-project** wordt gestart. Voer in het veld **Projectnaam****quickstart** in en kies **JavaSE-1.8** als uitvoeringsomgeving. Selecteer **Finish**.

   ![Schermopname van de wizard Nieuw Java-project](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-03-create-java-project.png)

1. Als het venster **Gekoppeld perspectief openen?** wordt weergegeven, selecteert u **Perspectief openen**.

1. Klik in de **Package explorer** met de rechtermuisknop op het **quickstart**-project. Kies **Configure** > **Convert to Maven project** in het context menu.

   ![Schermopname van Package explorer](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-04-convert-to-maven-project.png)

1. Het venster **Nieuw POM maken** wordt weergegeven. Voer in het veld **groeps-id** *com. micro soft. cognitiveservices. speech. samples*in en voer in het veld **artefact-id** *Quick*start in. Selecteer vervolgens **Voltooien**.

   ![Schermopname van het venster Nieuw POM maken](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-05-configure-maven-pom.png)

1. Open het bestand *pom.xml* en bewerk dit.

   * Maak aan het einde van het bestand, vóór de afsluitende tag `</project>`, een `repositories`-element met een verwijzing naar de Maven-opslagplaats voor de Speech-SDK, zoals hier getoond:

     [!code-xml[POM Repositories](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#repositories)]

   * Voeg ook een `dependencies` -element toe, met de Speech SDK versie 1.11.0 als afhankelijkheid:

     [!code-xml[POM Dependencies](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/pom.xml#dependencies)]

   * Sla de wijzigingen op.
