---
title: Overzicht van aangepaste roldefinities
description: Beschrijft het concept van het maken van aangepaste roldefinities voor beheerde toepassingen.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 09/16/2019
ms.openlocfilehash: 88e42fd9626276f6c77b46b33c138407f91d06ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650758"
---
# <a name="custom-role-definition-artifact-in-azure-managed-applications"></a>Aangepast artefact voor roldefinitie in Azure Managed Applications

Aangepaste roldefinitie is een optioneel artefact in beheerde toepassingen. Het wordt gebruikt om te bepalen welke machtigingen de beheerde toepassing nodig heeft om de functies uit te voeren.

In dit artikel vindt u een overzicht van het aangepaste artefact voor roldefinitie en de mogelijkheden ervan.

## <a name="custom-role-definition-artifact"></a>Aangepast artefact voor roldefinitie

U moet de aangepaste roldefinitieartefact customRoleDefinition.json een naam geven. Plaats het op hetzelfde niveau als createUiDefinition.json en mainTemplate.json in het .zip-pakket dat een beheerde toepassingsdefinitie maakt. Zie [Een beheerde toepassingsdefinitie publiceren](publish-managed-app-definition-quickstart.md) voor meer informatie over het maken van het .zip-pakket en het publiceren van een beheerde toepassingsdefinitie.

## <a name="custom-role-definition-schema"></a>Schema voor aangepaste roldefinitie

Het bestand customRoleDefinition.json heeft `roles` een eigenschap op het hoogste niveau die een scala aan rollen is. Deze rollen zijn de machtigingen die de beheerde toepassing moet functioneren. Momenteel zijn alleen ingebouwde rollen toegestaan, maar u meerdere rollen opgeven. Een rol kan worden verwezen door de ID van de roldefinitie of door de rolnaam.

Voorbeeld van JSON voor aangepaste roldefinitie:

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

Een rol bestaat uit `$.properties.roleName` a `id`of een :

```json
{
    "id": null,
    "properties": {
        "roleName": "Contributor"
    }
}
```

> [!NOTE]
> U het `id` `roleName` of veld gebruiken. Er is er maar één nodig. Deze velden worden gebruikt om de roldefinitie op te zoeken die moet worden toegepast. Als beide worden geleverd, zal het `id` veld worden gebruikt.

|Eigenschap|Vereist?|Beschrijving|
|---------|---------|---------|
|id|Ja|De ID van de ingebouwde rol. U de volledige ID of alleen de GUID gebruiken.|
|roleName|Ja|De naam van de ingebouwde rol.|