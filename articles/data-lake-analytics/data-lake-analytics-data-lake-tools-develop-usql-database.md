---
title: Een U-SQL-databaseproject ontwikkelen - Azure Data Lake
description: Meer informatie over het ontwikkelen van een U-SQL-database met Azure Data Lake Tools voor Visual Studio.
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: a9b271b5f7d4e53dbf871d03dd43b62b9299aa53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309931"
---
# <a name="use-a-u-sql-database-project-to-develop-a-u-sql-database-for-azure-data-lake"></a>Een U-SQL-databaseproject gebruiken om een U-SQL-database voor Azure Data Lake te ontwikkelen

U-SQL-database biedt gestructureerde weergaven over ongestructureerde gegevens en beheerde gestructureerde gegevens in tabellen. Het biedt ook een algemene metadata catalogus systeem voor het organiseren van uw gestructureerde gegevens en aangepaste code. De database is het concept dat deze gerelateerde objecten samengroepeert.

Meer informatie over [U-SQL-database en Gegevensdefinitietaal (DDL)](/u-sql/data-definition-language-ddl-statements). 

Het U-SQL-databaseproject is een projecttype in Visual Studio dat ontwikkelaars helpt bij het snel en eenvoudig ontwikkelen, beheren en implementeren van hun U-SQL-databases.

## <a name="create-a-u-sql-database-project"></a>Een U-SQL-databaseproject maken

Azure Data Lake Tools voor Visual Studio heeft na versie 2.3.3000.0 een nieuwe projectsjabloon toegevoegd, genaamd U-SQL-databaseproject. Als u een U-SQL-project wilt maken, selecteert u **Bestand > Nieuw > Project**. Het U-SQL Database Project is te vinden onder **Azure Data Lake > U-SQL-knooppunt.**

![Data Lake-hulpprogramma's voor Visual Studio - U-SQL-databaseproject maken](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-usql-database-project-creation.png) 

## <a name="develop-u-sql-database-objects-by-using-a-database-project"></a>U-SQL-databaseobjecten ontwikkelen met behulp van een databaseproject

Klik met de rechtermuisknop op het U-SQL-databaseproject. De optie **> nieuw item toevoegen**selecteren . U alle nieuwe ondersteunde objecttypen vinden in de wizard **Nieuw item toevoegen.** 

Voor een niet-assemblageobject (bijvoorbeeld een functie met tabelwaarde) wordt een nieuw U-SQL-script gemaakt nadat u een nieuw item hebt toegevoegd. U beginnen met het ontwikkelen van de DDL-instructie voor dat object in de editor.

Voor een assemblageobject biedt de tool een gebruiksvriendelijke ui-editor waarmee u de assemblage registreren en DLL-bestanden en andere extra bestanden implementeren. In de volgende stappen ziet u hoe u een definitie van een assemblageobject toevoegt aan het U-SQL-databaseproject:

1.  Voeg verwijzingen toe naar het C#-project met de UDO/UDAG/UDF voor het U-SQL-databaseproject.

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databaseprojectverwijzing toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference.png) 

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databaseprojectverwijzing toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-project-reference-wizard.png)

2.  Kies in de ontwerpweergave van de assemblage de assemblage waarnaar wordt verwezen in Het vervolgkeuzemenu Assemblage maken in het vervolgkeuzemenu **Referentie.**

    ![Data Lake Tools voor Visual Studio - maak assemblage op basis van referentie](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-create-assembly-from-reference.png)

3.  Voeg **beheerde afhankelijkheden** en **extra bestanden toe** als die er zijn. Wanneer u extra bestanden toevoegt, gebruikt het gereedschap het relatieve pad om ervoor te zorgen dat de samenstellingen zowel op uw lokale machine als later op de bouwmachine kunnen worden gevonden. 

@_DeployTempDirectoryis een vooraf gedefinieerde variabele die het gereedschap naar de uitvoermap build wijst. Onder de map buildoutput heeft elke assemblage een submap met de naam van de assemblage. Alle DLL's en extra bestanden bevinden zich in die submap. 
 
## <a name="build-a-u-sql-database-project"></a>Een U-SQL-databaseproject bouwen

De buildoutput voor een U-SQL-databaseproject is een U-SQL-databaseimplementatiepakket, genoemd met het achtervoegsel. `.usqldbpack` Het `.usqldbpack` pakket is een .zip-bestand dat alle DDL-instructies in één U-SQL-script in de **DDL-map** bevat, en alle DLL's en extra bestanden voor verzamelingen in de **map Temp.**

Meer informatie over [het bouwen van een U-SQL-databaseproject met de msbuild-opdrachtregel en een buildtaak van Azure DevOps Services](data-lake-analytics-cicd-overview.md).

## <a name="deploy-a-u-sql-database"></a>Een U-SQL-database implementeren

