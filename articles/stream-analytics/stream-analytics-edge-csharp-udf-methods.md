---
title: .NET-standaard functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)
description: Meer informatie over het schrijven van door de gebruiker gedefinieerde c#-functies voor Stream Analytics taken.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 835eeaa0036d824a65918102ab7a00c3cf236931
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72988395"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)

Azure Stream Analytics biedt een SQL-achtige query taal voor het uitvoeren van trans formaties en berekeningen op basis van gegevens stromen van gebeurtenissen. Er zijn veel ingebouwde functies, maar voor sommige complexe scenario's is extra flexibiliteit vereist. Met .NET Standard door de gebruiker gedefinieerde functies (UDF) kunt u uw eigen functies aanroepen die zijn geschreven in een .netC#Standard F#-taal (, enzovoort) om de stream Analytics query taal uit te breiden. Met UDFs kunt u complexe wiskundige berekeningen uitvoeren, aangepaste ML-modellen importeren met behulp van ML.NET en aangepaste toerekenings logica voor ontbrekende gegevens gebruiken. De UDF-functie voor Stream Analytics-taken is momenteel beschikbaar als preview-versie en mag niet worden gebruikt in werk belastingen voor de productie.

## <a name="overview"></a>Overzicht
Met de Visual Studio-hulpprogram ma's voor Azure Stream Analytics kunt u gemakkelijker Udf's schrijven, uw taken lokaal testen (zelfs offline) en uw Stream Analytics-taak publiceren naar Azure. Na publicatie naar Azure kunt u uw taak implementeren op IoT-apparaten met behulp van IoT Hub.

Er zijn drie manieren waarop UDF's kunnen worden geïmplementeerd:

* CodeBehind-bestanden in een ASA-project
* UDF van een lokaal project
* Een bestaand pakket van een Azure Storage-account

## <a name="package-path"></a>Pad naar pakket

