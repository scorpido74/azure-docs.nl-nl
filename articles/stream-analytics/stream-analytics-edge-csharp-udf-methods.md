---
title: .NET-standaard functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)
description: Meer informatie over het schrijven van door de gebruiker gedefinieerde c#-functies voor Stream Analytics taken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426321"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)

Azure Stream Analytics biedt een SQL-achtige querytaal voor het uitvoeren van transformaties en berekeningen via event gegevensstromen. Er zijn veel ingebouwde functies, maar sommige complexe scenario's vereisen meer flexibiliteit. Met .NET Standard-door gebruiker gedefinieerde functies (UDF's), kunt u uw eigen functies die zijn geschreven in elke standaard .NET-taal aanroepen (C#, F#, enzovoort) om uit te breiden de querytaal van Stream Analytics. UDF's kunnen u ingewikkelde wiskundige berekeningen uitvoeren, aangepaste ML-modellen met behulp van ML.NET importeren en gebruiken van aangepaste toerekening logica voor ontbrekende gegevens. De UDF-functie voor Stream Analytics-taken is momenteel beschikbaar als preview-versie en mag niet worden gebruikt in werk belastingen voor de productie.

Door de gebruiker gedefinieerde .NET-functie voor Cloud taken is beschikbaar in:
* US - west-centraal
* Europa - noord
* VS - oost
* US - west
* VS - oost 2
* Europa - west

Als u geïnteresseerd bent in het gebruik van deze functie in een andere regio, kunt u [toegang aanvragen](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Overzicht
Visual Studio-hulpprogramma's voor Azure Stream Analytics kunt u eenvoudig voor u om te schrijven, UDF's, uw taken lokaal testen (zelfs offline), en uw Stream Analytics-taak publiceert naar Azure. Zodra gepubliceerd naar Azure, kunt u uw taak kunt implementeren op IoT-apparaten met behulp van IoT-Hub.

Er zijn drie manieren waarop UDF's kunnen worden geïmplementeerd:

* CodeBehind-bestanden in een project ASA
* UDF vanuit een lokale project
* Een bestaand pakket van een Azure storage-account

## <a name="package-path"></a>Pad voor het pakket

De indeling van een UDF-pakket heeft het pad `/UserCustomCode/CLR/*`. Dynamic Link Libraries (DLL's) en de resources worden gekopieerd onder de `/UserCustomCode/CLR/*` map waarmee isolatie van de gebruiker dll-bestanden van het systeem en Azure Stream Analytics-dll's. Dit pad voor het pakket wordt gebruikt voor alle functies, ongeacht de methode die wordt gebruikt om te gebruiken.

## <a name="supported-types-and-mapping"></a>Ondersteunde typen en -toewijzing

|**UDF-type (C#)**  |**Azure Stream Analytics-typen**  |
|---------|---------|
|lang  |  bigint   |
|double  |  double   |
|string  |  nvarchar(max)   |
|Datum/tijd  |  Datum/tijd   |
|Struct  |  Irecords   |
|object  |  Irecords   |
|\<object Matrix >  |  IArray   |
|Dictionary < string, object >  |  Irecords   |

## <a name="codebehind"></a>CodeBehind
U kunt de gebruiker gedefinieerde functies schrijven in de **Script.asql** CodeBehind. Visual Studio-hulpprogramma's wordt automatisch met het compileren van het bestand CodeBehind in een assemblagebestand. De assembly's zijn verpakt als een zip-bestand en geüpload naar uw storage-account wanneer u de taak naar Azure verzendt. U kunt informatie over het schrijven van een C#-UDF CodeBehind door met de [UDF C# voor Stream Analytics Edge-taken](stream-analytics-edge-csharp-udf.md) zelfstudie. 

## <a name="local-project"></a>Lokale-project
De gebruiker gedefinieerde functies kunnen worden geschreven in een assembly die later wordt verwezen in een Azure Stream Analytics-query. Dit is de aanbevolen optie voor complexe functies waarvoor de volledige kracht van een taal voor .NET Standard buiten de expressietaal, zoals procedurele logica of recursie. UDF's in een lokale-project kunnen ook worden gebruikt wanneer u moet de functie logische delen tussen verschillende Azure Stream Analytics-query's. UDF's toe te voegen aan uw lokale project biedt u de mogelijkheid om te debuggen en testen van uw functies lokaal vanuit Visual Studio.

Om te verwijzen naar een lokale project:

1. Maak een nieuwe klassebibliotheek in uw oplossing.
2. De code schrijven in uw klasse. Houd er rekening mee dat de klassen moeten worden gedefinieerd als *openbare* en objecten moeten worden gedefinieerd als *statische openbare*. 
3. Bouw uw project. De hulpprogramma's wordt verpakt de artefacten in de bin-map naar een zip-bestand en upload het zip-bestand naar het storage-account. Gebruik voor externe verwijzingen Assemblyverwijzing in plaats van het NuGet-pakket.
4. Verwijzen naar de nieuwe klasse in uw Azure Stream Analytics-project.
5. Voeg een nieuwe functie in uw Azure Stream Analytics-project.
6. Configureren van het assemblypad in het configuratiebestand van de taak `JobConfig.json`. Stel in de Assembly-pad op **lokale projectverwijzing of CodeBehind**.
7. Zowel de function-project en de Azure Stream Analytics-project opnieuw.  

### <a name="example"></a>Voorbeeld

In dit voor beeld is **UDFTest** een C# klassen bibliotheek project en **ASAUDFDemo** het Azure stream Analytics project, dat verwijst naar **UDFTest**.

![Azure Stream Analytics IoT Edge-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Bouw uw C#-project, zodat u een verwijzing naar de C#-UDF uit de Azure Stream Analytics-query toevoegen.
    
   ![Bouw een Azure Stream Analytics IoT Edge-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Voeg de verwijzing naar het C# project in het ASA-project toe. Met de rechtermuisknop op het knooppunt verwijzingen in en kies verwijzing toevoegen.

   ![Voeg een verwijzing naar een C#-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Kies de naam van de C#-project in de lijst. 
    
   ![De naam van uw C#-project kiezen uit de lijst](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. U ziet de **UDFTest** vermeld onder **verwijzingen** in **Solution Explorer**.

   ![Weergave van de gebruiker gedefinieerd verwijzing naar een functie in de solution explorer](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klik met de rechtermuisknop op de **functies** map en kies **Nieuw Item**.

   ![Nieuw item toevoegen aan functies in Azure Stream Analytics Edge-oplossing](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Een C#-functie toevoegen **SquareFunction.json** aan uw Azure Stream Analytics-project.

   ![Selecteer CSharp-functie van Stream Analytics Edge-items in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbelklik op de functie in **Solution Explorer** om het configuratiedialoogvenster te openen.

   ![Configuratie van de C-sharp-functie in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Kies in de C#-functie-configuratie, **laden vanaf de ASA-projectverwijzing** en de bijbehorende assembly en klasse methode namen in de vervolgkeuzelijst. Om te verwijzen naar de methoden, typen en functies in de Stream Analytics query, moeten de klassen als *openbaar* worden gedefinieerd en moeten de objecten als *statisch openbaar*worden gedefinieerd.

   ![Stream Analytics-C sharp functie configuratie](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Bestaande pakketten

U kunt .NET Standard UDF's in een IDE van uw keuze maken en deze aanroepen vanuit uw Azure Stream Analytics-query. Compileert u eerst uw code en het pakket met de dll-bestanden. De indeling van het pakket is het pad `/UserCustomCode/CLR/*`. Vervolgens kunt u uploaden `UserCustomCode.zip` naar de hoofdmap van de container in uw Azure storage-account.

Zodra de assembly zip-pakketten zijn geüpload naar uw Azure storage-account, kunt u de functies in Azure Stream Analytics-query's. U hoeft alleen de opslag gegevens in de configuratie van de Stream Analytics-taak op te slaan. U kunt de functie lokaal met deze optie niet testen omdat Visual Studio-hulpprogramma's wordt het pakket niet downloaden. Het pad voor het pakket wordt rechtstreeks naar de service geparseerd. 

Het configureren van het assemblypad in het configuratiebestand van de taak `JobConfig.json`:

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

* .NET standard UDF's kunnen alleen worden geschreven in Visual Studio en gepubliceerd naar Azure. Alleen-lezen versie van .NET Standard UDF's kunnen worden weergegeven onder **functies** in Azure portal. Schrijven van .NET Standard-functies wordt niet ondersteund in Azure portal.

* De Azure portal query-editor bevat een fout opgetreden bij het gebruik van .NET Standard UDF in de portal. 

* Omdat de aangepaste code context met Azure Stream Analytics-engine deelt, kan niet iets dat een conflicterende naamruimte/dll-naam met Azure Stream Analytics-code heeft service verwijzen naar aangepaste code. Bijvoorbeeld, u kan niet verwijzen naar *Newtonsoft Json*.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een C# door de gebruiker gedefinieerde functie schrijven voor een Azure stream Analytics-taak (preview-versie)](stream-analytics-edge-csharp-udf.md)
* [Zelfstudie: Azure Stream Analytics gebruiker gedefinieerde JavaScript-functies](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
