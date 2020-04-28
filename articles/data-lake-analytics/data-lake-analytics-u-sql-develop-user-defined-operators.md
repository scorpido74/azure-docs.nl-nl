---
title: Door de gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen-Azure Data Lake Analytics
description: Meer informatie over het ontwikkelen van door de gebruiker gedefinieerde Opera tors die moeten worden gebruikt en opnieuw gebruikt in Azure Data Lake Analytics taken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "71316545"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Door de gebruiker gedefinieerde U-SQL-Opera tors ontwikkelen (Udo's)
In dit artikel wordt beschreven hoe u door de gebruiker gedefinieerde Opera tors ontwikkelt om gegevens in een U-SQL-taak te verwerken.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Een door de gebruiker gedefinieerde operator definiëren en gebruiken in U-SQL
**Een U-SQL-taak maken en verzenden**

1. Selecteer in Visual Studio **File > New > project > U-SQL project**.
2. Klik op **OK**. Visual Studio maakt een oplossing met een script. usql-bestand.
3. Vouw in **Solution Explorer**script. usql uit en dubbel klik vervolgens op **script.usql.cs**.
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
6. Open **script. usql**en plak het volgende U-SQL-script:

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
10. Als u nog geen verbinding hebt gemaakt met uw Azure-abonnement, wordt u gevraagd om uw Azure-account referenties in te voeren.
11. Klik op **verzenden**. De inzendings resultaten en taak koppeling zijn beschikbaar in het resultaten venster wanneer de verzen ding is voltooid.
12. Klik op de knop **vernieuwen** om de nieuwste taak status weer te geven en het scherm te vernieuwen.

**Als u de uitvoer wilt bekijken**

1. Vouw in **Server Explorer** **Azure**uit, vouw **Data Lake Analytics**uit, vouw uw data Lake Analytics account uit, vouw **opslag accounts**uit, klik met de rechter muisknop op de standaard opslag en klik vervolgens op **Explorer**.
2. Vouw voor beelden uit, vouw uitvoer uit en dubbel klik vervolgens op **Stuur Programma's. CSV**.

## <a name="see-also"></a>Zie ook
* [U-SQL-expressies uitbreiden met gebruikers code](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Data Lake-Hulpprogram Ma's voor Visual Studio gebruiken voor het ontwikkelen van U-SQL-toepassingen](data-lake-analytics-data-lake-tools-get-started.md)
