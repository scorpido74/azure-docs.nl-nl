---
title: .NET-standaard functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)
description: Meer informatie over het schrijven van door de gebruiker gedefinieerde c#-functies voor Stream Analytics taken.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: 4f9d117ccc763744411bfe24163ed955532e8e56
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921859"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Door de gebruiker gedefinieerde .NET Standard-functies ontwikkelen voor Azure Stream Analytics-taken (preview-versie)

Azure Stream Analytics biedt een SQL-achtige query taal voor het uitvoeren van trans formaties en berekeningen op basis van gegevens stromen van gebeurtenissen. Er zijn veel ingebouwde functies, maar voor sommige complexe scenario's is extra flexibiliteit vereist. Met .NET Standard door de gebruiker gedefinieerde functies (UDF) kunt u uw eigen functies aanroepen die zijn geschreven in een .NET Standard-taal (C#, F #, etc.) om de Stream Analytics query taal uit te breiden. Met UDFs kunt u complexe wiskundige berekeningen uitvoeren, aangepaste ML-modellen importeren met behulp van ML.NET en aangepaste toerekenings logica voor ontbrekende gegevens gebruiken. De UDF-functie voor Stream Analytics-taken is momenteel in de previewfase en mag niet worden gebruikt voor productieworkloads.

Door de gebruiker gedefinieerde .NET-functie voor Cloud taken is beschikbaar in:
* VS - west-centraal
* Europa - noord
* VS - oost
* VS - west
* VS - oost 2
* Europa -west

