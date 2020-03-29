---
title: Gids voor U-SQL-programmabiliteit voor Azure Data Lake
description: Meer informatie over de set services in Azure Data Lake Analytics waarmee u een cloudgebaseerd big data-platform maken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162575"
---
# <a name="u-sql-programmability-guide"></a>Gids voor U-SQL-programmabiliteit

U-SQL is een querytaal die is ontworpen voor big data-type workloads. Een van de unieke kenmerken van U-SQL is de combinatie van de SQL-achtige declaratieve taal met de uitbreidbaarheid en programmeerbaarheid die wordt geleverd door C#. In deze handleiding concentreren we ons op de uitbreidbaarheid en programmeerbaarheid van de U-SQL-taal die is ingeschakeld door C#.

## <a name="requirements"></a>Vereisten

Azure [Data Lake Tools voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)downloaden en installeren.

## <a name="get-started-with-u-sql"></a>Aan de slag met U-SQL  

Bekijk het volgende U-SQL-script:

```
@a  = 
  SELECT * FROM 
    (VALUES
       ("Contoso",   1500.0, "2017-03-39"),
       ("Woodgrove", 2700.0, "2017-04-10")
    ) AS D( customer, amount, date );

@results =
  SELECT
    customer,
    amount,
    date
  FROM @a;    
```

In dit script worden `@a` `@results`twee rijsets gedefinieerd: en . RowSet `@results` wordt gedefinieerd `@a`op .

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typen en -expressies in U-SQL-script

Een U-SQL Expression is een C#-expressie in `AND` `OR`combinatie `NOT`met U-SQL logische bewerkingen zoals , en . U-SQL-expressies kunnen worden gebruikt met SELECT, EXTRACT, WHERE, HAVING, GROUP BY en DECLARE. In het volgende script wordt bijvoorbeeld een tekenreeks ontlijnd als een DatumTijd-waarde.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

In het volgende fragment wordt een tekenreeks ontlijnd als Datumtijdwaarde in een DECLARE-instructie.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C#-expressies gebruiken voor gegevenstypeconversies

In het volgende voorbeeld wordt uitgelegd hoe u een datumtijdgegevensconversie uitvoeren met C#-expressies. In dit specifieke scenario worden tekenreeksdatumgegevens geconverteerd naar standaarddatumtijd met middernacht 00:00:00 tijdnotatie.

```
DECLARE @dt = "2016-07-06 10:23:15";

@rs1 =
  SELECT 
    Convert.ToDateTime(Convert.ToDateTime(@dt).ToString("yyyy-MM-dd")) AS dt,
    dt AS olddt
  FROM @rs0;

OUTPUT @rs1 
  TO @output_file 
  USING Outputters.Text();
```

### <a name="use-c-expressions-for-todays-date"></a>C#-expressies gebruiken voor de datum van vandaag

Als u de datum van vandaag wilt trekken, kunnen we de volgende C#-expressie gebruiken:`DateTime.Now.ToString("M/d/yyyy")`

Hier volgt een voorbeeld van het gebruik van deze expressie in een script:

```
@rs1 =
  SELECT
    MAX(guid) AS start_id,
    MIN(dt) AS start_time,
    MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
    MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
    DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
    user,
    des
  FROM @rs0
  GROUP BY user, des;
```
## <a name="using-net-assemblies"></a>.NET-samenstellingen gebruiken

Het extensibiliteitsmodel van U-SQL is sterk afhankelijk van de mogelijkheid om aangepaste code toe te voegen van .NET-assemblages. 

### <a name="register-a-net-assembly"></a>Een .NET-assemblage registreren

Gebruik `CREATE ASSEMBLY` de instructie om een .NET-verzameling in een U-SQL-database te plaatsen. Daarna kunnen U-SQL-scripts deze samenstellingen `REFERENCE ASSEMBLY` gebruiken met behulp van de instructie. 

De volgende code laat zien hoe u een vergadering registreert:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

In de volgende code ziet u hoe u naar een verzameling verwijst:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Raadpleeg de [instructies voor de registratie](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) van de assemblage die dit onderwerp in meer detail behandelen.


### <a name="use-assembly-versioning"></a>Assemblageversiemaken gebruiken
Momenteel gebruikt U-SQL de .NET Framework-versie 4.5. Zorg er dus voor dat uw eigen samenstellingen compatibel zijn met die versie van de runtime.

Zoals eerder vermeld, u-SQL voert code in een 64-bits (x64) formaat. Zorg er dus voor dat uw code is samengesteld om op x64 te draaien. Anders krijgt u de fout met de verkeerde indeling eerder weergegeven.

Elk geüploade e-dll- en resourcebestand, zoals een andere runtime, een native assembly of een config-bestand, kan maximaal 400 MB bedragen. De totale omvang van de geïmplementeerde resources, hetzij via DEPLOY RESOURCE, hetzij via verwijzingen naar assemblages en hun extra bestanden, mag niet meer dan 3 GB bedragen.

Houd er ten slotte rekening mee dat elke U-SQL-database slechts één versie van een bepaalde assemblage kan bevatten. Als u bijvoorbeeld zowel versie 7 als versie 8 van de NewtonSoft-Json.NET-bibliotheek nodig hebt, moet u ze registreren in twee verschillende databases. Bovendien kan elk script slechts verwijzen naar één versie van een bepaalde assemblage DLL. In dit opzicht volgt U-SQL de C# assemblagebeheer en versiesemantiek.

## <a name="use-user-defined-functions-udf"></a>Gebruik door de gebruiker gedefinieerde functies: UDF
U-SQL-gebruikersgedefinieerde functies, of UDF, zijn programmeerroutines die parameters accepteren, een actie uitvoeren (zoals een complexe berekening) en het resultaat van die actie als waarde retourneren. De retourwaarde van UDF kan slechts één scalar zijn. U-SQL UDF kan worden aangeroepen in U-SQL base script net als elke andere C # scalaire functie.

We raden u aan u-SQL-gebruikersgedefinieerde functies te initialiseren als **openbaar** en **statisch.**

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Laten we eerst eens kijken naar het eenvoudige voorbeeld van het maken van een UDF.

In dit gebruiksscenario moeten we de fiscale periode bepalen, inclusief het fiscale kwartaal en de fiscale maand van de eerste aanmelding voor de specifieke gebruiker. De eerste fiscale maand van het jaar in ons scenario is juni.

Als u de fiscale periode wilt berekenen, introduceren we de volgende functie C#:

```
public static string GetFiscalPeriod(DateTime dt)
{
    int FiscalMonth=0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter=0;
    if (FiscalMonth >=1 && FiscalMonth<=3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return "Q" + FiscalQuarter.ToString() + ":P" + FiscalMonth.ToString();
}
```

Het berekent gewoon fiscale maand en kwartaal en geeft een tekenreekswaarde. Voor juni, de eerste maand van het eerste fiscale kwartaal, gebruiken we "Q1:P1". Voor juli gebruiken we "Q1:P2", enzovoort.

Dit is een reguliere C#-functie die we gaan gebruiken in ons U-SQL-project.

