---
title: Taakruntime-omgevingsvariabelen - Azure Batch | Microsoft Documenten
description: Richtlijnen en verwijzing naar variabele richtlijnen voor azure batchanalytics voor taakruntime-omgeving.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 09/12/2019
ms.author: labrenne
ms.openlocfilehash: ebaa06acf309a0f941b8b4efd76fa4e9e7460810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053951"
---
# <a name="azure-batch-runtime-environment-variables"></a>Runtime-omgevingsvariabelen voor Azure Batch

De [Azure Batch-service](https://azure.microsoft.com/services/batch/) stelt de volgende omgevingsvariabelen in op compute nodes. U deze omgevingsvariabelen verwijzen in taakopdrachtregels en in de programma's en scripts die worden uitgevoerd door de opdrachtregels.

Zie [Omgevingsinstellingen voor taken voor](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks)meer informatie over het gebruik van omgevingsvariabelen met Batch.

## <a name="environment-variable-visibility"></a>Zichtbaarheid van omgevingsvariabele

Deze omgevingsvariabelen zijn alleen zichtbaar in de context van de **taakgebruiker,** het gebruikersaccount op het knooppunt waaronder een taak wordt uitgevoerd. U ziet deze *niet* als u [extern verbinding maakt](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) met een rekenknooppunt via Remote Desktop Protocol (RDP) of Secure Shell (SSH) en de omgevingsvariabelen weergeeft. Dit komt doordat het gebruikersaccount dat voor de externe verbinding wordt gebruikt, niet hetzelfde is als het account dat door de taak wordt gebruikt.

Als u de huidige waarde van `cmd.exe` een omgevingsvariabele wilt `/bin/sh` krijgen, start u op een Windows-compute-knooppunt of op een Linux-knooppunt:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Opdrachtregeluitbreiding van omgevingsvariabelen

De opdrachtregels die worden uitgevoerd door taken op compute nodes worden niet onder een shell uitgevoerd. Daarom kunnen deze opdrachtregels niet in eigen land profiteren van shell-functies, zoals omgevingsvariabele uitbreiding (dit omvat de). `PATH` Als u van dergelijke functies wilt profiteren, moet u de shell in de opdrachtregel **aanroepen.** Start `cmd.exe` bijvoorbeeld op Windows compute `/bin/sh` nodes of op Linux-knooppunten:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Omgevingsvariabelen

| Naam van de variabele                     | Beschrijving                                                              | Beschikbaarheid | Voorbeeld |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | De naam van het batchaccount waartoe de taak behoort.                  | Alle taken.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | De URL van het batchaccount. | Alle taken. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Een voorvoegsel van alle variabelen voor de app-pakketomgeving. Als toepassingsversie 'FOO'-versie '1' bijvoorbeeld op een pool is geïnstalleerd, wordt de omgevingsvariabele AZ_BATCH_APP_PACKAGE_FOO_1 (op Linux) of AZ_BATCH_APP_PACKAGE_FOO#1 (op Windows). AZ_BATCH_APP_PACKAGE_FOO_1 wijst naar de locatie waar het pakket is gedownload (een map). Wanneer u de standaardversie van het app-pakket gebruikt, gebruikt u de AZ_BATCH_APP_PACKAGE omgevingsvariabele zonder de versienummers. Als in Linux, en de naam van het applicatiepakket is "Agent-linux-x64" en de versie is "1.1.46.0, de naam van de omgeving is eigenlijk: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, met behulp van underscores en kleine letters. Klik [hier](https://docs.microsoft.com/azure/batch/batch-application-packages#execute-the-installed-applications) voor meer informatie. | Elke taak met een bijbehorend app-pakket. Ook beschikbaar voor alle taken als het knooppunt zelf toepassingspakketten heeft. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) of AZ_BATCH_APP_PACKAGE_FOO #1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Een verificatietoken dat toegang verleent tot een beperkte set batchservicebewerkingen. Deze omgevingsvariabele is alleen aanwezig als de [verificatieTokenInstellingen](/rest/api/batchservice/task/add#authenticationtokensettings) worden ingesteld wanneer de [taak wordt toegevoegd.](/rest/api/batchservice/task/add#request-body) De tokenwaarde wordt in de Batch-API's gebruikt als referenties om een Batch-client te maken, zoals in de [BatchClient.Open() .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Alle taken. | OAuth2-toegangstoken |
| AZ_BATCH_CERTIFICATES_DIR       | Een map in de [werkmap voor taken][files_dirs] waarin certificaten zijn opgeslagen voor Linux-compute nodes. Deze omgevingsvariabele is niet van toepassing op Windows-compute nodes.                                                  | Alle taken.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | De lijst met knooppunten die zijn toegewezen aan een `nodeIP,nodeIP`taak met meerdere [instanties][multi_instance] in de indeling . | Primaire en subtaken met meerdere taken. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Hiermee geeft u op of het huidige knooppunt het hoofdknooppunt is voor een [taak met meerdere instanties][multi_instance]. Mogelijke waarden `true` `false`zijn en .| Primaire en subtaken met meerdere taken. | `true` |
| AZ_BATCH_JOB_ID                 | De ID van de job waartoe de taak behoort. | Alle taken behalve starttaak. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Het volledige pad van de [taakmap taakvoorbereiding][files_dirs] op het knooppunt. | Alle taken behalve de taak- en taakvoorbereidingstaak starten. Alleen beschikbaar als de taak is geconfigureerd met een taakvoorbereidingstaak. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Het volledige pad van de werklijst voor [taakvoorbereiding op][files_dirs] het knooppunt. | Alle taken behalve de taak- en taakvoorbereidingstaak starten. Alleen beschikbaar als de taak is geconfigureerd met een taakvoorbereidingstaak. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | Het IP-adres en de poort van het compute-knooppunt waarop de primaire taak van een [taak met meerdere instanties][multi_instance] wordt uitgevoerd. | Primaire en subtaken met meerdere taken. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | De id van het knooppunt waaraan de taak is toegewezen. | Alle taken. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Als `true`, is het huidige knooppunt een speciaal knooppunt. Als `false`, het is een [node met lage prioriteit](batch-low-pri-vms.md). | Alle taken. | `true` |
| AZ_BATCH_NODE_LIST              | De lijst met knooppunten die zijn toegewezen aan een `nodeIP;nodeIP`taak met meerdere [instanties][multi_instance] in de indeling . | Primaire en subtaken met meerdere taken. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Het volledige pad van het node level [bestandssysteem mount](virtual-file-mount.md) locatie waar alle mount mappen bevinden. Windows-bestandsshares gebruiken een stationsletter, dus voor Windows maakt het mount-station deel uit van apparaten en stations.  |  Alle taken, inclusief starttaak, hebben toegang tot de gebruiker, aangezien de gebruiker op de hoogte is van de mount-machtigingen voor de gemonteerde map. | In Ubuntu, bijvoorbeeld, de locatie is:`/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Het volledige pad van de wortel van alle [batchmappen][files_dirs] op het knooppunt. | Alle taken. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Het volledige pad van de [gedeelde map][files_dirs] op het knooppunt. Alle taken die op een knooppunt worden uitgevoerd, hebben lees-/schrijftoegang tot deze map. Taken die op andere knooppunten worden uitgevoerd, hebben geen externe toegang tot deze map (het is geen 'gedeelde' netwerkmap). | Alle taken. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Het volledige pad van de [taakmap starten][files_dirs] op het knooppunt. | Alle taken. | C:\user\tasks\opstarten |
| AZ_BATCH_POOL_ID                | De ID van de pool waarin de taak wordt uitgevoerd. | Alle taken. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Het volledige pad van de [taakmap][files_dirs] op het knooppunt. Deze map `stdout.txt` bevat `stderr.txt` de en voor de taak, en de AZ_BATCH_TASK_WORKING_DIR. | Alle taken. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | De id van de huidige taak. | Alle taken behalve starttaak. | taak001 |
| AZ_BATCH_TASK_SHARED_DIR | Een mappad dat identiek is voor de primaire taak en elke subtaak van een [taak met meerdere instanties.][multi_instance] Het pad bestaat op elk knooppunt waarop de taak met meerdere instancewordt uitgevoerd en is gelezen/schrijven toegankelijk voor de taakopdrachten die op dat knooppunt worden uitgevoerd (zowel de [coördinatieopdracht][coord_cmd] als de [toepassingsopdracht).][app_cmd] Subtaken of een primaire taak die op andere knooppunten worden uitgevoerd, hebben geen externe toegang tot deze map (het is geen 'gedeelde' netwerkmap). | Primaire en subtaken met meerdere taken. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletaak |
| AZ_BATCH_TASK_WORKING_DIR       | Het volledige pad van de [taakwerkmap][files_dirs] op het knooppunt. De momenteel lopende taak heeft lees-/schrijftoegang tot deze map. | Alle taken. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | De lijst met knooppunten en het aantal kernen per knooppunt die zijn toegewezen aan een [taak met meerdere instanties][multi_instance]. Knooppunten en kernen worden weergegeven in de indeling`numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, waarbij het aantal knooppunten wordt gevolgd door een of meer ip-adressen van knooppunten en het aantal kernen voor elk. |  Primaire en subtaken met meerdere taken. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
