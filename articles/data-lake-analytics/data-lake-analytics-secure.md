---
title: Azure Data Lake Analytics beveiligen voor meerdere gebruikers
description: Meer informatie over het configureren van meerdere gebruikers voor het uitvoeren van taken in Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "60813367"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Gebruikers toegang tot taak gegevens configureren voor taak gegevens in Azure Data Lake Analytics 

In Azure Data Lake Analytics kunt u meerdere gebruikers accounts of service-principals gebruiken om taken uit te voeren. 

Als u wilt dat dezelfde gebruikers de gedetailleerde taak gegevens zien, moeten de gebruikers de inhoud van de taak mappen kunnen lezen. De taak mappen bevinden zich in de `/system/` map. 

Als de benodigde machtigingen niet zijn geconfigureerd, ziet de gebruiker mogelijk een fout melding:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Gebruikers toegang tot taak gegevens configureren

U kunt de **wizard gebruiker toevoegen** gebruiken om de acl's te configureren voor de mappen. Zie [een nieuwe gebruiker toevoegen](data-lake-analytics-manage-use-portal.md#add-a-new-user)voor meer informatie.

Als u meer gedetailleerde controle nodig hebt of de machtigingen wilt scripteren, kunt u de mappen als volgt beveiligen:

1. **Uitvoerings** machtigingen verlenen (via een toegangs-ACL) in de hoofdmap:
   - /
   
2. Verleen de machtigingen **uitvoeren** en **lezen** (via een toegangs-ACL en een standaard-ACL) voor de mappen die de taak mappen bevatten. Voor een specifieke taak die op 25 mei 2018 is uitgevoerd, moeten deze mappen bijvoorbeeld worden geopend:
   - /systeemstatus
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Volgende stappen
[Een nieuwe gebruiker toevoegen](data-lake-analytics-manage-use-portal.md#add-a-new-user)
