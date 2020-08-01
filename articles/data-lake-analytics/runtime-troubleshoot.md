---
title: Problemen met de Azure Data Lake Analytics U-SQL runtime-fouten oplossen
description: Meer informatie over het oplossen van fouten in U-SQL-runtime.
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/10/2019
ms.openlocfilehash: 39b4a3bc5e5f70a5699f4fd84ec86dc61cf21483
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475257"
---
# <a name="learn-how-to-troubleshoot-u-sql-runtime-failures-due-to-runtime-changes"></a>Meer informatie over het oplossen van problemen met U-SQL-runtime als gevolg van runtime wijzigingen

De Azure Data Lake U-SQL-runtime, met inbegrip van compiler, Optimizer en taak beheer, is wat uw U-SQL-code verwerkt.

## <a name="choosing-your-u-sql-runtime-version"></a>Uw U-SQL-runtime versie kiezen

Wanneer u u-SQL-taken vanuit Visual Studio, de ADL-SDK of de Azure Data Lake Analytics Portal verzendt, gebruikt uw taak de momenteel beschik bare standaard runtime. Nieuwe versies van de U-SQL-runtime worden regel matig vrijgegeven en bevatten kleine updates en beveiligings oplossingen.

U kunt ook een aangepaste runtime versie kiezen. omdat u een nieuwe update wilt uitproberen, moet u een oudere versie van een runtime blijven of zijn voorzien van een hotfix voor een gemeld probleem waarin u niet kunt wachten op de gewone nieuwe update.

> [!CAUTION]
> Het kiezen van een runtime die afwijkt van de standaard waarde, is de kans om uw U-SQL-taken te verbreken. Gebruik deze andere versies alleen voor test doeleinden.

In zeldzame gevallen kan Microsoft Ondersteuning een andere versie van een runtime vastmaken als de standaard waarde voor uw account. Zorg ervoor dat u deze pincode zo snel mogelijk herstelt. Als u vastmaakt aan die versie, verloopt deze op een later tijdstip.

### <a name="monitoring-your-jobs-u-sql-runtime-version"></a>De runtime-versie van uw taken controleren

U kunt de geschiedenis bekijken van welke runtime versie uw vorige taken hebben gebruikt in de taak geschiedenis van uw account via de job browser van Visual Studio of de taak geschiedenis van de Azure Portal.

1. Ga in het Azure Portal naar uw Data Lake Analytics-account.
2. Selecteer **alle taken weer geven**. Er wordt een lijst weer gegeven met alle actieve en recent voltooide taken in het account.
3. Klik desgewenst op **filter** om u te helpen de taken te vinden op **tijds bereik**, **taak naam**en waarden voor **Auteur** .
4. U kunt de runtime zien die wordt gebruikt in de voltooide taken.

![De runtime versie van een eerdere taak weer geven](./media/runtime-troubleshoot/prior-job-usql-runtime-version-.png)

De beschik bare runtime versies veranderen in de loop van de tijd. De standaard runtime wordt altijd ' default ' genoemd en we blijven ten minste de vorige runtime voor enige tijd beschikbaar, en u kunt speciale Runtimes om verschillende redenen beschikbaar maken. Expliciet benoemde Runtimes volgen doorgaans de volgende indeling (cursief worden gebruikt voor variabele onderdelen en [] duidt optionele onderdelen aan):

release_YYYYMMDD_adl_buildno [_modifier]

Release_20190318_adl_3394512_2 betekent bijvoorbeeld dat de tweede versie van de build 3394512 van de runtime-versie van maart 18 2019 en release_20190318_adl_3394512_private een persoonlijke build van dezelfde release. Opmerking: de datum is gerelateerd aan wanneer de laatste controle is uitgevoerd voor die versie en niet noodzakelijkerwijs de officiële release datum.


## <a name="troubleshooting-u-sql-runtime-version-issues"></a>Problemen met de versie van SQL runtime oplossen

Er zijn twee mogelijke runtime-versie problemen die kunnen optreden:

1. Een script of gebruikers code verandert het gedrag van een release in de volgende. Dergelijke breuk wijzigingen worden normaal gesp roken vooraf door gegeven aan de publicatie van release opmerkingen. Als u een dergelijke wijziging tegen komt, neemt u contact op met Microsoft Ondersteuning om dit gedrag te melden (als dit nog niet is gedocumenteerd) en verzendt u uw taken voor de oudere runtime versie.

2. U hebt een niet-standaard runtime gebruikt, hetzij expliciet, impliciet als deze is vastgemaakt aan uw account en deze runtime is na enige tijd verwijderd. Als er ontbrekende Runtimes optreden, moet u uw scripts bijwerken om uit te voeren met de huidige standaard runtime. Als u meer tijd nodig hebt, neemt u contact op met Microsoft Ondersteuning

## <a name="see-also"></a>Zie tevens

- [Overzicht van Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Azure Data Lake Analytics beheren met Azure Portal](data-lake-analytics-manage-use-portal.md)
- [Taken in Azure Data Lake Analytics bewaken met behulp van de Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