Hier is hoe de code-behind sectie eruit ziet in dit scenario:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        public static string GetFiscalPeriod(DateTime dt)
        {
            int FiscalMonth=0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter=0;
            if (FiscalMonth >=1 && FiscalMonth<=3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return "Q" + FiscalQuarter.ToString() + ":" + FiscalMonth.ToString();
        }
    }
}
```

Nu gaan we deze functie aanroepen vanuit het basis U-SQL-script. Hiervoor moeten we een volledig gekwalificeerde naam voor de functie opgeven, inclusief de naamruimte, die in dit geval NameSpace.Class.Function(parameter) is.

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Hieronder volgt het werkelijke U-SQL-basisscript:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt DateTime,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

DECLARE @default_dt DateTime = Convert.ToDateTime("06/01/2016");

@rs1 =
    SELECT
        MAX(guid) AS start_id,
    MIN(dt) AS start_time,
        MIN(Convert.ToDateTime(Convert.ToDateTime(dt<@default_dt?@default_dt:dt).ToString("yyyy-MM-dd"))) AS start_zero_time,
        MIN(USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)) AS start_fiscalperiod,
        user,
        des
    FROM @rs0
    GROUP BY user, des;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Hieronder volgt het uitvoerbestand van de scriptuitvoering:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

Dit voorbeeld toont een eenvoudig gebruik van inline UDF in U-SQL.

### <a name="keep-state-between-udf-invocations"></a>Status tussen UDF-aanroepingen behouden
U-SQL C# programmeerbaarheidsobjecten kunnen geavanceerder zijn, waarbij gebruik wordt gemaakt van interactiviteit via de code-behind globale variabelen. Laten we eens kijken naar de volgende business use-case scenario.

In grote organisaties kunnen gebruikers schakelen tussen verschillende interne toepassingen. Deze kunnen microsoft Dynamics CRM, PowerBI, enzovoort omvatten. Klanten kunnen een telemetrie-analyse toepassen van hoe gebruikers schakelen tussen verschillende toepassingen, wat de gebruikstrends zijn, enzovoort. Het doel voor het bedrijf is om het gebruik van toepassingen te optimaliseren. Ze willen ook verschillende toepassingen of specifieke aanmeldingsroutines combineren.

Om dit doel te bereiken, moeten we sessie-ID's en vertragingstijd bepalen tussen de laatste sessie die heeft plaatsgevonden.

We moeten een eerdere aanmelding vinden en deze aanmelding vervolgens toewijzen aan alle sessies die aan dezelfde toepassing worden gegenereerd. De eerste uitdaging is dat U-SQL base script ons niet toestaat om berekeningen toe te passen over reeds berekende kolommen met LAG functie. De tweede uitdaging is dat we de specifieke sessie voor alle sessies binnen dezelfde periode moeten houden.

Om dit probleem op te lossen, gebruiken we `static public string globalSession;`een globale variabele in een code-behind sectie: .

Deze globale variabele wordt toegepast op de gehele rijset tijdens onze scriptuitvoering.

Hier is de code-behind sectie van ons U-SQL-programma:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;

namespace USQLApplication21
{
    public class UserSession
    {
        static public string globalSession;
        static public string StampUserSession(string eventTime, string PreviousRow, string Session)
        {

            if (!string.IsNullOrEmpty(PreviousRow))
            {
                double timeGap = Convert.ToDateTime(eventTime).Subtract(Convert.ToDateTime(PreviousRow)).TotalMinutes;
                if (timeGap <= 60) {return Session;}
                else {return Guid.NewGuid().ToString();}
            }
            else {return Guid.NewGuid().ToString();}

        }

        static public string getStampUserSession(string Session)
        {
            if (Session != globalSession && !string.IsNullOrEmpty(Session)) { globalSession = Session; }
            return globalSession;
        }

    }
}
```

In dit voorbeeld `static public string globalSession;` wordt de `getStampUserSession` globale variabele weergegeven die in de functie wordt gebruikt en telkens opnieuw worden geïnitialiseerd wanneer de parameter Session wordt gewijzigd.

Het U-SQL-basisscript is als volgt:

```
DECLARE @in string = @"\UserSession\test1.tsv";
DECLARE @out1 string = @"\UserSession\Out1.csv";
DECLARE @out2 string = @"\UserSession\Out2.csv";
DECLARE @out3 string = @"\UserSession\Out3.csv";

@records =
    EXTRACT DataId string,
            EventDateTime string,           
            UserName string,
            UserSessionTimestamp string

    FROM @in
    USING Extractors.Tsv();

@rs1 =
    SELECT 
        EventDateTime,
        UserName,
    LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,          
        string.IsNullOrEmpty(LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,           
        USQLApplication21.UserSession.StampUserSession
           (
            EventDateTime,
            LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC),
            LAG(UserSessionTimestamp, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)
           ) AS UserSessionTimestamp
    FROM @records;

@rs2 =
    SELECT 
        EventDateTime,
        UserName,
        LAG(EventDateTime, 1) 
        OVER(PARTITION BY UserName ORDER BY EventDateTime ASC) AS prevDateTime,
        string.IsNullOrEmpty( LAG(EventDateTime, 1) OVER(PARTITION BY UserName ORDER BY EventDateTime ASC)) AS Flag,
        USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp) AS UserSessionTimestamp
    FROM @rs1
    WHERE UserName != "UserName";

OUTPUT @rs2
    TO @out2
    ORDER BY UserName, EventDateTime ASC
    USING Outputters.Csv();
```

Functie `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` wordt hier aangeroepen tijdens de tweede berekening van de geheugenrijset. Het passeert `UserSessionTimestamp` de kolom en `UserSessionTimestamp` retourneert de waarde totdat deze is gewijzigd.

Het uitvoerbestand is als volgt:

```
"2016-02-19T07:32:36.8420000-08:00","User1",,True,"72a0660e-22df-428e-b672-e0977007177f"
"2016-02-17T11:52:43.6350000-08:00","User2",,True,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-17T11:59:08.8320000-08:00","User2","2016-02-17T11:52:43.6350000-08:00",False,"4a0cd19a-6e67-4d95-a119-4eda590226ba"
"2016-02-11T07:04:17.2630000-08:00","User3",,True,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-11T07:10:33.9720000-08:00","User3","2016-02-11T07:04:17.2630000-08:00",False,"51860a7a-1610-4f74-a9ea-69d5eef7cd9c"
"2016-02-15T21:27:41.8210000-08:00","User3","2016-02-11T07:10:33.9720000-08:00",False,"4d2bc48d-bdf3-4591-a9c1-7b15ceb8e074"
"2016-02-16T05:48:49.6360000-08:00","User3","2016-02-15T21:27:41.8210000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-16T06:22:43.6390000-08:00","User3","2016-02-16T05:48:49.6360000-08:00",False,"dd3006d0-2dcd-42d0-b3a2-bc03dd77c8b9"
"2016-02-17T16:29:53.2280000-08:00","User3","2016-02-16T06:22:43.6390000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T16:39:07.2430000-08:00","User3","2016-02-17T16:29:53.2280000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-17T17:20:39.3220000-08:00","User3","2016-02-17T16:39:07.2430000-08:00",False,"2fa899c7-eecf-4b1b-a8cd-30c5357b4f3a"
"2016-02-19T05:23:54.5710000-08:00","User3","2016-02-17T17:20:39.3220000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T05:48:37.7510000-08:00","User3","2016-02-19T05:23:54.5710000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T06:40:27.4830000-08:00","User3","2016-02-19T05:48:37.7510000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T07:27:37.7550000-08:00","User3","2016-02-19T06:40:27.4830000-08:00",False,"6ca7ed80-c149-4c22-b24b-94ff5b0d824d"
"2016-02-19T19:35:40.9450000-08:00","User3","2016-02-19T07:27:37.7550000-08:00",False,"3f385f0b-3e68-4456-ac74-ff6cef093674"
"2016-02-20T00:07:37.8250000-08:00","User3","2016-02-19T19:35:40.9450000-08:00",False,"685f76d5-ca48-4c58-b77d-bd3a9ddb33da"
"2016-02-11T09:01:33.9720000-08:00","User4",,True,"9f0cf696-c8ba-449a-8d5f-1ca6ed8f2ee8"
"2016-02-17T06:30:38.6210000-08:00","User4","2016-02-11T09:01:33.9720000-08:00",False,"8b11fd2a-01bf-4a5e-a9af-3c92c4e4382a"
"2016-02-17T22:15:26.4020000-08:00","User4","2016-02-17T06:30:38.6210000-08:00",False,"4e1cb707-3b5f-49c1-90c7-9b33b86ca1f4"
"2016-02-18T14:37:27.6560000-08:00","User4","2016-02-17T22:15:26.4020000-08:00",False,"f4e44400-e837-40ed-8dfd-2ea264d4e338"
"2016-02-19T01:20:31.4800000-08:00","User4","2016-02-18T14:37:27.6560000-08:00",False,"2136f4cf-7c7d-43c1-8ae2-08f4ad6a6e08"
```

