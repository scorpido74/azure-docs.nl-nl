---
title: Een Azure-functie maken met Kotlin en IntelliJ
description: Meer informatie over het maken en publiceren van een eenvoudige, door HTTP geactiveerde, serverloze app op Azure met Kotlin en IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: fa834e3ac4946c4f617e857342f850445eebfd30
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055450"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Quickstart: Uw eerste door HTTP geactiveerde functie maken met Kotlin en IntelliJ

In dit artikel wordt beschreven hoe u een [serverloos](https://azure.microsoft.com/overview/serverless-computing/) functieproject maakt met IntelliJ IDEA en Apache Maven. Ook wordt uitgelegd hoe u lokaal fouten opspoort in de functiecode in de Integrated Development Environment (IDE) en vervolgens het functieproject implementeert in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Als u een functie wilt ontwikkelen met Kotlin en IntelliJ, installeert u de volgende software:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), versie 8
- [Apache Maven](https://maven.apache.org) versie 3.0 of hoger
- [IntelliJ-idee](https://www.jetbrains.com/idea/download), Community of Ultimate-versies met Maven
- [Azure-CLI](/cli/azure)
- [Versie 2.x](functions-run-local.md#v2) van de Azure Functions Core Tools. Het is een lokale ontwikkelingsomgeving voor het schrijven, uitvoeren en debuggen van Azure Functions.

> [!IMPORTANT]
> De omgevingsvariabele JAVA_HOME moet zijn ingesteld op de installatielocatie van de JDK om de stappen in dit artikel te voltooien.

## <a name="create-a-functions-project"></a>Een Functions-project maken

1. Selecteer **Nieuw project maken** in IntelliJ IDEA.  
1. Selecteer in het venster **Nieuw project** **Maven** in het linkerdeelvenster.
1. Selecteer het selectievakje **Maken van archetype**, en selecteer vervolgens **Archetype toevoegen** voor het [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. Voltooi in het venster **Archtetype toevoegen** de velden als volgt:
    - _GroupId:_ com.microsoft.azure
    - _ArtifactId_: azure-functions-kotlin-archetype
    - _Version_: Gebruik de nieuwste versie van [de centrale opslagplaats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Een Maven-project maken van archetype in IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecteer **OK**, en selecteer vervolgens **Volgende**.
1. Voer uw gegevens in voor het huidige project en selecteer **Voltooien**.

Maven maakt de projectbestanden in een nieuwe map met dezelfde naam als de waarde _ArtifactId_. De gegenereerde code van het project is een eenvoudige, [door HTTP getriggerde](./functions-bindings-http-webhook.md) functie die de hoofdtekst van de activerende HTTP-aanvraag weergeeft.

## <a name="run-functions-locally-in-the-ide"></a>Functies lokaal uitvoeren in de IDE

> [!NOTE]
> Als u de functies lokaal wilt uitvoeren en fouten wilt opsporen, controleert u of u [Azure Functions Core Tools, versie 2](functions-run-local.md#v2) hebt geïnstalleerd.

1. Wijzigingen handmatig importeren of [automatisch importeren](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Op de werkbalk **Maven-projecten**.
1. Vouw **Levenscyclus** uit en open **pakket**. De oplossing is gebouwd en verpakt in een nieuw gemaakte doelmap.
1. Vouw **Invoegtoepassingen** > **azure-functions** uit en open **azure-functions:run** uit om de lokale runtime van Azure Functions te starten.  
  ![Maven-werkbalk voor Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Sluit het dialoogvenster uitvoeren wanneer u klaar bent met het testen van de functie. Er kan slechts één functiehost actief zijn en lokaal worden uitgevoerd.

## <a name="debug-the-function-in-intellij"></a>Lokaal fouten opsporen in de functie in IntelliJ

1. Als u de functiehost in de foutopsporingsmodus wilt starten, voegt u **-DenableDebug** als argument toe wanneer u de functie uitvoert. U kunt de configuratie in [maven-doelen](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) wijzigen of de volgende opdracht uitvoeren in een terminalvenster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Deze opdracht zorgt ervoor dat de functiehost een poort voor foutopsporing opent op 5005.

1. Selecteer in het menu **Uitvoeren** **Configuraties bewerken**.
1. Selecteer **'+'** om een **Externe** toe te voegen.
1. Voltooi de velden _Naam_ en _Instellingen_ en selecteer vervolgens **OK** om de configuratie op te slaan.
1. Na de installatie selecteert u **Fouten opsporen < Naam van de externe configuratie >** of drukt u op SHIFT + F9 op het toetsenbord om de foutopsporing te starten.

   ![Functies voor foutopsporing in IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Wanneer u klaar bent, stopt u de foutopsporing en het actieve proces. Er kan slechts één functiehost actief zijn en lokaal worden uitgevoerd.

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

1. Voordat u uw functie kunt implementeren in Azure, moet u zich [aanmelden met behulp van de Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implementeer de code in een nieuwe functie-app met de Maven-target `azure-functions:deploy`. U kunt ook de optie **Azure-functions:deploy** in het venster Maven-projecten selecteren.

   ```
   mvn azure-functions:deploy
   ```

1. Zoek de URL voor uw functie in de Azure CLI-uitvoer nadat de functie succesvol is geïmplementeerd.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Volgende stappen

Nu u uw eerste Kotlin-functie naar Azure hebt geïmplementeerd, neemt u [Azure Functions Java-handleiding voor ontwikkelaars](functions-reference-java.md) door voor meer informatie over het ontwikkelen van Java- en Kotlin-functies. Deze handleiding is vooralsnog door een vertaalmachine vertaald.
- U kunt extra functies met verschillende triggers toevoegen aan uw project met de Maven-target `azure-functions:add`.
