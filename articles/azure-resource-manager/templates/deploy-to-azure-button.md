---
title: De knop Implementeren in Azure
description: Gebruik de knop om Azure Resource Manager-sjablonen te implementeren vanuit een GitHub-repository.
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 88436eac970b252d7b0bc7bccee4131e06e9e0cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77109042"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Een implementatieknop gebruiken om sjablonen uit gitHub-repository te implementeren

In dit artikel wordt beschreven hoe u de knop **Implementeren naar Azure** gebruiken om sjablonen uit een GitHub-opslagplaats te implementeren. U de knop rechtstreeks toevoegen aan het README.md-bestand in uw GitHub-opslagplaats of aan een webpagina die verwijst naar de opslagplaats.

## <a name="use-common-image"></a>Algemene afbeelding gebruiken

Als u de knop wilt toevoegen aan uw webpagina of opslagplaats, gebruikt u de volgende afbeelding:

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

De afbeelding wordt weergegeven als:

![De knop Implementeren in Azure](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>URL maken voor het implementeren van sjabloon

Als u de URL voor uw sjabloon wilt maken, begint u met de onbewerkte URL naar de sjabloon in uw repo:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Vervolgens codeert de URL het. U een online encoder gebruiken of een opdracht uitvoeren. In het volgende PowerShell-voorbeeld ziet u hoe u een waarde coderen.

```powershell
[uri]::EscapeDataString($url)
```

De voorbeeld-URL heeft de volgende waarde wanneer de URL is gecodeerd.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Elke koppeling begint met dezelfde basis-URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Voeg de koppeling met URL-gecodeerde sjabloon toe aan het einde van de basis-URL.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Je hebt je volledige URL voor de link.

## <a name="create-deploy-to-azure-button"></a>Knop Implementeren naar Azure maken

Tot slot, zet de link en het beeld samen.

Gebruik README.md:

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

Als u de volledige oplossing wilt testen, selecteert u de volgende knop:

[![Implementeren naar Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

De portal toont een deelvenster waarmee u eenvoudig parameterwaarden opgeven. De parameters zijn vooraf gevuld met de standaardwaarden van de sjabloon.

![Portal gebruiken om te implementeren](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Volgende stappen

- Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor meer informatie over sjablonen.
