---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/12/2020
ms.author: glenga
ms.openlocfilehash: 256510f855256e648ae9203f46eb9f66c9ffaed6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77029151"
---
## <a name="publish-the-project-to-azure"></a>Het project naar Azure publiceren

In deze sectie maakt u een functie-app en gerelateerde resources in uw Azure-abonnement en implementeert u uw code. 

1. Kies het Azure-pictogram in de activiteitsbalk en kies vervolgens in het gebied **Azure: Functions** de **knop Implementeren naar functie-app...**

    ![Uw project publiceren naar Azure](media/functions-publish-project-vscode/function-app-publish-project.png)

1. Geef de volgende informatie bij de prompts:

    + **Abonnement selecteren**: Kies het te gebruiken abonnement. Dit zie je niet als je maar één abonnement hebt.

    + **Functie-app selecteren**in `+ Create new Function App` Azure `Advanced`: Kiezen (niet). Dit artikel biedt geen ondersteuning voor de [geavanceerde publicatiestroom](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options). 
    
    >[!IMPORTANT]
    > Als u in een bestaande functie-app publiceert, wordt de inhoud van die app in Azure overschreven. 
    
    + **Voer een wereldwijd unieke naam in voor de functie-app:** Typ een naam die geldig is in een URL-pad. De naam die u typt, wordt gevalideerd om ervoor te zorgen dat deze uniek is in Azure-functies. 
    
    ::: zone pivot="programming-language-python"
    + **Selecteer een runtime:** kies de versie van Python waarop u lokaal hebt uitgevoerd. U `python --version` de opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    ::: zone pivot="programming-language-javascript,programming-language-typescript"
    + **Selecteer een runtime:** kies de versie van Node.js waarop u lokaal hebt uitgevoerd. U `node --version` de opdracht gebruiken om uw versie te controleren.
    ::: zone-end

    + **Selecteer een locatie voor nieuwe resources:** kies voor betere prestaties een [regio](https://azure.microsoft.com/regions/) bij u in de buurt. 
    
1.  Wanneer deze zijn voltooid, worden de volgende Azure-resources in uw abonnement gemaakt:

    + **[Resourcegroep:](../articles/azure-resource-manager/management/overview.md)** bevat alle gemaakte Azure-resources. De naam is gebaseerd op de naam van uw functie-app.
    + **[Opslagaccount](../articles//storage/common/storage-introduction.md#types-of-storage-accounts)**: Er wordt een standaardopslagaccount gemaakt met een unieke naam die is gebaseerd op de naam van uw functie-app.
    + **[Hostingplan](../articles/azure-functions/functions-scale.md)**: er wordt een verbruiksplan gemaakt in de regio West-VS om uw serverloze functie-app te hosten.
    + **Functie-app**: Uw project wordt geïmplementeerd in deze nieuwe functie-app.
    + **Toepassingsinzichten**: Een instantie die is verbonden met uw functie-app, wordt gemaakt op basis van uw functienaam.

    Nadat de functie-app is gemaakt en het implementatiepakket is toegepast, wordt er een melding weergegeven. 
    
1. Selecteer **Uitvoer weergeven** in deze melding om de resultaten van maken en implementeren weer te geven, inclusief de Azure-resources die u hebt gemaakt. Als u de melding mist, selecteert u het belpictogram in de rechterbenedenhoek om deze opnieuw te zien.

    ![Volledige melding maken](media/functions-publish-project-vscode/function-create-notifications.png)
