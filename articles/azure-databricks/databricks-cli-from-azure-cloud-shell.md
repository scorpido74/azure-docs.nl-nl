---
title: 'Databricks CLI van Azure Cloud Shell gebruiken '
description: Meer informatie over het gebruik van de Databricks CLI van Azure Cloud Shell om bewerkingen uit te voeren op Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: mamccrea
ms.openlocfilehash: efb0d3222bfd98b15502163979425d47fa459e07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73605725"
---
# <a name="use-databricks-cli-from-azure-cloud-shell"></a>Databricks CLI van Azure Cloud Shell gebruiken

Meer informatie over het gebruik van de Databricks CLI van Azure Cloud Shell om bewerkingen uit te voeren op Databricks.

## <a name="prerequisites"></a>Vereisten

* Een Azure Databricks-werkruimte en -cluster. Zie Aan [de slag met Azure Databricks](quickstart-create-databricks-workspace-portal.md)voor instructies. 

* Stel een persoonlijk toegangstoken in Databricks in. Zie [Tokenbeheer](/azure/databricks/dev-tools/api/latest/authentication)voor instructies .

## <a name="use-the-azure-cloud-shell"></a>De Azure Cloud Shell gebruiken

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
 
2. Klik in de rechterbovenhoek op het **Pictogram Cloud Shell.**

   ![Cloud Shell starten](./media/databricks-cli-from-azure-cloud-shell/launch-azure-cloud-shell.png "Azure Cloud Shell starten")

3. Zorg ervoor dat u **Bash** selecteert voor de Cloud Shell-omgeving. U kiezen uit de vervolgkeuzelijst, zoals in de volgende schermafbeelding wordt weergegeven.

   ![Selecteer Bash voor de Cloud Shell-omgeving](./media/databricks-cli-from-azure-cloud-shell/select-bash-for-shell.png "Bash selecteren") 

4. Maak een virtuele omgeving waarin u de Databricks CLI installeren. In het onderstaande fragment maakt `databrickscli`u een virtuele omgeving genaamd .

       virtualenv -p /usr/bin/python2.7 databrickscli

5. Schakel over naar de virtuele omgeving die u hebt gemaakt.

       source databrickscli/bin/activate

6. Installeer de Databricks CLI.

       pip install databricks-cli

7. Stel verificatie in met Databricks met behulp van het toegangstoken dat u moet hebben gemaakt, dat wordt vermeld als onderdeel van vereisten. Gebruik de volgende opdracht:

       databricks configure --token

    U ontvangt de volgende aanwijzingen:

    * Eerst wordt u gevraagd om de Databricks-host in te voeren. Voer de waarde `https://eastus2.azuredatabricks.net`in de notatie in . Oost **US 2** is hier de Azure-regio waar u uw Azure Databricks-werkruimte hebt gemaakt.

    * Vervolgens wordt u gevraagd om een token in te voeren. Voer het token in dat u eerder hebt gemaakt.

Zodra u deze stappen hebt voltooid, u Databricks CLI van Azure Cloud Shell gebruiken.

## <a name="use-databricks-cli"></a>Databricks CLI gebruiken

U nu beginnen met het gebruik van de Databricks CLI. Voer bijvoorbeeld de volgende opdracht uit om alle Clusters van Databricks weer te geven die u in uw werkruimte hebt.

    databricks clusters list

U ook de volgende opdracht gebruiken om toegang te krijgen tot het Databricks-bestandssysteem (DBFS).

    databricks fs ls


Zie [Databricks CLI](/azure/databricks/dev-tools/databricks-cli)voor een volledige referentie over opdrachten.


## <a name="next-steps"></a>Volgende stappen

* Zie Azure [CLI-overzicht](../cloud-shell/overview.md) voor meer informatie over Azure CLI
* Zie [Azure CLI-verwijzing](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest) voor een lijst met opdrachten voor Azure CLI
* Zie [Databricks CLI](/azure/databricks/dev-tools/databricks-cli) voor een lijst met opdrachten voor Databricks CLI


