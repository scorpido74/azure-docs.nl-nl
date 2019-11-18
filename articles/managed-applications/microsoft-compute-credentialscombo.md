---
title: Gebruikers interface-element van Azure CredentialsCombo | Microsoft Docs
description: Hierin wordt het UI-element micro soft. compute. CredentialsCombo voor Azure Portal beschreven.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/29/2018
ms.author: tomfitz
ms.openlocfilehash: c1b4791f2ec80eba25a00e22cb4298b4c97da4de
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151082"
---
# <a name="microsoftcomputecredentialscombo-ui-element"></a>Microsoft.Compute.CredentialsCombo UI element

Een groep besturings elementen met ingebouwde validatie voor Windows-en Linux-wacht woorden en open bare SSH-sleutels.

## <a name="ui-sample"></a>UI-voor beeld

Voor Windows zien gebruikers het volgende:

![Microsoft.Compute.CredentialsCombo Windows](./media/managed-application-elements/microsoft.compute.credentialscombo-windows.png)

Voor Linux waarbij een wacht woord is geselecteerd, zien gebruikers:

![Micro soft. compute. CredentialsCombo Linux-wacht woord](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-password.png)

Voor Linux waarvoor een open bare SSH-sleutel is geselecteerd, zien gebruikers:

![Micro soft. compute. CredentialsCombo Linux-sleutel](./media/managed-application-elements/microsoft.compute.credentialscombo-linux-key.png)

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

Als `osPlatform` **Windows**is of als `osPlatform` **Linux** is en de gebruiker een wacht woord heeft opgegeven in plaats van een open bare SSH-sleutel, retourneert het besturings element de volgende uitvoer:

```json
{
  "authenticationType": "password",
  "password": "p4ssw0rddem0",
}
```

Als `osPlatform` **Linux** is en de gebruiker een open bare SSH-sleutel heeft gegeven, retourneert het besturings element de volgende uitvoer:

```json
{
  "authenticationType": "sshPublicKey",
  "sshPublicKey": "AAAAB3NzaC1yc2EAAAABIwAAAIEA1on8gxCGJJWSRT4uOrR13mUaUk0hRf4RzxSZ1zRbYYFw8pfGesIFoEuVth4HKyF8k1y4mRUnYHP1XNMNMJl1JcEArC2asV8sHf6zSPVffozZ5TT4SfsUu/iKy9lUcCfXzwre4WWZSXXcPff+EHtWshahu3WzBdnGxm5Xoi89zcE=",
}
```

## <a name="remarks"></a>Opmerkingen

- `osPlatform` moet worden opgegeven, en dit kan **Windows** of **Linux**zijn.
- Als `constraints.required` is ingesteld op **True**, moeten de tekst vakken voor het wacht woord of de open bare SSH-sleutel waarden bevatten om te kunnen valideren. De standaard waarde is **True**.
- Als `options.hideConfirmation` is ingesteld op **True**, wordt het tweede tekstvak voor het bevestigen van het wacht woord van de gebruiker verborgen. De standaardwaarde is **false**.
- Als `options.hidePassword` is ingesteld op **True**, is de optie voor het gebruik van wachtwoord verificatie verborgen. Het kan alleen worden gebruikt wanneer `osPlatform` **Linux**is. De standaardwaarde is **false**.
- Aanvullende beperkingen voor de toegestane wacht woorden kunnen worden geïmplementeerd met behulp van de eigenschap `customPasswordRegex`. De teken reeks in `customValidationMessage` wordt weer gegeven wanneer een wacht woord aangepaste validatie mislukt. De standaard waarde voor beide eigenschappen is **Null**.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
