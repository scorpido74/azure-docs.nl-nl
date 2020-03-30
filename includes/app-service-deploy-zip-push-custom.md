---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 79fb8517ec6880e8a3eae0e74275567a24644b87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175838"
---
## <a name="deployment-customization"></a>Implementatieaanpassing

Het implementatieproces gaat ervan uit dat het .zip-bestand dat u pusht een kant-en-klare app bevat. Standaard worden er geen aanpassingen uitgevoerd. Als u dezelfde buildprocessen wilt inschakelen die u met continue integratie krijgt, voegt u het volgende toe aan de toepassingsinstellingen:

    SCM_DO_BUILD_DURING_DEPLOYMENT=true 

Wanneer u .zip push-implementatie gebruikt, is deze instelling standaard **onwaar.** De standaardinstelling **geldt** voor implementaties voor continue integratie. Wanneer u bent ingesteld op **true,** worden uw implementatiegerelateerde instellingen gebruikt tijdens de implementatie. U deze instellingen configureren als app-instellingen of in een configuratiebestand met .deployment dat zich in de hoofdmap van uw .zip-bestand bevindt. Zie [Repository- en implementatiegerelateerde instellingen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#repository-and-deployment-related-settings) voor meer informatie in de implementatieverwijzing.