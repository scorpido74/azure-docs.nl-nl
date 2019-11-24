---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: aac2f3ea2b52ac0319f96279deed13c1145749bd
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/23/2019
ms.locfileid: "74451569"
---
## <a name="deploy-template-from-cloud-shell"></a>Sjabloon implementeren vanuit Cloud Shell

U kunt [Cloud Shell](../articles/cloud-shell/overview.md) gebruiken om uw sjabloon te implementeren. To deploy an external template, provide the URI of the template exactly as you would for any external deployment. To deploy a local template, you must first load your template into the storage account for your Cloud Shell. This section describes how to load the template to your cloud shell account, and deploy it as a local file. If you haven't used Cloud Shell, see [Overview of Azure Cloud Shell](../articles/cloud-shell/overview.md) for information about setting it up.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer de Cloud Shell-resourcegroep. Het naampatroon is `cloud-shell-storage-<region>`.

   ![Resourcegroep selecteren](./media/resource-manager-cloud-shell-deploy/select-cloud-shell-resource-group.png)

1. Selecteer het opslagaccount voor Cloud Shell.

   ![Opslagaccount selecteren](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Selecteer **Blobs**.

   ![Blobs selecteren](./media/resource-manager-cloud-shell-deploy/select-blobs.png)

1. Selecteer **+ Container**.

   ![Container toevoegen](./media/resource-manager-cloud-shell-deploy/add-container.png)

1. Give your container a name and an access level. The sample template in this article contains no sensitive information, so allow anonymous read access. Selecteer **OK**.

   ![Provide container values](./media/resource-manager-cloud-shell-deploy/provide-container-values.png)

1. Select the container you created.

   ![Select new container](./media/resource-manager-cloud-shell-deploy/select-container.png)

1. Selecteer **Uploaden**.

   ![Upload blob](./media/resource-manager-cloud-shell-deploy/upload-blob.png)

1. Zoek de sjabloon en upload deze.

   ![Bestand uploaden](./media/resource-manager-cloud-shell-deploy/find-and-upload-template.png)

1. After it has uploaded, select the template.

   ![Select new template](./media/resource-manager-cloud-shell-deploy/select-new-template.png)

1. Copy the URL.

   ![Copy URL](./media/resource-manager-cloud-shell-deploy/copy-url.png)

1. Open de prompt.

   ![Cloud Shell openen](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
