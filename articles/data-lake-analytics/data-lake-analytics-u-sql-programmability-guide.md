---
title: Programmeer handleiding voor U-SQL voor Azure Data Lake
description: Meer informatie over de set met Services in Azure Data Lake Analytics waarmee u een big data platform in de cloud kunt maken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 63be271e-7c44-4d19-9897-c2913ee9599d
ms.topic: conceptual
ms.date: 06/30/2017
ms.openlocfilehash: dc55615d7a5c6ae9a393ed4fd5f49cd92aedc0f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73162575"
---
# <a name="u-sql-programmability-guide"></a>Programmeer handleiding voor U-SQL

U-SQL is een query taal die is ontworpen voor big data type werk belastingen. Een van de unieke functies van U-SQL is de combi natie van de SQL-achtige declaratieve taal met de uitbreid baarheid en programmeer baarheid die wordt verschaft door C#. In deze hand leiding wordt gestreefd naar de uitbreid baarheid en programmeer baarheid van de U-SQL-taal die is ingeschakeld door C#.

## <a name="requirements"></a>Vereisten

Down load en Installeer [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

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

Dit script definieert twee rijen sets: `@a` en `@results`. De `@results` rijenset is `@a`gedefinieerd vanuit.

## <a name="c-types-and-expressions-in-u-sql-script"></a>C#-typen en-expressies in U-SQL-script

Een U-SQL-expressie is een C#-expressie in combi natie met logische U `AND`- `OR`SQL- `NOT`bewerkingen, zoals, en. U-SQL-expressies kunnen worden gebruikt in combi natie met selecteren, uitpakken, waar, groeperen op en DECLAReren. Met het volgende script wordt bijvoorbeeld een teken reeks geparseerd als een DateTime-waarde.

```
@results =
  SELECT
    customer,
    amount,
    DateTime.Parse(date) AS date
  FROM @a;    
```

In het volgende fragment wordt een teken reeks geparseerd als DateTime-waarde in een DECLARe-instructie.

```
DECLARE @d = DateTime.Parse("2016/01/01");
```

### <a name="use-c-expressions-for-data-type-conversions"></a>C#-expressies gebruiken voor gegevens type conversies

In het volgende voor beeld ziet u hoe u een datum/tijd-gegevens conversie kunt uitvoeren met behulp van C#-expressies. In dit specifieke scenario worden datetime-gegevens met een teken reeks geconverteerd naar een standaard-datetime met een datum notatie van middernacht 00:00:00.

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

We kunnen de volgende C#-expressie gebruiken om de datum van vandaag te halen:`DateTime.Now.ToString("M/d/yyyy")`

Hier volgt een voor beeld van het gebruik van deze expressie in een script:

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
## <a name="using-net-assemblies"></a>.NET-assembly's gebruiken

Het uitbreidings model van U-SQL is sterk afhankelijk van de mogelijkheid om aangepaste code uit .NET-assembly's toe te voegen. 

### <a name="register-a-net-assembly"></a>Een .NET-assembly registreren

Gebruik de `CREATE ASSEMBLY` instructie voor het plaatsen van een .NET-assembly in een U-SQL database. Daarna kunnen U-SQL-scripts deze assembly's gebruiken met behulp `REFERENCE ASSEMBLY` van de-instructie. 

De volgende code laat zien hoe u een assembly registreert:

```
CREATE ASSEMBLY MyDB.[MyAssembly]
   FROM "/myassembly.dll";
```

De volgende code laat zien hoe u naar een assembly verwijst:

```
REFERENCE ASSEMBLY MyDB.[MyAssembly];
```

Raadpleeg de [instructies van de assembly-registratie](https://blogs.msdn.microsoft.com/azuredatalake/2016/08/26/how-to-register-u-sql-assemblies-in-your-u-sql-catalog/) voor meer informatie over dit onderwerp.


### <a name="use-assembly-versioning"></a>Assembly versie beheer gebruiken
Op dit moment gebruikt U-SQL de .NET Framework versie 4,5. Zorg ervoor dat uw eigen assembly's compatibel zijn met deze versie van de runtime.

Zoals eerder vermeld, voert U-SQL-code uit in een 64-bits (x64)-indeling. Zorg er dus voor dat uw code wordt gecompileerd om te worden uitgevoerd op x64. Anders krijgt u de onjuiste indelings fout die eerder is weer gegeven.

Elke geüploade assembly-DLL en resource bestand, zoals een andere runtime, een systeem eigen assembly of een configuratie bestand, kunnen Maxi maal 400 MB groot zijn. De totale grootte van geïmplementeerde resources, hetzij via DEPLOY RESOURCE, hetzij via verwijzingen naar assembly's en hun extra bestanden, kan niet groter zijn dan 3 GB.

Houd er rekening mee dat elke U-SQL database slechts één versie van een bepaalde assembly mag bevatten. Als u bijvoorbeeld versie 7 en versie 8 van de Json.NET-bibliotheek van Newton Soft nodig hebt, moet u deze registreren in twee verschillende data bases. Bovendien kan elk script slechts verwijzen naar één versie van een bepaalde assembly-DLL. In dit opzicht volgt U-SQL de C#-assemblage beheer en versie beheer semantiek.

## <a name="use-user-defined-functions-udf"></a>Door de gebruiker gedefinieerde functies gebruiken: UDF
Door de gebruiker gedefinieerde U-SQL-functies of UDF zijn programmeer routines die para meters accepteren, een actie uitvoeren (zoals een complexe berekening) en het resultaat van die actie als een waarde Retour neren. De geretourneerde waarde van UDF kan alleen één scalair zijn. U-SQL UDF kan worden aangeroepen in U-SQL-basis script, zoals elke andere scalaire functie van C#.

U wordt aangeraden de door de gebruiker gedefinieerde U-SQL-functies als **Public** en **static**te initialiseren.

```
public static string MyFunction(string param1)
{
    return "my result";
}
```

Eerst kijken we naar het eenvoudige voor beeld van het maken van een UDF.

In dit geval moet u de fiscale periode bepalen, inclusief het fiscale kwar taal en de boek maand van de eerste aanmelding voor de specifieke gebruiker. De eerste boek maand van het jaar in ons scenario is juni.

Als u de boek periode wilt berekenen, wordt de volgende C#-functie geïntroduceerd:

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

De fiscale maand en het kwar taal worden alleen berekend en er wordt een teken reeks waarde geretourneerd. Voor juni, de eerste maand van het eerste boek kwartaal, gebruiken we "Q1: P1". Voor juli gebruiken we "Q1: P2", enzovoort.

Dit is een reguliere C#-functie die we gaan gebruiken in het U-SQL-project.

Hier ziet u hoe de code-behind sectie in dit scenario wordt weer gegeven:

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

Nu gaan we deze functie aanroepen vanuit het basis-U-SQL-script. Hiervoor moeten we een volledig gekwalificeerde naam voor de functie opgeven, met inbegrip van de naam ruimte, in dit geval naam ruimte. class. function (para meter).

```
USQL_Programmability.CustomFunctions.GetFiscalPeriod(dt)
```

Hieronder vindt U het feitelijke U-SQL-basis script:

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

Hieronder volgt het uitvoer bestand van de uitvoering van het script:

```
0d8b9630-d5ca-11e5-8329-251efa3a2941,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User1",""

20843640-d771-11e5-b87b-8b7265c75a44,2016-02-11T07:04:17.2630000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User2",""

301f23d2-d690-11e5-9a98-4b4f60a1836f,2016-02-11T09:01:33.9720000-08:00,2016-06-01T00:00:00.0000000,"Q3:8","User3",""
```

In dit voor beeld wordt een eenvoudig gebruik van inline UDF in U-SQL gedemonstreerd.

### <a name="keep-state-between-udf-invocations"></a>Status tussen UDF-aanroepen blijven
U-SQL C#-programmeer bare objecten kunnen geavanceerder worden en interactiviteit gebruiken via de globale variabelen van de code-behind. Laten we eens kijken naar het volgende scenario voor zakelijk gebruik.

In grote organisaties kunnen gebruikers scha kelen tussen varieteiten van interne toepassingen. Dit kan micro soft Dynamics CRM, PowerBI, enzovoort zijn. Klanten willen mogelijk een telemetrie-analyse Toep assen op de manier waarop gebruikers scha kelen tussen verschillende toepassingen, de gebruiks trends, enzovoort. Het doel van het bedrijf is het optimaliseren van het toepassings gebruik. Het kan ook zijn dat er verschillende toepassingen of specifieke aanmeldings routines moeten worden gecombineerd.

Om dit doel te verzorgen, moeten we de sessie-Id's en vertragings tijd tussen de laatste sessie bepalen die zijn opgetreden.

U moet een eerdere aanmelding vinden en deze aanmelding vervolgens toewijzen aan alle sessies die worden gegenereerd voor dezelfde toepassing. De eerste uitdaging is dat U-SQL-basis script geen berekeningen kan Toep assen op reeds berekende kolommen met de functie LAG. De tweede uitdaging is dat de specifieke sessie binnen dezelfde periode voor alle sessies moet worden bewaard.

Om dit probleem op te lossen, gebruiken we een globale variabele in een code- `static public string globalSession;`behind-sectie:.

Deze globale variabele wordt tijdens de uitvoering van het script toegepast op de hele rijenset.

Dit is de code-behind sectie van het U-SQL-programma:

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

In dit voor beeld ziet u `static public string globalSession;` de globale variabele `getStampUserSession` die wordt gebruikt in de functie en die telkens opnieuw wordt geïnitialiseerd wanneer de sessie parameter wordt gewijzigd.

Het U-SQL base-script ziet er als volgt uit:

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

De `USQLApplication21.UserSession.getStampUserSession(UserSessionTimestamp)` functie wordt hier genoemd tijdens de tweede berekening van de geheugen rijenset. De `UserSessionTimestamp` kolom wordt door gegeven en retourneert de waarde `UserSessionTimestamp` totdat deze is gewijzigd.

Het uitvoer bestand is als volgt:

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

In dit voor beeld ziet u een complexere gebruiks scenario waarbij we een globale variabele in een code-behind-sectie gebruiken die wordt toegepast op de hele geheugen rijenset.

## <a name="use-user-defined-types-udt"></a>Door de gebruiker gedefinieerde typen gebruiken: UDT
Door de gebruiker gedefinieerde typen of UDT is een andere programmeer baarheids functie van U-SQL. U-SQL UDT fungeert als een standaard door de gebruiker gedefinieerd C#-type. C# is een sterk getypeerde taal waarmee ingebouwde en aangepaste door de gebruiker gedefinieerde typen kunnen worden gebruikt.

U-SQL kan wille keurige UDTs niet impliciet serialiseren of deserialiseren wanneer de UDT tussen hoek punten in rijen sets wordt door gegeven. Dit betekent dat de gebruiker een expliciete formatter moet opgeven met behulp van de IFormatter-interface. Dit biedt U-SQL met de methoden serialiseren en deserialiseren voor de UDT.

> [!NOTE]
> De ingebouwde extracten en outputters van U-SQL kunnen momenteel geen UDT-gegevens serialiseren of deserialiseren naar of van bestanden, zelfs als de IFormatter is ingesteld. Als u UDT-gegevens naar een bestand met de uitvoer instructie schrijft of als u een extractie uitvoert met een Extractor, moet u deze door geven als een teken reeks of byte matrix. Vervolgens roept u de code voor serialisatie en deserialisatie aan (dat wil zeggen, de methode ToString () van UDT). Door de gebruiker gedefinieerde uittreksels en outputters kunnen ook UDTs lezen en schrijven.

Als we UDT proberen te gebruiken in EXTRACTOR of outputter (van eerdere selectie), zoals hier wordt weer gegeven:

```
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

De volgende fout wordt weer gegeven:

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

Als u wilt werken met UDT in outputter, moet u deze serialiseren naar een teken reeks met de methode ToString () of een aangepaste outputter maken.

UDTs kan momenteel niet worden gebruikt in GROUP BY. Als UDT in GROUP BY wordt gebruikt, wordt de volgende fout gegenereerd:

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

We moeten het volgende doen om een UDT te definiëren:

* Voeg de volgende naam ruimten toe:

```
using Microsoft.Analytics.Interfaces
using System.IO;
```

* Add `Microsoft.Analytics.Interfaces`, vereist voor de UDT-interfaces. Daarnaast `System.IO` kan het nodig zijn om de IFormatter-interface te definiëren.

* Definieer een gebruikt gedefinieerd type met het kenmerk SqlUserDefinedType.

**SqlUserDefinedType** wordt gebruikt om een type definitie in een assembly te markeren als een door de gebruiker gedefinieerd type (UDT) in U-SQL. De eigenschappen van het kenmerk reflecteren de fysieke kenmerken van de UDT. Deze klasse kan niet worden overgenomen.

SqlUserDefinedType is een vereist kenmerk voor de UDT-definitie.

De constructor van de klasse:  

* SqlUserDefinedTypeAttribute (type formatter)

* Type formatter: vereiste para meter voor het definiëren van een UDT-formatter--specifiek moet het type `IFormatter` van de interface hier worden door gegeven.

```
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Voor de standaard-UDT is ook definitie van de IFormatter-interface vereist, zoals wordt weer gegeven in het volgende voor beeld:

```
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

De `IFormatter` interface is een serialisatie en de serialisatie van een object grafiek met het hoofd \<type Typeparamref name = "T" >.

\<typeparam name = "T" >het hoofd type voor de object grafiek die moet worden geserialiseerd en gedeserialiseerd.

* **Deserialiseren**: de gegevens op de verschafte stroom deserialiseren en het grafiek object opnieuw indelen.

* **Serialiseren**: een object of grafiek met objecten met de opgegeven basis naar de opgegeven stroom serialiseren.

`MyType`exemplaar: exemplaar van het type.  
`IColumnWriter`schrijver/ `IColumnReader` lezer: de onderliggende kolom stroom.  
`ISerializationContext`context: Enum die een set vlaggen definieert waarmee de bron-of doel context voor de stream wordt opgegeven tijdens de serialisatie.

* **Tussenliggend**: Hiermee geeft u op dat de bron-of doel context geen persistente archief is.

* **Persistentie**: Hiermee geeft u op dat de bron-of doel context een persistente opslag is.

Als een normaal C#-type kan een U-SQL UDT-definitie onderdrukkingen bevatten voor Opera tors zoals +/= =/! =. Het kan ook statische methoden bevatten. Als we deze UDT bijvoorbeeld gaan gebruiken als een para meter voor een statistische functie met U-SQL MIN, moeten we < operator override definiëren.

Eerder in deze hand leiding is een voor beeld van de identificatie van de fiscale periode van de specifieke datum `Qn:Pn (Q1:P10)`in de notatie gedemonstreerd. In het volgende voor beeld ziet u hoe u een aangepast type definieert voor fiscale periode waarden.

Hieronder volgt een voor beeld van een code-behind sectie met de aangepaste UDT-en IFormatter-Interface:

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

Het gedefinieerde type bevat twee getallen: kwar taal en maand. De `==/!=/>/<` Opera tors en `ToString()` de statische methode worden hier gedefinieerd.

Zoals eerder vermeld, kan UDT worden gebruikt in SELECT-expressies, maar kan niet worden gebruikt in outputter/EXTRACTOR zonder aangepaste serialisatie. De waarde moet worden geserialiseerd als een teken reeks met `ToString()` of worden gebruikt in combi natie met een aangepaste outputter/extractor.

Nu gaan we het gebruik van UDT bespreken. In de sectie code-behind is de functie GetFiscalPeriod gewijzigd in het volgende:

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

Zoals u kunt zien, wordt de waarde van het type FiscalPeriod geretourneerd.

Hier bieden we een voor beeld van hoe u dit verder in het U-SQL-basis script kunt gebruiken. In dit voor beeld worden verschillende soorten UDT-aanroep van het U-SQL-script gedemonstreerd.

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

Hier volgt een voor beeld van een volledige code-behind:

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

## <a name="use-user-defined-aggregates-udagg"></a>Door de gebruiker gedefinieerde statistische functies gebruiken: UDAGG
Door de gebruiker gedefinieerde aggregaties zijn alle functies voor aggregatie die niet aan U zijn gekoppeld met U-SQL. Het voor beeld kan een statistische functie zijn voor het uitvoeren van aangepaste wiskundige berekeningen, het samen voegen van teken reeksen, bewerkingen met teken reeksen, enzovoort.

De door de gebruiker gedefinieerde definitie van de basis klasse is als volgt:

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

**SqlUserDefinedAggregate** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde statistische functie. Deze klasse kan niet worden overgenomen.

Het kenmerk SqlUserDefinedType is **optioneel** voor de definitie van de UDAGG.


Met de basis klasse kunt u drie abstracte para meters door geven: twee als invoer parameters en een als resultaat. De gegevens typen zijn variabele en moeten tijdens de overname van de klasse worden gedefinieerd.

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

* **Init** roept één keer op voor elke groep tijdens de berekening. Het bevat een initialisatie routine voor elke samenvoegings groep.  
* De **accumulatie** wordt één keer uitgevoerd voor elke waarde. Het biedt de belangrijkste functionaliteit voor het aggregatie algoritme. Het kan worden gebruikt om waarden samen te voegen met verschillende gegevens typen die tijdens de overname van de klasse worden gedefinieerd. Er kunnen twee para meters van variabele gegevens typen worden geaccepteerd.
* Er wordt één keer per samenvoegings groep een **afsluiting** uitgevoerd om het resultaat voor elke groep te kunnen uitvoeren.

Als u de juiste invoer-en uitvoer gegevens typen wilt declareren, gebruikt u de klassedefinitie als volgt:

```
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: eerste para meter die moet worden verzameld
* T2: tweede para meter die moet worden verzameld
* TResult: retour type van Terminate

Bijvoorbeeld:

```
public class GuidAggregate : IAggregate<string, int, int>
```

of

```
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>UDAGG gebruiken in U-SQL
Als u UDAGG wilt gebruiken, moet u deze eerst definiëren in de code-behind of ernaar verwijzen vanuit de bestaande programmeer baarheids-DLL, zoals eerder besproken.

Gebruik vervolgens de volgende syntaxis:

```
AGG<UDAGG_functionname>(param1,param2)
```

Hier volgt een voor beeld van UDAGG:

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

En base U-SQL-script:

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

In dit scenario voor gebruik worden klasse-GUID'S voor de specifieke gebruikers samengevoegd.

## <a name="use-user-defined-objects-udo"></a>Door gebruiker gedefinieerde objecten gebruiken: UDO
U kunt met u-SQL aangepaste programmeer baarheids objecten definiëren, die door de gebruiker gedefinieerde objecten of UDO worden genoemd.

Hier volgt een lijst met UDO in U-SQL:

* Door de gebruiker gedefinieerde extra heren
    * Rij per rij extra heren
    * Wordt gebruikt voor het implementeren van gegevens extractie vanuit aangepaste gestructureerde bestanden

* Door de gebruiker gedefinieerde outputters
    * Uitvoer rij per rij
    * Wordt gebruikt om aangepaste gegevens typen of aangepaste bestands indelingen uit te voeren

* Door de gebruiker gedefinieerde processors
    * Eén rij maken en een rij genereren
    * Hiermee wordt het aantal kolommen verminderd of worden nieuwe kolommen gegenereerd met waarden die zijn afgeleid van een bestaande kolomset

* Door de gebruiker gedefinieerde appliers
    * Eén rij maken en 0 tot n rijen opleveren
    * Wordt gebruikt met OUTER/CROSS APPLY

* Door de gebruiker gedefinieerde combi Naties
    * Combineert rijen sets--door de gebruiker gedefinieerde samen voegingen

* Door de gebruiker gedefinieerde verlaagers
    * N rijen nemen en één rij genereren
    * Hiermee wordt het aantal rijen verminderd

UDO wordt doorgaans expliciet genoemd in U-SQL-script als onderdeel van de volgende U-SQL-instructies:

* EXTRACT
* UITVOER
* HOST
* VERPLAATSEN
* BESPAREN

> [!NOTE]  
> UDO zijn beperkt tot 0,5 GB geheugen.  Deze beperking van het geheugen geldt niet voor lokale uitvoeringen.

## <a name="use-user-defined-extractors"></a>Door de gebruiker gedefinieerde extra heren gebruiken
U kunt met behulp van een extractie-instructie externe gegevens importeren met u-SQL. Een instructie EXTRACT kan ingebouwde UDO-extracten gebruiken:  

* *Extracters. Text ()*: levert extra heren uit tekst bestanden met scheidings tekens van verschillende code ringen.

* *Extracters. CSV ()*: biedt extra heren van bestanden met door komma's gescheiden waarden (CSV) van verschillende code ringen.

* *Extracters. TSV ()*: biedt extractie van bestanden met door tabs gescheiden waarden (TSV) van verschillende code ringen.

Het kan handig zijn om een aangepaste Extractor te ontwikkelen. Dit kan handig zijn bij het importeren van gegevens als u een van de volgende taken wilt uitvoeren:

* Wijzig de invoer gegevens door kolommen te splitsen en afzonderlijke waarden te wijzigen. De PROCESSOR functionaliteit is beter voor het combi neren van kolommen.
* Niet-gestructureerde gegevens, zoals webpagina's en e-mail berichten of semi-gestructureerde gegevens, zoals XML/JSON parseren.
* Gegevens parseren in niet-ondersteunde code ring.

Als u een door de gebruiker gedefinieerde Extractor of USIEF wilt definiëren, moet u een `IExtractor` interface maken. Alle invoer parameters voor het Extractor, zoals kolom/rij scheidings tekens en code ring, moeten worden gedefinieerd in de constructor van de-klasse. De `IExtractor` interface moet ook een definitie voor de `IEnumerable<IRow>` onderdrukking bevatten:

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

Het kenmerk **SqlUserDefinedExtractor** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerd extractor. Deze klasse kan niet worden overgenomen.

SqlUserDefinedExtractor is een optioneel kenmerk voor de definitie van USIEF. Hiermee wordt de eigenschap AtomicFileProcessing voor het object USIEF gedefinieerd.

* BOOL AtomicFileProcessing   

* **True** = geeft aan dat voor deze extractie atomische invoer bestanden (JSON, XML,...) zijn vereist
* **False** = geeft aan dat deze extractie kan omgaan met gesplitste/gedistribueerde bestanden (CSV, seq,...)

De belangrijkste USIEF-Programmeer bare objecten zijn **invoer** en **uitvoer**. Het invoer object wordt gebruikt voor het opsommen van `IUnstructuredReader`invoer gegevens als. Het uitvoer object wordt gebruikt om uitvoer gegevens als resultaat van de extractor-activiteit in te stellen.

De invoer gegevens worden geopend via `System.IO.Stream` en `System.IO.StreamReader`.

Voor de inventarisatie van invoer kolommen wordt de invoer stroom eerst gesplitst met behulp van een scheidings teken in een rij.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Daarna kunt u de invoer rij verder splitsen in kolom onderdelen.

```
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Voor het instellen van uitvoer gegevens gebruiken we `output.Set` de-methode.

Het is belang rijk om te begrijpen dat de aangepaste Extractor alleen kolommen en waarden uitvoert die met de uitvoer zijn gedefinieerd. Methode aanroep instellen.

```
output.Set<string>(count, part);
```

De daad werkelijke extractie uitvoer wordt geactiveerd door aan `yield return output.AsReadOnly();`te roepen.

Hieronder vindt u het extractor-voor beeld:

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

In dit scenario gebruikt de extractor opnieuw de GUID voor de kolom GUID en worden de waarden van de kolom gebruiker geconverteerd naar hoofd letters. Aangepaste extracten kunnen complexere resultaten opleveren door invoer gegevens te parseren en te bewerken.

Hieronder volgt een basis-U-SQL-script dat gebruikmaakt van een aangepaste extractor:

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

## <a name="use-user-defined-outputters"></a>Door de gebruiker gedefinieerde outputters gebruiken
De door de gebruiker gedefinieerde outputter is een andere U-SQL-UDO waarmee u ingebouwde U-SQL-functionaliteit kunt uitbreiden. Net als bij het Extractor zijn er verschillende ingebouwde outputten.

* *Outputters. Text ()*: schrijft gegevens naar tekst bestanden met scheidings tekens van verschillende code ringen.
* *Outputters. CSV ()*: schrijft gegevens naar bestanden met door komma's gescheiden waarden (CSV) van verschillende code ringen.
* *Outputters. TSV ()*: schrijft gegevens naar bestanden met door tabs gescheiden waarden (TSV) van verschillende code ringen.

Met aangepaste outputter kunt u gegevens in een aangepaste, gedefinieerde indeling schrijven. Dit kan handig zijn voor de volgende taken:

* Het schrijven van gegevens naar semi-gestructureerde of ongestructureerde bestanden.
* Het schrijven van gegevens wordt niet ondersteund door code ring.
* Het wijzigen van uitvoer gegevens of het toevoegen van aangepaste kenmerken.

Voor het definiëren van een door de gebruiker gedefinieerde outputter moeten we `IOutputter` de interface maken.

Hieronder volgt de implementatie `IOutputter` van de basis klasse:

```
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alle invoer parameters voor de outputter, zoals kolom/rij-scheidings tekens, code ring, enzovoort, moeten worden gedefinieerd in de constructor van de-klasse. De `IOutputter` interface moet ook een definitie voor `void Output` override bevatten. Het kenmerk `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kan eventueel worden ingesteld voor de verwerking van atomische bestanden. Zie de volgende Details voor meer informatie.

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

* `Output`wordt aangeroepen voor elke invoercel. Retourneert de `IUnstructuredWriter output` rijenset.
* De constructor-klasse wordt gebruikt om para meters door te geven aan de door de gebruiker gedefinieerde outputter.
* `Close`wordt gebruikt om de dure status vrij te geven of te bepalen wanneer de laatste rij is geschreven.

**SqlUserDefinedOutputter** kenmerk geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde outputter. Deze klasse kan niet worden overgenomen.

SqlUserDefinedOutputter is een optioneel kenmerk voor een definitie van een door de gebruiker gedefinieerde outputter. Deze wordt gebruikt om de eigenschap AtomicFileProcessing te definiëren.

* BOOL AtomicFileProcessing   

* **True** = geeft aan dat deze outputter atomische uitvoer bestanden vereist (JSON, XML,...)
* **False** = geeft aan dat deze outputter kan omgaan met gesplitste/gedistribueerde bestanden (CSV, seq,...)

De belangrijkste Programmeer bare objecten zijn **rij** en **uitvoer**. Het object **Row** wordt gebruikt voor het opsommen van `IRow` uitvoer gegevens als interface. **Uitvoer** wordt gebruikt om uitvoer gegevens naar het doel bestand in te stellen.

De uitvoer gegevens worden via de `IRow` interface geopend. Er wordt een rij per keer door gegeven aan de uitvoer gegevens.

De afzonderlijke waarden worden opgesomd door de Get-methode van de IRow-interface aan te roepen:

```
row.Get<string>("column_name")
```

Afzonderlijke kolom namen kunnen worden bepaald door het `row.Schema`aanroepen van:

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Met deze aanpak kunt u een flexibele outputter voor elk meta gegevens schema maken.

De uitvoer gegevens worden naar het bestand geschreven met `System.IO.StreamWriter`behulp van. De stream-para meter is `output.BaseStream` ingesteld als onderdeel `IUnstructuredWriter output`van.

Houd er rekening mee dat het belang rijk is om de gegevens buffer te leegmaken naar het bestand na elke rij herhaling. Daarnaast moet het `StreamWriter` object worden gebruikt met het kenmerk wegwerp ingeschakeld (standaard) en met het sleutel woord **using** :

```
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Als dat niet het geval is, roept u de methode Flush () expliciet na elke iteratie. Dit wordt in het volgende voor beeld weer gegeven.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Kop-en voet teksten instellen voor door de gebruiker gedefinieerde outputter
Als u een koptekst wilt instellen, gebruikt u de stroom voor het uitvoeren van één iteratie.

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

De code in het eerste `if (isHeaderRow)` blok wordt slechts één keer uitgevoerd.

Gebruik de verwijzing naar het exemplaar van `System.IO.Stream` object (`output.BaseStream`) voor de voet tekst. Schrijf de voet tekst in de methode Close () van `IOutputter` de interface.  (Zie het volgende voor beeld voor meer informatie.)

Hier volgt een voor beeld van een door de gebruiker gedefinieerde outputter:

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

En U-SQL base-script:

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

Dit is een HTML-outputter waarmee een HTML-bestand met tabel gegevens wordt gemaakt.

### <a name="call-outputter-from-u-sql-base-script"></a>Outputter aanroepen vanuit het U-SQL-basis script
Als u een aangepaste outputter wilt aanroepen vanuit het basis-U-SQL-script, moet u het nieuwe exemplaar van het outputter-object maken.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Om te voor komen dat u een exemplaar van het object maakt in het basis script, kunt u een functie-wrapper maken, zoals wordt weer gegeven in het vorige voor beeld:

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

In dit geval ziet de oorspronkelijke aanroep er als volgt uit:

```
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="use-user-defined-processors"></a>Door de gebruiker gedefinieerde processors gebruiken
Een door de gebruiker gedefinieerde processor of UDP is een type U-SQL-UDO waarmee u de binnenkomende rijen kunt verwerken door programmeer functies toe te passen. Met UDP kunt u kolommen combi neren, waarden wijzigen en nieuwe kolommen toevoegen, indien nodig. In principe kunt u een rijenset verwerken om vereiste gegevens elementen te maken.

Als u een UDP wilt definiëren, moet u een `IProcessor` interface met het `SqlUserDefinedProcessor` kenmerk maken. Dit is optioneel voor UDP.

Deze interface moet de definitie bevatten voor de `IRow` onderdrukking van de interface-Rijset, zoals wordt weer gegeven in het volgende voor beeld:

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

**SqlUserDefinedProcessor** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde processor. Deze klasse kan niet worden overgenomen.

Het kenmerk SqlUserDefinedProcessor is **optioneel** voor de UDP-definitie.

De belangrijkste Programmeer bare objecten zijn **invoer** en **uitvoer**. Het invoer object wordt gebruikt voor het opsommen van invoer kolommen en uitvoer en voor het instellen van uitvoer gegevens als resultaat van de processor activiteit.

Voor de inventarisatie van invoer kolommen gebruiken we `input.Get` de-methode.

```
string column_name = input.Get<string>("column_name");
```

De para meter `input.Get` voor de methode is een kolom die wordt door gegeven als `PRODUCE` onderdeel van de `PROCESS` component van de-instructie van het U-SQL-basis script. Het juiste gegevens type moet hier worden gebruikt.

Gebruik de `output.Set` -methode voor uitvoer.

Het is belang rijk te weten dat de aangepaste producent alleen kolommen en waarden uitvoert die zijn gedefinieerd met `output.Set` de aanroep van de methode.

```
output.Set<string>("mycolumn", mycolumn);
```

De werkelijke processor uitvoer wordt geactiveerd door aan te `return output.AsReadOnly();`roepen.

Hier volgt een voor beeld van een processor:

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

In dit scenario wordt een nieuwe kolom met de naam ' full_description ' gegenereerd door de bestaande kolommen te combi neren, in dit geval ' gebruiker ' in hoofd letters en ' des '. Er wordt ook een GUID opnieuw gegenereerd en de oorspronkelijke en nieuwe GUID-waarden worden geretourneerd.

Zoals u kunt zien in het vorige voor beeld, kunt u C#-methoden `output.Set` aanroepen tijdens methode aanroep.

Hieronder volgt een voor beeld van een base U-SQL-script dat gebruikmaakt van een aangepaste processor:

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

## <a name="use-user-defined-appliers"></a>Door de gebruiker gedefinieerde appliers gebruiken
Met een door de gebruiker gedefinieerde U-SQL-Applier kunt u een aangepaste C#-functie aanroepen voor elke rij die wordt geretourneerd door de buitenste tabel expressie van een query. De juiste invoer wordt geëvalueerd voor elke rij van de linkerkant invoer en de rijen die worden geproduceerd, worden gecombineerd voor de uiteindelijke uitvoer. De lijst met kolommen die worden geproduceerd door de operator APPLY, is de combi natie van de set kolommen aan de linkerkant en de juiste invoer.

Door de gebruiker gedefinieerde Applier wordt aangeroepen als onderdeel van de USQL SELECT-expressie.

De standaard aanroep van de door de gebruiker gedefinieerde Applier ziet er als volgt uit:

```
SELECT …
FROM …
CROSS APPLYis used to pass parameters
new MyScript.MyApplier(param1, param2) AS alias(output_param1 string, …);
```

Zie voor meer informatie over het gebruik van appliers in een SELECT [-expressie u-SQL selecteert u selecteren uit cross Apply en outer apply](/u-sql/statements-and-expressions/select/from/select-selecting-from-cross-apply-and-outer-apply).

De definitie van de door de gebruiker gedefinieerde Applier-basis klasse is als volgt:

```
public abstract class IApplier : IUserDefinedOperator
{
protected IApplier();

public abstract IEnumerable<IRow> Apply(IRow input, IUpdatableRow output);
}
```

Om een door de gebruiker gedefinieerde Applier te definiëren, moeten we de `IApplier` interface maken met het`SqlUserDefinedApplier`kenmerk [], dat optioneel is voor een door de gebruiker gedefinieerde Applier-definitie.

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

* Apply wordt aangeroepen voor elke rij van de buitenste tabel. Hiermee wordt de `IUpdatableRow` uitvoer rijenset geretourneerd.
* De constructor-klasse wordt gebruikt om para meters door te geven aan de door de gebruiker gedefinieerde Applier.

**SqlUserDefinedApplier** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerd Applier. Deze klasse kan niet worden overgenomen.

**SqlUserDefinedApplier** is **optioneel** voor een door de gebruiker gedefinieerde Applier-definitie.


De belangrijkste Programmeer bare objecten zijn als volgt:

```
public override IEnumerable<IRow> Apply(IRow input, IUpdatableRow output)
```

Invoer rijen worden als `IRow` invoer door gegeven. De uitvoer rijen worden gegenereerd als `IUpdatableRow` uitvoer interface.

U kunt afzonderlijke kolom namen bepalen door de `IRow` schema methode aan te roepen.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Voor het ophalen van de werkelijke gegevens waarden van `IRow`de binnenkomende, gebruiken we de methode Get ( `IRow` ) van de interface.

```
mycolumn = row.Get<int>("mycolumn")
```

Of we gebruiken de schema kolom naam:

```
row.Get<int>(row.Schema[0].Name)
```

De uitvoer waarden moeten worden ingesteld met `IUpdatableRow` de volgende uitvoer:

```
output.Set<int>("mycolumn", mycolumn)
```

Het is belang rijk om te begrijpen dat aangepaste appliers alleen uitvoer kolommen en waarden die zijn `output.Set` gedefinieerd met methode aanroep.

De daad werkelijke uitvoer wordt geactiveerd door aan `yield return output.AsReadOnly();`te roepen.

De door de gebruiker gedefinieerde Applier-para meters kunnen worden door gegeven aan de constructor. Applier kan een variabel aantal kolommen retour neren dat moet worden gedefinieerd tijdens de aanroep van de Applier in base U-SQL-script.

```
new USQL_Programmability.ParserApplier ("all") AS properties(make string, model string, year string, type string, millage int);
```

Hier volgt het door de gebruiker gedefinieerde Applier-voor beeld:

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

Hieronder vindt U het basis U-SQL-script voor deze door de gebruiker gedefinieerde Applier:

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

In dit scenario voor gebruik fungeert door de gebruiker gedefinieerde Applier als een door komma's gescheiden waarde parser voor de eigenschappen van de auto vloot. De rijen van het invoer bestand zien er als volgt uit:

```
103 Z1AB2CD123XY45889   Ford,Explorer,2005,SUV,152345
303 Y0AB2CD34XY458890   Chevrolet,Cruise,2010,4Dr,32455
210 X5AB2CD45XY458893   Nissan,Altima,2011,4Dr,74000
```

Het is een standaard, tabgescheiden TSV-bestand met een eigenschappen kolom die auto-eigenschappen bevat, zoals merk en model. Deze eigenschappen moeten worden geparseerd naar de tabel kolommen. Met de Applier die wordt geleverd, kunt u ook een dynamisch aantal eigenschappen in de rijenset met resultaten genereren, op basis van de para meter die wordt door gegeven. U kunt alle eigenschappen of een specifieke set eigenschappen genereren.

    …USQL_Programmability.ParserApplier ("all")
    …USQL_Programmability.ParserApplier ("make")
    …USQL_Programmability.ParserApplier ("make&model")

De door de gebruiker gedefinieerde Applier kan worden aangeroepen als een nieuw exemplaar van het Applier-object:

```
CROSS APPLY new MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

Of met het aanroepen van een wrapper-fabrieks methode:

```csharp
    CROSS APPLY MyNameSpace.MyApplier (parameter: "value") AS alias([columns types]…);
```

## <a name="use-user-defined-combiners"></a>Door de gebruiker gedefinieerde combi Naties gebruiken
Met de door de gebruiker gedefinieerde combi natie of UDC kunt u rijen uit de rijen sets links en rechts combi neren op basis van aangepaste logica. Door de gebruiker gedefinieerde combi natie wordt gebruikt met de combi natie-expressie.

Er wordt een combi natie aangeroepen met de combi natie-expressie die de benodigde informatie bevat over de invoer rijen sets, de groeperings kolommen, het verwachte resultaten schema en aanvullende informatie.

Als u een combi natie wilt aanroepen in een base U-SQL-script, gebruikt u de volgende syntaxis:

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

Zie [expressie combi neren (U-SQL)](/u-sql/statements-and-expressions/combine-expression)voor meer informatie.

Voor het definiëren van een door de gebruiker gedefinieerde combi natie moet de `ICombiner` interface worden gemaakt met het`SqlUserDefinedCombiner`kenmerk [], dat optioneel is voor een door de gebruiker gedefinieerde combi Naties definitie.

Basis `ICombiner` klassen definitie:

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

De aangepaste implementatie van een `ICombiner` interface moet de definitie voor een `IEnumerable<IRow>` combi natie van onderdrukking bevatten.

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

Het kenmerk **SqlUserDefinedCombiner** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde combi natie. Deze klasse kan niet worden overgenomen.

**SqlUserDefinedCombiner** wordt gebruikt om de eigenschap van de combine modus te definiëren. Het is een optioneel kenmerk voor een door de gebruiker gedefinieerde combi Naties definitie.

CombinerMode-modus

CombinerMode Enum kan de volgende waarden hebben:

* Volledig (0) elke uitvoer rij is mogelijk afhankelijk van alle invoer rijen van links en rechts met dezelfde sleutel waarde.

* Links (1) elke uitvoermap is afhankelijk van één rij met invoer van links (en mogelijk alle rijen van rechts met dezelfde sleutel waarde).

* Rechts (2) elke uitvoermap is afhankelijk van één invoer rij van rechts (en mogelijk alle rijen vanaf de linkerkant met dezelfde sleutel waarde).

* Binnenste (3) elke uitvoermap is afhankelijk van één invoer rij van links naar rechts en met dezelfde waarde.

Voor beeld:`SqlUserDefinedCombiner(Mode=CombinerMode.Left)`[]


De belangrijkste Programmeer bare objecten zijn:

```csharp
    public override IEnumerable<IRow> Combine(IRowset left, IRowset right,
        IUpdatableRow output
```

Invoer rijen worden door gegeven als het **linker** -en **rechter** `IRowset` type van de interface. Beide rijen sets moeten worden geïnventariseerd voor verwerking. U kunt elke interface één keer opsommen. Daarom moet u deze indien nodig opsommen en opslaan in de cache.

Voor cache doeleinden kunnen we een lijst\<T\> -Type geheugen structuur maken als gevolg van een uitvoering van een LINQ-query, met name `IRow`<>. Het anonieme gegevens type kan ook worden gebruikt tijdens de inventarisatie.

Zie [Inleiding tot LINQ-query's (C#)](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries) voor meer informatie over LINQ-Query's [en\<IEnumerable\> t-interface](/dotnet/api/system.collections.generic.ienumerable-1) voor meer informatie\<over\> IEnumerable t-interface.

Voor het ophalen van de werkelijke gegevens waarden van `IRowset`de binnenkomende, gebruiken we de methode Get ( `IRow` ) van de interface.

```
mycolumn = row.Get<int>("mycolumn")
```

U kunt afzonderlijke kolom namen bepalen door de `IRow` schema methode aan te roepen.

```
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Of gebruik de naam van de schema kolom:

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

Na het opsommen van beide rijen sets gaan we alle rijen door lopen. Voor elke rij in de rijenset links gaan we alle rijen vinden die voldoen aan de voor waarde van onze combi natie.

De uitvoer waarden moeten worden ingesteld met `IUpdatableRow` de uitvoer.

```
output.Set<int>("mycolumn", mycolumn)
```

De daad werkelijke uitvoer wordt geactiveerd door aan te `yield return output.AsReadOnly();`roepen.

Hieronder volgt een combi natie van voor beeld:

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

In dit scenario voor gebruik maken we een analyse rapport voor de detail handelaar. Het doel is om alle producten te vinden die meer dan $20.000 kosten en die de website sneller verkopen dan via de normale leverancier binnen een bepaald tijds bestek.

Dit is het basis U-SQL-script. U kunt de logica vergelijken tussen een gewone samen voeging en een combi natie:

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

Een door de gebruiker gedefinieerde combi natie kan worden aangeroepen als een nieuw exemplaar van het Applier-object:

```
USING new MyNameSpace.MyCombiner();
```


Of met het aanroepen van een wrapper-fabrieks methode:

```
USING MyNameSpace.MyCombiner();
```

## <a name="use-user-defined-reducers"></a>Door de gebruiker gedefinieerde reducties gebruiken

U kunt met behulp van de door de gebruiker gedefinieerde operator Extensibility Framework in C# aangepaste Rijset-verminderingen schrijven en een IReducer-interface implementeren.

Een door de gebruiker gedefinieerde versmaller of UDR kan worden gebruikt om onnodige rijen te elimineren tijdens het ophalen van gegevens (importeren). Het kan ook worden gebruikt voor het bewerken en evalueren van rijen en kolommen. Op basis van de programmeer baarheids logica kan deze ook bepalen welke rijen moeten worden geëxtraheerd.

Als u een UDR-klasse wilt definiëren, moet u `IReducer` een interface met een `SqlUserDefinedReducer` optioneel kenmerk maken.

Deze klasse-interface moet een definitie bevatten voor `IEnumerable` de onderdrukking van de interface-rijenset.

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

Het kenmerk **SqlUserDefinedReducer** geeft aan dat het type moet worden geregistreerd als een door de gebruiker gedefinieerde verminderr. Deze klasse kan niet worden overgenomen.
**SqlUserDefinedReducer** is een optioneel kenmerk voor een door de gebruiker gedefinieerde beperkings definitie. Deze wordt gebruikt voor het definiëren van de eigenschap IsRecursive.

* BOOL IsRecursive    
* **waar** = geeft aan of deze versmaller een associatief en Commutative is

De belangrijkste Programmeer bare objecten zijn **invoer** en **uitvoer**. Het invoer object wordt gebruikt voor het opsommen van invoer rijen. Uitvoer wordt gebruikt om uitvoer rijen in te stellen als gevolg van het verminderen van de activiteit.

Voor de opsomming van invoer rijen gebruiken we de `Row.Get` -methode.

```
foreach (IRow row in input.Rows)
{
    row.Get<string>("mycolumn");
}
```

De para meter voor `Row.Get` de methode is een kolom die wordt door gegeven als onderdeel `PRODUCE` van de klasse `REDUCE` van de instructie van het U-SQL-basis script. Het juiste gegevens type moet hier ook worden gebruikt.

Gebruik de `output.Set` -methode voor uitvoer.

Het is belang rijk om te begrijpen dat aangepaste reduceerere waarden alleen worden uitgevoerd die zijn gedefinieerd `output.Set` met de aanroep van de methode.

```
output.Set<string>("mycolumn", guid);
```

De werkelijke verminderings uitvoer wordt geactiveerd door aan `yield return output.AsReadOnly();`te roepen.

Hier volgt een voor beeld van een reducer:

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

In dit scenario voor gebruik worden rijen met een lege gebruikers naam overs Laan. Voor elke rij in de rijenset wordt elke vereiste kolom gelezen en wordt vervolgens de lengte van de gebruikers naam geëvalueerd. De werkelijke rij wordt alleen uitgevoerd als de waarde van de gebruikers naam meer dan 0 is.

Hieronder volgt een basis-U-SQL-script dat gebruikmaakt van een aangepaste verminderr:

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
