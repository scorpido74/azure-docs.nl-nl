---
title: Overzicht van definities van aangepaste rollen
description: Hierin wordt het concept beschreven van het maken van aangepaste roldefinities voor beheerde toepassingen.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650758"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Definitie artefact voor aangepaste rollen in Azure Managed Applications

De definitie van een aangepaste rol is een optioneel artefact in beheerde toepassingen. De methode wordt gebruikt om te bepalen welke machtigingen de beheerde toepassing nodig heeft om de functies uit te voeren.

Dit artikel bevat een overzicht van het definitie artefact van aangepaste rollen en de mogelijkheden ervan.

## <a name="custom-role-definition-artifact"></a>Definitie artefact van aangepaste rol

U moet de aangepaste functie definitie artefact customRoleDefinition. json noemen. Plaats het op hetzelfde niveau als createUiDefinition. json en mainTemplate. json in het zip-pakket dat een definitie van een beheerde toepassing maakt. Zie [een definitie van een beheerde toepassing publiceren](publish-managed-app-definition-quickstart.md) voor meer informatie over het maken van een zip-pakket en het publiceren van een definitie van een beheerde toepassing.

## <a name="custom-role-definition-schema"></a>Definitie schema voor aangepaste rol

Het bestand customRoleDefinition. json heeft een `roles` eigenschap op het hoogste niveau die een matrix met rollen is. Deze rollen zijn de machtigingen die door de beheerde toepassing moeten worden gebruikt. Op dit moment zijn alleen ingebouwde rollen toegestaan, maar u kunt meerdere rollen opgeven. Naar een rol kan worden verwezen met de ID van de roldefinitie of met de rolnaam.

Voor beeld-JSON voor aangepaste roldefinitie:

```json
{
    "contentVersion": "0.0.0.1",
    "roles": [
        {
            "properties": {
                "roleName": "Contributor"
            }
        },
        {
            "id": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
        },
        {
            "id": "/providers/Microsoft.Authorization/roledefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c"
        }
    ]
}
```

## <a name="roles"></a>Rollen

Een rol bestaat uit een `$.properties.roleName` of een `id`:

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> U kunt het veld `id` of `roleName` gebruiken. Er is slechts één vereist. Deze velden worden gebruikt voor het opzoeken van de roldefinitie die moet worden toegepast. Als beide zijn opgegeven, wordt het `id` veld gebruikt.

|Eigenschap|Vereist?|Beschrijving|
|---------|---------|---------|
|id|Ja|De ID van de ingebouwde rol. U kunt de volledige ID of alleen de GUID gebruiken.|
|roleName|Ja|De naam van de ingebouwde rol.|