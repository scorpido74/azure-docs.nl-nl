---
title: Fouten opsporen in U-SQL-taken-Azure Data Lake-Hulpprogram Ma's voor Visual Studio code
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio code om U-SQL-taken lokaal uit te voeren en fouten op te sporen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.topic: conceptual
ms.date: 07/14/2017
ms.openlocfilehash: 7c425ee8c9fd99420bbcbba31c7a74c360938aef
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338253"
---
# <a name="run-u-sql-and-debug-locally-in-visual-studio-code"></a>U-SQL en lokaal fouten opsporen in Visual Studio code
In dit artikel wordt beschreven hoe U U-SQL-taken uitvoert op een lokale ontwikkel computer om de vroege code fasen te versnellen of om code lokaal te debuggen in Visual Studio code. Zie [Azure data Lake-Hulpprogram ma's voor Visual Studio code gebruiken](data-lake-analytics-data-lake-tools-for-vscode.md)voor instructies over het Azure data Lake-hulp programma voor Visual Studio code.

Alleen Windows-installaties van de Azure Data Lake-Hulpprogram Ma's voor Visual Studio ondersteunen de actie om U-SQL lokaal uit te voeren en U-SQL lokaal te debuggen. Installaties op macOS-en Linux-besturings systemen bieden geen ondersteuning voor deze functie.

## <a name="set-up-the-u-sql-local-run-environment"></a>De lokale uitvoerings omgeving van U-SQL instellen

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen en voer **vervolgens ADL in: Down load het lokale** uitvoerings pakket om de pakketten te downloaden.  

   ![De ADL LocalRun-afhankelijkheids pakketten downloaden](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/downloadtheadllocalrunpackage.png)

2. Zoek de afhankelijkheids pakketten in het pad dat wordt weer gegeven in het deel venster **uitvoer** en installeer vervolgens build-en Win10SDK 10240. Hier volgt een voor beeld van een pad:  
`C:\Users\xxx\AppData\Roaming\LocalRunDependency` 

   ![De afhankelijkheids pakketten zoeken](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/LocateDependencyPath.png)

   2,1 als u de **build-versie**wilt installeren, klikt u op visualcppbuildtools_full. exe in de map LocalRunDependency en volgt u de instructies in de wizard.   

    ![Build-versie installeren](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallBuildTools.png)

   2,2 om **Win10SDK 10240**te installeren, klikt u op sdksetup. exe in de map LocalRunDependency/win10sdk_ 10.0.10240 _2 en volgt u de instructies in de wizard.  

    ![Win10SDK 10240 installeren](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/InstallWin10SDK.png)

3. Stel de omgevings variabele in. Stel de omgevings variabele **SCOPE_CPP_SDK** in op:  
`C:\Users\XXX\AppData\Roaming\LocalRunDependency\CppSDK_3rdparty`  


## <a name="start-the-local-run-service-and-submit-the-u-sql-job-to-a-local-account"></a>Start de lokale run-service en verzend de U-SQL-taak naar een lokaal account 
Gebruik ADL voor de eerste keer dat de **gebruiker wordt gebruikt: Down load het lokale**run-pakket om lokale run-pakketten te downloaden als u geen [lokale run-SQL-omgeving hebt ingesteld](#set-up-the-u-sql-local-run-environment).

1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen en voer **vervolgens ADL in: Start de lokale uitvoerings**service.   
2. Selecteer **accepteren** om de licentie voorwaarden voor micro soft-software voor de eerste keer te accepteren. 

   ![De licentie voorwaarden voor micro soft-software accepteren](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/AcceptEULA.png)   
3. De cmd-console wordt geopend. Voor de eerste keer dat gebruikers worden opgegeven, moet u **3**invoeren en vervolgens het pad naar de lokale map voor de invoer en uitvoer van uw gegevens zoeken. Als u het pad met backslashes niet hebt gedefinieerd, kunt u een slash door sturen. Voor andere opties kunt u de standaard waarden gebruiken.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio code Local run CMD](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-cmd.png)
4. Selecteer CTRL + SHIFT + P om het opdracht palet te openen, **Voer ADL in: Verzend taak**en selecteer vervolgens **lokaal** om de taak naar uw lokale account te verzenden.

   ![Data Lake-Hulpprogram Ma's voor Visual Studio code lokale selecteren](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-select-local.png)
5. Nadat u de taak hebt verzonden, kunt u de details van de verzen ding bekijken. Selecteer **jobUrl** in het venster **uitvoer** om de details van de verzen ding weer te geven. U kunt ook de status van de taak inzending bekijken vanuit de cmd-console. Voer **7** in de cmd-console in als u meer taak details wilt weten.

   ![Data Lake tools for Visual Studio code Local run uitvoer](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-run-result.png)
   ![data Lake tools for Visual Studio code Local run CMD status](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-localrun-cmd-status.png) 


## <a name="start-a-local-debug-for-the-u-sql-job"></a>Een lokale fout opsporing starten voor de U-SQL-taak  
Voor de eerste gebruiker:

1. Gebruik **ADL: Down load het lokale**run-pakket om lokale run-pakketten te downloaden als u geen [lokale run-SQL-omgeving hebt ingesteld](#set-up-the-u-sql-local-run-environment).
2. Installeer .NET Core SDK 2,0, zoals wordt voorgesteld in het bericht venster, als dit niet is geïnstalleerd.
 
  ![herinnering installeert DotNet](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/remind-install-dotnet.png)
3. Installeer C# voor Visual Studio-code zoals wordt voorgesteld in het bericht venster als dat niet is geïnstalleerd. Klik op **installeren** om door te gaan en VSCode opnieuw te starten.

    ![Te installeren herinneringC#](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/install-csharp.png)

Volg de onderstaande stappen om lokaal fout opsporing uit te voeren:
  
1. Selecteer CTRL + SHIFT + P om het opdracht palet te openen en voer **vervolgens ADL in: Start de lokale uitvoerings**service. De cmd-console wordt geopend. Zorg ervoor dat de **Data root** is ingesteld.
2. Stel een onderbrekings punt C# in voor de code-behind.
3. Terug naar de Script Editor, klik met de rechter **muisknop en selecteer ADL: Lokale fout**opsporing.
    
   ![Data Lake-Hulpprogram Ma's voor Visual Studio code Local debug-resultaat](./media/data-lake-analytics-data-lake-tools-for-vscode-local-run-and-debug/data-lake-tools-for-vscode-local-debug-result.png)


## <a name="next-steps"></a>Volgende stappen
* [De Azure Data Lake-tools gebruiken voor Visual Studio-code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL ontwikkelen met python, R en CSharp voor Azure Data Lake Analytics in VSCode](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
* [Aan de slag met Data Lake Analytics met behulp van Power shell](data-lake-analytics-get-started-powershell.md)
* [Aan de slag met Data Lake Analytics met behulp van de Azure Portal](data-lake-analytics-get-started-portal.md)
* [Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken voor het ontwikkelen van U-SQL-toepassingen](data-lake-analytics-data-lake-tools-get-started.md)
* [Data Lake Analytics-catalogus (U-SQL) gebruiken](data-lake-analytics-use-u-sql-catalog.md)
