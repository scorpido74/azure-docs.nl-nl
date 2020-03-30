---
title: FoutopsporingsC#-code voor Azure Data Lake U-SQL-taken
description: In dit artikel wordt beschreven hoe u een U-SQL-foutoppas debuggen met Azure Data Lake Tools voor Visual Studio.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: bcd0b01e-1755-4112-8e8a-a5cabdca4df2
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 72239fc1679d2ebbfd9c9b5be6b79b58efb760cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315802"
---
# <a name="debug-user-defined-c-code-for-failed-u-sql-jobs"></a>Foutopsporing door de gebruiker gedefinieerde C#-code voor mislukte U-SQL-taken

U-SQL biedt een extensibiliteitsmodel met C#. In U-SQL-scripts is het eenvoudig om C#-functies aan te roepen en analytische functies uit te voeren die SQL-achtige declaratieve taal niet ondersteunt. Zie [U-SQL-programmeerbaarheidshandleiding](https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-u-sql-programmability-guide#use-user-defined-functions-udf)voor meer informatie over u-SQL-extensibiliteit. 

In de praktijk kan het zijn dat elke code moet worden gedebugd, maar het is moeilijk om een gedistribueerde taak met aangepaste code in de cloud te debuggen met beperkte logboekbestanden. [Azure Data Lake Tools voor Visual Studio](https://aka.ms/adltoolsvs) biedt een functie genaamd Failed **Vertex Debug**, waarmee u gemakkelijker de fouten debuggen die in uw aangepaste code optreden. Wanneer u-SQL-taak mislukt, houdt de service de status van fout vast en helpt de tool u om de cloudfoutomgeving naar de lokale machine te downloaden voor foutopsporing. De lokale download legt de volledige cloudomgeving vast, inclusief alle invoergegevens en gebruikerscode.

In de volgende video wordt een mislukte Vertex-foutopsporing in Azure Data Lake-hulpprogramma's voor Visual Studio getoond.

> [!VIDEO https://www.youtube.com/embed/3enkNvprfm4]
>

> [!IMPORTANT]
> Visual Studio vereist de volgende twee updates voor het gebruik van deze functie: [Microsoft Visual C++ 2015 Redistributable Update 3](https://www.microsoft.com/en-us/download/details.aspx?id=53840) en de Universal C [Runtime voor Windows](https://www.microsoft.com/download/details.aspx?id=50410).
>

## <a name="download-failed-vertex-to-local-machine"></a>Download mislukte hoekpunt naar lokale machine

Wanneer u een mislukte taak opent in Azure Data Lake Tools voor Visual Studio, ziet u een gele waarschuwingsbalk met gedetailleerde foutberichten op het fouttabblad.

1. Klik **op Downloaden** om alle benodigde bronnen en invoerstreams te downloaden. Als de download niet is voltooid, klikt u op **Opnieuw proberen**.

2. Klik **op Openen** nadat de download is voltooid om een lokale foutopsporingsomgeving te genereren. Er wordt een nieuwe oplossing voor foutopsporing geopend en als u een bestaande oplossing hebt geopend in Visual Studio, moet u deze opslaan en sluiten voordat u debugging.

![Azure Data Lake Analytics U-SQL debugt visual studio download hoekpunt](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-download-vertex.png)

## <a name="configure-the-debugging-environment"></a>De foutopsporingsomgeving configureren

> [!NOTE]
> Controleer eerst uitzonderingen op **Runtime in** de algemene taal in het venster Uitzonderingsinstellingen **(Ctrl + Alt + E).**

![Azure Data Lake Analytics U-SQL-debuging visuele studio-instelling](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-clr-exception-setting.png)

In het nieuwe gelanceerde Visual Studio-exemplaar u al dan niet de door de gebruiker gedefinieerde C#-broncode vinden:

1. [Ik kan mijn broncode vinden in de oplossing](#source-code-is-included-in-debugging-solution)

2. [Ik kan mijn broncode niet vinden in de oplossing](#source-code-is-not-included-in-debugging-solution)

### <a name="source-code-is-included-in-debugging-solution"></a>Broncode is opgenomen in debugging oplossing

Er zijn twee gevallen dat de C# broncode wordt vastgelegd:

1. De gebruikerscode wordt gedefinieerd in code-behind-bestand (meestal genoemd `Script.usql.cs` in een U-SQL-project).

2. De gebruikerscode wordt gedefinieerd in c# klassebibliotheekproject voor U-SQL-toepassing en geregistreerd als verzameling met **foutopsporingsgegevens**.

Als de broncode naar de oplossing wordt geïmporteerd, u de debuggingtools van Visual Studio (horloge, variabelen, enz.) gebruiken om het probleem op te lossen:

1. Druk op **F5** om de foutopsporing te starten. De code wordt uitgevoerd totdat deze wordt gestopt door een uitzondering.

2. Open het broncodebestand en stel breekpunten in en druk vervolgens op **F5** om de code stap voor stap te debuggen.

    ![Azure Data Lake Analytics U-SQL-foutopsporingsuitzondering](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-exception.png)

### <a name="source-code-is-not-included-in-debugging-solution"></a>Broncode is niet opgenomen in debugging oplossing

Als de gebruikerscode niet is opgenomen in het code-behind-bestand, of als u de verzameling niet hebt geregistreerd met **foutopsporingsgegevens,** wordt de broncode niet automatisch opgenomen in de foutopsporingsoplossing. In dit geval hebt u extra stappen nodig om uw broncode toe te voegen:

1. Klik met de rechtermuisknop op **Oplossing 'VertexDebug' > Voeg > bestaand project toe...** om de broncode van de assemblage te vinden en het project toe te voegen aan de foutopsporingsoplossing.

    ![Azure Data Lake Analytics U-SQL-debug-project toevoegen](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-add-project-to-debug-solution.png)

2. Download het pad van de projectmap voor **het project FailedVertexDebugHost.** 

3. Klik met de rechtermuisknop op **het project toegevoegde assemblagebroncode > Eigenschappen,** selecteer het tabblad **Bouwen** aan de linkerkant en plak het gekopieerde pad dat eindigt met \bin\debug als **uitvoer> uitvoerpad**. Het uiteindelijke uitvoerpad is als `<DataLakeTemp path>\fd91dd21-776e-4729-a78b-81ad85a4fba6\loiu0t1y.mfo\FailedVertexDebug\FailedVertexDebugHost\bin\Debug\`.

    ![Azure Data Lake Analytics U-SQL-foutopsporingsset pdb-pad](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-set-pdb-path.png)

Na deze instellingen begint u met foutopsporing met **F5** en breekpunten. U ook de debuggingtools van Visual Studio (horloge, variabelen, enz.) gebruiken om het probleem op te lossen.

> [!NOTE]
> Herbouw het project van de assemblagebroncode telkens nadat u de code hebt gewijzigd om bijgewerkte .pdb-bestanden te genereren.

## <a name="resubmit-the-job"></a>De taak opnieuw indienen

Als het project na het opsporen is voltooid, wordt in het uitvoervenster het volgende bericht weergegeven:

    The Program 'LocalVertexHost.exe' has exited with code 0 (0x0).

![Azure Data Lake Analytics U-SQL-foutopsporing geslaagd](./media/data-lake-analytics-debug-u-sql-jobs/data-lake-analytics-debug-succeed.png)

Ga als u de mislukte taak opnieuw in:

1. Kopieer de C#-code voor taken met code-behindoplossingen naar het `Script.usql.cs`broncodebestand (meestal).

2. Voor taken met assemblages klikt u met de rechtermuisknop op het project voor de broncode van de assemblage in defoutopsporingoplossing en registreert u de bijgewerkte .dll-samenstellingen in uw Azure Data Lake-catalogus.

3. De U-SQL-taak opnieuw indienen.

## <a name="next-steps"></a>Volgende stappen

- [Gids voor U-SQL-programmabiliteit](data-lake-analytics-u-sql-programmability-guide.md)
- [U-SQL-gebruikersgedefinieerde operatoren ontwikkelen voor Azure Data Lake Analytics-taken](data-lake-analytics-u-sql-develop-user-defined-operators.md)
- [U-SQL-taken testen en controleren op fouten met behulp van lokale uitvoering en de Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)
- [Een abnormale terugkerende taak oplossen](data-lake-analytics-data-lake-tools-debug-recurring-job.md)
