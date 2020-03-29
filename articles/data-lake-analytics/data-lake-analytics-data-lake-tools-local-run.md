---
title: Azure Data Lake U-SQL-scripts uitvoeren op uw lokale machine
description: Meer informatie over het gebruik van Azure Data Lake Tools voor Visual Studio om U-SQL-taken uit te voeren op uw lokale machine.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "62113932"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>U-SQL-scripts uitvoeren op uw lokale machine

Wanneer u U-SQL-scripts ontwikkelt, u tijd en kosten besparen door de scripts lokaal uit te voeren. Azure Data Lake Tools voor Visual Studio ondersteunt het uitvoeren van U-SQL-scripts op uw lokale machine. 

## <a name="basic-concepts-for-local-runs"></a>Basisconcepten voor lokale runs

In de volgende grafiek worden de componenten voor lokale run weergegeven en hoe deze componenten worden toegewezen aan de cloud.

|Onderdeel|Lokaal uitvoeren|Cloudrun|
|---------|---------|---------|
|Storage|Hoofdmap lokale gegevens|Standaard Azure Data Lake Store-account|
|Compute|U-SQL lokale run engine|Azure Data Lake Analytics-service|
|Beheer omgeving|Werkmap op lokale machine|Azure Data Lake Analytics-cluster|

De volgende secties geven meer informatie over lokale runcomponenten.

### <a name="local-data-root-folders"></a>Hoofdmappen voor lokale gegevens

Een lokale map met gegevensbasis is een **lokaal archief** voor het lokale compute-account. Elke map in het lokale bestandssysteem op uw lokale machine kan een lokale hoofdmap voor gegevens zijn. Het is hetzelfde als het standaard Azure Data Lake Store-account van een Data Lake Analytics-account. Overschakelen naar een andere gegevenshoofdmap is net als overschakelen naar een ander standaardwinkelaccount. 

De hoofdmap voor gegevens wordt als volgt gebruikt:
- Metagegevens opslaan. Voorbeelden zijn databases, tabellen, functies met tabelwaarde en samenstellingen.
- Zoek de invoer- en uitvoerpaden op die worden gedefinieerd als relatieve paden in U-SQL-scripts. Door relatieve paden te gebruiken, is het eenvoudiger om uw U-SQL-scripts te implementeren in Azure.

### <a name="u-sql-local-run-engines"></a>U-SQL lokale run engines

