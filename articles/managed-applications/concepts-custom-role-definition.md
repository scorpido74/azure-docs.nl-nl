---
title: Overzicht van definities van aangepaste rollen in Azure Managed Applications | Microsoft Docs
description: Hierin wordt het concept beschreven van het maken van aangepaste roldefinities voor beheerde toepassingen.
services: managed-applications
ms.service: managed-applications
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: d645eebefde473e404f7760d2bc8a67c7e3e9087
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609032"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Definitie artefact voor aangepaste rollen in Azure Managed Applications

De definitie van een aangepaste rol is een optioneel artefact in beheerde toepassingen. Het wordt gebruikt om te bepalen welke machtigingen de beheerde toepassing nodig heeft om de functies uit te voeren.

Dit artikel bevat een overzicht van aangepaste functie-definitie artefacten en de mogelijkheden ervan.

## <a name="custom-role-definition-artifact"></a>Definitie artefact van aangepaste rol

Het definitie artefact van de aangepaste roldefinitie moet de naam **customRoleDefinition. json** hebben en op hetzelfde niveau worden geplaatst als **createUiDefinition. json** en **mainTemplate. json** in het zip-pakket dat een definitie van een beheerde toepassing maakt. Zie [een definitie van een beheerde toepassing publiceren](publish-managed-app-definition-quickstart.md) voor meer informatie over het maken van een zip-pakket en het publiceren van een definitie van een beheerde toepassing.

## <a name="custom-role-definition-schema"></a>Definitie schema voor aangepaste rol

Het bestand **customRoleDefinition. json** heeft een eigenschap `roles` op het hoogste niveau. Dit is een matrix met rollen. Elk van deze rollen zijn de machtigingen die de beheerde toepassing nodig heeft. Op dit moment zijn alleen ingebouwde rollen toegestaan, maar er kunnen meerdere rollen worden opgegeven. Naar de rol kan worden verwezen met de ID van de roldefinitie of met de rolnaam.

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

## <a name="role"></a>Rol

Een rol bestaat uit een `$.properties.roleName` of `id`.

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!Note]
> Alleen een van de `id`-of `roleName` veld is vereist. Deze velden worden gebruikt om de roldefinitie op te zoeken die u wilt Toep assen. Als beide zijn opgegeven, wordt het `id` veld gebruikt.

|Eigenschap|Vereist|Beschrijving|
|---------|---------|---------|
|Id|*klikt*|De ID van de ingebouwde rol. Deze eigenschap kan de volledige ID of alleen de GUID zijn.|
|RoleName|*klikt*|De naam van de ingebouwde rol.|