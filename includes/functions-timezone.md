---
ms.openlocfilehash: b4fbeb6baa4516ca4cf3ca6194195fae2c688b07
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165652"
---
De standaardtijd zone die wordt gebruikt in de CRON-expressies is Coordinated Universal Time (UTC). Als u de CRON-expressie op basis van een andere tijd zone wilt gebruiken, maakt u een app-instelling voor de functie-app met de naam `WEBSITE_TIME_ZONE` . 

De waarde van deze instelling is afhankelijk van het besturings systeem en het plan waarop de functie-app wordt uitgevoerd.

|Besturingssysteem |Plannen |Waarde |
|-|-|-|
| **Windows** |Alles | Stel de waarde in op de naam van de gewenste tijd zone, zoals wordt weer gegeven in [micro soft time zone index] ( https://docs.microsoft.com/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10) . |
| **Linux** |Premium<br/>Toegewezen |Stel de waarde in op de naam van de gewenste tijd zone, zoals weer gegeven in de [TZ-data base](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). |

> [!NOTE]
> `WEBSITE_TIME_ZONE`wordt momenteel niet ondersteund in het verbruiks abonnement voor Linux.

*Eastern (standaard tijd* ) (Windows) of *America/New_York* (Linux) is bijvoorbeeld UTC-05:00. Als u wilt dat uw timer trigger wordt geactiveerd om 10:00 uur EST elke dag, gebruikt u de volgende NCRONTAB-expressie die accounts voor UTC-tijd zone:

```
"0 0 15 * * *"
``` 

Of maak een app-instelling voor de naam van de functie `WEBSITE_TIME_ZONE` -app, stel de waarde in op `Eastern Standard Time` (Windows) of `America/New_York` (Linux) en gebruik vervolgens de volgende NCRONTAB-expressie: 

```
"0 0 10 * * *"
``` 

Wanneer u gebruikt `WEBSITE_TIME_ZONE` , wordt de tijd aangepast voor tijd wijzigingen in de specifieke tijd zone, zoals zomer tijd. 
