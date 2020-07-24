---
title: Een Azure-functie maken met Java en IntelliJ
description: Meer informatie over het maken en publiceren van een eenvoudige, door HTTP geactiveerde, serverloze app op Azure met Java en IntelliJ.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: b1eb430f94ef545fa74ed225c427a121a47f7cf8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87055791"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>Uw eerste Azure-functie maken met Java en IntelliJ

In dit artikel wordt het volgende beschreven:
- Een functie project zonder [Server](https://azure.microsoft.com/overview/serverless-computing/) maken met INTELLIJ-idee
- Stappen voor het testen en opsporen van fouten in de functie in de Integrated Development Environment (IDE) op uw eigen computer
- Instructies voor het implementeren van het functie project op Azure Functions

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>De ontwikkelomgeving instellen

Als u een functie wilt ontwikkelen met Java en IntelliJ, installeert u de volgende software:

+ Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
+ Een door [Azure ondersteunde Java Development Kit (JDK)](https://aka.ms/azure-jdks) voor Java 8
+ Een [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition of Community Edition geïnstalleerd
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ Nieuwste [functie kern hulpprogramma's](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>Installatie en aanmelding

1. Selecteer in het dialoogvenster Instellingen/voorkeuren van IntelliJ (CTRL+ALT+S) **Invoegtoepassingen**. Ga vervolgens naar de **Azure-toolkit voor IntelliJ** in de **Marketplace** en klik op **Installeren**. Klik na de installatie op **Opnieuw opstarten** om de invoegtoepassing te activeren. 

    ![Azure-toolkit voor IntelliJ-invoegtoepassing in Marketplace][marketplace]

2. Open in de zijbalk **Azure Explorer** in uw Azure-account en klik op het pictogram **Aanmelden bij Azure** in de balk boven (of vanuit het IDEA-menu **Hulpprogramma’s/Azure/Aanmelden bij Azure**).
    ![De opdracht Aanmelden bij Azure in IntelliJ][intellij-azure-login]

3. Selecteer **Apparaataanmelding** in het venster **Aanmelden bij Azure** en klik op **Aanmelden** ([andere aanmeldingsopties](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)).

   ![Het venster Aanmelden bij Azure met apparaataanmelding geselecteerd][intellij-azure-popup]

4. Klik op **Kopiëren en openen** in het dialoogvenster **Azure-apparaataanmelding**.

   ![Het dialoogvenster Azure-apparaataanmelding][intellij-azure-copycode]

5. Plak uw apparaatcode (die is gekopieerd toen u in de vorige stap op **Kopiëren en openen** klikte) in de browser en klik op **Volgende**.

   ![De apparaataanmeldingsbrowser][intellij-azure-link-ms-account]

6. Selecteer in het dialoogvenster **Abonnementen selecteren** de abonnementen die u wilt gebruiken en klik op **OK**.

   ![Het dialoogvenster Abonnementen selecteren][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>Uw lokale project maken

In deze sectie gebruikt u Azure-toolkit voor IntelliJ om een lokaal Azure Functions-project te maken. Verderop in dit artikel publiceert u de functiecode in Azure. 

1. Open IntelliJ Welkom dialoog venster, selecteer *Nieuw project maken* om een nieuwe project wizard te openen, selecteer *Azure functions*.

    ![Functions-project maken](media/functions-create-first-java-intellij/create-functions-project.png)

1. Selecteer *http-trigger*, klik op *volgende* en volg de wizard om alle configuraties op de volgende pagina's te door lopen. Bevestig de project locatie en klik op *volt ooien*. Het nieuwe project wordt vervolgens met Intellj-idee geopend.

    ![Functies maken Project volt ooien](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>De functie-app lokaal uitvoeren

1. Ga naar `src/main/java/org/example/functions/HttpTriggerFunction.java` om de gegenereerde code te zien. Naast regel *17*ziet u dat er een knop groen *uitvoeren* wordt weer gegeven, klikt u erop en selecteert u *' Azure-function-examen... '.* u zult zien dat uw functie-app lokaal wordt uitgevoerd met een paar Logboeken.

    ![Local run functions-project](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![Uitvoer van Local run functions](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. U kunt de functie proberen door het afgedrukte eind punt te openen vanuit de browser, bijvoorbeeld `http://localhost:7071/api/HttpTrigger-Java?name=Azure` .

    ![Test resultaat van lokale uitvoerings functies](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. Het logboek wordt ook in uw idee afgedrukt, nu kunt u de functie stoppen door te klikken op de knop *stoppen* .

    ![Test logboek voor lokale uitvoering van functies](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>Fout opsporing van de functie-app lokaal

1. Nu gaan we proberen om uw functie-app lokaal op te sporen, klikt u op de knop *fout opsporing* op de werk balk (als u deze niet ziet, klikt u op *weer geven > vormgeving-> werk balk* om de werk balk in te scha kelen).

    ![Knop lokale functie fout opsporing](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. Klik op regel *20* van het bestand `src/main/java/org/example/functions/HttpTriggerFunction.java` om een onderbrekings punt toe te voegen en toegang tot het eind punt te krijgen. `http://localhost:7071/api/HttpTrigger-Java?name=Azure` u vindt het onderbrekings punt. u kunt meer probleemoplossings functies, zoals *stap*, *kijken*en *evaluatie*, proberen. Stop de foutopsporingssessie door te klikken op de knop stoppen.

    ![Lokale functies voor fout opsporing](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>Uw functie-app implementeren in azure

1. Klik met de rechter muisknop op uw project in IntelliJ project Verkenner, selecteer *Azure-> implementeren naar Azure functions*

    ![Functies implementeren in azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. Als u nog geen functie-app hebt, klikt u op *geen beschik bare functie. Klik hier om een nieuwe te maken*.

    ![Functies implementeren in azure Create app](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. Typ de naam van de functie-app en kies het juiste abonnement/platform/resource groep/App Service plan, u kunt hier ook resource groep/App Service plan maken. Behoud de app-instellingen ongewijzigd. Klik op *OK* en wacht enkele minuten totdat de nieuwe functie is gemaakt. Na *het maken van een nieuwe functie-app...* de voortgangs balk verdwijnt.

    ![Functies implementeren in de Azure-wizard voor het maken van een app](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. Selecteer de functie-app die u wilt implementeren. (de nieuwe functie-app die u zojuist hebt gemaakt, wordt automatisch geselecteerd). Klik op *uitvoeren* om uw functies te implementeren.

    ![Functies implementeren voor uitvoering van Azure](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Functies implementeren in azure-logboek](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>Azure Functions van idee beheren

1. U kunt uw functies in uw idee beheren met *Azure Explorer* . klik op *functie-app*. u ziet hier al uw functies.

    ![Functies weer geven in Verkenner](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. Klik om een van uw functies te selecteren en klik met de rechter muisknop, selecteer *Eigenschappen weer geven* om de detail pagina te openen. 

    ![Eigenschappen van functies weer geven](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. Klik met de rechter muisknop op de functie *http trigger-Java*en selecteer *trigger functie*. u ziet dat de browser wordt geopend met de trigger-URL.

    ![Functies implementeren voor uitvoering van Azure](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>Meer functies aan het project toevoegen

1. Klik met de rechter muisknop op het pakket *org. voor beeld. functions* en Select *New-> Azure function-klasse*. 

    ![Functies toevoegen aan het project item](media/functions-create-first-java-intellij/add-functions-entry.png)

1. Vul de klassenaam *HttpTest* in en selecteer *http trigger* in de wizard functie klasse maken, klik op *OK* om te maken. u kunt op deze manier nieuwe functies maken, zoals u dat wilt.

    ![Functies toevoegen aan de trigger voor het project selecteren](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![Functies toevoegen aan de project uitvoer](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>Functies opschonen

1. Azure Functions in azure Verkenner verwijderen
      
      ![Functies toevoegen aan de trigger voor het project selecteren](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>Volgende stappen

U hebt een Java-functies project gemaakt met een door HTTP geactiveerde functie, deze uitvoeren op uw lokale computer en geïmplementeerd in Azure. Breid uw functie nu uit door...

> [!div class="nextstepaction"]
> [Een Azure Storage wachtrij-uitvoer binding toevoegen](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
