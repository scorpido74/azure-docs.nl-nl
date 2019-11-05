---
title: bestand opnemen
description: bestand opnemen
services: data-factory
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 06/27/2019
ms.author: jingwang
ms.custom: include file
ms.openlocfilehash: 26793c91c85ba4cc33a07358ab8ff318becb325f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490686"
---
## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription"></a>Azure-abonnement
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="azure-roles"></a>Azure-rollen
Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol *Inzender* of *Eigenaar*, of moet dit een *beheerder* van het Azure-abonnement zijn. Als u de machtigingen die u in het abonnement hebt, wilt weer geven, gaat u naar de [Azure Portal](https://portal.azure.com), selecteert u uw gebruikers naam in de rechter bovenhoek, selecteert u **meer opties** (...) en selecteert u vervolgens **Mijn machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren.

Als u onderliggende resources wilt maken en beheren voor Data Factory, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en integratieruntimes, zijn de volgende vereisten van toepassing:

- Als u onderliggende resources in Azure Portal wilt maken en beheren, moet u de rol **Data Factory-inzender** op minimaal het niveau van de resourcegroep hebben.
- Voor het maken en beheren van onderliggende resources met PowerShell of de SDK is de rol **Inzender** op minimaal het resourceniveau voldoende.

Zie het artikel [Rollen toevoegen](../articles/billing/billing-add-change-azure-subscription-administrator.md) voor voorbeelden van instructies voor het toevoegen van een gebruiker aan een rol.

Raadpleeg voor meer informatie de volgende artikelen:

- [Rol Data Factory-inzender](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Rollen en machtigingen voor Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-account
In deze Snelstart gaat u een algemeen Azure Storage-account (en dan met name voor Blob Storage) gebruiken als zowel *bron-* als *doel*gegevensarchieven. Zie het artikel [Een opslagaccount maken](../articles/storage/common/storage-quickstart-create-account.md) als u geen Azure Storage-account hebt voor algemene doeleinden en er een wilt maken. 

#### <a name="get-the-storage-account-name"></a>De naam van het opslag account ophalen
U hebt de naam van uw Azure Storage-account nodig voor deze Quick Start. De volgende procedure bevat stappen voor het ophalen van de naam van uw opslag account: 

1. Ga in een webbrowser naar de [Azure Portal](https://portal.azure.com) en meld u aan met uw Azure-gebruikers naam en-wacht woord.
2. Selecteer in het menu Azure Portal **alle services**en selecteer vervolgens **opslag** > **opslag accounts**. U kunt ook op elke pagina *opslag accounts* zoeken en selecteren.
3. Filter op de pagina **opslag accounts** op uw opslag account (indien nodig) en selecteer vervolgens uw opslag account. 

U kunt ook op elke pagina *opslag accounts* zoeken en selecteren.

#### <a name="create-a-blob-container"></a>Een blob-container maken
In deze sectie maakt u in Azure Blob Storage een blobcontainer met de naam **adftutorial**.

1. Selecteer op de pagina opslag account **overzicht** > **blobs**.
2. Selecteer op de werk balk *\<account naam >*  - **blobs** de pagina **container**.
3. Voer in het dialoogvenster **Nieuwe container** als naam **adftutorial** in en selecteer **OK**. De pagina *\<account naam >*  - **blobs** is bijgewerkt met **adftutorial** in de lijst met containers.

   ![Lijst met containers](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Een invoercel en een bestand voor de BLOB-container toevoegen
In deze sectie maakt u een map met de naam **invoer** in de container die u zojuist hebt gemaakt en uploadt u een voorbeeld bestand naar de map invoer. Voordat u begint, opent u een tekst editor, zoals **Klad blok**, en maakt u een bestand met de naam **EMP. txt** met de volgende inhoud:

```emp.txt
John, Doe
Jane, Doe
```

Sla het bestand op in de map **C:\ADFv2QuickStartPSH** . (Als de map nog niet bestaat, maakt u deze.) Ga vervolgens terug naar de Azure Portal en voer de volgende stappen uit:

1. Selecteer in de pagina naam van het *\<-Account >*  - **blobs** waar u bent gebleven **adftutorial** in de lijst met bijgewerkte containers.

   1. Als u het venster hebt gesloten of een andere pagina hebt bezocht, meldt u zich opnieuw aan bij de [Azure Portal](https://portal.azure.com) .
   1. Selecteer in het menu Azure Portal **alle services**en selecteer vervolgens **opslag** > **opslag accounts**. U kunt ook op elke pagina *opslag accounts* zoeken en selecteren.
   1. Selecteer uw opslag account en selecteer vervolgens **blobs** > **adftutorial**.

2. Selecteer **uploaden**op de werk balk van de pagina van de **adftutorial** -container.
3. Selecteer op de pagina **BLOB uploaden** het vak **bestanden** , blader naar en selecteer het bestand **EMP. txt** .
4. Vouw de kop **Geavanceerd** uit. De pagina wordt nu weer gegeven zoals weer gegeven:

   ![De koppeling Geavanceerd selecteren](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Voer in het vak **uploaden naar map** **invoer**in.
6. Selecteer de knop **Uploaden**. Als het goed is, ziet u in de lijst nu het bestand **emp.txt**, evenals de uploadstatus hiervan.
7. Selecteer het pictogram **sluiten** (een **X**) om de pagina **BLOB uploaden** te sluiten.

Zorg ervoor dat de **adftutorial** -container pagina geopend is. U gaat hiermee aan het einde van deze QuickStart de uitvoer controleren.