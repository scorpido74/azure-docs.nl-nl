---
title: De U-SQL-runtime-fouten van Azure Data Lake Analytics oplossen
description: Meer informatie over het oplossen van U-SQL runtime-fouten.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 1e3fb218e6cda5619bfa1a0936e07d6731a9cc93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648452"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Meer informatie over het oplossen van U-SQL-runtime-fouten als gevolg van wijzigingen in de runtime

De Azure Data Lake U-SQL-runtime, inclusief de compiler, optimizer en jobmanager, is wat uw U-SQL-code verwerkt.

## <a name="choosing-your-u-sql-runtime-version"></a>Uw U-SQL runtime-versie kiezen

Wanneer u U-SQL-taken indient van Visual Studio, de ADL SDK of de Azure Data Lake Analytics-portal, gebruikt uw taak de momenteel beschikbare standaardruntime. Nieuwe versies van de U-SQL runtime worden regelmatig uitgebracht en bevatten zowel kleine updates als beveiligingsoplossingen.

U ook een aangepaste runtime-versie kiezen; ofwel omdat u een nieuwe update wilt uitproberen, op een oudere versie van een runtime wilt blijven of een hotfix wilt krijgen voor een gerapporteerd probleem waarbij u niet wachten op de reguliere nieuwe update.

> [!CAUTION]
> Het kiezen van een runtime die anders is dan de standaard, heeft de potentie om uw U-SQL-taken te verbreken. Gebruik deze andere versies alleen voor het testen.

In zeldzame gevallen kan Microsoft Support een andere versie van een runtime vastmaken als standaardvoor uw account. Zorg ervoor dat u deze pin zo snel mogelijk terugzet. Als u vastblijft aan die versie, vervalt deze op een later tijdstip.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>Uw taken u-SQL runtime-versie bewaken

U de geschiedenis zien van welke runtime-versie uw eerdere taken in de taakgeschiedenis van uw account hebben gebruikt, via de taakbrowser van de Visual Studio of de taakgeschiedenis van de Azure-portal.

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Selecteer **Alle taken weergeven**. Er verschijnt een lijst met alle actieve en recent voltooide taken in het account.
3. Klik optioneel op **Filter** om u te helpen de taken te vinden op **tijdbereik,** **taaknaam**en **auteurswaarden.**
4. U de runtime zien die wordt gebruikt in de voltooide taken.

![De runtime-versie van een eerdere taak weergeven](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

De beschikbare runtime-versies veranderen in de loop van de tijd. De standaard runtime wordt altijd "standaard" genoemd en we houden ten minste de vorige runtime enige tijd beschikbaar en maken speciale runtimes beschikbaar om verschillende redenen. Expliciet benoemde runtimes volgen over het algemeen de volgende indeling (cursief worden gebruikt voor variabele onderdelen en [] geeft optionele onderdelen aan):

release_YYYYMMDD_adl_buildno[_modifier]

Zo betekent release_20190318_adl_3394512_2 de tweede versie van de build 3394512 van de runtime-release van 18 maart 2019 en betekent release_20190318_adl_3394512_private een private build van dezelfde release. Opmerking: De datum is gerelateerd aan wanneer de laatste check-in is genomen voor die release en niet noodzakelijkerwijs de officiële releasedatum.

Hieronder volgen de momenteel beschikbare runtime-versies.

- release_20190318_adl_3394512
- release_20190318_adl_5832669 de huidige standaardinstelling
- release_20190703_adl_4713356

## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Problemen met u-SQL-runtime-versies oplossen

Er zijn twee mogelijke runtime-versieproblemen die u tegenkomen:

1. Een script of een gebruikerscode verandert het gedrag van de ene release naar de volgende. Dergelijke baanbrekende wijzigingen worden normaal gesproken van tevoren gecommuniceerd met de publicatie van releasenotes. Als u zo'n baanbrekende wijziging tegenkomt, neem dan contact op met Microsoft Support om dit breekgedrag te melden (voor het geval het nog niet is gedocumenteerd) en dien uw taken in tegen de oudere runtime-versie.

2. U hebt een niet-standaard runtime expliciet of impliciet gebruikt wanneer deze is vastgemaakt aan uw account en die runtime na enige tijd is verwijderd. Als u ontbrekende runtimes tegenkomt, moet u uw scripts upgraden om te worden uitgevoerd met de huidige standaardruntime. Als u extra tijd nodig hebt, neemt u contact op met Microsoft Support

## <a name="see-also"></a>Zie ook

- [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics beheren met Azure-portal](data-lake-analytics-manage-use-portal.md)
- [Taken in Azure Data Lake Analytics bewaken met de Azure-portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
