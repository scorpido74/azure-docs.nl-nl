---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74451569"
---
## <a name="deploy-template-from-cloud-shell"></a>Sjabloon implementeren vanuit Cloud Shell

U kunt [Cloud Shell](../articles/cloud-shell/overview.md) gebruiken om uw sjabloon te implementeren. Als u een externe sjabloon wilt implementeren, geeft u de URI van de sjabloon op exact dezelfde manier als bij elke externe implementatie. Als u een lokale sjabloon wilt implementeren, moet u uw sjabloon eerst laden in het opslag account voor uw Cloud Shell. In deze sectie wordt beschreven hoe u de sjabloon in uw Cloud shell-account laadt en implementeert als een lokaal bestand. Als u Cloud Shell niet hebt gebruikt, raadpleegt u [overzicht van Azure Cloud shell](../articles/cloud-shell/overview.md) voor informatie over het instellen hiervan.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer de Cloud Shell-resourcegroep. Het naampatroon is `cloud-shell-storage-<region>`.

   ![Resourcegroep selecteren](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Selecteer het opslagaccount voor Cloud Shell.

   ![Opslagaccount selecteren](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecteer **blobs**.

   ![Blobs selecteren](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selecteer **+ Container**.

   ![Container toevoegen](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Geef de container een naam en een toegangs niveau. De voorbeeld sjabloon in dit artikel bevat geen gevoelige informatie, dus sta anonieme lees toegang toe. Selecteer **OK**.

   ![Container waarden opgeven](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Selecteer de container die u hebt gemaakt.

   ![Nieuwe container selecteren](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selecteer **Uploaden**.

   ![Blob uploaden](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Zoek de sjabloon en upload deze.

   ![Bestand uploaden](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. Nadat de app is geüpload, selecteert u de sjabloon.

   ![Nieuwe sjabloon selecteren](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Kopieer de URL.

   ![URL kopiëren](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Open de prompt.

   ![Cloud Shell openen](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
