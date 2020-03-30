---
title: Uw cluster beveiligen in Azure Data Explorer
description: In dit artikel wordt beschreven hoe u uw cluster in Azure Data Explorer in de Azure-portal beveiligen.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 0f935999b68a7283c032d43c42d688b273d5c450
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720341"
---
# <a name="secure-your-cluster-in-azure-data-explorer---azure-portal"></a>Uw cluster beveiligen in Azure Data Explorer - Azure-portal

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) helpt uw gegevens te beschermen en te beschermen om te voldoen aan uw organisatorische beveiligings- en nalevingsverplichtingen. Het biedt volumeversleuteling voor het besturingssysteem en gegevensschijven van uw virtuele clustermachines. Het integreert ook met [Azure Key Vault](/azure/key-vault/), waarmee we de schijfversleutelingssleutels en -geheimen kunnen beheren en ervoor kunnen zorgen dat alle gegevens op de VM-schijven worden versleuteld. 
  
## <a name="enable-encryption-at-rest-in-the-azure-portal"></a>Versleuteling in de rust inschakelen in de Azure-portal
  
Met de clusterbeveiligingsinstellingen u schijfversleuteling op uw cluster inschakelen. Versleuteling [in de rust](/azure/security/fundamentals/encryption-atrest) op uw cluster inschakelen biedt gegevensbescherming voor opgeslagen gegevens (in rust). 

1. Ga in de Azure-portal naar uw Azure Data Explorer-clusterbron. Selecteer **Beveiliging** onder **Security**de kop Instellingen . 

    ![Versleuteling in rust inschakelen](media/manage-cluster-security/security-encryption-at-rest.png)

1. Selecteer **in** het venster Beveiliging de optie **Aan** voor de beveiligingsinstelling **Schijfversleuteling.** 

1. Selecteer **Opslaan**.
 
> [!NOTE]
> Selecteer **Uit** om de versleuteling uit te schakelen nadat deze is ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

[Clusterstatus controleren](/azure/data-explorer/check-cluster-health)
