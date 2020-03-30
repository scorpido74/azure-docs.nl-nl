---
title: Sjabloon exporteren in Azure-portal
description: Gebruik Azure portal om een Azure Resource Manager-sjabloon te exporteren vanuit bronnen in uw abonnement.
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 8cdba58a7a2ba998bac7fc0225ff957047cd69b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273733"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Export met één en meerdere resources naar een sjabloon in Azure-portal

Als u wilt helpen bij het maken van Azure Resource Manager-sjablonen, u een sjabloon exporteren uit bestaande bronnen. Met de geëxporteerde sjabloon u inzicht krijgen in de syntaxis en eigenschappen van JSON die uw resources implementeren. Als u toekomstige implementaties wilt automatiseren, begint u met de geëxporteerde sjabloon en wijzigt u deze voor uw scenario.

Met Resource Manager u een of meer bronnen kiezen om naar een sjabloon te exporteren. U zich richten op precies de resources die u nodig hebt in de sjabloon.

In dit artikel ziet u hoe u sjablonen exporteren via de portal. U ook [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [REST API](/rest/api/resources/resourcegroups/exporttemplate)gebruiken.

## <a name="choose-the-right-export-option"></a>Kies de juiste exportoptie

Er zijn twee manieren om een sjabloon te exporteren:

* **Exporteren uit resourcegroep of resource**. Met deze optie genereert u een nieuwe sjabloon uit bestaande bronnen. De geëxporteerde sjabloon is een momentopname van de huidige status van de resourcegroep. U een volledige resourcegroep of specifieke resources binnen die resourcegroep exporteren.

* **Exporteren vóór implementatie of uit de geschiedenis**. Met deze optie wordt een exacte kopie opgehaald van een sjabloon die wordt gebruikt voor implementatie.

Afhankelijk van de optie die u kiest, hebben de geëxporteerde sjablonen verschillende kwaliteiten.

| Van resourcegroep of resource | Vóór implementatie of uit de geschiedenis |
| --------------------- | ----------------- |
| Sjabloon is een momentopname van de huidige status van de resources. Het bevat alle handmatige wijzigingen die u na de implementatie hebt aangebracht. | Sjabloon toont alleen de status van resources op het moment van implementatie. Alle handmatige wijzigingen die u na de implementatie hebt aangebracht, zijn niet inbegrepen. |
| U selecteren welke resources uit een resourcegroep u wilt exporteren. | Alle resources voor een specifieke implementatie zijn inbegrepen. U geen subset van deze resources kiezen of resources toevoegen die op een ander tijdstip zijn toegevoegd. |
| Sjabloon bevat alle eigenschappen voor de resources, inclusief enkele eigenschappen die u normaal gesproken niet zou instellen tijdens de implementatie. U deze eigenschappen verwijderen of opschonen voordat u de sjabloon opnieuw gebruikt. | Sjabloon bevat alleen de eigenschappen die nodig zijn voor de implementatie. De sjabloon is gebruiksklaar. |
| Sjabloon bevat waarschijnlijk niet alle parameters die u nodig hebt voor hergebruik. De meeste eigenschapswaarden zijn hardgecodeerd in de sjabloon. Als u de sjabloon opnieuw wilt implementeren in andere omgevingen, moet u parameters toevoegen die de mogelijkheid vergroten om de resources te configureren.  U **De** optie Opnemen van parameters opheffen opheffen opheffen opheffen, zodat u uw eigen parameters maken. | Sjabloon bevat parameters die het eenvoudig maken om opnieuw te implementeren in verschillende omgevingen. |

Exporteer de sjabloon uit een resourcegroep of resource wanneer:

* U moet wijzigingen vastleggen in de resources die zijn aangebracht na de oorspronkelijke implementatie.
* U wilt selecteren welke resources worden geëxporteerd.

Exporteer de sjabloon vóór implementatie of uit de geschiedenis, wanneer:

* U wilt een eenvoudig te hergebruiken sjabloon.
* U hoeft geen wijzigingen op te nemen die u na de oorspronkelijke implementatie hebt aangebracht.

## <a name="limitations"></a>Beperkingen

Bij het exporteren vanuit een resourcegroep of resource wordt de geëxporteerde sjabloon gegenereerd uit de [gepubliceerde schema's](https://github.com/Azure/azure-resource-manager-schemas/tree/master/schemas) voor elk resourcetype. Af en toe heeft het schema niet de nieuwste versie voor een resourcetype. Controleer uw geëxporteerde sjabloon om te controleren of deze de eigenschappen bevat die u nodig hebt. Bewerk indien nodig de geëxporteerde sjabloon om de API-versie te gebruiken die u nodig hebt.

De functie exportsjabloon biedt geen ondersteuning voor het exporteren van Azure Data Factory-resources. Zie [Een gegevensfabriek kopiëren of klonen in Azure Data Factory](https://aka.ms/exportTemplateViaAdf)voor meer informatie over hoe u gegevensfabriekresources exporteren.

Als u resources wilt exporteren die zijn gemaakt via een klassiek implementatiemodel, moet u [deze migreren naar het implementatiemodel resourcebeheer](https://aka.ms/migrateclassicresourcetoarm).

## <a name="export-template-from-a-resource-group"></a>Sjabloon exporteren uit een resourcegroep

Ga als lid van de export van een of meer resources uit een resourcegroep:

1. Selecteer de resourcegroep die de resources bevat die u wilt exporteren.

1. Selecteer een of meer bronnen door de selectievakjes in te schakelen.  Als u alles wilt selecteren, schakelt u het selectievakje links van **Naam**in. Het **menu-item Sjabloon exporteren** wordt pas ingeschakeld nadat u ten minste één resource hebt geselecteerd.

   ![Alle resources exporteren](./media/export-template-portal/select-all-resources.png)

    Op de schermafbeelding is alleen het opslagaccount geselecteerd.
1. Selecteer **Sjabloon Exporteren**.

1. De geëxporteerde sjabloon wordt weergegeven en is beschikbaar om te downloaden en te implementeren.

   ![Sjabloon weergeven](./media/export-template-portal/show-template.png)

   **Parameters opnemen** is standaard geselecteerd.  Wanneer deze optie is geselecteerd, worden alle sjabloonparameters opgenomen wanneer de sjabloon wordt gegenereerd. Als u uw eigen parameters wilt maken, schakelt u dit selectievakje in om deze niet op te nemen.

## <a name="export-template-from-a-resource"></a>Sjabloon exporteren vanuit een resource

Eén resource exporteren:

1. Selecteer de resourcegroep met de resource die u wilt exporteren.

1. Selecteer de resource die u wilt exporteren om de resource te openen.

1. Selecteer voor die resource **de optie Sjabloon Exporteren** in het linkerdeelvenster.

   ![Bron exporteren](./media/export-template-portal/export-single-resource.png)

1. De geëxporteerde sjabloon wordt weergegeven en is beschikbaar om te downloaden en te implementeren. De sjabloon bevat slechts één resource. **Parameters opnemen** is standaard geselecteerd.  Wanneer deze optie is geselecteerd, worden alle sjabloonparameters opgenomen wanneer de sjabloon wordt gegenereerd. Als u uw eigen parameters wilt maken, schakelt u dit selectievakje in om deze niet op te nemen.

## <a name="export-template-before-deployment"></a>Sjabloon exporteren vóór implementatie

1. Selecteer de Azure-service die u wilt implementeren.

1. Vul de waarden voor de nieuwe service in.

1. Nadat u de validatie hebt doorlopen, maar voordat u de implementatie start, selecteert u **Een sjabloon downloaden voor automatisering**.

   ![Een sjabloon downloaden](./media/export-template-portal/download-before-deployment.png)

1. De sjabloon wordt weergegeven en is beschikbaar voor download en implementatie.


## <a name="export-template-after-deployment"></a>Sjabloon exporteren na implementatie

U de sjabloon exporteren die is gebruikt om bestaande resources te implementeren. De sjabloon die u krijgt is precies degene die werd gebruikt voor implementatie.

1. Selecteer de resourcegroep die u wilt exporteren.

1. Selecteer de koppeling onder **Implementaties**.

   ![Implementatiegeschiedenis selecteren](./media/export-template-portal/select-deployment-history.png)

1. Selecteer een van de implementaties uit de implementatiegeschiedenis.

   ![Implementatie selecteren](./media/export-template-portal/select-details.png)

1. Selecteer **Sjabloon**. De sjabloon die voor deze implementatie wordt gebruikt, wordt weergegeven en is beschikbaar om te downloaden.

   ![Sjabloon selecteren](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het exporteren van sjablonen met [Azure CLI,](../management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)of [REST API](/rest/api/resources/resourcegroups/exporttemplate).
- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor de syntaxis van de resourcemanagersjabloon .
- Zie de [stapsgewijze zelfstudies](/azure/azure-resource-manager/)voor meer informatie over het ontwikkelen van sjablonen.
- Zie [sjabloonverwijzing](/azure/templates/)voor het weergeven van de sjabloonschema's van Azure Resource Manager .
