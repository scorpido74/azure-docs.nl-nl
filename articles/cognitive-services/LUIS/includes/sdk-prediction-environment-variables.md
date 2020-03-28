---
title: bestand opnemen
description: bestand opnemen
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772442"
---
### <a name="create-an-environment-variable"></a>Een omgevingsvariabele maken

Maak met uw runtime-sleutel en het eindpunt runtime omgevingsvariabelen voor verificatie en toegang:

* `LUIS_RUNTIME_KEY`- De runtime resource-sleutel voor het verifiëren van uw aanvragen.
* `LUIS_RUNTIME_ENDPOINT`- Het eindpunt runtime dat aan uw sleutel is gekoppeld.
* `LUIS_APP_ID`- De openbare LUIS IoT-app-id is `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production`Of`staging`

Als u van plan bent deze quickstart te gebruiken om toegang te krijgen tot uw eigen app, moet u aanvullende stappen uitvoeren:
* De app maken en de app-id ophalen
* De runtime-sleutel toewijzen aan de app in de LUIS-portal
* Test de URL met de browser, zodat u toegang hebt tot de app

Gebruik de instructies voor uw besturingssysteem.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Nadat u de omgevingsvariabelen hebt toegevoegd, start u het consolevenster opnieuw.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Nadat u de omgevingsvariabelen `source ~/.bashrc` hebt toegevoegd, voert u vanuit uw consolevenster uit om de wijzigingen effectief te maken.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk `.bash_profile`uw onaantal en voeg de omgevingsvariabele toe:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Nadat u de omgevingsvariabelen `source .bash_profile` hebt toegevoegd, voert u vanuit uw consolevenster uit om de wijzigingen effectief te maken.

***