Een U-SQL local run engine is een **lokaal compute account** voor U-SQL-taken. Gebruikers kunnen U-SQL-taken lokaal uitvoeren via Azure Data Lake Tools voor Visual Studio. Lokale uitvoeringen worden ook ondersteund via de Azure Data Lake U-SQL SDK-opdrachtregel en programmeerinterfaces. Meer informatie over de [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Werkmappen

Wanneer u een U-SQL-script uitvoert, is een werkende mapmap nodig om compilatieresultaten in de cache op te zetten, logboeken uit te voeren en andere functies uit te voeren. In Azure Data Lake Tools voor Visual Studio is de werkmap de werkmap van het U-SQL-project. Het bevindt `<U-SQL project root path>/bin/debug>`zich onder. De werkmap wordt elke keer dat een nieuwe run wordt geactiveerd, gereinigd.

## <a name="local-runs-in-microsoft-visual-studio"></a>Lokale uitvoeringen in Microsoft Visual Studio

Azure Data Lake-hulpprogramma's voor Visual Studio hebben een ingebouwde lokale run engine. De tools oppervlak de motor als een lokale compute account. Als u een U-SQL-script lokaal wilt uitvoeren, selecteert u het vervolgkeuzemenu **lokaal-machine-** of **Lokaal-project** in het vervolgkeuzemenu voor de editormarge van het script. Selecteer vervolgens **Verzenden**.

![Een U-SQL-script verzenden naar een lokaal account](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Lokaal wordt uitgevoerd met een lokaal-machine-account

Een **lokaal-machine-account** is een gedeeld lokaal rekenaccount met één lokale hoofdmap voor gegevens als het lokale winkelaccount. Standaard bevindt de map gegevensbasis zich op **C:\Gebruikersgebruikersnaam\<>\AppData\Local\USQLDataRoot**. Het is ook configureerbaar via **Tools** > **Data Lake-opties** > **en -instellingen.**

![Een hoofdmap voor lokale gegevens configureren](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Een U-SQL-project is vereist voor een lokale run. De werkmap van het U-SQL-project wordt gebruikt voor de u-SQL local run working directory. Compilatieresultaten, logboeken uitvoeren en andere taakrungerelateerde bestanden worden gegenereerd en opgeslagen onder de map werken tijdens de lokale run. Elke keer dat u het script opnieuw uitvoert, worden alle bestanden in de werkmap gereinigd en geregenereerd.

## <a name="local-runs-with-a-local-project-account"></a>Lokale uitvoeringen met een lokaal projectaccount

Een **lokaal projectaccount** is een projectgeïsoleerde lokale compute-account voor elk project met een geïsoleerde lokale gegevenshoofdmap. Elk actief U-SQL-project dat wordt geopend in `(Local-project: <project name>)` Solution Explorer in Visual Studio heeft een bijbehorend account. De accounts worden weergegeven in zowel Server Explorer in Visual Studio als in de marge van de U-SQL-scripteditor.  

Het **lokale projectaccount** biedt een schone en geïsoleerde ontwikkelomgeving. Een **lokaal-machine-account** heeft een gedeelde lokale hoofdmap voor gegevens die metagegevens en invoer- en uitvoergegevens opslaat voor alle lokale taken. Maar een **lokaal projectaccount** maakt een tijdelijke lokale gegevenshoofdmap onder een U-SQL-projectwerkmap elke keer dat een U-SQL-script wordt uitgevoerd. Deze tijdelijke hoofdmap voor gegevens wordt gereinigd wanneer een herbouw of opnieuw uitvoeren plaatsvindt. 

Een U-SQL-project beheert de geïsoleerde lokale run-omgeving via een projectverwijzing en -eigenschap. U de invoergegevensbronnen voor U-SQL-scripts configureren in zowel het project als de databaseomgevingen waarnaar wordt verwezen.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>De invoergegevensbron voor een lokaal projectaccount beheren 

Een U-SQL-project maakt een lokale gegevenshoofdmap en stelt gegevens in voor een **lokaal projectaccount.** Een tijdelijke map met gegevensroot wordt schoongemaakt en opnieuw gemaakt onder de werkmap u-SQL-project elke keer dat een heropbouw en lokale run plaatsvindt. Alle gegevensbronnen die zijn geconfigureerd door het U-SQL-project, worden naar deze tijdelijke lokale map met lokale gegevens gekopieerd voordat de lokale taak wordt uitgevoerd. 

U de hoofdmap van uw gegevensbronnen configureren. Klik met de rechtermuisknop op **U-SQL-projectProperty** > **Property** > **Test Data Source**. Wanneer u een U-SQL-script uitvoert op een **lokaal projectaccount,** worden alle bestanden en submappen in de map **Testgegevensbron** gekopieerd naar de tijdelijke lokale map met gegevens. Bestanden onder submappen zijn inbegrepen. Nadat een lokale taak is uitgevoerd, kunnen de uitvoerresultaten ook worden gevonden onder de tijdelijke lokale hoofdmap voor lokale gegevens in de werkmap van het project. Al deze uitvoer wordt verwijderd en gereinigd wanneer het project wordt herbouwd en gereinigd. 

![De testgegevensbron van een project configureren](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Een databaseomgeving met verwijzing beheren voor een **lokaal projectaccount** 

Als een U-SQL-query wordt gebruikt of query's met U-SQL-databaseobjecten worden gebruikt, moet u de databaseomgevingen lokaal gereed maken voordat u het U-SQL-script lokaal uitvoert. Voor een **lokaal-projectaccount** kunnen u-SQL-databaseafhankelijkheden worden beheerd door U-SQL-projectverwijzingen. U U-SQL-databaseprojectverwijzingen toevoegen aan uw U-SQL-project. Voordat U-SQL-scripts worden uitgevoerd op een **lokaal projectaccount,** worden alle databases waarnaar wordt verwezen geïmplementeerd in de tijdelijke lokale map met gegevens. En voor elke run wordt de tijdelijke map met gegevensroot gereinigd als een nieuwe geïsoleerde omgeving.

Zie dit gerelateerde artikel:
* Meer informatie over het beheren van U-SQL-databasedefinities en -verwijzingen in [U-SQL-databaseprojecten](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Het verschil tussen **lokale-machine** en **lokaal-project** accounts

Een **lokaal-machine-account** simuleert een Azure Data Lake Analytics-account op lokale machines van gebruikers. Het deelt dezelfde ervaring met een Azure Data Lake Analytics-account. Een **lokaal projectaccount** biedt een gebruiksvriendelijke lokale ontwikkelomgeving. Met deze omgeving kunnen gebruikers databaseverwijzingen en invoergegevens implementeren voordat ze scripts lokaal uitvoeren. Een **lokaal-machine-account** biedt een gedeelde permanente omgeving die toegankelijk is via alle projecten. Een **lokaal projectaccount** biedt voor elk project een geïsoleerde ontwikkelomgeving. Het is vernieuwd voor elke run. Een **Lokaal projectaccount** biedt een snellere ontwikkelingservaring door snel nieuwe wijzigingen toe te passen.

Meer verschillen tussen **lokale-machine-** en **lokale projectaccounts** worden weergegeven in de volgende tabel:

|Verschilhoek|Lokale machine|Lokaal project|
|----------------|---------------|---------------|
|Lokale toegang|Kan worden benaderd door alle projecten.|Alleen het bijbehorende project heeft toegang tot dit account.|
|Hoofdmap lokale gegevens|Een permanente lokale map. Geconfigureerd via **tools** > **gegevensmeeropties** > en **-instellingen**.|Een tijdelijke map die is gemaakt voor elke lokale run onder de werkmap U-SQL-project. De map wordt schoongemaakt wanneer een herbouw of opnieuw wordt uitgevoerd.|
|Invoergegevens voor een U-SQL-script|Het relatieve pad onder de permanente lokale hoofdmap voor gegevens.|Instellen via **U-SQL-projecteigenschap** > **Testgegevensbron**. Alle bestanden en submappen worden gekopieerd naar de tijdelijke hoofdmap voor gegevens voordat een lokale run wordt uitgevoerd.|
|Uitvoergegevens voor een U-SQL-script|Relatief pad onder de permanente lokale map met gegevens.|Uitvoer naar de tijdelijke hoofdmap voor gegevens. De resultaten worden gereinigd wanneer een herbouw of rerun plaatsvindt.|
|Database-implementatie onder verwijzing|Naar basisgegevens worden niet automatisch geïmplementeerd wanneer ze worden uitgevoerd op een **lokaal-machine-account.** Hetzelfde geldt voor het indienen bij een Azure Data Lake Analytics-account.|Naar basisgegevens worden automatisch geïmplementeerd in het **lokale projectaccount** voordat een lokale run wordt uitgevoerd. Alle databaseomgevingen worden schoongemaakt en opnieuw geïmplementeerd wanneer een heropbouw of opnieuw wordt uitgevoerd.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Een lokale run met de U-SQL SDK

U U-SQL-scripts lokaal uitvoeren in Visual Studio en ook de Azure Data Lake U-SQL SDK gebruiken om U-SQL-scripts lokaal uit te voeren met opdrachtregel- en programmeerinterfaces. Via deze interfaces u lokale u-SQL-uitvoeringen en tests automatiseren.

Meer informatie over de [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Volgende stappen

- [Een CI/CD-pijplijn instellen voor Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Uw Azure Data Lake Analytics-code testen.](data-lake-analytics-cicd-test.md)
