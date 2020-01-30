---
title: bestand opnemen
description: bestand opnemen
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772442"
---
### <a name="create-an-environment-variable"></a>Een omgevings variabele maken

Met uw runtime sleutel en het runtime-eind punt kunt u omgevings variabelen maken voor verificatie en toegang:

* `LUIS_RUNTIME_KEY`-de runtime bron sleutel voor het verifiëren van uw aanvragen.
* `LUIS_RUNTIME_ENDPOINT`-het runtime-eind punt dat aan uw sleutel is gekoppeld.
* `LUIS_APP_ID`-de ID van de open bare LUIS IoT-app is `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production` of `staging`

Als u van plan bent om deze Snelstartgids te gebruiken om toegang te krijgen tot uw eigen app, moet u extra stappen uitvoeren:
* De app maken en de App-ID ophalen
* De runtime sleutel toewijzen aan de app in de LUIS-Portal
* De URL testen met de browser, waarmee u toegang hebt tot de app

Volg de instructies voor uw besturings systeem.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Nadat u de omgevings variabelen hebt toegevoegd, start u het console venster opnieuw.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Nadat u de omgevings variabelen hebt toegevoegd, voert u `source ~/.bashrc` uit vanuit het console venster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw `.bash_profile`en voeg de omgevings variabele toe:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Nadat u de omgevings variabelen hebt toegevoegd, voert u `source .bash_profile` uit vanuit het console venster om de wijzigingen van kracht te laten worden.

***
