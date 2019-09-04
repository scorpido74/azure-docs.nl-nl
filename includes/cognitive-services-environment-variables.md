---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274578"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Een omgevings variabele configureren voor verificatie

Toepassingen moeten toegang verifiëren tot de Cognitive Services die ze gebruiken. Als u zich wilt verifiëren, raden we u aan een omgevings variabele te maken voor het opslaan van de sleutels voor uw Azure-resources. 

Nadat u uw sleutel hebt, schrijft u deze naar een nieuwe omgevings variabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `your-key` door een van de sleutels voor uw resource.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Nadat u de omgevingsvariabele hebt toegevoegd, moet u actieve programma's die de omgevingsvariabele moeten lezen, opnieuw starten. Start ook het consolevenster opnieuw. Als u bijvoorbeeld Visual Studio als editor gebruikt, start u Visual Studio opnieuw voordat u het voor beeld uitvoert.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macostabunix"></a>[MacOS](#tab/unix)

Bewerk uw .bash_profile en voeg de omgevingsvariabele toe:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

***