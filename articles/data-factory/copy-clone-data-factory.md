---
title: Een data factory in Azure Data Factory kopiëren of klonen
description: Meer informatie over het kopiëren of klonen van een data factory in Azure Data Factory
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414111"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Een data factory in Azure Data Factory kopiëren of klonen

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u een data factory kopieert of kloont in Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Cases gebruiken voor het klonen van een data factory

Hier volgen enkele van de omstandigheden waarin het handig is om een data factory te kopiëren of te klonen:

-   De **naam van resources wijzigen**. Azure biedt geen ondersteuning voor het hernoemen van resources. Als u de naam van een data factory wilt wijzigen, kunt u de data factory met een andere naam klonen en vervolgens het bestaande item verwijderen.

-   **Fout opsporing van wijzigingen** wanneer de functies voor fout opsporing niet voldoende zijn. Als u uw wijzigingen wilt testen, kunt u de wijzigingen in een andere fabriek testen voordat u deze toepast op uw hoofd. In de meeste scenario's kunt u debug gebruiken. Wijzigingen in triggers, zoals de werking van uw wijzigingen wanneer een trigger automatisch wordt geactiveerd of over een tijd venster, zijn mogelijk niet testable eenvoudig zonder in te checken. In deze gevallen is het klonen van de fabriek en het Toep assen van de wijzigingen. Omdat Azure Data Factory kosten voornamelijk door het aantal uitvoeringen, leidt de tweede Factory niet tot extra kosten.

## <a name="how-to-clone-a-data-factory"></a>Een data factory klonen

1. Met de Data Factory gebruikers interface in de Azure Portal kunt u de volledige payload van uw data factory exporteren naar een resource manager-sjabloon, samen met een parameter bestand waarmee u de waarden kunt wijzigen die u wilt wijzigen wanneer u uw fabriek kloont.

1. Als vereiste moet u de doel-data factory maken vanuit de Azure Portal.

1. Als u een SelfHosted IntegrationRuntime in uw bron-Factory hebt, moet u deze vooraf maken met dezelfde naam in de doel-Factory. Als u de SelfHosted-IRs wilt delen tussen verschillende fabrieken, kunt u het patroon gebruiken dat [hier](source-control.md#best-practices-for-git-integration)wordt gepubliceerd.

1. Als u zich in de GIT-modus bevindt, wordt de Resource Manager-sjabloon van de fabriek in het adf_publish vertakking van de opslag plaats opgeslagen in GIT wanneer u publiceert vanuit de portal.

1. Voor andere scenario's kunt u de Resource Manager-sjabloon downloaden door te klikken op de knop **Resource Manager-sjabloon exporteren** in de portal.

1. Nadat u de Resource Manager-sjabloon hebt gedownload, kunt u deze implementeren via de standaard implementatie methoden van Resource Manager-sjablonen.

1. Uit veiligheids overwegingen bevat de gegenereerde Resource Manager-sjabloon geen geheime informatie, zoals wacht woorden voor gekoppelde services. Als gevolg hiervan moet u deze wacht woorden opgeven als implementatie parameters. Als u geen para meters kunt opgeven, moet u de verbindings reeksen en wacht woorden van de gekoppelde services ophalen van Azure Key Vault.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de richt lijnen voor het maken van een data factory in het Azure Portal in [een Data Factory maken met behulp van de Azure Data Factory-gebruikers interface](quickstart-create-data-factory-portal.md).
