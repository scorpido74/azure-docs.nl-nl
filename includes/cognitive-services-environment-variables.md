---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274578"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Een omgevingsvariabele configureren voor verificatie

Toepassingen moeten de toegang tot de cognitieve services die ze gebruiken verifiëren. Als u dit wilt verifiëren, raden we u aan een omgevingsvariabele te maken om de sleutels voor uw Azure-bronnen op te slaan. 

Nadat u uw sleutel hebt, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale machine waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `your-key` door een van de sleutels voor uw resource.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Nadat u de omgevingsvariabele hebt toegevoegd, moet u actieve programma's die de omgevingsvariabele moeten lezen, opnieuw starten. Start ook het consolevenster opnieuw. Als u bijvoorbeeld Visual Studio als editor gebruikt, start u Visual Studio opnieuw op voordat u het voorbeeld uitvoert.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Nadat u de omgevingsvariabele toevoegt, voert u `source ~/.bashrc` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

#### <a name="macos"></a>[Macos](#tab/unix)

Bewerk uw .bash_profile en voeg de omgevingsvariabele toe:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

Nadat u de omgevingsvariabele toevoegt, voert u `source .bash_profile` uit vanuit het consolevenster om de wijzigingen van kracht te laten worden.

***