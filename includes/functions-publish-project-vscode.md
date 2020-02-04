---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 8c63d314c253152d2815a70831870fe331071c68
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964088"
---
## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en gerelateerde resources in uw Azure-abonnement en implementeert u vervolgens uw code. 

1. Kies het pictogram van Azure in de activiteiten balk en kies in het gebied **Azure: functions** de knop **implementeren in functie app...** .

    ![Uw project naar Azure publiceren](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    + **Abonnement selecteren**: Kies het abonnement dat u wilt gebruiken. U ziet deze niet als u slechts één abonnement hebt.

    + **Selecteer functie-app in azure**: Kies `+ Create new Function App` (niet `Advanced`). Dit artikel biedt geen ondersteuning voor de [Geavanceerde publicatie stroom](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 
    
    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Selecteer een runtime**: Kies de versie van python die lokaal wordt uitgevoerd. U kunt de `python --version` opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecteer een runtime**: Kies de versie van node. js die u lokaal uitvoert. U kunt de `node --version` opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    + **Selecteer een locatie voor nieuwe resources**: Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
1.  Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt:

    + **[Resource groep](../articles/azure-resource-manager/management/overview.md)** : bevat alle gemaakte Azure-resources. De naam is gebaseerd op de naam van uw functie-app.
    + **[Opslag account](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : een standaard opslag account wordt gemaakt met een unieke naam die is gebaseerd op de naam van uw functie-app.
    + **[Hosting plan](../articles/azure-functions/functions-scale.md)** : er wordt een verbruiks abonnement in de regio vs West gemaakt om uw serverloze functie-app te hosten.
    + **Functie-app**: uw project wordt geïmplementeerd naar en wordt uitgevoerd in deze nieuwe functie-app.
    + **[Application Insights]()** : een exemplaar, dat is verbonden met uw functie-app, wordt gemaakt op basis van de functie naam.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 
    
1. Selecteer **uitvoer weer geven** in deze melding om de resultaten voor het maken en implementeren weer te geven, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u in de rechter benedenhoek het klok pictogram om het weer te geven.

    ![Volledige melding maken](media/functions-publish-project-vscode/function-create-notifications.png)