Als u geïnteresseerd bent in het gebruik van deze functie in een andere regio, kunt u [toegang aanvragen](https://aka.ms/ccodereqregion).

## <a name="overview"></a>Overzicht
Met de Visual Studio-hulpprogram ma's voor Azure Stream Analytics kunt u gemakkelijker Udf's schrijven, uw taken lokaal testen (zelfs offline) en uw Stream Analytics-taak publiceren naar Azure. Na publicatie naar Azure kunt u uw taak implementeren op IoT-apparaten met behulp van IoT Hub.

Er zijn drie manieren waarop UDF's kunnen worden geïmplementeerd:

* CodeBehind-bestanden in een ASA-project
* UDF van een lokaal project
* Een bestaand pakket van een Azure Storage-account

## <a name="package-path"></a>Pad naar pakket

De indeling van een UDF-pakket heeft het pad `/UserCustomCode/CLR/*` . Dynamic Link Libraries (Dll's) en bronnen worden gekopieerd in de `/UserCustomCode/CLR/*` map, waarmee gebruikers-dll's van systeem-en Azure stream Analytics-dll's kunnen worden geïsoleerd. Het pad naar het pakket wordt gebruikt voor alle functies, ongeacht de methode die wordt gebruikt om deze te gebruiken.

## <a name="supported-types-and-mapping"></a>Ondersteunde typen en toewijzing
Voor Azure Stream Analytics waarden die in C# moeten worden gebruikt, moeten ze worden gemarshald van de ene omgeving naar de andere. Marshaling gebeurt voor alle invoer parameters van een UDF. Elk type Azure Stream Analytics heeft een overeenkomend type in C# dat wordt weer gegeven in de onderstaande tabel:

|**Azure Stream Analytics type** |**C#-type** |
|---------|---------|
|bigint | long |
|float | double |
|nvarchar (max) | tekenreeks |
|datum/tijd | DateTime |
|Record | Woordenlijst\<string, object> |
|Matrix | Object [] |

Hetzelfde geldt wanneer gegevens moeten worden gemarshald van C# naar Azure Stream Analytics, wat gebeurt bij de uitvoer waarde van een UDF. In de volgende tabel ziet u welke typen worden ondersteund:

|**C#-type**  |**Azure Stream Analytics type**  |
|---------|---------|
|long  |  bigint   |
|double  |  float   |
|tekenreeks  |  nvarchar (max)   |
|DateTime  |  dateTime   |
|bouw  |  Record   |
|object  |  Record   |
|Object []  |  Matrix   |
|Woordenlijst\<string, object>  |  Record   |

## <a name="codebehind"></a>CodeBehind
U kunt door de gebruiker gedefinieerde functies schrijven in het **script. asql** CodeBehind. Visual Studio Tools compileert het CodeBehind-bestand automatisch in een assembly-bestand. De assembly's worden verpakt als een zip-bestand en geüpload naar uw opslag account wanneer u uw taak naar Azure verzendt. U kunt meer informatie over het schrijven van een C# UDF met behulp van CodeBehind door de zelf studie [C# UDF voor stream Analytics Edge-taken](stream-analytics-edge-csharp-udf.md) te volgen. 

## <a name="local-project"></a>Lokaal project
Door de gebruiker gedefinieerde functies kunnen worden geschreven in een assembly die later wordt verwezen in een Azure Stream Analytics-query. Dit is de aanbevolen optie voor complexe functies waarvoor de volledige kracht van een .NET Standard-taal is vereist dan de expressie taal, zoals procedurele logica of recursie. Udf's van een lokaal project kan ook worden gebruikt wanneer u de functie logica tussen verschillende Azure Stream Analytics query's moet delen. Door Udf's toe te voegen aan uw lokale project krijgt u de mogelijkheid om uw functies lokaal vanuit Visual Studio te debuggen en te testen.

Verwijzen naar een lokaal project:

1. Maak een nieuwe klassen bibliotheek in uw oplossing.
2. Schrijf de code in uw klasse. Houd er rekening mee dat de klassen als *openbaar* moeten worden gedefinieerd en dat objecten als *statisch openbaar*moeten worden gedefinieerd. 
3. Compileer het nieuwe project. Met de hulpprogram ma's worden alle artefacten in de map bin verpakt naar een zip-bestand en wordt het zip-bestand geüpload naar het opslag account. Voor externe verwijzingen gebruikt u assembly-verwijzing in plaats van het NuGet-pakket.
4. Verwijs naar de nieuwe klasse in uw Azure Stream Analytics-project.
5. Voeg een nieuwe functie toe aan uw Azure Stream Analytics-project.
6. Configureer het pad van de assembly in het taak configuratie bestand, `JobConfig.json` . Stel het pad van de assembly in op **lokale project verwijzing of CodeBehind**.
7. Bouw zowel het functie project als het Azure Stream Analytics project opnieuw op.  

### <a name="example"></a>Voorbeeld

In dit voor beeld is **UDFTest** een C#-klassen bibliotheek project en **ASAUDFDemo** het Azure stream Analytics project, dat verwijst naar **UDFTest**.

![Azure Stream Analytics IoT Edge-project in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Bouw uw C#-project, waarmee u een verwijzing naar uw C# UDF kunt toevoegen vanuit de Azure Stream Analytics-query.
    
   ![Een Azure Stream Analytics IoT Edge-project maken in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Voeg de verwijzing naar het C#-project in het ASA-project toe. Klik met de rechter muisknop op het knoop punt verwijzingen en kies verwijzing toevoegen.

   ![Een verwijzing naar een C#-project in Visual Studio toevoegen](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Kies de naam van het C#-project in de lijst. 
    
   ![Kies de naam van uw C#-project in de lijst met verwijzingen](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. U ziet de **UDFTest** vermeld onder **verwijzingen** in **Solution Explorer**.

   ![De verwijzing naar de door de gebruiker gedefinieerde functie in Solution Explorer weer geven](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Klik met de rechter muisknop op de map **functions** en kies **Nieuw item**.

   ![Nieuw item toevoegen aan functies in Azure Stream Analytics EDGE-oplossing](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Voeg een C#-functie toe **SquareFunction.js** aan uw Azure stream Analytics-project.

   ![Selecteer de functie CSharp van Stream Analytics Edge-items in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Dubbel klik op de functie in **Solution Explorer** om het dialoog venster configuratie te openen.

   ![C-scherpe functie configuratie in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. In de C#-functie configuratie kiest u **laden van ASA-project verwijzing** en de bijbehorende assembly-, klasse-en methode namen van de vervolg keuzelijst. Om te verwijzen naar de methoden, typen en functies in de Stream Analytics query, moeten de klassen als *openbaar* worden gedefinieerd en moeten de objecten als *statisch openbaar*worden gedefinieerd.

   ![Stream Analytics C sharp-functie configuratie](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Bestaande pakketten

U kunt in elke wille keurige IDE uw keuze maken over .NET-standaard-UDFs en deze oproepen vanuit uw Azure Stream Analytics-query. Compileer eerst uw code en verpak alle Dll's. De indeling van het pakket heeft het pad `/UserCustomCode/CLR/*` . Upload vervolgens `UserCustomCode.zip` naar de hoofdmap van de container in uw Azure Storage-account.

Zodra de ZIP-pakketten van de assembly naar uw Azure Storage-account zijn geüpload, kunt u de functies in Azure Stream Analytics query's gebruiken. U hoeft alleen de opslag gegevens in de configuratie van de Stream Analytics-taak op te slaan. U kunt de functie niet lokaal met deze optie testen omdat Visual Studio Tools uw pakket niet kan downloaden. Het pad naar het pakket wordt rechtstreeks naar de service geparseerd. 

Het pad van de assembly configureren in het taak configuratie bestand `JobConfig.json` :

Vouw de sectie **Configuratie van de door de gebruiker gedefinieerde code** uit en vul de configuratie in met de volgende voorgestelde waarden:

   |**Instelling**|**Voorgestelde waarde**|
   |-------|---------------|
   |Resource globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslaginstellingen| < uw abonnement >|
   |Globale opslaginstellingen opslagaccount| < uw opslagaccount >|
   |Resource aangepaste code opslaginstellingen|Kies gegevensbron van het huidige account|
   |Aangepaste code opslaginstellingen opslagaccount|< uw opslagaccount >|
   |Container aangepaste code opslaginstellingen|< uw opslagcontainer >|
   |Assembly bron aangepaste code|Bestaande assembly pakketten uit de Cloud|
   |Assembly bron aangepaste code|UserCustomCode.zip|

## <a name="user-logging"></a>Gebruikers logboek registratie
Met het mechanisme voor logboek registratie kunt u aangepaste gegevens vastleggen terwijl een taak wordt uitgevoerd. U kunt logboek gegevens gebruiken om fouten op te sporen of de juistheid van de aangepaste code in realtime te evalueren.

Met de- `StreamingContext` klasse kunt u Diagnostische gegevens publiceren met behulp van de `StreamingDiagnostics.WriteError` functie. De volgende code toont de interface die wordt weer gegeven door Azure Stream Analytics.

```csharp
public abstract class StreamingContext
{
    public abstract StreamingDiagnostics Diagnostics { get; }
}

public abstract class StreamingDiagnostics
{
    public abstract void WriteError(string briefMessage, string detailedMessage);
}
```

`StreamingContext`wordt door gegeven als invoer parameter voor de UDF-methode en kan worden gebruikt in de UDF voor het publiceren van aangepaste logboek gegevens. In het onderstaande voor beeld `MyUdfMethod` definieert u een **gegevens** invoer, die wordt opgegeven door de query en een **context** invoer als de `StreamingContext` , die wordt opgegeven door de runtime-engine. 

```csharp
public static long MyUdfMethod(long data, StreamingContext context)
{
    // write log
    context.Diagnostics.WriteError("User Log", "This is a log message");
    
    return data;
}
```

De `StreamingContext` waarde hoeft niet door de SQL-query worden door gegeven. Azure Stream Analytics biedt automatisch een context object als er een invoer parameter aanwezig is. Het gebruik van de `MyUdfMethod` wordt niet gewijzigd, zoals wordt weer gegeven in de volgende query:

```sql
SELECT udf.MyUdfMethod(input.value) as udfValue FROM input
```

U kunt logboek berichten openen via de [Diagnostische logboeken](data-errors.md).

## <a name="limitations"></a>Beperkingen
De UDF-preview heeft momenteel de volgende beperkingen:

* .NET Standard Udf's kan alleen worden gemaakt in Visual Studio en naar Azure worden gepubliceerd. Alleen-lezen versies van .NET Standard Udf's kunnen worden weer gegeven onder **functies** in de Azure Portal. Het ontwerpen van .NET-standaard functies wordt niet ondersteund in de Azure Portal.

* De Azure Portal query-editor toont een fout bij het gebruik van .NET Standard UDF in de portal. 

* Omdat de aangepaste code aandelen context met Azure Stream Analytics-engine, kan aangepaste code niet verwijzen naar iets met een conflicterende naam ruimte/dll_name met Azure Stream Analytics code. U kunt bijvoorbeeld niet verwijzen naar *Newton Soft JSON*.

* Ondersteunende bestanden die in het project zijn opgenomen, worden gekopieerd naar het door de gebruiker aangepaste code zip-bestand dat wordt gebruikt wanneer u de taak naar de Cloud publiceert. Alle bestanden in submappen worden rechtstreeks gekopieerd naar de hoofdmap van de map met aangepaste gebruikers code in de Cloud wanneer deze is uitgepakt. De zip wordt ' afgevlakt ' bij het decomprimeren.

* Aangepaste code van gebruiker biedt geen ondersteuning voor lege mappen. Voeg geen lege mappen toe aan de ondersteunende bestanden in het project.

## <a name="next-steps"></a>Volgende stappen

* [Zelf studie: een door de gebruiker gedefinieerde C#-functie voor een Azure Stream Analytics-taak schrijven (preview)](stream-analytics-edge-csharp-udf.md)
* [Zelfstudie: Door gebruiker gedefinieerde JavaScript-functies in Azure Stream Analytics](stream-analytics-javascript-user-defined-functions.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)
