---
title: Azure Data Lake Analytics-accountbeleid beheren
description: Meer informatie over het gebruik van accountbeleid om het gebruik van een Data Lake Analytics-account te beheren, zoals maximale AU's en maximale taken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.assetid: 0a6102d1-7554-4df2-b487-4dae9a7287b6
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 4689714073047e383a53a04bd0069a8a27afdf9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72966435"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Azure Data Lake Analytics beheren met accountbeleid

Met accountbeleid u bepalen hoe resources een Azure Data Lake Analytics-account worden gebruikt. Met dit beleid u de kosten van het gebruik van Azure Data Lake Analytics beheren. Met dit beleid u bijvoorbeeld onverwachte kostenpieken voorkomen door te beperken hoeveel AU's het account tegelijkertijd kan gebruiken.

## <a name="account-level-policies"></a>Beleid op accountniveau

Dit beleid is van toepassing op alle taken in een Data Lake Analytics-account.

### <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximaal aantal AU's in een Data Lake Analytics-account
Een beleid bepaalt het totale aantal Analytics Units (AU's) dat uw Data Lake Analytics-account kan gebruiken. Standaard is de waarde ingesteld op 250. Als deze waarde bijvoorbeeld is ingesteld op 250 AU's, u één taak uitvoeren met 250 AU's toegewezen, of 10 taken met elk 25 AU's. Extra taken die worden ingediend, worden in de wachtrij geplaatst totdat de lopende taken zijn voltooid. Wanneer lopende taken zijn voltooid, worden AU's vrijgemaakt voor de taken in de wachtrij die moeten worden uitgevoerd.

Ga als het gaat om het aantal AU's voor uw Data Lake Analytics-account te wijzigen:

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Limieten en beleid**.
3. Verplaats **onder Maximale AU's**de schuifregelaar om een waarde te selecteren of voer de waarde in het tekstvak in. 
4. Klik op **Opslaan**.

> [!NOTE]
> Als u meer dan de standaardAU's (250) nodig hebt, klikt u in de portal op **Help+Ondersteuning** om een ondersteuningsverzoek in te dienen. Het aantal AU's dat beschikbaar is in uw Data Lake Analytics-account kan worden verhoogd.
>

### <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximaal aantal taken dat tegelijkertijd kan worden uitgevoerd
Dit beleid beperkt het aantal taken dat tegelijkertijd kan worden uitgevoerd. Standaard is deze waarde ingesteld op 20. Als uw Data Lake Analytics AUs beschikbaar heeft, worden nieuwe taken onmiddellijk uitgevoerd totdat het totale aantal lopende taken de waarde van dit beleid heeft bereikt. Wanneer u het maximum aantal taken bereikt dat tegelijkertijd kan worden uitgevoerd, worden volgende taken in de prioriteitsvolgorde in de wachtrij geplaatst totdat een of meer lopende taken zijn voltooid (afhankelijk van de beschikbare AU's).

Ga als het gaat om het aantal taken dat tegelijkertijd kan worden uitgevoerd, te wijzigen:

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Limieten en beleid**.
3. Verplaats **onder Maximum aantal lopende taken**de schuifregelaar om een waarde te selecteren of voer de waarde in het tekstvak in. 
4. Klik op **Opslaan**.

> [!NOTE]
> Als u meer dan het standaardaantal (20) taken moet uitvoeren, klikt u in de portal op **Help+Ondersteuning** om een ondersteuningsverzoek in te dienen. Het aantal taken dat tegelijkertijd kan worden uitgevoerd in uw Data Lake Analytics-account, kan worden verhoogd.
>

### <a name="how-long-to-keep-job-metadata-and-resources"></a>Hoe lang duurt het om metagegevens en resources van vacatures te bewaren 
Wanneer uw gebruikers U-SQL-taken uitvoeren, bewaart de Data Lake Analytics-service alle gerelateerde bestanden. Deze bestanden omvatten het U-SQL-script, de DLL-bestanden waarnaar wordt verwezen in het U-SQL-script, gecompileerde bronnen en statistieken. De bestanden bevinden zich in de /system/map van het standaard Azure Data Lake Storage-account. Met dit beleid bepaalt u hoe lang deze bronnen worden opgeslagen voordat ze automatisch worden verwijderd (de standaardinstelling is 30 dagen). U deze bestanden gebruiken voor foutopsporing en voor het afstemmen van taken die u in de toekomst opnieuw zult uitvoeren.

Ga als u zo lang als u metagegevens en bronnen van de taak wilt bewaren:

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Limieten en beleid**.
3. Verplaats onder **Dagen om taakquery's te behouden,** verplaats de schuifregelaar om een waarde te selecteren of voer de waarde in het tekstvak in.  
4. Klik op **Opslaan**.

## <a name="job-level-policies"></a>Beleid op functieniveau

Met beleid op taakniveau u de maximale AU's beheren en de maximale prioriteit die individuele gebruikers (of leden van specifieke beveiligingsgroepen) kunnen instellen op taken die ze indienen. Met dit beleid u de kosten van gebruikers beheren. Hiermee u ook bepalen wat het effect is dat geplande taken kunnen hebben op productietaken met hoge prioriteit die worden uitgevoerd in hetzelfde Data Lake Analytics-account.

Data Lake Analytics heeft twee beleidsregels die u op taakniveau instellen:

* **AU-limiet per taak**: gebruikers kunnen alleen taken indienen die maximaal dit aantal AU's hebben. Standaard is deze limiet hetzelfde als de maximale AU-limiet voor het account.
* **Prioriteit:** gebruikers kunnen alleen taken indienen met een prioriteit die lager is dan of gelijk is aan deze waarde. Een hoger getal duidt op een lagere prioriteit. Standaard is deze limiet ingesteld op 1, wat de hoogst mogelijke prioriteit is.

Er is een standaardbeleid ingesteld voor elk account. Het standaardbeleid is van toepassing op alle gebruikers van het account. U aanvullende beleidsregels maken voor specifieke gebruikers en groepen. 

> [!NOTE]
> Beleid op accountniveau en beleid op taakniveau zijn gelijktijdig van toepassing.
>

### <a name="add-a-policy-for-a-specific-user-or-group"></a>Een beleid voor een specifieke gebruiker of groep toevoegen

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Limieten en beleid**.
3. Klik **onder Limieten voor het indienen van**vacatures op de knop Beleid **toevoegen.** Selecteer of voer vervolgens de volgende instellingen in:
    1. **Naam van het rekenbeleid:** voer een beleidsnaam in om u te herinneren aan het doel van het beleid.
    2. **Selecteer Gebruiker of groep:** selecteer de gebruiker of groep op dit beleid.
    3. **Stel de AU-limiet voor werk**in: stel de AU-limiet in die van toepassing is op de geselecteerde gebruiker of groep.
    4. **De prioriteitslimiet instellen:** stel de prioriteitslimiet in die van toepassing is op de geselecteerde gebruiker of groep.

4. Klik op **Ok**.

5. Het nieuwe beleid wordt vermeld in de tabel **Standaardbeleid** onder **Limieten voor het indienen van vacatures**. 

### <a name="delete-or-edit-an-existing-policy"></a>Een bestaand beleid verwijderen of bewerken

1. Ga in de Azure-portal naar uw Data Lake Analytics-account.
2. Klik **op Limieten en beleid**.
3. Zoek **onder Limieten voor het indienen van**vacatures het beleid dat u wilt bewerken.
4.  Als u de opties **Verwijderen** en Bewerken wilt **weergeven,** klikt u in de meest rechtse kolom van de tabel op `...`.

## <a name="additional-resources-for-job-policies"></a>Aanvullende middelen voor taakbeleid
* [Blogbericht beleidsoverzicht](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-overview/)
* [Blogbericht op accountniveau](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy/)
* [Blogbericht op taakniveau](https://blogs.msdn.microsoft.com/azuredatalake/2017/06/08/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy/)

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Data Lake Analytics](data-lake-analytics-overview.md)
* [Aan de slag met Data Lake Analytics met behulp van de Azure-portal](data-lake-analytics-get-started-portal.md)
* [Azure Data Lake Analytics beheren met Azure PowerShell](data-lake-analytics-manage-use-powershell.md)