---
title: .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken (Voorbeeld)
description: Meer informatie over het schrijven van c# door de gebruiker gedefinieerde functies voor Stream Analytics-taken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426321"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>.NET Standard door de gebruiker gedefinieerde functies voor Azure Stream Analytics-taken ontwikkelen (Voorbeeld)

Azure Stream Analytics biedt een SQL-achtige querytaal voor het uitvoeren van transformaties en berekeningen via gegevensstromen. Er zijn veel ingebouwde functies, maar sommige complexe scenario's vereisen extra flexibiliteit. Met .NET Standard door de gebruiker gedefinieerde functies (UDF) u uw eigen functies aanroepen die in elke .NET-standaardtaal (C#, F#, enz.) zijn geschreven om de querytaal van Stream Analytics uit te breiden. Met UDF's u complexe wiskundige berekeningen uitvoeren, aangepaste ML-modellen importeren met ML.NET en aangepaste toerekeningslogica gebruiken voor ontbrekende gegevens. De UDF-functie voor Stream Analytics-taken is momenteel in preview en mag niet worden gebruikt in productieworkloads.

.NET-gebruikersgedefinieerde functie voor cloudtaken is beschikbaar in:
* VS - west-centraal
* Europa - noord
* VS - oost
* VS - west
* VS - oost 2
* Europa -west

