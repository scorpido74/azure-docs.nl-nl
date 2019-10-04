---
title: Azure Resource Manager sjabloon exporteren met behulp van de Azure Portal
description: Gebruik Azure Portal om een Azure Resource Manager sjabloon te exporteren vanuit resources in uw abonnement.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: tomfitz
ms.openlocfilehash: 15f527dfe517dcb3329b8b61243d7c5054eedb56
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71959700"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Eén en meerdere resources exporteren naar een sjabloon in Azure Portal

Als u hulp nodig hebt bij het maken van Azure Resource Manager sjablonen, kunt u een sjabloon uit bestaande resources exporteren. De geëxporteerde sjabloon helpt u bij het begrijpen van de JSON-syntaxis en eigenschappen waarmee uw resources worden geïmplementeerd. Als u toekomstige implementaties wilt automatiseren, begint u met de geëxporteerde sjabloon en wijzigt u deze voor uw scenario.

Met Resource Manager kunt u een of meer resources kiezen voor het exporteren naar een sjabloon. U kunt zich richten op de resources die u nodig hebt in de sjabloon.

In dit artikel wordt beschreven hoe u sjablonen exporteert via de portal. U kunt ook [Azure cli](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [rest API](/rest/api/resources/resourcegroups/exporttemplate)gebruiken.

## <a name="choose-the-right-export-option"></a>Kies de optie voor de juiste export

Er zijn twee manieren om een sjabloon te exporteren:

* **Exporteren uit resource groep of resource**. Met deze optie wordt een nieuwe sjabloon gegenereerd op basis van bestaande resources. De geëxporteerde sjabloon is een moment opname van de huidige status van de resource groep. U kunt een volledige resource groep exporteren of specifieke resources binnen die resource groep.

* **Exporteren vóór implementatie of van geschiedenis**. Met deze optie wordt een exacte kopie van een sjabloon opgehaald die wordt gebruikt voor de implementatie.

Afhankelijk van de optie die u kiest, hebben de geëxporteerde sjablonen verschillende kwaliteiten.

| Van resource groep of resource | Vóór implementatie of vanuit geschiedenis |
| --------------------- | ----------------- |
| De sjabloon is een moment opname van de huidige status van de resource. Het bevat alle hand matige wijzigingen die u na de implementatie hebt aangebracht. | Met de sjabloon wordt alleen de status van resources op het moment van de implementatie weer gegeven. Hand matige wijzigingen die u na de implementatie hebt aangebracht, zijn niet opgenomen. |
| U kunt selecteren welke resources uit een resource groep u wilt exporteren. | Alle resources voor een specifieke implementatie zijn opgenomen. U kunt geen subset van deze resources kiezen of resources toevoegen die op een ander tijdstip zijn toegevoegd. |
| De sjabloon bevat alle eigenschappen voor de resources, waaronder enkele eigenschappen die u niet normaal hebt ingesteld tijdens de implementatie. Mogelijk wilt u deze eigenschappen verwijderen of opschonen voordat u de sjabloon opnieuw gebruikt. | De sjabloon bevat alleen de eigenschappen die nodig zijn voor de implementatie. De sjabloon is klaar voor gebruik. |
| De sjabloon bevat waarschijnlijk niet alle para meters die u nodig hebt voor hergebruik. De meeste eigenschaps waarden worden vastgelegd in de sjabloon. Als u de sjabloon opnieuw wilt implementeren in andere omgevingen, moet u para meters toevoegen die de mogelijkheid verhogen om de resources te configureren. | De sjabloon bevat para meters waarmee u eenvoudig opnieuw kunt implementeren in verschillende omgevingen. |

Exporteer de sjabloon van een resource groep of resource als:

* U moet wijzigingen vastleggen in de resources die zijn gemaakt na de oorspronkelijke implementatie.
* U wilt selecteren welke resources worden geëxporteerd.

Exporteer de sjabloon vóór de implementatie of vanuit de geschiedenis, wanneer:

* U wilt een sjabloon die u gemakkelijk wilt gebruiken.
* U hoeft geen wijzigingen op te nemen die u hebt aangebracht na de oorspronkelijke implementatie.

## <a name="export-template-from-resource-group"></a>Sjabloon exporteren uit resource groep

Een of meer resources uit een resource groep exporteren:

1. Selecteer de resource groep die de resources bevat die u wilt exporteren.

1. Als u alle resources in de resource groep wilt exporteren, selecteert u alle en vervolgens **sjabloon exporteren**. De optie **sjabloon exporteren** wordt alleen ingeschakeld wanneer u ten minste één resource hebt geselecteerd.

   ![Alle resources exporteren](./media/export-template-portal/select-all-resources.png)

1. Schakel de selectie vakjes naast deze resources in om specifieke resources te selecteren die u wilt exporteren. Selecteer vervolgens **sjabloon exporteren**.

   ![Te exporteren resources selecteren](./media/export-template-portal/select-resources.png)

1. De geëxporteerde sjabloon wordt weer gegeven en kan worden gedownload.

   ![Sjabloon weer geven](./media/export-template-portal/show-template.png)

## <a name="export-template-from-resource"></a>Sjabloon uit resource exporteren

Een resource exporteren:

1. Selecteer de resource groep met de resource die u wilt exporteren.

1. Selecteer de resource die u wilt exporteren.

   ![Resource selecteren](./media/export-template-portal/select-link-resource.png)

1. Selecteer voor die resource **sjabloon exporteren** in het linkerdeel venster.

   ![Resource exporteren](./media/export-template-portal/export-single-resource.png)

1. De geëxporteerde sjabloon wordt weer gegeven en kan worden gedownload. De sjabloon bevat alleen de afzonderlijke resource.

## <a name="export-template-before-deployment"></a>Sjabloon exporteren vóór implementatie

1. Selecteer de Azure-service die u wilt implementeren.

1. Vul de waarden in voor de nieuwe service.

1. Nadat u de validatie hebt door gegeven, selecteert u **een sjabloon voor Automation downloaden**voordat u de implementatie start.

   ![Sjabloon downloaden](./media/export-template-portal/download-before-deployment.png)

1. De sjabloon wordt weer gegeven en kan worden gedownload.

   ![Sjabloon weer geven](./media/export-template-portal/show-template-before-deployment.png)

## <a name="export-template-after-deployment"></a>Sjabloon exporteren na implementatie

U kunt de sjabloon die is gebruikt voor het implementeren van bestaande resources exporteren. De sjabloon die u krijgt, is precies die waarmee de implementatie is uitgevoerd.

1. Selecteer de resource groep die u wilt exporteren.

1. Selecteer de koppeling onder **implementaties**.

   ![Implementatie geschiedenis selecteren](./media/export-template-portal/select-deployment-history.png)

1. Selecteer een van de implementaties in de implementatie geschiedenis.

   ![Implementatie selecteren](./media/export-template-portal/select-details.png)

1. Selecteer een **sjabloon**. De sjabloon die wordt gebruikt voor deze implementatie wordt weer gegeven en kan worden gedownload.

   ![Sjabloon selecteren](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het exporteren van sjablonen met [Azure cli](manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [rest API](/rest/api/resources/resourcegroups/exporttemplate).
- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager sjablonen](./resource-group-authoring-templates.md)voor meer informatie over de syntaxis van de Resource Manager-sjabloon.
- Zie [Stapsgewijze zelf studies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloon verwijzing](/azure/templates/)voor het weer geven van de Azure Resource Manager sjabloon schema's.