In dit voorbeeld wordt een ingewikkelder use-casescenario gedemonstreerd waarin we een globale variabele gebruiken in een code-behind sectie die wordt toegepast op de hele geheugenrijset.

## <a name="use-user-defined-types-udt"></a>Door de gebruiker gedefinieerde typen gebruiken: UDT
Door de gebruiker gedefinieerde typen of UDT is een andere programmeerbaarheidsfunctie van U-SQL. U-SQL UDT werkt als een gewoon C#-door de gebruiker gedefinieerd type. C# is een sterk getypte taal die het gebruik van ingebouwde en aangepaste door de gebruiker gedefinieerde typen mogelijk maakt.

U-SQL kan willekeurige UDT's niet impliciet serialiseren of deserialiseren wanneer de UDT wordt doorgegeven tussen vertices in rijsets. Dit betekent dat de gebruiker een expliciete formatter moet verstrekken met behulp van de IFormatter-interface. Dit biedt U-SQL met de serialiseren en de-serialiseren methoden voor de UDT.

> [!NOTE]
> De ingebouwde extractors en outputters van U-SQL kunnen UDT-gegevens momenteel niet serialiseren of deserialiseren van udt-gegevens van of naar bestanden, zelfs met de IFormatter-set. Dus als u UDT-gegevens schrijft naar een bestand met de uitvoerinstructie of het leest met een afzuiger, moet u deze doorgeven als een tekenreeks of bytearray. Dan noemt u de serialisatie- en deserialisatiecode (dat wil zeggen de methode ToString() van de UDT expliciet. Door de gebruiker gedefinieerde extractors en outputters kunnen daarentegen UDT's lezen en schrijven.

Als we proberen om UDT te gebruiken in EXTRACTOR of OUTPUTTER (van eerdere SELECT), zoals hier weergegeven:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

We ontvangen de volgende fout:

```
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

Om met UDT in outputter te werken, moeten we deze serialiseren met de methode ToString() of een aangepaste outputter maken.

UDT's kunnen momenteel niet worden gebruikt in GROUP BY. Als UDT wordt gebruikt in GROEP BY, wordt de volgende fout gegenereerd:

```
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

Om een UDT te definiëren, moeten we:

* Voeg de volgende naamruimten toe:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Toevoegen `Microsoft.Analytics.Interfaces`, wat nodig is voor de UDT-interfaces. Bovendien kan `System.IO` het nodig zijn om de IFormatter-interface te definiëren.

* Definieer een gebruikt gedefinieerd type met het kenmerk SqlUserDefinedType.

**SqlUserDefinedType** wordt gebruikt om een typedefinitie in een verzameling te markeren als een door de gebruiker gedefinieerd type (UDT) in U-SQL. De eigenschappen op het kenmerk weerspiegelen de fysieke kenmerken van de UDT. Deze klasse kan niet worden geërfd.

SqlUserDefinedType is een vereist kenmerk voor UDT-definitie.

De constructeur van de klasse:  

* SqlUserDefinedTypeAttribute (type formatter)

* Type formatter: Vereiste parameter om een UDT formatter te `IFormatter` definiëren - specifiek, het type van de interface moet hier worden doorgegeven.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typische UDT vereist ook de definitie van de IFormatter-interface, zoals in het volgende voorbeeld wordt weergegeven:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

De `IFormatter` interface serialiseert en deserialiseert een objectgrafiek met \<het hoofdtype typeparamrefnaam="T">.

\<typeparamname="T">Het hoofdtype voor de objectgrafiek om te serialiseren en te deserialiseren.

* **Deserialiseren**: De-serialiseert de gegevens op de meegeleverde stroom en reconstrueert de grafiek van objecten.

* **Serialiseren**: Serialiseert een object, of grafiek van objecten, met de gegeven wortel aan de meegeleverde stroom.

`MyType`voorbeeld: instantie van het type.  
`IColumnWriter`schrijver `IColumnReader` / lezer: De onderliggende kolomstroom.  
`ISerializationContext`context: enum dat een set vlaggen definieert die de bron- of doelcontext voor de stream tijdens serialisatie opgeeft.

* **Tussenliggende**: geeft aan dat de bron- of doelcontext geen blijvend opslagarchief is.

* **Persistentie**: hiermee geeft u op dat de bron- of doelcontext een blijvend archief is.

Als een normaal C#-type kan een U-SQL UDT-definitie overschrijvingen bevatten voor operators zoals +/==/!=. Het kan ook statische methoden bevatten. Als we deze UDT bijvoorbeeld gaan gebruiken als parameter voor een U-SQL MIN-aggregaatfunctie, moeten we < overschrijven van operatoren definiëren.

Eerder in deze gids demonstreerden we een voorbeeld voor de identificatie `Qn:Pn (Q1:P10)`van de fiscale periode vanaf de specifieke datum in het formaat . In het volgende voorbeeld ziet u hoe u een aangepast type definieert voor waarden voor de fiscale periode.

Hieronder volgt een voorbeeld van een code-behind sectie met aangepaste UDT en IFormatter interface:

```
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Het gedefinieerde type bevat twee getallen: kwartaal en maand. Operators `==/!=/>/<` en `ToString()` statische methode worden hier gedefinieerd.

Zoals eerder vermeld, kan UDT worden gebruikt in SELECT-expressies, maar kan het niet worden gebruikt in OUTPUTTER/EXTRACTOR zonder aangepaste serialisatie. Het moet ofwel worden geserialiseerd `ToString()` als een string met of gebruikt met een aangepaste OUTPUTTER / EXTRACTOR.

Laten we het nu hebben over het gebruik van UDT. In een code-behind sectie hebben we onze Functie GetFiscalPeriod als volgt gewijzigd:

```
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Zoals u zien, wordt de waarde van ons type FiscalPeriod geretourneerd.

Hier geven we een voorbeeld van hoe het verder te gebruiken in U-SQL base script. In dit voorbeeld worden verschillende vormen van UDT-aanroep uit U-SQL-script getoond.

```
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
           start_id,
           dt,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           fiscalquarter,
           fiscalmonth,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Hier is een voorbeeld van een volledige code-behind sectie:

```
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }



        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Gebruik door de gebruiker gedefinieerde aggregaten: UDAGG
