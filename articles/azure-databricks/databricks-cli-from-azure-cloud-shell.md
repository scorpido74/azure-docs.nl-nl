---
title: 'Databricks CLI van Azure Cloud Shell gebruiken '
description: Meer informatie over het gebruik van de Databricks CLI van Azure Cloud Shell voor het uitvoeren van bewerkingen op Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: b327403b29f26f6c1f43f9dfe7aabd5c3c7e0a68
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299904"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Databricks CLI van Azure Cloud Shell gebruiken

Meer informatie over het gebruik van de Databricks CLI van Azure Cloud Shell voor het uitvoeren van bewerkingen op Databricks.

## <a name="prerequisites"></a>Vereisten

* Een Azure Databricks-werk ruimte en-cluster. Zie [aan de slag met Azure Databricks](quickstart-create-databricks-workspace-portal.md)voor instructies. 

* Stel een persoonlijk toegangs token in op Databricks. Zie [token beheer](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management)voor instructies.

## <a name="use-the-azure-cloud-shell"></a>De Azure Cloud Shell gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
 
2. Klik in de rechter bovenhoek op het pictogram **Cloud shell** .

   Starten ![Cloud shell](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "starten Azure Cloud shell")

3. Zorg ervoor dat u **bash** selecteert voor de Cloud shell omgeving. U kunt een keuze uit de vervolg keuzelijst selecteren, zoals in de volgende scherm afbeelding wordt weer gegeven.

   ![Selecteer bash voor de Cloud shell omgeving](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Selecteer bash") 

4. Maak een virtuele omgeving waarin u de Databricks CLI kunt installeren. In het onderstaande code fragment maakt u een virtuele omgeving met de naam `databrickscli`.

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Schakel over naar de virtuele omgeving die u hebt gemaakt.

       source databrickscli/bin/activate

6. Installeer de Databricks CLI.

       pip install databricks-cli

7. Stel verificatie met Databricks in met behulp van het toegangs token dat u moet maken, vermeld als onderdeel van de vereisten. Gebruik de volgende opdracht:

       databricks configure --token

    De volgende prompts worden weer gegeven:

    * Eerst wordt u gevraagd om de Databricks-host in te voeren. Voer de waarde in de notatie `https://eastus2.azuredatabricks.net` in. **VS Oost 2** is hier de Azure-regio waar u uw Azure Databricks-werk ruimte hebt gemaakt.

    * Vervolgens wordt u gevraagd een token in te voeren. Voer het token in dat u eerder hebt gemaakt.

Nadat u deze stappen hebt voltooid, kunt u Databricks CLI van Azure Cloud Shell gaan gebruiken.

## <a name="use-databricks-cli"></a>Databricks CLI gebruiken

U kunt nu beginnen met het gebruik van de Databricks CLI. Voer bijvoorbeeld de volgende opdracht uit om alle Databricks-clusters in uw werk ruimte weer te geven.

    databricks clusters list

U kunt ook de volgende opdracht gebruiken om toegang te krijgen tot het Databricks-bestands systeem (DBFS).

    databricks fs ls


Zie [DATABRICKS cli](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)voor een volledige naslag informatie over opdrachten.


## <a name="next-steps"></a>Volgende stappen

* Zie [overzicht van Azure cli](../cloud-shell/overview.md) voor meer informatie over Azure cli
* Zie [Naslag informatie voor Azure cli](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) voor een lijst met opdrachten voor Azure cli
* Zie [DATABRICKS cli](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html) (Engelstalig) voor een lijst met opdrachten voor Databricks cli


