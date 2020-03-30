---
title: U-SQL-taken debuggen - Azure Data Lake-hulpprogramma's voor Visual Studio-code
description: Meer informatie over het lokaal uitvoeren en opsporen van U-SQL-taken met Azure Data Lake Tools voor Visual Studio Code.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: e51b5640163546c673a1b0f61da47ccd992f27ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72030037"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>U-SQL en debuging lokaal uitvoeren in Visual Studio Code
In dit artikel wordt beschreven hoe u-SQL-taken op een lokale ontwikkelingsmachine kunnen uitvoeren om vroege coderingsfasen te versnellen of om code lokaal te debuggen in Visual Studio Code. Zie [Azure Data Lake Tools voor Visual Studio-code gebruiken voor](data-lake-analytics-data-lake-tools-for-vscode.md)instructies over Azure Data Lake Tool voor Visual Studio-code .

Alleen Windows-installaties van de Azure Data Lake Tools voor Visual Studio ondersteunen de actie om U-SQL lokaal uit te voeren en U-SQL lokaal te debuggen. Installaties op macOS- en Linux-gebaseerde besturingssystemen ondersteunen deze functie niet.

## <a name="set-up-the-u-sql-local-run-environment"></a>De lokale beheeromgeving u-SQL instellen

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen en voer **Vervolgens ADL: Lokaal runpakket downloaden** in om de pakketten te downloaden.  

   ![Download de Afhankelijkheidspakketten van ADL LocalRun](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Zoek de afhankelijkheidspakketten van het pad in het deelvenster **Uitvoer** en installeer Vervolgens BuildTools en Win10SDK 10240. Hier is een voorbeeldpad:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![De afhankelijkheidspakketten zoeken](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2.1 Als u **BuildTools**wilt installeren, klikt u op visualcppbuildtools_full.exe in de map LocalRunDependency en volgt u de instructies van de wizard.   

    ![BuildTools installeren](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2.2 Om **Win10SDK 10240**te installeren, klikt u op sdksetup.exe in de map LocalRunDependency/Win10SDK_10.0.10240_2 en volgt u de instructies van de wizard.  

    ![Win10SDK 10240 installeren](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Stel de omgevingsvariabele in. Stel de **SCOPE_CPP_SDK** omgevingsvariabele in op:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Start de lokale run-service en dien de U-SQL-taak in bij een lokaal account 
Voor de eerste gebruiker gebruikt u **ADL: Download Local Run Package** om lokale runpakketten te downloaden, als u de lokale [beheeromgeving u-SQL](#set-up-the-u-sql-local-run-environment)niet hebt ingesteld.

1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen en voer **Vervolgens ADL in: Lokale runservice starten**.   
2. Selecteer **Accepteren** om de licentievoorwaarden voor Microsoft-software voor de eerste keer te accepteren. 

   ![Accepteer de licentievoorwaarden voor Microsoft-software](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. De cmd-console gaat open. Voor nieuwe gebruikers moet u **3**invoeren en vervolgens het lokale mappad zoeken voor uw gegevensinvoer en -uitvoer. Als u geen succes hebt bij het definiëren van het pad met backslashes, probeert u slashes door te spoelen. Voor andere opties u de standaardwaarden gebruiken.

   ![Data Lake Tools voor Visual Studio Code lokale run cmd](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen, voer **ADL: Taak in**en selecteer **Vervolgens Lokaal** om de taak in te dienen bij uw lokale account.

   ![Data Lake-hulpprogramma's voor Visual Studio-code selecteren lokaal](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Nadat u de taak hebt ingediend, u de inzendingsgegevens bekijken. Als u de inzendingsgegevens wilt weergeven, selecteert u **jobUrl** in het venster **Uitvoer.** U ook de status van taakindiening bekijken vanaf de cmd-console. Voer **7** in de cmd-console in als u meer taakgegevens wilt weten.

   ![Data Lake Tools voor Visual](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![Studio Code lokale run output Data Lake Tools voor Visual Studio Code lokale run cmd status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Een lokale foutopsporing voor de U-SQL-taak starten  
Voor de eerste gebruiker:

1. Gebruik **ADL: Download lokaal run-pakket** om lokale runpakketten te downloaden als u de [lokale beheeromgeving van U-SQL](#set-up-the-u-sql-local-run-environment)niet hebt ingesteld.
2. Installeer .NET Core SDK 2.0 zoals voorgesteld in het berichtenvak, indien niet geïnstalleerd.
 
  ![herinnering installeert Dotnet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installeer C# voor Visual Studio Code zoals voorgesteld in het berichtenvak als deze niet is geïnstalleerd.Klik **op Installeren** om door te gaan en start VSCode opnieuw.

    ![Herinnering voor installatie C #](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Volg onderstaande stappen om lokale foutopsporing uit te voeren:
  
1. Selecteer Ctrl+Shift+P om het opdrachtpalet te openen en voer **Vervolgens ADL in: Lokale runservice starten**. De cmd-console gaat open. Controleer of de **DataRoot** is ingesteld.
2. Stel een breekpunt in uw C# code-behind.
3. Terug naar scripteditor, klik met de rechtermuisknop en selecteer **ADL: Lokale foutopsporing**.
    
   ![Resultaten van lokale foutopsporing van Gegevenslake-gegevens voor visual studiocode](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Volgende stappen
* [De Azure Data Lake-tools gebruiken voor Visual Studio-code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL ontwikkelen met Python, R en CSharp voor Azure Data Lake Analytics in VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Aan de slag met Data Lake Analytics met PowerShell](data-lake-analytics-get-started-powershell.md)
* [Aan de slag met Data Lake Analytics met de Azure-portal](data-lake-analytics-get-started-portal.md)
* [Data Lake-hulpprogramma's gebruiken voor Visual Studio voor het ontwikkelen van U-SQL-toepassingen](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-catalogus (Data Lake Analytics) gebruiken](data-lake-analytics-use-u-sql-catalog.md)