De indeling van een UDF-pakket heeft het pad `/UserCustomCode/CLR/*`. Dynamic Link Libraries (Dll's) en bronnen worden gekopieerd onder de map `/UserCustomCode/CLR/*`, waarmee gebruikers-Dll's van systeem-en Azure Stream Analytics-Dll's kunnen worden geïsoleerd. Het pad naar het pakket wordt gebruikt voor alle functies, ongeacht de methode die wordt gebruikt om deze te gebruiken.

## <a name="supported-types-and-mapping"></a>Ondersteunde typen en toewijzing

|**UDF-typeC#()**  |**Azure Stream Analytics type**  |
|---------|---------|
|omvang  |  bigint   |
|double  |  double   |
|string  |  nvarchar (max)   |
|DateTime  |  DateTime   |
|bouw  |  IRecord   |
|object  |  IRecord   |
|\<object Matrix >  |  IArray   |
|dictionary < teken reeks, object >  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
U kunt door de gebruiker gedefinieerde functies schrijven in het **script. asql** CodeBehind. Visual Studio Tools compileert het CodeBehind-bestand automatisch in een assembly-bestand. De assembly's worden verpakt als een zip-bestand en geüpload naar uw opslag account wanneer u uw taak naar Azure verzendt. U kunt meer informatie over het schrijven C# van een UDF met behulp van CodeBehind door de [ C# UDF voor de stream Analytics Edge-taken](stream-analytics-edge-csharp-udf.md) zelf studie te volgen. 

## <a name="local-project"></a>Lokaal project
Door de gebruiker gedefinieerde functies kunnen worden geschreven in een assembly die later wordt verwezen in een Azure Stream Analytics-query. Dit is de aanbevolen optie voor complexe functies waarvoor de volledige kracht van een .NET Standard-taal is vereist dan de expressie taal, zoals procedurele logica of recursie. Udf's van een lokaal project kan ook worden gebruikt wanneer u de functie logica tussen verschillende Azure Stream Analytics query's moet delen. Door Udf's toe te voegen aan uw lokale project krijgt u de mogelijkheid om uw functies lokaal vanuit Visual Studio te debuggen en te testen.

Verwijzen naar een lokaal project:

1. Maak een nieuwe klassen bibliotheek in uw oplossing.
2. Schrijf de code in uw klasse. Houd er rekening mee dat de klassen als *openbaar* moeten worden gedefinieerd en dat objecten als *statisch openbaar*moeten worden gedefinieerd. 
3. Bouw uw project. Met de hulpprogram ma's worden alle artefacten in de map bin verpakt naar een zip-bestand en wordt het zip-bestand geüpload naar het opslag account. Voor externe verwijzingen gebruikt u assembly-verwijzing in plaats van het NuGet-pakket.
4. Verwijs naar de nieuwe klasse in uw Azure Stream Analytics-project.
5. Voeg een nieuwe functie toe aan uw Azure Stream Analytics-project.
6. Configureer het pad naar de assembly in het configuratie bestand van de taak `JobConfig.json`. Stel het pad van de assembly in op **lokale project verwijzing of CodeBehind**.
7. Bouw zowel het functie project als het Azure Stream Analytics project opnieuw op.  

### <a name="example"></a>Voorbeeld

In dit voor beeld is **UDFTest** een C# klassen bibliotheek project en **ASAUDFDemo** het Azure stream Analytics project, dat verwijst naar **UDFTest**.

![Azure Stream Analytics IoT Edge-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Bouw uw C# project, waarmee u een verwijzing naar de C# UDF kunt toevoegen vanuit de Azure stream Analytics-query.
    
   ![Een Azure Stream Analytics IoT Edge-project maken in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Voeg de verwijzing naar het C# project in het ASA-project toe. Klik met de rechter muisknop op het knoop punt verwijzingen en kies verwijzing toevoegen.

   ![Een verwijzing naar een C# project in Visual Studio toevoegen](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Kies de C# project naam in de lijst. 
    
   ![Kies de C# naam van uw project in de lijst met verwijzingen](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. U ziet de **UDFTest** vermeld onder **verwijzingen** in **Solution Explorer**.

   ![De verwijzing naar de door de gebruiker gedefinieerde functie in Solution Explorer weer geven](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klik met de rechter muisknop op de map **functions** en kies **Nieuw item**.

   ![Nieuw item toevoegen aan functies in Azure Stream Analytics EDGE-oplossing](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Voeg een C# functie **SquareFunction. json** toe aan uw Azure stream Analytics-project.

   ![Selecteer de functie CSharp van Stream Analytics Edge-items in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbel klik op de functie in **Solution Explorer** om het dialoog venster configuratie te openen.

   ![C-scherpe functie configuratie in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Kies in C# de functie configuratie **laden van ASA-project verwijzing** en de bijbehorende assembly-, klasse-en methode namen in de vervolg keuzelijst. Om te verwijzen naar de methoden, typen en functies in de Stream Analytics query, moeten de klassen als *openbaar* worden gedefinieerd en moeten de objecten als *statisch openbaar*worden gedefinieerd.

   ![Stream Analytics C sharp-functie configuratie](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Bestaande pakketten

U kunt in elke wille keurige IDE uw keuze maken over .NET-standaard-UDFs en deze oproepen vanuit uw Azure Stream Analytics-query. Compileer eerst uw code en verpak alle Dll's. De indeling van het pakket heeft het pad `/UserCustomCode/CLR/*`. Upload `UserCustomCode.zip` vervolgens naar de hoofdmap van de container in uw Azure Storage-account.

Zodra de ZIP-pakketten van de assembly naar uw Azure Storage-account zijn geüpload, kunt u de functies in Azure Stream Analytics query's gebruiken. U hoeft alleen de opslag gegevens in de configuratie van de Stream Analytics-taak op te slaan. U kunt de functie niet lokaal met deze optie testen omdat Visual Studio Tools uw pakket niet kan downloaden. Het pad naar het pakket wordt rechtstreeks naar de service geparseerd. 

Als u het pad van de assembly wilt configureren in het configuratie bestand van de taak, `JobConfig.json`:

Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

   |**Instelling**|**Voorgestelde waarde**|
   |-------|---------------|
   |Resource voor algemene opslag instellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslag instellingen| < uw abonnement >|
   |Opslag account voor algemene opslag instellingen| < uw opslag account >|
   |Resource voor aangepaste code opslag instellingen|Kies gegevensbron van het huidige account|
   |Opslag account voor aangepaste code opslag|< uw opslag account >|
   |Container voor aangepaste code opslag|< uw opslag container >|
   |Assembly bron aangepaste code|Bestaande assembly pakketten uit de Cloud|
   |Assembly bron aangepaste code|UserCustomCode. zip|

## <a name="limitations"></a>Beperkingen
De UDF-preview heeft momenteel de volgende beperkingen:

* .NET Standard Udf's kan alleen worden gemaakt in Visual Studio en naar Azure worden gepubliceerd. Alleen-lezen versies van .NET Standard Udf's kunnen worden weer gegeven onder **functies** in de Azure Portal. Het ontwerpen van .NET-standaard functies wordt niet ondersteund in de Azure Portal.

* De Azure Portal query-editor toont een fout bij het gebruik van .NET Standard UDF in de portal. 

* Omdat de aangepaste code aandelen context met Azure Stream Analytics-engine, kan aangepaste code niet verwijzen naar iets met een conflicterende naam ruimte-dll_name met Azure Stream Analytics-code. U kunt bijvoorbeeld niet verwijzen naar *Newton Soft JSON*.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een C# door de gebruiker gedefinieerde functie schrijven voor een Azure stream Analytics-taak (preview-versie)](stream-analytics-edge-csharp-udf.md)
* [Zelf studie: door gebruiker gedefinieerde Java script-functies Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
