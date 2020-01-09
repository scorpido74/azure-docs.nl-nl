---
title: Naslag informatie over definitie artefacten weer geven
description: Hier vindt u een voor beeld van het weer geven van definitie artefacten voor Azure Managed Applications. De bestands naam is viewDefinition. json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75651200"
---
# <a name="reference-view-definition-artifact"></a>Verwijzing: definitie-artefact weer geven

Dit artikel bevat een verwijzing naar een *viewDefinition. json* -artefact in azure Managed Applications. Zie [definition artefact weer geven](concepts-view-definition.md)voor meer informatie over de configuratie van de ontwerp weergaven.

## <a name="view-definition"></a>Definitie weergeven

In de volgende JSON ziet u een voor beeld van het bestand *viewDefinition. json* voor Azure Managed Applications:

```json
{
  "views": [{
    "kind": "Overview",
    "properties": {
      "header": "Welcome to your Demo Azure Managed Application",
      "description": "This Managed application with Custom Provider is for demo purposes only.",
      "commands": [{
          "displayName": "Ping Action",
          "path": "/customping",
          "icon": "LaunchCurrent"
      }]
    }
  },
  {
    "kind": "CustomResources",
    "properties": {
      "displayName": "Users",
      "version": "1.0.0.0",
      "resourceType": "users",
      "createUIDefinition": {
        "parameters": {
          "steps": [{
            "name": "add",
            "label": "Add user",
            "elements": [{
              "name": "name",
              "label": "User's Full Name",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a full user name.",
              "constraints": { "required": true }
            },
            {
              "name": "location",
              "label": "User's Location",
              "type": "Microsoft.Common.TextBox",
              "defaultValue": "",
              "toolTip": "Provide a Location.",
              "constraints": { "required": true }
            }]
          }],
          "outputs": {
            "name": "[steps('add').name]",
            "properties": {
              "FullName": "[steps('add').name]",
              "Location": "[steps('add').location]"
            }
          }
        }
      },
      "commands": [{
        "displayName": "Custom Context Action",
        "path": "users/contextAction",
        "icon": "Start"
      }],
      "columns": [
        { "key": "properties.FullName", "displayName": "Full Name" },
        { "key": "properties.Location", "displayName": "Location", "optional": true }
      ]
    }
  }]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Zelf studie: een beheerde toepassing maken met aangepaste acties en resources](tutorial-create-managed-app-with-custom-provider.md)
- [Verwijzing: elementen artefact van gebruikers interface](reference-createuidefinition-artifact.md)
- [Verwijzing: implementatie sjabloon artefact](reference-main-template-artifact.md)