Het .usqldbpack-pakket kan worden geïmplementeerd op een lokaal account of een Azure Data Lake Analytics-account met behulp van Visual Studio of de implementatie-SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Een U-SQL-database implementeren in Visual Studio

U een U-SQL-database implementeren via een U-SQL-databaseproject of een .usqldbpack-pakket in Visual Studio.

#### <a name="deploy-through-a-u-sql-database-project"></a>Implementeren via een U-SQL-databaseproject

1.  Klik met de rechtermuisknop op het U-SQL-databaseproject en selecteer **Vervolgens Implementeren**.
2.  Selecteer in de **wizard U-SQL-database implementeren**het **ADLA-account** waarop u de database wilt implementeren. Zowel lokale accounts als ADLA-accounts worden ondersteund.
3.  **Databasebron** wordt automatisch ingevuld en wijst naar het .usqldbpack-pakket in de uitvoermap build van het project.
4.  Voer een naam in **Databasenaam** in om een database te maken. Als er al een database met dezelfde naam bestaat in het doelAzure Data Lake Analytics-account, worden alle objecten die zijn gedefinieerd in het databaseproject gemaakt zonder de database opnieuw te maken.
5.  Als u de U-SQL-database wilt implementeren, selecteert u **Verzenden**. Alle bronnen (samenstellingen en extra bestanden) worden geüpload en er wordt een U-SQL-taak ingediend die alle DDL-instructies bevat.

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databaseproject implementeren](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project.png)

    ![Gegevenslaketools voor visual studio- Wizard U-SQL-databaseproject implementeren](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-project-wizard.png)

#### <a name="deploy-through-a-u-sql-database-deployment-package"></a>Implementeren via een U-SQL-databaseimplementatiepakket

1.  Open **Server Explorer**. Vouw vervolgens het **Azure Data Lake Analytics-account** uit waarop u de database wilt implementeren.
2.  Klik met de rechtermuisknop op **U-SQL-databases**en kies **Database implementeren**.
3.  **Databasebron** instellen op het u-SQL-databaseimplementatiepakket (.usqldbpack-bestand) pad.
4.  Voer de **databasenaam** in om een database te maken. Als er een database met dezelfde naam is die al bestaat in het doelAzure Data Lake Analytics-account, worden alle objecten die zijn gedefinieerd in het databaseproject gemaakt zonder de database opnieuw te maken.

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databasepakket implementeren](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package.png)

    ![Gegevensmeerhulpprogramma's voor de wizard U-SQL-databasepakket implementeren](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-deploy-usql-database-package-wizard.png)
  
### <a name="deploy-u-sql-database-by-using-the-sdk"></a>U-SQL-database implementeren met behulp van de SDK

`PackageDeploymentTool.exe`biedt de programmeer- en opdrachtregelinterfaces die u-SQL-databases helpen implementeren. De SDK is opgenomen in het [U-SQL SDK Nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), gelegen op `build/runtime/PackageDeploymentTool.exe`.

[Meer informatie over de SDK en het instellen van CI/CD-pijplijn voor U-SQL-databaseimplementatie](data-lake-analytics-cicd-overview.md).

## <a name="reference-a-u-sql-database-project"></a>Een U-SQL-databaseproject naverwijzen

Een U-SQL-project kan verwijzen naar een U-SQL-databaseproject. De verwijzing heeft betrekking op twee workloads:

- *Projectbuild:* Stel de databaseomgevingen waarnaar wordt verwezen in voordat u de U-SQL-scripts bouwt. 
- *Lokale run tegen (een lokaal project)-account:* de databaseomgevingen waarnaar wordt verwezen, worden geïmplementeerd op (een lokaal project)-account voordat U-SQL-script wordt uitgevoerd. [Lees hier meer over lokale runs en het verschil tussen (de lokale machine) en (een lokaal project) account.](data-lake-analytics-data-lake-tools-local-run.md)

### <a name="how-to-add-a-u-sql-database-reference"></a>Een U-SQL-databaseverwijzing toevoegen

1. Klik met de rechtermuisknop op het U-SQL-project in **Solution Explorer**en kies **U-SQL-databaseverwijzing toevoegen...**.

    ![Data Lake Tools voor Visual Studio - databaseprojectreferentie toevoegen](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference.png)

2. Een databaseverwijzing configureren vanuit een U-SQL-databaseproject in de huidige oplossing of in een U-SQL-databasepakketbestand.
3. Geef de naam voor de database op.

    ![Gegevensmeerhulpprogramma's voor Visual Studio toevoegen databaseprojectverwijzingwizard](./media/data-lake-analytics-data-lake-tools-develop-usql-database/data-lake-tools-add-database-project-reference-wizard.png)

## <a name="next-steps"></a>Volgende stappen

- [Een CI/CD-pijplijn instellen voor Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Uw Azure Data Lake Analytics-code testen](data-lake-analytics-cicd-test.md)
- [U-SQL-script uitvoeren op uw lokale machine](data-lake-analytics-data-lake-tools-local-run.md)
