---
title: U-SQL-gebruikersdefined operators ontwikkelen - Azure Data Lake Analytics
description: Meer informatie over het ontwikkelen van door gebruikers gedefinieerde operators die kunnen worden gebruikt en hergebruikt in Azure Data Lake Analytics-taken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316545"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL-gebruikersdefined operators ontwikkelen (UDO's)
In dit artikel wordt beschreven hoe u door gebruikers gedefinieerde operatoren ontwikkelen om gegevens in een U-SQL-taak te verwerken.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Een door de gebruiker gedefinieerde operator definiëren en gebruiken in U-SQL
**Een U-SQL-taak maken en verzenden**

1. Selecteer in de Visual Studio **Bestand > Nieuwe > Project > U-SQL-project**.
2. Klik op **OK**. Visual Studio maakt een oplossing met een Script.usql-bestand.
3. Vouw vanuit **Solution Explorer**Script.usql uit en dubbelklik op **Script.usql.cs**.
4. Plak de volgende code in het bestand:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Open **Script.usql**en plak het volgende U-SQL-script:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Geef het Data Lake Analytics-account, de database en het schema op.
8. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Build Script**.
9. Ga naar **Solution Explorer**, klik met de rechtermuisknop op **Script.usql** en klik vervolgens op **Submit Script**.
10. Als u geen verbinding hebt gemaakt met uw Azure-abonnement, wordt u gevraagd uw Azure-accountreferenties in te voeren.
11. Klik **op Verzenden**. Indieningsresultaten en taakkoppeling zijn beschikbaar in het venster Resultaten wanneer de indiening is voltooid.
12. Klik **op** de knop Vernieuwen om de nieuwste taakstatus te bekijken en het scherm te vernieuwen.

**De uitvoer bekijken**

1. Vanuit **Server Explorer,** **azure**uitbreiden, Data **Lake Analytics**uitbreiden, uw Data Lake Analytics-account uitbreiden, **Opslagaccounts**uitbreiden, met de rechtermuisknop op de standaardopslag klikken en vervolgens op **Explorer**klikken.
2. Voorbeelden uitvouwen, Uitvoer uitvouwen en dubbelklik vervolgens op **Drivers.csv**.

## <a name="see-also"></a>Zie ook
* [U-SQL-expressies uitbreiden met usercode](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Data Lake-hulpprogramma's gebruiken voor Visual Studio voor het ontwikkelen van U-SQL-toepassingen](data-lake-analytics-data-lake-tools-get-started.md)
