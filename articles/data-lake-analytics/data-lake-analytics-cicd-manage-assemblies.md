---
title: U-SQL-assemblages beheren in een CI/CD-pijplijn - Azure Data Lake
description: Meer informatie over de aanbevolen procedures voor het beheren van U-SQL C#-verzamelingen in een CI/CD-pijplijn met Azure DevOps.
author: yanancai
ms.author: yanacai
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: e6de10ed712688e4ee9dccc22176e81ad5e574ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315854"
---
# <a name="best-practices-for-managing-u-sql-assemblies-in-a-cicd-pipeline"></a>Aanbevolen procedures voor het beheren van U-SQL-assemblages in een CI/CD-pijplijn

In dit artikel leert u hoe u u-SQL-assemblagebroncode beheert met het onlangs geïntroduceerde U-SQL-databaseproject. U leert ook hoe u een CI/CD-pijplijn (continuous integration and deployment) instelt voor assemblageregistratie met Azure DevOps.

## <a name="use-the-u-sql-database-project-to-manage-assembly-source-code"></a>Het U-SQL-databaseproject gebruiken om de broncode van de assemblage te beheren

[Het U-SQL-databaseproject](data-lake-analytics-data-lake-tools-develop-usql-database.md) is een projecttype in Visual Studio dat ontwikkelaars helpt bij het snel en eenvoudig ontwikkelen, beheren en implementeren van hun U-SQL-databases. U alle U-SQL-databaseobjecten (behalve referenties) beheren met het U-SQL-databaseproject. 

Als u de C#-broncode voor verzamelingen en de ddl U-SQL-scripts voor assemblageregistratie wilt beheren, gebruikt u de:

* U-SQL-databaseproject voor het beheren van U-SQL-scripts voor assemblageregistratie.
* Klassebibliotheek (Voor U-SQL-toepassing) om de C#-broncode en afhankelijkheden voor door de gebruiker gedefinieerde operatoren, functies en aggregators (UDO's, UDF's en UDAGs) te beheren.
* U-SQL-databaseproject om naar het klassebibliotheekproject te verwijzen. 

Een U-SQL-databaseproject kan verwijzen naar een klassebibliotheekproject (For U-SQL Application). U verzamelingen maken die zijn geregistreerd in de U-SQL-database met behulp van c#-broncode van dit klassebibliotheekproject (For U-SQL Application).

Volg deze stappen om projecten te maken en referenties toe te voegen.
1. Maak een klassebibliotheekproject (Voor U-SQL-toepassing) door **Bestand** > Nieuw**project****te** > selecteren. Het project staat onder het **Azure Data Lake-> U-SQL-knooppunt.**

   ![Data Lake Tools voor Visual Studio - C# klasse bibliotheekproject maken](./media/data-lake-analytics-cicd-manage-assemblies/create-c-sharp-class-library-project.png)
1. Voeg uw door de gebruiker gedefinieerde C#-code toe aan het project Klassebibliotheek (Voor U-SQL-toepassing).

1. Maak een U-SQL-project door **Bestand** > Nieuw**project****te** > selecteren . Het project bevindt zich onder het**U-SQL-knooppunt Azure** **Data Lake.** > 

   ![Data Lake-hulpprogramma's voor Visual Studio - U-SQL-databaseproject maken](media/data-lake-analytics-cicd-manage-assemblies/create-u-sql-database-project.png)
1. Voeg een verwijzing toe naar het C#-klassebibliotheekproject voor het U-SQL-databaseproject.

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databaseprojectverwijzing toevoegen](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference.png) 

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databaseprojectverwijzing toevoegen](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-add-project-reference-wizard.png)

