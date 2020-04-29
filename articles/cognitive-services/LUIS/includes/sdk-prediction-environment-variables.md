---
title: bestand opnemen
description: bestand opnemen
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76772442"
---
### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

Met uw runtime sleutel en het runtime-eind punt kunt u omgevings variabelen maken voor verificatie en toegang:

* `LUIS_RUNTIME_KEY`-De bron sleutel van de runtime voor het verifiëren van uw aanvragen.
* `LUIS_RUNTIME_ENDPOINT`-Het runtime-eind punt dat aan uw sleutel is gekoppeld.
* `LUIS_APP_ID`-De ID van de open bare LUIS `df67dcdb-c37d-46af-88e1-8b97951ca1c2`IOT-app is.
* `LUIS_APP_SLOT_NAME` - `production`of`staging`

Als u van plan bent om deze Snelstartgids te gebruiken om toegang te krijgen tot uw eigen app, moet u extra stappen uitvoeren:
* De app maken en de App-ID ophalen
* De runtime sleutel toewijzen aan de app in de LUIS-Portal
* De URL testen met de browser, waarmee u toegang hebt tot de app

Volg de instructies voor uw besturings systeem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Nadat u de omgevings variabelen hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Nadat u de omgevings variabelen hebt toegevoegd `source ~/.bashrc` , voert u uit vanuit het console venster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[macOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Nadat u de omgevings variabelen hebt toegevoegd `source .bash_profile` , voert u uit vanuit het console venster om de wijzigingen van kracht te laten worden.

***
