---
title: Gebruikersinterface-element CredentialsCombo
description: Beschrijft het Microsoft.Compute.CredentialsCombo UI-element voor Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: baee83e08505ddabb4680b5bbb4101479010ea47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652136"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI-element

Een groep besturingselementen met ingebouwde validatie voor Windows- en Linux-wachtwoorden en Openbare SSH-sleutels.

## <a name="ui-sample"></a>UI-voorbeeld

Voor Windows zien gebruikers het:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Voor Linux met een wachtwoord geselecteerd, zien gebruikers:

![Microsoft.Compute.CredentialsCombo Linux-wachtwoord](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Voor Linux met SSH public key geselecteerd, gebruikers zien:

![Microsoft.Compute.CredentialsCombo Linux-toets](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

## <a name="schema"></a>Schema

Gebruik voor Windows het volgende schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": {
    "password": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false
  },
  "osPlatform": "Windows",
  "visible": true
}
```

Gebruik voor **Linux**het volgende schema:

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.CredentialsCombo",
  "label": {
    "authenticationType": "Authentication type",
    "password": "Password",
    "confirmPassword": "Confirm password",
    "sshPublicKey": "SSH public key"
  },
  "toolTip": {
    "authenticationType": "",
    "password": "",
    "sshPublicKey": ""
  },
  "constraints": {
    "required": true,
    "customPasswordRegex": "^(?=.*[A-Za-z])(?=.*\\d)[A-Za-z\\d]{12,}$",
    "customValidationMessage": "The password must be alphanumeric, contain at least 12 characters, and have at least 1 letter and 1 number."
  },
  "options": {
    "hideConfirmation": false,
    "hidePassword": false
  },
  "osPlatform": "Linux",
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

Als `osPlatform` **Windows**het Windows `osPlatform` is of **Linux** en de gebruiker een wachtwoord heeft opgegeven in plaats van een Openbare SSH-sleutel, retourneert het besturingselement de volgende uitvoer:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Als `osPlatform` **Linux** en de gebruiker een SSH-openbare sleutel heeft verstrekt, retourneert het besturingselement de volgende uitvoer:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Opmerkingen

- `osPlatform`moet worden opgegeven, en kan **windows** of **Linux**.
- Als `constraints.required` deze is ingesteld op **true,** moeten de tekstvakken met betrekking tot de openbare sleutelvan het wachtwoord of de openbare sleutelvakken van SSH waarden hebben om met succes te valideren. De standaardwaarde is **waar**.
- Als `options.hideConfirmation` dit is ingesteld op **true,** wordt het tweede tekstvak voor het bevestigen van het wachtwoord van de gebruiker verborgen. De standaardwaarde is **onwaar**.
- Als `options.hidePassword` deze is ingesteld op **true,** is de optie om wachtwoordverificatie te gebruiken verborgen. Het kan alleen `osPlatform` worden gebruikt wanneer is **Linux**. De standaardwaarde is **onwaar**.
- Extra beperkingen op de toegestane wachtwoorden kunnen worden `customPasswordRegex` geïmplementeerd met behulp van de eigenschap. De tekenreeks `customValidationMessage` wordt weergegeven wanneer een wachtwoord de aangepaste validatie niet haalt. De standaardwaarde voor beide eigenschappen is **null**.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .
