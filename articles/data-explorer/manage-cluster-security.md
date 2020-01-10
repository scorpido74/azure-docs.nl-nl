---
title: Uw cluster beveiligen in azure Data Explorer
description: In dit artikel wordt beschreven hoe u uw cluster in azure Data Explorer kunt beveiligen binnen de Azure Portal.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75720341"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Uw cluster beveiligen in azure Data Explorer-Azure Portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) helpt uw gegevens te beschermen en beschermen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Het biedt volume versleuteling voor het besturings systeem en de gegevens schijven van de virtuele cluster machines. Het kan ook worden geïntegreerd met [Azure Key Vault](/azure/key-vault/), waarmee we de schijf versleutelings sleutels en geheimen kunnen beheren en beheren en ervoor zorgen dat alle gegevens op de VM-schijven worden versleuteld. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Versleuteling inschakelen in de rest van de Azure Portal
  
Met de beveiligings instellingen van uw cluster kunt u schijf versleuteling inschakelen voor uw cluster. Het inschakelen van [versleuteling in rust](/azure/security/fundamentals/encryption-atrest) op uw cluster biedt gegevens beveiliging voor opgeslagen gegevens (op rest). 

1. Ga in het Azure Portal naar de cluster bron van Azure Data Explorer. Selecteer in de kop **instellingen** de optie **beveiliging**. 

    ![Versleuteling inschakelen bij rest](media/manage-cluster-security/security-encryption-at-rest.png)

1. Selecteer in het venster **beveiliging** de optie **aan** voor de beveiligings instelling **schijf versleuteling** . 

1. Selecteer **Opslaan**.
 
> [!NOTE]
> Selecteer **uit** om de versleuteling uit te scha kelen nadat deze is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

[De cluster status controleren](/azure/data-explorer/check-cluster-health)
