---
title: bestand opnemen
description: bestand opnemen
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: mhopkins
ms.custom: include file
ms.openlocfilehash: 7dd22886d11c3a35a7a866ff7c9a4f56ea74cab7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75351228"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer de voorbeeldtoepassing een aanvraag voor Azure Storage indient, moet deze zijn geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u uw opslagaccountreferenties toe aan de toepassing als verbindingstekenreeks. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door uw werkelijke verbindingstekenreeks.

#### <a name="windows"></a>Windows

```cmd
setx AZURE_STORAGE_CONNECTION_STRING "<yourconnectionstring>"
```

Nadat u de omgevingsvariabele in Windows hebt toegevoegd, moet u een nieuwe instantie van het opdrachtvenster starten.

#### <a name="linux"></a>Linux

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export AZURE_STORAGE_CONNECTION_STRING="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Programma's opnieuw starten

Nadat u de omgevingsvariabele hebt toegevoegd, start u alle lopende programma's opnieuw die de omgevingsvariabele moeten lezen. Start bijvoorbeeld uw ontwikkelomgeving of editor opnieuw voordat u verdergaat.
