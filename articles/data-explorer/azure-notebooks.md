---
title: Azure-notitieblokken gebruiken om gegevens te analyseren in Azure Data Explorer
description: In dit onderwerp ziet u hoe u een query maakt met een Azure-notitieblok
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522405"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Azure-notitieblokken gebruiken om gegevens te analyseren in Azure Data Explorer

[Azure Notebooks](https://notebooks.azure.com/) is een Azure-cloudservice die het maken en delen van [Jupyter-notitieblokken](https://jupyter.org/)vereenvoudigt. Azure Notebooks maakt het eenvoudig om documentatie, code en de resultaten van het uitvoeren van de code te combineren.

> [!Note]
> * De volgende procedure gebruikt de Python-client in de Azure Notebooks-omgeving om Azure Data Explorer op te vragen. U Echter ook [KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) gebruiken om Azure Data Explorer op te vragen.
> * Sommige gebruikers meldden problemen met het verifiëren van Edge; totdat dergelijke problemen zijn opgelost, gebruik dan een andere browser.

## <a name="create-a-project"></a>Een project maken

1. Open [Azure-notitieblokken](https://notebooks.azure.com/) in uw browser en meld u aan.

1. Selecteer het tabblad **Mijn projecten** in de koptekst. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Selecteer **+ Nieuwe projecten**.
    
1. Ga als een nieuw **project naar** het volgende:
    1. Voer de **projectnaam**in .
    1. Schakel het selectievakje **Openbaar** uit.
        >[!Important]
        > Als u het selectievakje Openbaar niet uitkiest, wordt uw project weergegeven op het open internet.
    1. Selecteer **Maken**.
    
    ![Een nieuw project maken](media/azurenotebooks/an-create-new-project-blank.png)

    De Project-ID wordt automatisch gemaakt.

## <a name="create-a-notebook"></a>Een notebook maken

1. Maak in uw nieuwe project een notitieblok. Het notitieblok moet een [ondersteunde taal](https://github.com/Azure/azure-kusto-python#minimum-requirements)gebruiken.
Als u een notitieblok wilt maken, selecteert u **+ Nieuw** en selecteert **u Notitieblok**.

    ![Nieuw notitieblok maken](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Voer in het dialoogvenster **Nieuw notitieblok maken** de naam van het notitieblok in.

1. Selecteer **Python 3.6** en selecteer **Nieuw**.
    
    ![Nieuw notitieblok maken](media/azurenotebooks/an-create-new-notebook.png) 
    
1. Selecteer in uw project uw nieuwe notitieblok.

    ![Nieuw notitieblok selecteren](media/azurenotebooks/an-select-notebook.png)

    Wacht tot je Python-kernel initialiseert. Wanneer het gevulde cirkelpictogram verandert in een holle cirkelpictogram, u verdergaan.

    ![Kernel initialiseert](media/azurenotebooks/an-python-init-icon.png)

1. Als u de systeemmodule wilt importeren, voert u de volgende opdrachten in en selecteert u **Uitvoeren:**
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Als u een cel wilt uitvoeren, u ook op Shift+Enter drukken.

1.  Als u SDK-klassen wilt importeren, voert u de volgende opdrachten in en selecteert u **Uitvoeren:**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Als u de verbindingstekenreeks wilt maken en de Kusto-client wilt starten, voert u de volgende opdrachten in en selecteert u **Uitvoeren:**  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. Open bij de prompt een [https://aka.ms/devicelogin](https://aka.ms/devicelogin)nieuw browsertabblad op . 
   
1. Voer de autorisatiecode in en meld@microsoft.comuw AAD -account aan. De Kusto-client `kc` kan nu verifiëren naar Azure Data Explorer met uw identiteit.

1. Ga terug naar uw notitieblok om het resultaat van de verificatie te zien. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Een Kusto-query uitvoeren

1. Voer uw Kusto-query in en selecteer **Uitvoeren**. Bijvoorbeeld:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* [Kusto-python GitHub repo](https://github.com/Azure/azure-kusto-python)