Door de gebruiker gedefinieerde aggregaten zijn alle aggregatiegerelateerde functies die niet out-of-the-box met U-SQL worden verzonden. Het voorbeeld kan een aggregaat zijn om aangepaste wiskundige berekeningen uit te voeren, string-samenvoegingen, manipulaties met tekenreeksen, enzovoort.

De door de gebruiker gedefinieerde definitie van een geaggregeerde basisklasse is als volgt:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerd aggregaat. Deze klasse kan niet worden geërfd.

SqlUserDefinedType-kenmerk is **optioneel** voor UDAGG-definitie.


Met de basisklasse u drie abstracte parameters doorgeven: twee als invoerparameters en één als resultaat. De gegevenstypen zijn variabel en moeten worden gedefinieerd tijdens klasseovering.

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** beroept zich één keer voor elke groep tijdens de berekening. Het biedt een initialisatieroutine voor elke aggregatiegroep.  
* **Accumuleren** wordt eenmaal uitgevoerd voor elke waarde. Het biedt de belangrijkste functionaliteit voor het aggregatiealgoritme. Het kan worden gebruikt om waarden te verzamelen met verschillende gegevenstypen die worden gedefinieerd tijdens klasseoverering. Het kan twee parameters van variabele gegevenstypen accepteren.
* **Terminate** wordt eenmaal per aggregatiegroep uitgevoerd aan het einde van de verwerking om het resultaat voor elke groep uit te voeren.

Als u de juiste invoer- en uitvoergegevenstypen wilt declareren, gebruikt u de klassendefinitie als volgt:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: Eerste parameter die zich ophoopt
* T2: Tweede parameter die zich ophoopt
* TResult: Retourtype beëindigen

Bijvoorbeeld:

```
public class GuidAggregate : IAggregate<string, int, int>
```

of

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>UDAGG gebruiken in U-SQL
Om UDAGG te gebruiken, definieer t u het eerst in code-behind of verwijst u ernaar uit de bestaande programmeerbaarheid DLL zoals eerder besproken.

Gebruik vervolgens de volgende syntaxis:

```
AGG<UDAGG_functionname>(param1,param2)
```

Hier is een voorbeeld van UDAGG:

```
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

En basis U-SQL-script:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In dit gebruiksscenario worden klasse-GUID's voor de specifieke gebruikers samengevoegd.

## <a name="use-user-defined-objects-udo"></a>Door de gebruiker gedefinieerde objecten gebruiken: UDO
Met U-SQL u aangepaste programmeerbaarheidsobjecten definiëren, die door de gebruiker gedefinieerde objecten of UDO worden genoemd.

Het volgende is een lijst van UDO in U-SQL:

* Door de gebruiker gedefinieerde extractors
    * Rij voor rij extraheren
    * Wordt gebruikt voor het implementeren van gegevensextractie uit aangepaste gestructureerde bestanden

* Door de gebruiker gedefinieerde outputters
    * Rij voor uitvoer voor rij
    * Wordt gebruikt om aangepaste gegevenstypen of aangepaste bestandsindelingen uit te brengen

* Door de gebruiker gedefinieerde processors
    * Neem één rij en produceer één rij
    * Wordt gebruikt om het aantal kolommen te verminderen of nieuwe kolommen te produceren met waarden die zijn afgeleid van een bestaande kolomset

* Door de gebruiker gedefinieerde appliers
    * Neem één rij en produceer rijen van 0 tot n
    * Gebruikt met OUTER/CROSS APPLY

* Door de gebruiker gedefinieerde combiners
    * Combineert rijsets- door de gebruiker gedefinieerde JOIN's

* Door de gebruiker gedefinieerde reducers
    * Neem n-rijen en produceer één rij
    * Wordt gebruikt om het aantal rijen te verminderen

UDO wordt meestal expliciet genoemd in U-SQL-script als onderdeel van de volgende U-SQL-instructies:

* EXTRACT
* Output
* Proces
* Combineren
* Verminderen

> [!NOTE]  
> UDO's zijn beperkt tot 0,5 Gb geheugen verbruiken.  Deze geheugenbeperking is niet van toepassing op lokale uitvoeringen.

## <a name="use-user-defined-extractors"></a>Gebruik door de gebruiker gedefinieerde extractors
Met U-SQL u externe gegevens importeren met behulp van een EXTRACT-instructie. Een EXTRACT-instructie kan gebruik maken van ingebouwde UDO-extractors:  

* *Extractors.Text()*: Biedt extractie uit afgebakende tekstbestanden van verschillende coderingen.

* *Extractors.Csv()*: Biedt extractie uit door komma's gescheiden waarde (CSV)-bestanden van verschillende coderingen.

* *Extractors.Tsv()*: Biedt extractie uit tsv-bestanden (tab-separated value) van verschillende coderingen.

Het kan nuttig zijn om een aangepaste afzuigkap te ontwikkelen. Dit kan handig zijn bij het importeren van gegevens als we een van de volgende taken willen uitvoeren:

* Wijzig invoergegevens door kolommen te splitsen en afzonderlijke waarden te wijzigen. De PROCESSOR-functionaliteit is beter voor het combineren van kolommen.
* Parse ongestructureerde gegevens zoals webpagina's en e-mails, of semi-ongestructureerde gegevens zoals XML /JSON.
* Parse-gegevens in niet-ondersteunde codering.

Om een door de gebruiker gedefinieerde afzuiger of UDE `IExtractor` te definiëren, moeten we een interface maken. Alle invoerparameters voor de afzuiger, zoals kolom/rijscheidingstekens en codering, moeten worden gedefinieerd in de constructor van de klasse. De `IExtractor` interface moet ook een `IEnumerable<IRow>` definitie voor de override als volgt bevatten:

```
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Het kenmerk **SqlUserDefinedExtractor** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde extractor. Deze klasse kan niet worden geërfd.

SqlUserDefinedExtractor is een optioneel kenmerk voor UDE-definitie. Het wordt gebruikt om de eigenschap AtomicFileProcessing voor het UDE-object te definiëren.

* bool AtomicFileProcessing   

* **true** = Geeft aan dat deze extractor atoominvoerbestanden vereist (JSON, XML, ...)
* **false** = Geeft aan dat deze afzuiger kan omgaan met gesplitste / gedistribueerde bestanden (CSV, SEQ, ...)

De belangrijkste UDE-programmeerbaarheidsobjecten zijn **invoer** en **uitvoer**. Het invoerobject wordt gebruikt om invoergegevens `IUnstructuredReader`op te sommen als . Het uitvoerobject wordt gebruikt om uitvoergegevens in te stellen als gevolg van de afzuigactiviteit.

De invoergegevens worden `System.IO.Stream` geopend `System.IO.StreamReader`via en .

Voor invoerkolommen die worden opgesomd, splitsen we eerst de invoerstroom met behulp van een rijscheiding.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Vervolgens u de invoerrij verder splitsen in kolomdelen.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Om uitvoergegevens in te `output.Set` stellen, gebruiken we de methode.

