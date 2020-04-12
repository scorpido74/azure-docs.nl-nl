---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: d8665b4cec3357baee5d6c1b77b5719645575419
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81112878"
---
## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en gerelateerde resources in uw Azure-abonnement en implementeert u uw code. 

> [!IMPORTANT]
> Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 


1. Kies het Azure-pictogram in de activiteitsbalk en kies vervolgens in het gebied **Azure: Functions** de **knop Implementeren naar functie-app...**

    ![Uw project publiceren naar Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie bij de prompts:

    + **Abonnement selecteren**: Kies het te gebruiken abonnement. Dit zie je niet als je maar één abonnement hebt.

    + **Functie-app selecteren in Azure:** Kies `+ Create new Function App`. (Kies niet de `Advanced` optie, die niet in dit artikel wordt behandeld.)
      
    + **Voer een wereldwijd unieke naam in voor de functie-app:** Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om ervoor te zorgen dat deze uniek is in Azure-functies. 
    
    ::: zone pivot="programming-language-python"
    + **Selecteer een runtime:** kies de versie van Python waarop u lokaal hebt uitgevoerd. U `python --version` de opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecteer een runtime:** kies de versie van Node.js waarop u lokaal hebt uitgevoerd. U `node --version` de opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    + **Selecteer een locatie voor nieuwe resources:** kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
1.  Wanneer u klaar bent, worden de volgende Azure-resources in uw abonnement gemaakt met namen op basis van de naam van uw functie-app:
    
    + Een resourcegroep, een logische container voor gerelateerde resources.
    + Een standaard Azure Storage-account, dat status- en andere informatie over uw projecten bijhoudt.
    + Een verbruiksplan dat de onderliggende host voor uw serverloze functie-app definieert. 
    + Een functie-app, die de omgeving biedt voor het uitvoeren van uw functiecode. Met een functie-app u functies groeperen als een logische eenheid voor eenvoudiger beheer, implementatie en delen van resources binnen hetzelfde hostingplan.
    + Een instantie Application Insights die is verbonden met de functie-app, die het gebruik van uw serverloze functie bijhoudt.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 
    
1. Selecteer **Uitvoer weergeven** in deze melding om de resultaten van maken en implementeren weer te geven, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw te zien.

    ![Volledige melding maken](media/functions-publish-project-vscode/function-create-notifications.png)
