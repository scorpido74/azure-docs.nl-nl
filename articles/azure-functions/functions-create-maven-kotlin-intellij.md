---
title: Een Azure-functie maken met Kotlin en IntelliJ
description: Meer informatie over het maken en publiceren van een eenvoudige HTTP-geactiveerde, serverloze app op Azure met Kotlin en IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80674095"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Snelstart: maak uw eerste HTTP-geactiveerde functie met Kotlin en IntelliJ

In dit artikel ziet u hoe u een [serverloos](https://azure.microsoft.com/overview/serverless-computing/) functieproject maakt met IntelliJ IDEA en Apache Maven. Het laat ook zien hoe u uw functiecode lokaal debuggen in de geïntegreerde ontwikkelomgeving (IDE) en vervolgens het functieproject implementeren in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Installeer de volgende software om een functie te ontwikkelen met Kotlin en IntelliJ:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), versie 8
- [Apache Maven](https://maven.apache.org), versie 3.0 of hoger
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download), Community of Ultimate versies met Maven
- [Azure-CLI](https://docs.microsoft.com/cli/azure)
- [Versie 2.x](functions-run-local.md#v2) van de Core-hulpprogramma's voor Azure-functies. Het biedt een lokale ontwikkelomgeving voor het schrijven, uitvoeren en debuggen van Azure-functies.

> [!IMPORTANT]
> De JAVA_HOME omgevingsvariabele moet worden ingesteld op de installatielocatie van de JDK om de stappen in dit artikel uit te voeren.

## <a name="create-a-functions-project"></a>Een project Functies maken

1. Selecteer IntelliJ IDEA de optie **Nieuw project maken**.  
1. Selecteer **Maven** in het linkerdeelvenster in het venster **Nieuw project.**
1. Schakel het selectievakje **Maken van archetype** in en selecteer **Archetype toevoegen** voor het [azure-functions-kotlin-archetype.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
1. Vul in het venster **Archetype toevoegen** de velden als volgt in:
    - _GroupId_: com.microsoft.azure
    - _ArtefactId_: azure-functions-kotlin-archetype
    - _Versie_: Gebruik de nieuwste versie uit [de centrale repository](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![Maak een Maven project van archetype in IntelliJ IDEA](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecteer **OK**en selecteer **Volgende**.
1. Voer uw gegevens in voor het huidige project en selecteer **Voltooien**.

Maven maakt de projectbestanden in een nieuwe map met dezelfde naam als de _ArtefactId-waarde._ De gegenereerde code van het project is een eenvoudige [HTTP-geactiveerde](/azure/azure-functions/functions-bindings-http-webhook) functie die de hoofdtekst van het activerende HTTP-verzoek weerspiegelt.

## <a name="run-functions-locally-in-the-ide"></a>Functies lokaal uitvoeren in de IDE

> [!NOTE]
> Als u functies lokaal wilt uitvoeren en debuggen, controleert u of u [Azure Functions Core Tools, versie 2,](functions-run-local.md#v2)hebt geïnstalleerd.

1. Wijzigingen handmatig importeren of [automatisch importeren](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)inschakelen .
1. Open de werkbalk **Maven-projecten.**
1. Breid **de levenscyclus**uit en open het **pakket**. De oplossing is gebouwd en verpakt in een nieuw gemaakte doelmap.
1. Vouw**Azure-functies voor** **Plug-ins** > uit en open **azure-functies:voer uit** om de lokale runtime van Azure Functions te starten.  
  ![Werkbalk Maven voor Azure-functies](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Sluit het dialoogvenster Uitvoeren wanneer u klaar bent met het testen van uw functie. Er kan slechts één functiehost actief zijn en lokaal worden uitgevoerd.

## <a name="debug-the-function-in-intellij"></a>De functie debuggen in IntelliJ

1. Als u de functiehost wilt starten in de foutopsporingsmodus, voegt u **-DenableDebug** toe als argument wanneer u uw functie uitvoert. U de configuratie wijzigen in [mavendoelen](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) of de volgende opdracht uitvoeren in een terminalvenster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Met deze opdracht opent de functiehost een foutopsporingspoort op 5005.

1. Selecteer **Configuraties bewerken**in het menu **Uitvoeren** .
1. Selecteer **(+)** om een **afstandsbediening**toe te voegen .
1. Vul de velden _Naam_ en _instellingen in_ en selecteer **OK** om de configuratie op te slaan.
1. Selecteer na de installatie **Foutopsporing < naam van externe configuratie >** of druk op Shift+F9 op het toetsenbord om te beginnen met foutopsporing.

   ![Foutopsporingsfuncties in IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Wanneer u klaar bent, stopt u de foutopsporing en het loopproces. Er kan slechts één functiehost actief zijn en lokaal worden uitgevoerd.

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

1. Voordat u uw functie implementeren in Azure, moet u [zich aanmelden met de Azure CLI.](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

   ``` azurecli
   az login
   ```

1. Implementeer uw code in een `azure-functions:deploy` nieuwe functie met behulp van het Maven-doel. U ook de **azure-functies selecteren:deploy-optie** in het venster Maven-projecten.

   ```
   mvn azure-functions:deploy
   ```

1. Zoek de URL voor uw functie in de Azure CLI-uitvoer nadat de functie is geïmplementeerd.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Volgende stappen

Nu u uw eerste Kotlin-functie in Azure hebt geïmplementeerd, bekijkt u de [ontwikkelaarshandleiding javafuncties](functions-reference-java.md) voor meer informatie over het ontwikkelen van Java- en Kotlin-functies.
- Voeg extra functies met verschillende triggers `azure-functions:add` toe aan uw project met behulp van het Maven-doel.
