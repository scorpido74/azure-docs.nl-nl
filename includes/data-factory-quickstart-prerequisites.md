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
ms.openlocfilehash: 4d77cb8128105a40143a40e48ebe450115f7cf1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164133"
---
## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription"></a>Azure-abonnement
Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

### <a name="azure-roles"></a>Azure-rollen
Als u Data Factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, lid zijn van de rol *Inzender* of *Eigenaar*, of moet dit een *beheerder* van het Azure-abonnement zijn. Als u de machtigingen wilt weergeven die u in het abonnement hebt, gaat u naar de [Azure-portal,](https://portal.azure.com)selecteert u uw gebruikersnaam in de rechterbovenhoek, selecteert u "**...**" pictogram voor meer opties en selecteert u **Mijn machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren.

Als u onderliggende resources wilt maken en beheren voor Data Factory, waaronder gegevenssets, gekoppelde services, pijplijnen, triggers en integratieruntimes, zijn de volgende vereisten van toepassing:

- Als u onderliggende resources in Azure Portal wilt maken en beheren, moet u de rol **Data Factory-inzender** op minimaal het niveau van de resourcegroep hebben.
- Voor het maken en beheren van onderliggende resources met PowerShell of de SDK is de rol **Inzender** op minimaal het resourceniveau voldoende.

Zie het artikel [Rollen toevoegen](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) voor voorbeelden van instructies voor het toevoegen van een gebruiker aan een rol.

Raadpleeg voor meer informatie de volgende artikelen:

- [Rol Data Factory-inzender](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Rollen en machtigingen voor Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md)

### <a name="azure-storage-account"></a>Azure Storage-account
U gebruikt een Azure Storage-account voor algemene doeleinden (met name Blob-opslag) als *bron-* en *doelgegevensopslag* in deze quickstart. Zie [Een opslagaccount maken](../articles/storage/common/storage-account-create.md) om er een te maken als u geen Azure Storage-account voor algemene doeleinden hebt. 

#### <a name="get-the-storage-account-name"></a>De naam van het opslagaccount opmaken
U hebt de naam van uw Azure Storage-account nodig voor deze quickstart. De volgende procedure bevat stappen om de naam van uw opslagaccount op te halen: 

1. Ga in een webbrowser naar de [Azure-portal](https://portal.azure.com) en meld u aan met uw Azure-gebruikersnaam en -wachtwoord.
2. Selecteer in het menu van de Azure-portal **Alle services**en selecteer vervolgens**Opslagopslagaccounts** **Storage** > . U ook zoeken naar *opslagaccounts* op elke pagina en selecteren.
3. Filter op de pagina **Opslagaccounts** voor uw opslagaccount (indien nodig) en selecteer vervolgens uw opslagaccount. 

U ook zoeken naar *opslagaccounts* op elke pagina en selecteren.

#### <a name="create-a-blob-container"></a>Een blob-container maken
In deze sectie maakt u in Azure Blob Storage een blobcontainer met de naam **adftutorial**.

1. Selecteer op de pagina Opslagaccount de optie > **Overzichtscontainers**. **Overview**
2. Selecteer **op**de werkbalk van de  -  **Containers** * \< *pagina Accountnaam>containers de optie Container .
3. Voer in het dialoogvenster **Nieuwe container** als naam **adftutorial** in en selecteer **OK**. De * \< *pagina Accountnaam  - >**containers** wordt bijgewerkt om **adftutorial** op te nemen in de lijst met containers.

   ![Lijst van containers](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Een invoermap en -bestand toevoegen voor de blobcontainer
In deze sectie maakt u een map met de naam **invoer** in de container die u zojuist hebt gemaakt en uploadt u een voorbeeldbestand naar de invoermap. Open voordat u begint een teksteditor zoals **Kladblok**en maak een bestand met de naam **emp.txt** met de volgende inhoud:

```emp.txt
John, Doe
Jane, Doe
```

Sla het bestand op in de map **C:\ADFv2QuickStartPSH.** (Als de map nog niet bestaat, maakt u deze.) Ga vervolgens terug naar de Azure-portal en volg de volgende stappen:

1. Selecteer **adftutorial** in de  -  * \< *pagina Accountnaam>**containers** waar u was gebleven adftutorial in de bijgewerkte lijst met containers.

   1. Als u het venster hebt gesloten of naar een andere pagina bent gegaan, meldt u zich opnieuw aan bij de [Azure-portal.](https://portal.azure.com)
   1. Selecteer in het menu van de Azure-portal **Alle services**en selecteer vervolgens**Opslagopslagaccounts** **Storage** > . U ook zoeken naar *opslagaccounts* op elke pagina en selecteren.
   1. Selecteer uw opslagaccount en selecteer vervolgens**Adftutorial** **Containers** > .

2. Selecteer op de werkbalk van de **adftutorial-containerpagina** De optie **Uploaden**.
3. Selecteer op de **pagina Blob uploaden** het vak **Bestanden** en blader naar het **emp.txt-bestand** en selecteer deze.
4. De **kop Geavanceerd** uitbreiden. De pagina wordt nu weergegeven zoals weergegeven:

   ![De koppeling Geavanceerd selecteren](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Voer in het vak **Uploaden naar map** **invoer**in .
6. Selecteer de knop **Uploaden**. Als het goed is, ziet u in de lijst nu het bestand **emp.txt**, evenals de uploadstatus hiervan.
7. Selecteer het pictogram **Sluiten** (een **X)** om de **blobpagina uploaden** te sluiten.

Houd de **adftutorial-containerpagina** open. U gaat hiermee aan het einde van deze QuickStart de uitvoer controleren.