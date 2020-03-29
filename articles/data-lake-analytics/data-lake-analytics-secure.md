---
title: Azure Data Lake Analytics beveiligen voor meerdere gebruikers
description: Meer informatie over het configureren van meerdere gebruikers om taken uit te voeren in Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813367"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Gebruikerstoegang tot taakgegevens configureren voor taakgegevens in Azure Data Lake Analytics 

In Azure Data Lake Analytics u meerdere gebruikersaccounts of serviceprincipals gebruiken om taken uit te voeren. 

Om dezelfde gebruikers de gedetailleerde taakinformatie te laten zien, moeten de gebruikers de inhoud van de taakmappen kunnen lezen. De taakmappen bevinden `/system/` zich in de map. 

Als de benodigde machtigingen niet zijn geconfigureerd, kan de gebruiker een fout zien:`Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Gebruikerstoegang tot taakgegevens configureren

U de **wizard Gebruiker toevoegen** gebruiken om de ACL's op de mappen te configureren. Zie [Een nieuwe gebruiker toevoegen voor](data-lake-analytics-manage-use-portal.md#add-a-new-user)meer informatie.

Als u meer gedetailleerde besturingselement nodig hebt of de machtigingen moet scripten, beveiligt u de mappen als volgt:

1. Machtigingen **voor uitvoeren** (via een acl-toegang) verlenen op de hoofdmap:
   - /
   
2. Machtigingen **voor uitvoeren** en **lezen** (via een acl-toegang en een standaardACL) verlenen op de mappen die de taakmappen bevatten. Voor een specifieke taak die op 25 mei 2018 is uitgevoerd, moeten deze mappen bijvoorbeeld worden geopend:
   - /systeem
   - /systeem/jobservice
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