Als u geïnteresseerd bent in het gebruik van deze functie in een andere regio, u [toegang aanvragen.](https://aka.ms/ccodereqregion)

## <a name="overview"></a>Overzicht
Met de hulpprogramma's voor Visual Studio voor Azure Stream Analytics u eenvoudig UDF's schrijven, uw taken lokaal (zelfs offline) testen en uw Stream Analytics-taak naar Azure publiceren. Nadat u in Azure is gepubliceerd, u uw taak implementeren op IoT-apparaten met IoT Hub.

Er zijn drie manieren waarop UDF's kunnen worden geïmplementeerd:

* CodeBehind-bestanden in een ASA-project
* UDF van een lokaal project
* Een bestaand pakket van een Azure-opslagaccount

## <a name="package-path"></a>Pakketpad

Het formaat van een UDF-pakket heeft het pad. `/UserCustomCode/CLR/*` Dynamic Link Libraries (DLL's) en `/UserCustomCode/CLR/*` resources worden gekopieerd onder de map, waarmee gebruikersdllen van systeem- en Azure Stream Analytics-dllen kunnen worden geïsoleerd. Dit pakketpad wordt gebruikt voor alle functies, ongeacht de methode die wordt gebruikt om ze in dienst te nemen.

## <a name="supported-types-and-mapping"></a>Ondersteunde typen en toewijzing

|**UDF-type (C#)**  |**Azure Stream Analytics-type**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|tekenreeks  |  nvarchar(max.   |
|Datetime  |  Datetime   |
|Struct  |  IRecord   |
|object  |  IRecord   |
|>\<  |  IArray (IArray)   |
|woordenboek<tekenreeks, object>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind CodeBehind
U door de gebruiker gedefinieerde functies schrijven in de **Script.asql** CodeBehind. Visual Studio-tools compileren het CodeBehind-bestand automatisch in een assemblagebestand. De samenstellingen worden verpakt als een zip-bestand en geüpload naar uw opslagaccount wanneer u uw taak indient bij Azure. U leren hoe u een C# UDF met CodeBehind schrijven door de zelfstudie [C# UDF voor Stream Analytics Edge-taken te](stream-analytics-edge-csharp-udf.md) volgen. 

## <a name="local-project"></a>Lokaal project
Door de gebruiker gedefinieerde functies kunnen worden geschreven in een verzameling waarnaar later wordt verwezen in een Azure Stream Analytics-query. Dit is de aanbevolen optie voor complexe functies die de volledige kracht van een .NET-standaardtaal vereisen die verder gaat dan de expressietaal, zoals procedurele logica of recursie. UDF's uit een lokaal project kunnen ook worden gebruikt wanneer u de functielogica moet delen in verschillende Azure Stream Analytics-query's. Als u UDF's toevoegt aan uw lokale project, u uw functies lokaal debuggen en testen vanuit Visual Studio.

Ga als lid van het onderzoek naar een lokaal project:

1. Maak een nieuwe klassenbibliotheek in uw oplossing.
2. Schrijf de code in je klas. Houd er rekening mee dat de klassen moeten worden gedefinieerd als *openbaar* en objecten moeten worden gedefinieerd als *statisch openbaar*. 
3. Compileer het nieuwe project. De hulpprogramma's verpakken alle artefacten in de opslagmap naar een zip-bestand en uploaden het zip-bestand naar het opslagaccount. Gebruik voor externe referenties assemblagereferentie in plaats van het NuGet-pakket.
4. Verwijs naar de nieuwe klasse in uw Azure Stream Analytics-project.
5. Voeg een nieuwe functie toe aan uw Azure Stream Analytics-project.
6. Configureer het assemblagepad in `JobConfig.json`het taakconfiguratiebestand. Stel het assemblagepad in op **lokale projectverwijzing of CodeBehind**.
7. Herbouw zowel het functieproject als het Azure Stream Analytics-project.  

### <a name="example"></a>Voorbeeld

In dit voorbeeld is **UDFTest** een C#-klassebibliotheekproject en **ASAUDFDemo** is het Azure Stream Analytics-project, dat verwijst naar **UDFTest.**

![Azure Stream Analytics IoT Edge-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Bouw uw C#-project, waarmee u een verwijzing naar uw C# UDF toevoegen vanuit de Azure Stream Analytics-query.
    
   ![Een Azure Stream Analytics IoT Edge-project bouwen in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Voeg de verwijzing naar het C#-project toe in het ASA-project. Klik met de rechtermuisknop op het knooppunt Verwijzingen en kies Naslagwaarde.

   ![Een verwijzing toevoegen aan een C#-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Kies de naam C#-project in de lijst. 
    
   ![Uw C#-projectnaam kiezen in de referentielijst](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. U ziet de **UDFTest** die wordt weergegeven onder **Verwijzingen** in **Solution Explorer.**

   ![De door de gebruiker gedefinieerde functieverwijzing weergeven in solution explorer](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klik met de rechtermuisknop op de map **Functies** en kies **Nieuw item**.

   ![Nieuw item toevoegen aan functies in Azure Stream Analytics Edge-oplossing](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Voeg een C#-functie **SquareFunction.json** toe aan uw Azure Stream Analytics-project.

   ![CSharp-functie selecteren uit Stream Analytics Edge-items in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbelklik op de functie in **Solution Explorer** om het configuratiedialoogvenster te openen.

   ![C-configuratie van scherpe functies in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Kies in de functieconfiguratie C# **laden in ASA Project Reference** en de bijbehorende verzamel-, klasse- en methodenamen uit de vervolgkeuzelijst. Als u wilt verwijzen naar de methoden, typen en functies in de query Stream Analytics, moeten de klassen worden gedefinieerd als *openbaar* en moeten de objecten worden gedefinieerd als *statisch openbaar*.

   ![Geavanceerde functieconfiguratie van Stream Analytics C](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Bestaande pakketten

U .NET Standard UDF's in elke IDE naar keuze schrijven en deze aanroepen vanuit uw Azure Stream Analytics-query. Verzamel eerst uw code en pakket alle DLL's. Het formaat van het `/UserCustomCode/CLR/*`pakket heeft het pad. Upload vervolgens `UserCustomCode.zip` naar de hoofdmap van de container in uw Azure-opslagaccount.

Zodra zip-pakketten voor assemblage zijn geüpload naar uw Azure-opslagaccount, u de functies gebruiken in Azure Stream Analytics-query's. Het enige wat u hoeft te doen, is de opslaggegevens opnemen in de taakconfiguratie van Stream Analytics. U de functie niet lokaal testen met deze optie, omdat de hulpprogramma's van Visual Studio uw pakket niet downloaden. Het pakketpad wordt rechtstreeks aan de service ontleed. 

Ga als volgt te werk om `JobConfig.json`het assemblagepad in het taakconfiguratiebestand te configureren:

Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

   |**Instelling**|**Voorgestelde waarde**|
   |-------|---------------|
   |Bron voor globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement op globale opslaginstellingen| < uw abonnement >|
   |Opslagaccount voor globale opslaginstellingen| < uw opslagaccount >|
   |Bron van aangepaste codeopslaginstellingen|Kies gegevensbron van het huidige account|
   |Opslagaccount voor aangepaste code-opslaginstellingen|< uw opslagaccount >|
   |Container met aangepaste codeopslaginstellingen|< uw opslagcontainer >|
   |Aangepaste codeassemblagebron|Bestaande assemblagepakketten uit de cloud|
   |Aangepaste codeassemblagebron|UserCustomCode.zip|

## <a name="limitations"></a>Beperkingen
De UDF-preview heeft momenteel de volgende beperkingen:

* .NET Standard UDF's kunnen alleen worden geschreven in Visual Studio en worden gepubliceerd in Azure. Alleen-lezen versies van .NET Standard UDF's kunnen worden bekeken onder **Functies** in de Azure-portal. Het ontwerpen van .NET Standard-functies wordt niet ondersteund in de Azure-portal.

* De Azure-portalqueryeditor geeft een fout weer bij het gebruik van .NET Standard UDF in de portal. 

* Omdat de aangepaste code context deelt met Azure Stream Analytics-engine, kan aangepaste code niet verwijzen naar iets dat een conflicterende naamruimte/dll_name heeft met Azure Stream Analytics-code. U bijvoorbeeld niet verwijzen naar *Newtonsoft Json*.

## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Een door de gebruiker gedefinieerde functie schrijven voor een Azure Stream Analytics-taak (Voorbeeld)](stream-analytics-edge-csharp-udf.md)
* [Zelfstudie: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
