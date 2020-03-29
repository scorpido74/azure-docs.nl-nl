---
title: Referentie van definitieartefact weergeven
description: Biedt een voorbeeld van het artefact voor weergavedefinitie voor Azure Managed Applications. De bestandsnaam is viewDefinition.json.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 07/11/2019
ms.openlocfilehash: 5173db54abef132a4a4d5d117881352ca37d6b23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651200"
---
# <a name="reference-view-definition-artifact"></a>Referentie: Artefact voor definitie weergeven

Dit artikel is een verwijzing naar een *viewDefinition.json-artefact* in Azure Managed Applications. Zie Artefact definitie van weergaven [weergeven](concepts-view-definition.md)voor meer informatie over de configuratie van weergaven.

## <a name="view-definition"></a>Definitie weergeven

In het volgende JSON wordt een voorbeeld weergegeven van het bestand *viewDefinition.json* voor Azure Managed Applications:

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

- [Zelfstudie: Beheerde toepassing maken met aangepaste acties en resources](tutorial-create-managed-app-with-custom-provider.md)
- [Naslag: Artefact van gebruikersinterface-elementen](reference-createuidefinition-artifact.md)
- [Naslag: Artefact van implementatiesjablonen](reference-main-template-artifact.md)