Het is belangrijk om te begrijpen dat de aangepaste afzuigkap alleen kolommen en waarden uitvoert die worden gedefinieerd met de uitvoer. Stel methodeaanroep in.

```
output.Set<string>(count, part);
```

De werkelijke extractor-uitvoer `yield return output.AsReadOnly();`wordt geactiveerd door aanteroepen .

Hieronder volgt het voorbeeld van de afzuigkap:

```
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

In dit use-case scenario regenereert de extractor de GUID voor de kolom 'guid' en converteert de waarden van de kolom 'gebruiker' naar hoofdletters. Aangepaste extractors kunnen meer gecompliceerde resultaten opleveren door invoergegevens te ontzeggen en te manipuleren.

Hieronder volgt het Basis U-SQL-script dat een aangepaste afzuigkap gebruikt:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-outputters"></a>Gebruik door de gebruiker gedefinieerde outputters
Door de gebruiker gedefinieerde outputter is een andere U-SQL UDO waarmee u de ingebouwde U-SQL-functionaliteit uitbreiden. Net als bij de afzuigkap zijn er verschillende ingebouwde outputters.

* *Outputters.Text()*: Schrijft gegevens om tekstbestanden van verschillende coderingen af te gebak.
* *Outputters.Csv()*: Schrijft gegevens naar CSV-bestanden (comma-separated value) van verschillende coderingen.
* *Outputters.Tsv()*: Schrijft gegevens naar TSV-bestanden (Tab-separated value) van verschillende coderingen.

Met de aangepaste uitvoerter u gegevens schrijven in een aangepaste gedefinieerde indeling. Dit kan handig zijn voor de volgende taken:

* Gegevens schrijven naar semi-gestructureerde of ongestructureerde bestanden.
* Het schrijven van gegevens die niet worden ondersteund coderingen.
* Uitvoergegevens wijzigen of aangepaste kenmerken toevoegen.

Om door de gebruiker gedefinieerde outputter te `IOutputter` definiëren, moeten we de interface maken.

Hieronder volgt `IOutputter` de implementatie van de basisklasse:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alle invoerparameters voor de outputter, zoals kolom/rijscheidingstekens, codering, enzovoort, moeten worden gedefinieerd in de constructor van de klasse. De `IOutputter` interface moet ook `void Output` een definitie bevatten voor overschrijven. Het `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kenmerk kan optioneel worden ingesteld voor atomaire bestandsverwerking. Zie de volgende details voor meer informatie.

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output`wordt opgeroepen voor elke invoerrij. Het retourneert de `IUnstructuredWriter output` rijset.
* De klasse Constructor wordt gebruikt om parameters door te geven aan de door de gebruiker gedefinieerde outputter.
* `Close`wordt gebruikt om optioneel te overschrijven om de dure status vrij te geven of te bepalen wanneer de laatste rij is geschreven.

**SqlUserDefinedOutputter-kenmerk** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde outputter. Deze klasse kan niet worden geërfd.

SqlUserDefinedOutputter is een optioneel kenmerk voor een door de gebruiker gedefinieerde outputterdefinitie. Het wordt gebruikt om de eigenschap AtomicFileProcessing te definiëren.

* bool AtomicFileProcessing   

* **true** = Geeft aan dat deze outputter atoomuitvoerbestanden (JSON, XML, ...) vereist
* **false** = Geeft aan dat deze outputter kan omgaan met gesplitste / gedistribueerde bestanden (CSV, SEQ, ...)

De belangrijkste programmeerbaarheidsobjecten zijn **rij** en **uitvoer**. Het **rijobject** wordt gebruikt om uitvoergegevens `IRow` op te sommen als interface. **Uitvoer** wordt gebruikt om uitvoergegevens in te stellen op het doelbestand.

De uitvoergegevens worden geopend `IRow` via de interface. Uitvoergegevens worden een rij tegelijk doorgegeven.

De afzonderlijke waarden worden opgesomd door de methode Downloaden van de IRow-interface aan te roepen:

```
row.Get<string>("column_name")
```

Individuele kolomnamen kunnen worden `row.Schema`bepaald door aan te roepen:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Deze aanpak stelt u in staat om een flexibele outputter te bouwen voor elk metadataschema.

De uitvoergegevens worden geschreven `System.IO.StreamWriter`om bestand met behulp van . De parameter stream `output.BaseStream` is ingesteld `IUnstructuredWriter output`op als onderdeel van .

Houd er rekening mee dat het belangrijk is om de gegevensbuffer na elke rijiteratie naar het bestand door te spoelen. Bovendien moet `StreamWriter` het object worden gebruikt met het kenmerk Wegwerp ingeschakeld (standaard) en met het **trefwoord gebruiken:**

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Anders roept u de methode Flush() expliciet aan na elke iteratie. We laten dit zien in het volgende voorbeeld.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Kop- en voetteksten instellen voor door de gebruiker gedefinieerde outputter
Als u een koptekst wilt instellen, gebruikt u de uitvoeringsstroom van één iteratie.

```
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

De code in `if (isHeaderRow)` het eerste blok wordt slechts één keer uitgevoerd.

Gebruik voor de voettekst de verwijzing `System.IO.Stream` naar`output.BaseStream`de instantie van object ( ). Schrijf de voettekst in de methode `IOutputter` Sluiten() van de interface.  (Zie het volgende voorbeeld voor meer informatie.)

Hieronder volgt een voorbeeld van een door de gebruiker gedefinieerde outputter:

