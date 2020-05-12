---
title: bestand opnemen
description: bestand opnemen
services: storage
author: codemillmatt
ms.service: storage
ms.topic: include
ms.date: 11/23/2019
ms.author: masoucou
ms.custom: include file
ms.openlocfilehash: 02586d38903c60ba8982753ca0bd3e15192d5deb
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006235"
---
### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer de voorbeeld toepassing een aanvraag indient om Azure Storage, moet deze worden geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u de referenties van uw opslag account toe aan de toepassing als een connection string. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](./media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u uw connection string hebt gekopieerd, stelt u deze in op een variabele class niveau in uw *MainPage.xaml.cs* -bestand. Open *MainPaage.xaml.cs* en zoek de `storageConnectionString` variabele. Vervang `<yourconnectionstring>` door de werkelijke Connection String.

Hier volgt de code:

```csharp
string storageConnectionString = "<yourconnectionstring>";
```