---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 41dfb809cdab00f4f9bee335d92522f37a438c68
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82109808"
---
## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en gerelateerde resources in uw Azure-abonnement en implementeert u vervolgens uw code. 

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 


1. Kies het pictogram van Azure in de activiteiten balk en kies in het gebied **Azure: functions** de knop **implementeren in functie app...** .

    ![Uw project naar Azure publiceren](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie op bij de prompts:

    + **Selecteer een map**: Kies een map in uw werk ruimte of blader naar een submap die uw functie-app bevat. Dit wordt niet weer gegeven als u al een geldige functie-app hebt geopend.

    + **Abonnement selecteren**: Kies het abonnement dat u wilt gebruiken. U ziet deze niet als u slechts één abonnement hebt.

    + **Selecteer functie-app in azure**: kiezen `+ Create new Function App`. (Kies niet de `Advanced` optie die niet in dit artikel wordt behandeld.)
      
    + **Voer een globaal unieke naam in voor de functie-app**: Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om er zeker van te zijn dat deze uniek is in Azure Functions. 
    
    ::: zone pivot="programming-language-python"
    + **Selecteer een runtime**: Kies de versie van python die lokaal wordt uitgevoerd. U kunt de `python --version` opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecteer een runtime**: Kies de versie van node. js die u lokaal uitvoert. U kunt de `node --version` opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    + **Selecteer een locatie voor nieuwe resources**: Kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
1.  Wanneer dit is voltooid, worden de volgende Azure-resources in uw abonnement gemaakt met behulp van namen op basis van de naam van uw functie-app:
    
    + Een resource groep, een logische container voor gerelateerde resources.
    + Een standaard Azure Storage account, waarmee de status en andere informatie over uw projecten worden bijgehouden.
    + Een verbruiks plan dat de onderliggende host definieert voor uw serverloze functie-app. 
    + Een functie-app, waarmee u de omgeving voor het uitvoeren van uw functie code kunt gebruiken. Met een functie-app kunt u functies groeperen als logische eenheid voor eenvoudiger beheer, implementatie en delen van resources binnen hetzelfde hosting plan.
    + Een Application Insights exemplaar dat is gekoppeld aan de functie-app, waarmee het gebruik van uw serverloze functie wordt bijgehouden.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 
    
1. Selecteer **uitvoer weer geven** in deze melding om de resultaten voor het maken en implementeren weer te geven, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u in de rechter benedenhoek het klok pictogram om het weer te geven.

    ![Volledige melding maken](media/functions-publish-project-vscode/function-create-notifications.png)