```
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

En U-SQL basisscript:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Dit is een HTML-outputter, die een HTML-bestand met tabelgegevens maakt.

### <a name="call-outputter-from-u-sql-base-script"></a>Call outputter van U-SQL basisscript
Als u een aangepaste outputter wilt aanroepen vanuit het basis U-SQL-script, moet de nieuwe instantie van het uitvoerobject worden gemaakt.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Om te voorkomen dat een instantie van het object in het basisscript wordt gemaakt, kunnen we een functiewrapper maken, zoals in ons eerdere voorbeeld wordt weergegeven:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

In dit geval ziet de oorspronkelijke oproep er als volgt uit:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Door de gebruiker gedefinieerde processors gebruiken
Door de gebruiker gedefinieerde processor, of UDP, is een type U-SQL UDO waarmee u de binnenkomende rijen verwerken door programmeerbaarheidsfuncties toe te passen. Met UDP u kolommen combineren, waarden wijzigen en indien nodig nieuwe kolommen toevoegen. In principe helpt het om een rijset te verwerken om vereiste gegevenselementen te produceren.

Om een UDP te definiëren, `IProcessor` moeten `SqlUserDefinedProcessor` we een interface maken met het kenmerk, wat optioneel is voor UDP.

Deze interface moet de `IRow` definitie bevatten voor de overschrijven van de interfacerijset, zoals in het volgende voorbeeld wordt weergegeven:

```
[SqlUserDefinedProcessor]
public class MyProcessor: IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
        …
 }
}
```

**SqlUserDefinedProcessor** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde processor. Deze klasse kan niet worden geërfd.

Het kenmerk SqlUserDefinedProcessor is **optioneel** voor de UDP-definitie.

De belangrijkste programmeerbaarheidsobjecten zijn **invoer** en **uitvoer**. Het invoerobject wordt gebruikt om invoerkolommen en -uitvoer op te sommen en uitvoergegevens in te stellen als gevolg van de processoractiviteit.

Voor invoerkolommen die worden opgesomd, `input.Get` gebruiken we de methode.

```
string column_name = input.Get<string>("column_name");
```

De parameter `input.Get` voor methode is een kolom die `PRODUCE` is `PROCESS` doorgegeven als onderdeel van de component van de instructie van het U-SQL-basisscript. We moeten hier het juiste gegevenstype gebruiken.

Gebruik de `output.Set` methode voor uitvoer.

Het is belangrijk op te merken dat aangepaste producent alleen kolommen `output.Set` en waarden produceert die zijn gedefinieerd met de methodeaanroep.

```
output.Set<string>("mycolumn", mycolumn);
```

De werkelijke processoruitvoer wordt `return output.AsReadOnly();`geactiveerd door aante roepen .

Hieronder volgt een processorvoorbeeld:

```
[SqlUserDefinedProcessor]
public class FullDescriptionProcessor : IProcessor
{
public override IRow Process(IRow input, IUpdatableRow output)
 {
     string user = input.Get<string>("user");
     string des = input.Get<string>("des");
     string full_description = user.ToUpper() + "=>" + des;
     output.Set<string>("dt", input.Get<string>("dt"));
     output.Set<string>("full_description", full_description);
     output.Set<Guid>("new_guid", Guid.NewGuid());
     output.Set<Guid>("guid", input.Get<Guid>("guid"));
     return output.AsReadOnly();
 }
}
```

In dit gebruiksscenario genereert de processor een nieuwe kolom genaamd "full_description" door de bestaande kolommen te combineren - in dit geval "gebruiker" in hoofdletters en "des". Het regenereert ook een GUID en retourneert de oorspronkelijke en nieuwe GUID-waarden.

Zoals u in het vorige voorbeeld zien, `output.Set` kunt u C#-methoden aanroepen tijdens het aanroepen van de methode.

Hieronder volgt een voorbeeld van het U-SQL-script dat een aangepaste processor gebruikt:

```
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
     PROCESS @rs0
     PRODUCE dt String,
             full_description String,
             guid Guid,
             new_guid Guid
     USING new USQL_Programmability.FullDescriptionProcessor();

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

## <a name="use-user-defined-appliers"></a>Gebruik door de gebruiker gedefinieerde appliers
Met een door u-SQL door de gebruiker gedefinieerde applier u een aangepaste C#-functie aanroepen voor elke rij die wordt geretourneerd door de buitenste tabelexpressie van een query. De juiste invoer wordt geëvalueerd voor elke rij van de linkerinvoer en de rijen die worden geproduceerd, worden gecombineerd voor de uiteindelijke uitvoer. De lijst met kolommen die door de operator APPLY worden geproduceerd, zijn de combinatie van de set kolommen links en rechts.

Door de gebruiker gedefinieerde applier wordt aangeroepen als onderdeel van de USQL SELECT-expressie.

De typische aanroep naar de door de gebruiker gedefinieerde applier ziet er als volgt uit:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Zie [U-SQL SELECT Selecteren in CROSS APPLY en OUTER APPLY](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply)voor meer informatie over het gebruik van appliers in een SELECT-expressie.

