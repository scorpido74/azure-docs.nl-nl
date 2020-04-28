---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175838"
---
## <a name="deployment-customization"></a>Implementatie aanpassing

Bij het implementatie proces wordt ervan uitgegaan dat het zip-bestand dat u pusht, een kant-en-klare app bevat. Standaard worden er geen aanpassingen uitgevoerd. Voeg het volgende toe aan de toepassings instellingen om dezelfde bouw processen in te scha kelen die u met continue integratie krijgt:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Wanneer u. zip push-implementatie gebruikt, is deze instelling standaard **Onwaar** . De standaard waarde is **True** voor continue integratie-implementaties. Als deze waarde is ingesteld op **True**, worden de implementatie-instellingen gebruikt tijdens de implementatie. U kunt deze instellingen configureren als app-instellingen of in een. implementatie configuratie bestand dat zich in de hoofdmap van uw zip-bestand bevindt. Zie instellingen voor opslag plaatsen [en implementaties](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) in de naslag informatie over de implementatie.