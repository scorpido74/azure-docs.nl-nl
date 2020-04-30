---
title: Een Azure-functie maken met Kotlin en IntelliJ
description: Meer informatie over het maken en publiceren van een eenvoudige, door HTTP geactiveerde, serverloze app op Azure met Kotlin en IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 2eb1a016e04a4150a76112c68683926810f5c66d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80674095"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Snelstartgids: uw eerste door HTTP geactiveerde functie maken met Kotlin en IntelliJ

In dit artikel wordt beschreven hoe u een functie project zonder [Server](https://azure.microsoft.com/overview/serverless-computing/) maakt met INTELLIJ-idee en Apache Maven. Ook wordt uitgelegd hoe u lokaal fouten opspoort in de functie code in de Integrated Development Environment (IDE) en vervolgens het functie project implementeert in Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Als u een functie wilt ontwikkelen met Kotlin en IntelliJ, installeert u de volgende software:

- [Java Developer Kit](https://aka.ms/azure-jdks) (JDK), versie 8
- [Apache Maven](https://maven.apache.org), versie 3,0 of hoger
- [IntelliJ-ideeën](https://www.jetbrains.com/idea/download),-community's of-Ultimate-versies met maven
- [Azure-CLI](https://docs.microsoft.com/cli/azure)
- [Versie 2. x](functions-run-local.md#v2) van de Azure functions core tools. Het biedt een lokale ontwikkel omgeving voor het schrijven, uitvoeren en fout opsporing van Azure Functions.

> [!IMPORTANT]
> De omgevings variabele JAVA_HOME moet worden ingesteld op de installatie locatie van de JDK om de stappen in dit artikel te volt ooien.

## <a name="create-a-functions-project"></a>Een functions-project maken

1. In IntelliJ-idee selecteert u **Nieuw project maken**.  
1. Selecteer in het venster **New Project** **maven** in het linkerdeel venster.
1. Schakel het selectie vakje **maken op basis van archetype** in en selecteer vervolgens **archetype toevoegen** voor de [Azure-functions-Kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. In het venster **archetype toevoegen** vult u de velden als volgt in:
    - _GroupId_: com. micro soft. Azure
    - _ArtifactId_: Azure-functions-Kotlin-archetype
    - _Versie_: gebruik de meest recente versie van [de centrale opslag plaats](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)
    ![een Maven-project maken op basis van archetype in IntelliJ-idee](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Selecteer **OK**en selecteer vervolgens **volgende**.
1. Voer uw gegevens in voor het huidige project en selecteer **volt ooien**.

Maven maakt de project bestanden in een nieuwe map met dezelfde naam als de _ArtifactId_ -waarde. De gegenereerde code van het project is een eenvoudige [, door http getriggerde](/azure/azure-functions/functions-bindings-http-webhook) functie die de hoofd tekst van de TRIGGERende HTTP-aanvraag echot.

## <a name="run-functions-locally-in-the-ide"></a>Functies lokaal uitvoeren in de IDE

> [!NOTE]
> Als u de functies lokaal wilt uitvoeren en fouten wilt opsporen, controleert u of u [Azure functions core tools, versie 2](functions-run-local.md#v2)hebt geïnstalleerd.

1. Wijzigingen hand matig importeren of [automatisch importeren](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)inschakelen.
1. Open de werk balk **Maven projecten** .
1. Vouw **levens cyclus**uit en open vervolgens **package**. De oplossing is gebouwd en verpakt in een nieuw gemaakte doelmap.
1. Vouw **invoeg toepassingen** > uit**Azure-functions** en open **Azure-functions: Voer uit** om de Azure functions lokale runtime te starten.  
  ![Maven-werk balk voor Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Sluit het dialoog venster uitvoeren wanneer u klaar bent met het testen van de functie. Er kan slechts één functie-host actief zijn en lokaal worden uitgevoerd.

## <a name="debug-the-function-in-intellij"></a>Fouten opsporen in de functie in IntelliJ

1. Als u de functie-host in de foutopsporingsmodus wilt starten, voegt u **-DenableDebug** als het argument toe wanneer u de functie uitvoert. U kunt de configuratie in maven- [doel stellingen](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) wijzigen of de volgende opdracht uitvoeren in een Terminal venster:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   Deze opdracht zorgt ervoor dat de functie-host een poort voor fout opsporing opent op 5005.

1. Selecteer in het menu **uitvoeren** de optie **configuraties bewerken**.
1. Selecteer **(+)** om een **extern**onderdeel toe te voegen.
1. Vul de velden _naam_ en _instellingen_ in en selecteer **OK** om de configuratie op te slaan.
1. Selecteer na de installatie **fout opsporing < naam van de externe configuratie >** of druk op SHIFT + F9 op het toetsen bord om de fout opsporing te starten.

   ![Functies voor fout opsporing in IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Wanneer u klaar bent, stopt u de fout opsporing en het actieve proces. Er kan slechts één functie-host actief zijn en lokaal worden uitgevoerd.

## <a name="deploy-the-function-to-azure"></a>De functie implementeren in Azure

1. Voordat u uw functie kunt implementeren in azure, moet u [zich aanmelden met behulp van de Azure cli](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Implementeer uw code in een nieuwe functie met behulp `azure-functions:deploy` van het maven-doel. U kunt ook de optie **Azure-functions: Deploy** selecteren in het venster maven projects.

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

Nu u uw eerste Kotlin-functie hebt geïmplementeerd in azure, raadpleegt u de [hand leiding voor ontwikkel aars voor Java-functies](functions-reference-java.md) voor meer informatie over het ontwikkelen van Java-en Kotlin-functies.
- Voeg extra functies met verschillende triggers aan uw project toe met behulp van het `azure-functions:add` maven-doel.
