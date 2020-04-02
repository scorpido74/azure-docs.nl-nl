---
title: Taalextensies beheren in uw Azure Data Explorer-cluster.
description: Gebruik taalextensie om andere talen te integreren in uw KQL-query's in Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522470"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Taalextensies beheren in uw Azure Data Explorer-cluster (Voorbeeld)

Met de functie taalextensies u plug-ins voor taalextensies gebruiken om andere talen te integreren in uw KQL-query's in Azure Data Explorer. Wanneer u een door de gebruiker gedefinieerde functie (UDF) uitvoert met een relevant script, krijgt het script tabelgegevens als invoer en wordt verwacht dat het tabelvormige uitvoer produceert. De runtime van de plug-in wordt gehost in een [sandbox,](/azure/kusto/concepts/sandboxes)een geïsoleerde en veilige omgeving die wordt uitgevoerd op de knooppunten van het cluster. In dit artikel beheert u de plug-in voor taalextensies in uw Azure Data Explorer-cluster binnen de Azure-portal.

> [!NOTE]
> Azure Data Explorer-taalextensies die momenteel worden ondersteund, zijn Python en R.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Maak [een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Taalextensies inschakelen op uw cluster

> [!WARNING]
> Controleer de [beperkingen](#limitations) voordat u een taalextensie inschakelt.

Voer de volgende stappen uit om taalextensies in uw cluster in te schakelen:

1. Ga in de Azure-portal naar uw Azure Data Explorer-cluster. 
1. Selecteer **Configuraties**in **Instellingen**. 
1. Selecteer **in** het deelvenster Configuraties de optie **Aan** om een taalextensie in te schakelen.
1. Selecteer **Opslaan**.
 
    ![taalextensie op](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> Het inschakelen van het taaluitbreidingsproces kan enkele minuten duren. Gedurende die tijd wordt uw cluster opgeschort.
 
## <a name="run-language-extension-integrated-queries"></a>Geïntegreerde query's voor taalextensie uitvoeren

* Meer informatie over het [uitvoeren van geïntegreerde KQL-query's van Python.](/azure/kusto/query/pythonplugin)
* Meer informatie over het [uitvoeren van R-geïntegreerde KQL-query's](/azure/kusto/query/rplugin). 

## <a name="disable-language-extensions-on-your-cluster"></a>Taalextensies uitschakelen op uw cluster

> [!NOTE]
> Het uitschakelen van taalextensies kan enkele minuten duren.

Voer de volgende stappen uit om taalextensies in uw cluster uit te schakelen:

1. Ga in de Azure-portal naar uw Azure Data Explorer-cluster. 
1. Selecteer **Configuraties**in **Instellingen**. 
1. Selecteer uit **het** deelvenster Configuraties om een taalextensie uit te schakelen in het deelvenster **Configuraties.**
1. Selecteer **Opslaan**.

    ![Taalextensie uit](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Beperkingen

* De functie taalextensies biedt geen ondersteuning voor [schijfversleuteling.](manage-cluster-security.md) 
* De runtime sandbox voor taalextensies wijst schijfruimte toe, zelfs als er geen query wordt uitgevoerd in het bereik van de desbetreffende taal.

