---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: f1553a5c9d55366b2764877b48d0606ff8e0b370
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76842132"
---
## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en gerelateerde resources in uw Azure-abonnement en implementeert u vervolgens uw code. 

1. Kies het pictogram van Azure in de activiteiten balk en kies in het gebied **Azure: functions** de knop **implementeren in functie app...** .

    ![Uw project naar Azure publiceren](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    ::: zone pivot="programming-language-csharp,programming-language-powershell"

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----- |
    | Abonnement selecteren | Uw abonnement | Wordt weer gegeven wanneer u meerdere abonnementen hebt. |
    | functie-app selecteren in azure | + Nieuwe functie-app maken | Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. |
    | Voer een wereld wijd unieke naam in voor de functie-app | Unieke naam | Geldige tekens voor de naam van en functie-app zijn `a-z`, `0-9` en `-`. |
    | Selecteer een locatie voor nieuwe resources | Regio | Kies een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. | 

    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python"

    | Vraag | Waarde | Beschrijving |
    | ------ | ----- | ----- |
    | Abonnement selecteren | Uw abonnement | Wordt weer gegeven wanneer u meerdere abonnementen hebt. |
    | functie-app selecteren in azure | + Nieuwe functie-app maken | Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. |
    | Voer een wereld wijd unieke naam in voor de functie-app | Unieke naam | Geldige tekens voor de naam van en functie-app zijn `a-z`, `0-9` en `-`. |
    | Een runtime selecteren | Uw versie | Kies de taal versie die u lokaal uitvoert. |
    | Selecteer een locatie voor nieuwe resources | Regio | Kies een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. | 

    ::: zone-end

    
1.  Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt:

    + **[Resource groep](../articles/azure-resource-manager/management/overview.md)** : bevat alle gemaakte Azure-resources. De naam is gebaseerd op de naam van uw functie-app.
    + **[Opslag account](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)** : een standaard opslag account wordt gemaakt met een unieke naam die is gebaseerd op de naam van uw functie-app.
    + **[Hosting plan](../articles/azure-functions/functions-scale.md)** : er wordt een verbruiks abonnement in de regio vs West gemaakt om uw serverloze functie-app te hosten.
    + **Functie-app**: uw project wordt geïmplementeerd naar en wordt uitgevoerd in deze nieuwe functie-app.
    + **[Application Insights]()** : een exemplaar, dat is verbonden met uw functie-app, wordt gemaakt op basis van de functie naam.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 
    
1. Selecteer **uitvoer weer geven** in deze melding om de resultaten voor het maken en implementeren weer te geven, inclusief de Azure-resources die u hebt gemaakt.

    ![Volledige melding maken](media/functions-publish-project-vscode/function-create-notifications.png)

1. Ga terug naar het gebied **Azure: functions** in de zijbalk en vouw de nieuwe functie-app uit onder uw abonnement. Vouw **functies**uit, klik met de rechter muisknop (Windows) of CTRL + klik (MacOS) op **HttpExample**en kies **functie-URL kopiëren**.

    ![Kopieer de functie-URL voor de nieuwe HTTP-trigger](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
