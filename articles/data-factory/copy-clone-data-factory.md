---
title: Een gegevensfabriek kopiëren of klonen in Azure Data Factory
description: Meer informatie over het kopiëren of klonen van een gegevensfabriek in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 01/09/2019
ms.openlocfilehash: 5e44bda8648fbf26487b04cf36a8fd0ec085c411
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414111"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Een gegevensfabriek kopiëren of klonen in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een gegevensfabriek in Azure Data Factory kopieert of kloont.

## <a name="use-cases-for-cloning-a-data-factory"></a>Use cases voor het klonen van een datafabriek

Hier zijn enkele van de omstandigheden waarin u het nuttig kan vinden om een gegevensfabriek te kopiëren of te klonen:

-   **Naam wijzigen van resources**. Azure biedt geen ondersteuning voor het hernoemen van resources. Als u de naam van een gegevensfabriek wilt wijzigen, u de gegevensfabriek met een andere naam klonen en vervolgens de bestaande verwijderen.

-   **Foutopsporing verandert** wanneer de foutopsporingsfuncties niet voldoende zijn. Soms wilt u uw wijzigingen in een andere fabriek testen voordat u deze toepast op uw belangrijkste. In de meeste scenario's u Foutopsporing gebruiken. Wijzigingen in triggers, zoals hoe uw wijzigingen zich gedragen wanneer een trigger automatisch wordt aangeroepen of over een tijdvenster, zijn mogelijk niet gemakkelijk toetsbaar zonder in te checken. In deze gevallen, klonen van de fabriek en het toepassen van uw wijzigingen is er veel zin. Aangezien Azure Data Factory voornamelijk kosten in rekening brengt door het aantal uitvoeringen, leidt de tweede fabriek niet tot extra kosten.

## <a name="how-to-clone-a-data-factory"></a>Een gegevensfabriek klonen

1. Met de gebruikersinterface van de gegevensfabriek in de Azure-portal u de volledige payload van uw gegevensfabriek exporteren naar een Resource Manager-sjabloon, samen met een parameterbestand waarmee u alle waarden wijzigen die u wilt wijzigen wanneer u uw fabriek kloont.

1. Als voorwaarde moet u uw doelgegevensfabriek maken vanuit de Azure-portal.

1. Als u een SelfHosted IntegrationRuntime in uw bronfabriek hebt, moet u deze met dezelfde naam in de doelfabriek voormaken. Als u de SelfHosted IRs wilt delen tussen verschillende fabrieken, u het hier [gepubliceerde](source-control.md#best-practices-for-git-integration)patroon gebruiken.

1. Als u zich in de GIT-modus bevindt, wordt de resourcemanagersjabloon van de fabriek bij elke publicatie vanaf de portal opgeslagen in GIT in de adf_publish vertakking van de opslagplaats.

1. Voor andere scenario's kan de sjabloon Resourcemanager worden gedownload door te klikken op de **sjabloonknop Resourcebeheer exporteren** in de portal.

1. Nadat u de sjabloon Resourcemanager hebt gedownload, u deze implementeren via standaard methoden voor het implementeren van resourcebeheersjablonen.

1. Om veiligheidsredenen bevat de sjabloon gegenereerde Resource Manager geen geheime informatie, zoals wachtwoorden voor gekoppelde services. Als gevolg hiervan moet u deze wachtwoorden opgeven als implementatieparameters. Als het verstrekken van parameters niet wenselijk is, moet u de verbindingstekenreeksen en wachtwoorden van de gekoppelde services verkrijgen van Azure Key Vault.

## <a name="next-steps"></a>Volgende stappen

Bekijk de richtlijnen voor het maken van een gegevensfabriek in de Azure-portal in [Een gegevensfabriek maken met behulp van de gebruikersinterface van Azure Data Factory](quickstart-create-data-factory-portal.md).
