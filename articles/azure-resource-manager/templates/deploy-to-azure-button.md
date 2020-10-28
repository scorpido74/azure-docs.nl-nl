---
title: De knop Implementeren in Azure
description: Gebruik de knop om Azure Resource Manager sjablonen te implementeren vanuit een GitHub-opslag plaats.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 62a0a8b0336d9a7fcf00efb172775b9606bcef98
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675394"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Een implementatie knop gebruiken voor het implementeren van sjablonen uit de GitHub-opslag plaats

In dit artikel wordt beschreven hoe u de knop **implementeren in azure** gebruikt om sjablonen te implementeren vanuit een github-opslag plaats. U kunt de knop rechtstreeks toevoegen aan het README.md-bestand in de GitHub-opslag plaats. U kunt ook de knop toevoegen aan een webpagina die verwijst naar de opslag plaats.

Het implementatie bereik wordt bepaald door het sjabloon schema. Zie voor meer informatie:

* [resource groepen](deploy-to-resource-group.md)
* [geabonneerd](deploy-to-subscription.md)
* [beheer groepen](deploy-to-management-group.md)
* [tenants](deploy-to-tenant.md).

## <a name="use-common-image"></a>Algemene installatie kopie gebruiken

Gebruik de volgende afbeelding om de knop toe te voegen aan uw webpagina of opslag plaats:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

De afbeelding wordt weer gegeven als:

![De knop Implementeren in Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>URL maken voor het implementeren van de sjabloon

Als u de URL voor uw sjabloon wilt maken, begint u met de onbewerkte URL naar de sjabloon in uw opslag plaats. Als u de onbewerkte URL wilt zien, selecteert u **RAW** .

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="RAW selecteren":::

De indeling van de URL is:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Converteer vervolgens de URL naar een URL-gecodeerde waarde. U kunt een online coderings programma gebruiken of een opdracht uitvoeren. Het volgende Power shell-voor beeld laat zien hoe een URL een waarde codeert.

```powershell
[uri]::EscapeDataString($url)
```

De voor beeld-URL heeft de volgende waarde wanneer URL is gecodeerd.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Elke koppeling begint met dezelfde basis-URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Voeg uw sjabloon koppeling met URL-code ring toe aan het einde van de basis-URL.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

U hebt de volledige URL voor de koppeling.

## <a name="create-deploy-to-azure-button"></a>Knop voor implementeren naar Azure maken

Ten slotte plaatst u de koppeling en de afbeelding samen.

Als u de knop met prijs verlaging in het README.md-bestand in uw GitHub-opslag plaats of een webpagina wilt toevoegen, gebruikt u:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

Gebruik voor HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

## <a name="deploy-the-template"></a>De sjabloon implementeren

Selecteer de volgende knop om de volledige oplossing te testen:

[![Implementeren in Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

In de portal wordt een deel venster weer gegeven waarmee u eenvoudig parameter waarden kunt opgeven. De para meters worden vooraf ingevuld met de standaard waarden van de sjabloon.

![De portal gebruiken om te implementeren](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over sjablonen.
