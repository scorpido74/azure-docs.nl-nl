---
title: Het ui-definitiebestand testen
description: Beschrijft hoe u de gebruikerservaring testen voor het maken van uw Azure Managed Application via de portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: e2d075a58872f9337c7d1faa642a48047e2f9ddf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250185"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Uw portalinterface voor Azure Managed Applications testen

Nadat [u het bestand createUiDefinition.json](create-uidefinition-overview.md) voor uw beheerde toepassing hebt gemaakt, moet u de gebruikerservaring testen. Gebruik een sandbox-omgeving die uw bestand laadt in de portal om het testen te vereenvoudigen. U hoeft uw beheerde toepassing niet daadwerkelijk te implementeren. De sandbox presenteert uw gebruikersinterface in de huidige, full-screen portal-ervaring. U ook een script gebruiken voor het testen van de interface. Beide benaderingen worden weergegeven in dit artikel. De sandbox is de aanbevolen manier om een voorbeeld van de interface te bekijken.

## <a name="prerequisites"></a>Vereisten

* Een **bestand createUiDefinition.json.** Als u dit bestand niet hebt, kopieert u het [voorbeeldbestand.](https://github.com/Azure/azure-quickstart-templates/blob/master/100-marketplace-sample/createUiDefinition.json)

* Een Azure-abonnement. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="use-sandbox"></a>Sandbox gebruiken

1. Open de [Sandbox voor ui-definitie maken](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade).

   ![Sandbox weergeven](./media/test-createuidefinition/show-sandbox.png)

1. Vervang de lege definitie door de inhoud van het bestand createUiDefinition.json. Selecteer **Voorbeeld**.

   ![Voorbeeld selecteren](./media/test-createuidefinition/select-preview.png)

1. Het formulier dat u hebt gemaakt, wordt weergegeven. U de gebruikerservaring doorlopen en de waarden invullen.

   ![Formulier weergeven](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Problemen oplossen

Als uw formulier niet wordt weergegeven na het selecteren **van Voorbeeld,** u een syntaxisfout hebben. Zoek naar de rode indicator op de rechter schuifbalk en navigeer ernaar.

![Syntaxisfout weergeven](./media/test-createuidefinition/show-syntax-error.png)

Als uw formulier niet wordt weergegeven en u in plaats daarvan een pictogram van een wolk met traandruppel ziet, heeft uw formulier een foutmelding, zoals een ontbrekende eigenschap. Open de webhulpprogramma's voor webontwikkelaars in uw browser. De **console** geeft belangrijke berichten over uw interface weer.

![Fout weergeven](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Testscript gebruiken

Als u uw interface in de portal wilt testen, kopieert u een van de volgende scripts naar uw lokale machine:

* [PowerShell side-load script - Az Module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [PowerShell-script voor side-load - Azure-module](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Azure CLI-script voor side-load](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Voer het gedownloade script uit om uw interfacebestand in de portal te bekijken. Het script maakt een opslagaccount in uw Azure-abonnement en uploadt uw createUiDefinition.json-bestand naar het opslagaccount. Het opslagaccount wordt gemaakt wanneer u het script voor het eerst uitvoert of als het opslagaccount is verwijderd. Als het opslagaccount al bestaat in uw Azure-abonnement, wordt het script opnieuw gebruikt. Het script opent de portal en laadt uw bestand vanuit het opslagaccount.

Geef een locatie op voor het opslagaccount en geef de map op met het bestand createUiDefinition.json.

Gebruik voor PowerShell:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Gebruik voor Azure CLI:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Als uw bestand createUiDefinition.json zich in dezelfde map bevindt als het script en u het opslagaccount al hebt gemaakt, hoeft u deze parameters niet op te geven.

Gebruik voor PowerShell:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Gebruik voor Azure CLI:

```bash
./sideload-createuidef.sh
```

Het script opent een nieuw tabblad in uw browser. Het toont de portal met uw interface voor het maken van de beheerde toepassing.

Geef waarden op voor de velden. Wanneer u klaar bent, ziet u de waarden die worden doorgegeven aan de sjabloon die kan worden gevonden in de console voor hulpprogramma's voor ontwikkelaars van uw browser.

![Waarden weergeven](./media/test-createuidefinition/show-json.png)

U deze waarden gebruiken als parameterbestand voor het testen van uw implementatiesjabloon.

Als de portal aan het overzichtsscherm hangt, kan er een bug in de uitvoersectie zijn. U hebt bijvoorbeeld verwezen naar een besturingselement dat niet bestaat. Als een parameter in de uitvoer leeg is, verwijst de parameter mogelijk naar een eigenschap die niet bestaat. De verwijzing naar het besturingselement is bijvoorbeeld geldig, maar de eigenschapsverwijzing is niet geldig.

## <a name="test-your-solution-files"></a>Test uw oplossingsbestanden

Nu u hebt geverifieerd dat uw portalinterface werkt zoals verwacht, is het tijd om te valideren dat uw createUiDefinition-bestand correct is geïntegreerd met uw mainTemplate.json-bestand. U een validatiescripttest uitvoeren om de inhoud van uw oplossingsbestanden te testen, inclusief het createUiDefinition-bestand. Het script valideert de JSON-syntaxis, controleert op regex-expressies op tekstvelden en zorgt ervoor dat de uitvoerwaarden van de portalinterface overeenkomen met de parameters van uw sjabloon. Zie [Statische validatiecontroles uitvoeren voor sjablonen voor](https://github.com/Azure/azure-quickstart-templates/tree/master/test)informatie over het uitvoeren van dit script.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw portalinterface hebt gevaliderd, leest u meer over het beschikbaar maken van uw [azure-beheerde toepassing op de Marketplace.](publish-marketplace-app.md)