De door de gebruiker gedefinieerde definitie van een applier basisklasse is als volgt:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Als u een door de gebruiker gedefinieerde applier wilt definiëren, moeten we de `IApplier` interface maken met het kenmerk [`SqlUserDefinedApplier`] dat optioneel is voor een door de gebruiker gedefinieerde applier-definitie.

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
    public ParserApplier()
    {
        …
    }

    public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
    {
        …
    }
}
```

* Toepassen wordt aangeroepen voor elke rij van de buitenste tabel. Hiermee wordt `IUpdatableRow` de uitvoerrijset geretourneerd.
* De klasse Constructor wordt gebruikt om parameters door te geven aan de door de gebruiker gedefinieerde applier.

**SqlUserDefinedApplier** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde applier. Deze klasse kan niet worden geërfd.

**SqlUserDefinedApplier** is **optioneel** voor een door de gebruiker gedefinieerde applier-definitie.


De belangrijkste programmeerbaarheidsobjecten zijn als volgt:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Invoerrijsets worden `IRow` doorgegeven als invoer. De uitvoerrijen worden `IUpdatableRow` gegenereerd als uitvoerinterface.

Individuele kolomnamen kunnen worden `IRow` bepaald door de methode Schema aan te roepen.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Om de werkelijke gegevenswaarden uit `IRow`de binnenkomende, gebruiken `IRow` we de methode Get() van interface.

```
mycolumn = row.Get<int>("mycolumn")
```

Of we gebruiken de naam van de schemakolom:

```
row.Get<int>(row.Schema[0].Name)
```

De uitvoerwaarden moeten `IUpdatableRow` worden ingesteld met uitvoer:

```
output.Set<int>("mycolumn", mycolumn)
```

Het is belangrijk om te begrijpen dat aangepaste appliers `output.Set` alleen uitvoerkolommen en waarden die zijn gedefinieerd met methode aanroep.

De werkelijke uitvoer wordt `yield return output.AsReadOnly();`geactiveerd door aante roepen .

De door de gebruiker gedefinieerde applierparameters kunnen worden doorgegeven aan de constructor. Applier kan een variabel aantal kolommen retourneren dat moet worden gedefinieerd tijdens de applier-aanroep in basis U-SQL Script.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Hier is het door de gebruiker gedefinieerde applier voorbeeld:

```
[SqlUserDefinedApplier]
public class ParserApplier : IApplier
{
private string parsingPart;

public ParserApplier(string parsingPart)
{
    if (parsingPart.ToUpper().Contains("ALL")
    || parsingPart.ToUpper().Contains("MAKE")
    || parsingPart.ToUpper().Contains("MODEL")
    || parsingPart.ToUpper().Contains("YEAR")
    || parsingPart.ToUpper().Contains("TYPE")
    || parsingPart.ToUpper().Contains("MILLAGE")
    )
    {
    this.parsingPart = parsingPart;
    }
    else
    {
    throw new ArgumentException("Incorrect parameter. Please use: 'ALL[MAKE|MODEL|TYPE|MILLAGE]'");
    }
}

public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
{

    string[] properties = input.Get<string>("properties").Split(',');

    //  only process with correct number of properties
    if (properties.Count() == 5)
    {

    string make = properties[0];
    string model = properties[1];
    string year = properties[2];
    string type = properties[3];
    int millage = -1;

    // Only return millage if it is number, otherwise, -1
    if (!int.TryParse(properties[4], out millage))
    {
        millage = -1;
    }

    if (parsingPart.ToUpper().Contains("MAKE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("make", make);
    if (parsingPart.ToUpper().Contains("MODEL") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("model", model);
    if (parsingPart.ToUpper().Contains("YEAR") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("year", year);
    if (parsingPart.ToUpper().Contains("TYPE") || parsingPart.ToUpper().Contains("ALL")) output.Set<string>("type", type);
    if (parsingPart.ToUpper().Contains("MILLAGE") || parsingPart.ToUpper().Contains("ALL")) output.Set<int>("millage", millage);
    }
    yield return output.AsReadOnly();            
}
}
```

Hieronder volgt het basis U-SQL-script voor deze door de gebruiker gedefinieerde applier:

```
DECLARE @input_file string = @"c:\usql-programmability\car_fleet.tsv";
DECLARE @output_file string = @"c:\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        stocknumber int,
        vin String,
        properties String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT
        r.stocknumber,
        r.vin,
        properties.make,
        properties.model,
        properties.year,
        properties.type,
        properties.millage
    FROM @rs0 AS r
    CROSS APPLY
    new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

In dit gebruiksscenario fungeert de door de gebruiker gedefinieerde applier als een door komma's afgebakende waardeparser voor de eigenschappen van het wagenpark. De invoerbestandsrijen zien er als volgt uit:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Het is een typisch TSV-bestand met tabbladen met een eigenschappenkolom met auto-eigenschappen zoals merk en model. Deze eigenschappen moeten worden ontleed aan de tabelkolommen. Met de meegeleverde applier u ook een dynamisch aantal eigenschappen genereren in de resultatenrijset, op basis van de parameter die is doorgegeven. U alle eigenschappen of een specifieke set eigenschappen alleen genereren.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

De door de gebruiker gedefinieerde applier kan worden aangeroepen als een nieuw exemplaar van applier object:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Of met de aanroep van een wrapper fabriek methode:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Door de gebruiker gedefinieerde combiners gebruiken
Met een door de gebruiker gedefinieerde combiner of UDC u rijen van links- en rechterrijsets combineren op basis van aangepaste logica. Door de gebruiker gedefinieerde combiner wordt gebruikt met DE EXPRESSIE COMBINEREN.

Er wordt een combiner aangeroepen met de COMBINATIE-expressie die de nodige informatie geeft over zowel de invoerrijsets, de groeperingskolommen, het verwachte resultaatschema en aanvullende informatie.

Als u een combiner in een basis U-SQL-script wilt aanroepen, gebruiken we de volgende syntaxis:

```
Combine_Expression :=
    'COMBINE' Combine_Input
    'WITH' Combine_Input
    Join_On_Clause
    Produce_Clause
    [Readonly_Clause]
    [Required_Clause]
    USING_Clause.
```

Zie [EXPRESSIE COMBINEREN (U-SQL)](/u-sql/statements-and-expressions/combine-expression)voor meer informatie.

Als u een door de gebruiker gedefinieerde combiner wilt definiëren, moeten we de `ICombiner` interface maken met het kenmerk [`SqlUserDefinedCombiner`] dat optioneel is voor een door de gebruiker gedefinieerde Combiner-definitie.

Definitie `ICombiner` van basisklasse:

```
public abstract class ICombiner : IUserDefinedOperator
{
protected ICombiner();
public virtual IEnumerable<IRow> Combine(List<IRowset> inputs,
       IUpdatableRow output);
public abstract IEnumerable<IRow> Combine(IRowset left, IRowset right,
       IUpdatableRow output);
}
```

De aangepaste implementatie `ICombiner` van een interface `IEnumerable<IRow>` moet de definitie voor een overschrijffunctie combineren bevatten.

```
[SqlUserDefinedCombiner]
public class MyCombiner : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    …
}
}
```

Het kenmerk **SqlUserDefinedCombiner** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde combiner. Deze klasse kan niet worden geërfd.

**SqlUserDefinedCombiner** wordt gebruikt om de eigenschap Combiner-modus te definiëren. Het is een optioneel kenmerk voor een door de gebruiker gedefinieerde combinerdefinitie.

CombinerMode-modus

CombinerMode enum kan de volgende waarden aannemen:

* Volledige (0) Elke uitvoerrij is mogelijk afhankelijk van alle invoerrijen van links en rechts met dezelfde sleutelwaarde.

* Links (1) Elke uitvoerrij is afhankelijk van één invoerrij van links (en mogelijk alle rijen van rechts met dezelfde sleutelwaarde).

* Rechts (2) Elke uitvoerrij is afhankelijk van één invoerrij van rechts (en mogelijk alle rijen van links met dezelfde sleutelwaarde).

* Inner (3) Elke uitvoerrij is afhankelijk van één invoerrij van links en rechts met dezelfde waarde.

Voorbeeld:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[ ]


De belangrijkste programmeerbaarheidsobjecten zijn:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Invoerrijsets worden doorgegeven als **links** en **rechts** `IRowset` type interface. Beide rijsets moeten worden opgesomd voor verwerking. U elke interface slechts één keer opsommen, dus we moeten deze opsommen en in de cache bewaren indien nodig.

Voor cachingdoeleinden kunnen we\<een\> Lijst T-type geheugenstructuur maken als gevolg van `IRow` een LINQ-queryuitvoering, met name Lijst<>. Het anonieme gegevenstype kan ook worden gebruikt tijdens de opsomming.

Zie [Inleiding tot LINQ-query's (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) voor meer informatie over LINQ-query's en [IEnumerable\<T-interface\> ](/dotnet/api/system.collections.generic.ienumerable-1) voor meer informatie over de IEnumerable\<T-interface.\>

Om de werkelijke gegevenswaarden uit `IRowset`de binnenkomende, gebruiken `IRow` we de methode Get() van interface.

```
mycolumn = row.Get<int>("mycolumn")
```

Individuele kolomnamen kunnen worden `IRow` bepaald door de methode Schema aan te roepen.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Of met de naam van de schemakolom:

```
c# row.Get<int>(row.Schema[0].Name)
```

De algemene opsomming met LINQ ziet er als volgt uit:

```
var myRowset =
            (from row in left.Rows
                          select new
                          {
                              Mycolumn = row.Get<int>("mycolumn"),
                          }).ToList();
```

Na het opsommen van beide rijsets, gaan we door alle rijen lopen. Voor elke rij in de linker rijset, gaan we alle rijen vinden die voldoen aan de conditie van onze combiner.

De uitvoerwaarden moeten `IUpdatableRow` worden ingesteld met uitvoer.

```
output.Set<int>("mycolumn", mycolumn)
```

De werkelijke uitvoer wordt `yield return output.AsReadOnly();`geactiveerd door te bellen naar .

Hieronder volgt een combiner voorbeeld:

```
[SqlUserDefinedCombiner]
public class CombineSales : ICombiner
{

public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
    IUpdatableRow output)
{
    var internetSales =
    (from row in left.Rows
          select new
          {
              ProductKey = row.Get<int>("ProductKey"),
              OrderDateKey = row.Get<int>("OrderDateKey"),
              SalesAmount = row.Get<decimal>("SalesAmount"),
              TaxAmt = row.Get<decimal>("TaxAmt")
          }).ToList();

    var resellerSales =
    (from row in right.Rows
     select new
     {
     ProductKey = row.Get<int>("ProductKey"),
     OrderDateKey = row.Get<int>("OrderDateKey"),
     SalesAmount = row.Get<decimal>("SalesAmount"),
     TaxAmt = row.Get<decimal>("TaxAmt")
     }).ToList();

    foreach (var row_i in internetSales)
    {
    foreach (var row_r in resellerSales)
    {

        if (
        row_i.OrderDateKey > 0
        && row_i.OrderDateKey < row_r.OrderDateKey
        && row_i.OrderDateKey == 20010701
        && (row_r.SalesAmount + row_r.TaxAmt) > 20000)
        {
        output.Set<int>("OrderDateKey", row_i.OrderDateKey);
        output.Set<int>("ProductKey", row_i.ProductKey);
        output.Set<decimal>("Internet_Sales_Amount", row_i.SalesAmount + row_i.TaxAmt);
        output.Set<decimal>("Reseller_Sales_Amount", row_r.SalesAmount + row_r.TaxAmt);
        }

    }
    }
    yield return output.AsReadOnly();
}
}
```

In dit use-case scenario bouwen we een analyserapport voor de retailer. Het doel is om alle producten die meer dan $ 20.000 kosten en die verkopen via de website sneller dan via de reguliere retailer binnen een bepaald tijdsbestek te vinden.

Hier is het basis U-SQL-script. U de logica vergelijken tussen een gewone JOIN en een combiner:

```sql
DECLARE @LocalURI string = @"\usql-programmability\";

DECLARE @input_file_internet_sales string = @LocalURI+"FactInternetSales.txt";
DECLARE @input_file_reseller_sales string = @LocalURI+"FactResellerSales.txt";
DECLARE @output_file1 string = @LocalURI+"output_file1.tsv";
DECLARE @output_file2 string = @LocalURI+"output_file2.tsv";

@fact_internet_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    CustomerKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_internet_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@fact_reseller_sales =
EXTRACT
    ProductKey int ,
    OrderDateKey int ,
    DueDateKey int ,
    ShipDateKey int ,
    ResellerKey int ,
    EmployeeKey int ,
    PromotionKey int ,
    CurrencyKey int ,
    SalesTerritoryKey int ,
    SalesOrderNumber String ,
    SalesOrderLineNumber  int ,
    RevisionNumber int ,
    OrderQuantity int ,
    UnitPrice decimal ,
    ExtendedAmount decimal,
    UnitPriceDiscountPct float ,
    DiscountAmount float ,
    ProductStandardCost decimal ,
    TotalProductCost decimal ,
    SalesAmount decimal ,
    TaxAmt decimal ,
    Freight decimal ,
    CarrierTrackingNumber String,
    CustomerPONumber String
FROM @input_file_reseller_sales
USING Extractors.Text(delimiter:'|', encoding: Encoding.Unicode);

@rs1 =
SELECT
    fis.OrderDateKey,
    fis.ProductKey,
    fis.SalesAmount+fis.TaxAmt AS Internet_Sales_Amount,
    frs.SalesAmount+frs.TaxAmt AS Reseller_Sales_Amount
FROM @fact_internet_sales AS fis
     INNER JOIN @fact_reseller_sales AS frs
     ON fis.ProductKey == frs.ProductKey
WHERE
    fis.OrderDateKey < frs.OrderDateKey
    AND fis.OrderDateKey == 20010701
    AND frs.SalesAmount+frs.TaxAmt > 20000;

@rs2 =
COMBINE @fact_internet_sales AS fis
WITH @fact_reseller_sales AS frs
ON fis.ProductKey == frs.ProductKey
PRODUCE OrderDateKey int,
        ProductKey int,
        Internet_Sales_Amount decimal,
        Reseller_Sales_Amount decimal
USING new USQL_Programmability.CombineSales();

OUTPUT @rs1 TO @output_file1 USING Outputters.Tsv();
OUTPUT @rs2 TO @output_file2 USING Outputters.Tsv();
```

Een door de gebruiker gedefinieerde combiner kan worden aangeroepen als een nieuw exemplaar van het applier-object:

```
USING new MyNameSpace.MyCombiner();
```


Of met de aanroep van een wrapper fabriek methode:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Gebruik door de gebruiker gedefinieerde reducers

Met U-SQL u aangepaste rowset reducers in C# schrijven met behulp van het door de gebruiker gedefinieerde operatorextensibility framework en het implementeren van een IReducer-interface.

Door de gebruiker gedefinieerde reducer of UDR kan worden gebruikt om onnodige rijen tijdens gegevensextractie (import) te elimineren. Het kan ook worden gebruikt om rijen en kolommen te manipuleren en te evalueren. Op basis van de programmeerbaarheidslogica kan het ook bepalen welke rijen moeten worden geëxtraheerd.

Om een UDR-klasse te definiëren, moeten we een `IReducer` interface maken met een optioneel `SqlUserDefinedReducer` kenmerk.

Deze klasse-interface moet een `IEnumerable` definitie bevatten voor de overschrijven van de interfacerijset.

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
        …
    }

}
```

Het kenmerk **SqlUserDefinedReducer** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde reducer. Deze klasse kan niet worden geërfd.
**SqlUserDefinedReducer** is een optioneel kenmerk voor een door de gebruiker gedefinieerde reducerdefinitie. Het wordt gebruikt om IsRecursive eigenschap te definiëren.

* bool IsRecursief    
* **true** = Geeft aan of deze Reducer associatief en woon-werkverkeer is

De belangrijkste programmeerbaarheidsobjecten zijn **invoer** en **uitvoer**. Het invoerobject wordt gebruikt om invoerrijen op te sommen. Uitvoer wordt gebruikt om uitvoerrijen in te stellen als gevolg van het verminderen van de activiteit.

Voor invoerrijen opsomming, gebruiken we `Row.Get` de methode.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

De parameter `Row.Get` voor de methode is een kolom `PRODUCE` die wordt `REDUCE` doorgegeven als onderdeel van de klasse van de instructie van het U-SQL-basisscript. We moeten hier ook het juiste gegevenstype gebruiken.

Gebruik de `output.Set` methode voor uitvoer.

Het is belangrijk om te begrijpen dat aangepaste reducer `output.Set` alleen waarden produceert die worden gedefinieerd met de methodeaanroep.

```
output.Set<string>("mycolumn", guid);
```

De werkelijke reducer-uitvoer `yield return output.AsReadOnly();`wordt geactiveerd door aante roepen .

Hieronder volgt een reducer voorbeeld:

```
[SqlUserDefinedReducer]
public class EmptyUserReducer : IReducer
{

    public override IEnumerable<IRow> Reduce(IRowset input, IUpdatableRow output)
    {
    string guid;
    DateTime dt;
    string user;
    string des;

    foreach (IRow row in input.Rows)
    {
        guid = row.Get<string>("guid");
        dt = row.Get<DateTime>("dt");
        user = row.Get<string>("user");
        des = row.Get<string>("des");

        if (user.Length > 0)
        {
        output.Set<string>("guid", guid);
        output.Set<DateTime>("dt", dt);
        output.Set<string>("user", user);
        output.Set<string>("des", des);

        yield return output.AsReadOnly();
        }
    }
    }

}
```

In dit gebruiksscenario slaat de reducer rijen over met een lege gebruikersnaam. Voor elke rij in rowset wordt elke vereiste kolom gelezen en wordt de lengte van de gebruikersnaam geëvalueerd. De werkelijke rij wordt alleen uitgevoerd als de lengte van de gebruikersnaamwaarde meer dan 0 is.

Hieronder volgt het Basis U-SQL-script dat een aangepaste reducer gebruikt:

```
DECLARE @input_file string = @"\usql-programmability\input_file_reducer.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    REDUCE @rs0 PRESORT guid
    ON guid  
    PRODUCE guid string, dt DateTime, user String, des String
    USING new USQL_Programmability.EmptyUserReducer();

@rs2 =
    SELECT guid AS start_id,
           dt AS start_time,
           DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
           USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS start_fiscalperiod,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```