5. Maak een assemblagescript in het U-SQL-databaseproject door met de rechtermuisknop op het project te klikken en **Nieuw item toevoegen te**selecteren.

   ![Data Lake-hulpprogramma's voor Visual Studio: Verzamelscript toevoegen](media/data-lake-analytics-cicd-manage-assemblies/add-assembly-script.png)

1. Open het montagescript in de ontwerpweergave van de assemblage. Selecteer de verzameling waarnaar wordt verwezen in het vervolgkeuzemenu **Assemblage maken in referentie.**

    ![Data Lake Tools voor Visual Studio - maak assemblage op basis van referentie](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-create-assembly-from-reference.png)

7. Voeg **beheerde afhankelijkheden** en **extra bestanden**toe, als die er zijn. Wanneer u extra bestanden toevoegt, gebruikt het gereedschap het relatieve pad om ervoor te zorgen dat het de samenstellingen op uw lokale machine en later op de bouwmachine kan vinden.

_DeployTempDirectory in het editorvenster onderaan is een vooraf gedefinieerde variabele die het gereedschap naar de uitvoermap build wijst. ** \@** Onder de map buildoutput heeft elke assemblage een submap met de naam van de assemblage. Alle DLL's en extra bestanden bevinden zich in die submap.

## <a name="build-a-u-sql-database-project"></a>Een U-SQL-databaseproject bouwen

De buildoutput voor een U-SQL-databaseproject is een U-SQL-databaseimplementatiepakket. Het is genoemd met `.usqldbpack`het achtervoegsel. Het `.usqldbpack` pakket is een .zip-bestand dat alle DDL-instructies in één U-SQL-script in de DDL-map bevat. Alle ingebouwde .dll-bestanden en extra bestanden voor vergaderingen bevinden zich in de map Temp.

## <a name="deploy-a-u-sql-database"></a>Een U-SQL-database implementeren

Het `.usqldbpack` pakket kan worden geïmplementeerd naar een lokaal account of een Azure Data Lake Analytics-account. Gebruik Visual Studio of de implementatie-SDK. 

### <a name="deploy-a-u-sql-database-in-visual-studio"></a>Een U-SQL-database implementeren in Visual Studio

U een U-SQL-database implementeren met behulp `.usqldbpack` van een U-SQL-databaseproject of een pakket in Visual Studio.

#### <a name="deploy-by-using-a-u-sql-database-project"></a>Implementeren met behulp van een U-SQL-databaseproject

1.  Klik met de rechtermuisknop op het U-SQL-databaseproject en selecteer **Vervolgens Implementeren**.
2.  Selecteer in de wizard **U-SQL-database implementeren** het **ADLA-account** waarvoor u de database wilt implementeren. Zowel lokale accounts als ADLA-accounts worden ondersteund.
3.  **Databasebron** wordt automatisch ingevuld. Het wijst op het .usqldbpack-pakket in de build output-map van het project.
4.  Voer een naam in **Databasenaam** in om een database te maken. Als er al een database met dezelfde naam bestaat in het doelAzure Data Lake Analytics-account, worden alle objecten die zijn gedefinieerd in het databaseproject gemaakt zonder de database opnieuw te maken.
5.  Als u de U-SQL-database wilt implementeren, selecteert u **Verzenden**. Alle bronnen, zoals samenstellingen en extra bestanden, worden geüpload. Er wordt een U-SQL-taak ingediend die alle DDL-instructies bevat.

    ![Data Lake-hulpprogramma's voor Visual Studio- U-SQL-databaseproject implementeren](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project.png)

    ![Gegevenslaketools voor visual studio- Wizard U-SQL-databaseproject implementeren](./media/data-lake-analytics-cicd-manage-assemblies/data-lake-tools-deploy-usql-database-project-wizard.png)

### <a name="deploy-a-u-sql-database-in-azure-devops"></a>Een U-SQL-database implementeren in Azure DevOps

`PackageDeploymentTool.exe`biedt de programmeer- en opdrachtregelinterfaces die u-SQL-databases helpen implementeren. De SDK is opgenomen in het [U-SQL SDK Nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), gelegen op `build/runtime/PackageDeploymentTool.exe`.

In Azure DevOps u een opdrachtregeltaak en deze SDK gebruiken om een automatiseringspijplijn in te stellen voor de vernieuwing van de U-SQL-database. [Meer informatie over de SDK en het instellen van een CI/CD-pijplijn voor u-SQL-databaseimplementatie](data-lake-analytics-cicd-overview.md#deploy-u-sql-database-through-azure-pipelines).

## <a name="next-steps"></a>Volgende stappen

* [Een CI/CD-pijplijn instellen voor Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
* [Uw Azure Data Lake Analytics-code testen](data-lake-analytics-cicd-test.md)
* [U-SQL-script uitvoeren op uw lokale machine](data-lake-analytics-data-lake-tools-local-run.md)
