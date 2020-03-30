---
title: Een Azure Migrate-applianc instellen
description: Meer informatie over het instellen van een Azure Migrate-toestel om Vm's van VMware te beoordelen en te migreren.
ms.topic: article
ms.date: 11/18/2019
ms.openlocfilehash: 0447443bb6a642cac09566450ea2d9bb6f6453d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337660"
---
# <a name="set-up-an-azure-migrate-appliance"></a>Een Azure Migrate-toestel instellen

In dit artikel worden de opties voor het instellen van het Azure Migrate-toestel samengevat. 

Het Azure Migrate-toestel is een on-premises implementatie van lichtgewicht apparaten en wordt in een aantal scenario's gebruikt.

**Scenario** | **Details**
--- | ---
VMware VM's beoordelen met Azure Migrate:Server Assessment | VM-apps en -afhankelijkheden ontdekken<br/><br/> Verzamel metagegevens en prestatiemetadata voor beoordelingen.
Hyper-V VM's beoordelen met Azure Migrate:Server Assessment | Ontdek Hyper-V VM's<br/><br/> Verzamel metagegevens en prestatiemetadata voor beoordelingen.
Fysieke machines beoordelen | Ontdek machines als fysieke servers<br/><br/> Verzamel metagegevens en prestatiemetadata voor beoordelingen.
VMware VM's repliceren met agentloze migratie. | Repliceer VMware VM's zonder iets te installeren op de VM's die u wilt repliceren.


## <a name="deployment-options"></a>Implementatieopties

U het apparaat op een aantal manieren implementeren.

**Scenario** | **Sjabloon** | **Script** 
--- | --- | --- | ---
**VMware VM's beoordelen** | Implementeren met een gedownloade OVA-sjabloon.<br/><br/> Lees hoe u het toestel [implementeert met behulp van een sjabloon](how-to-set-up-appliance-vmware.md)of start de [zelfstudie van](tutorial-prepare-vmware.md) de beoordeling en implementeer het toestel tijdens de zelfstudie met een sjabloon.  | Implementeren met behulp van een PowerShell-installatiescript.<br/><br/>  [Instructies voor](deploy-appliance-script.md) toestelscript controleren.
**Hyper-V VM's beoordelen** | Implementeren met een gedownloade VHD-sjabloon. <br/><br/> Lees hoe u het toestel [implementeert met behulp van een sjabloon](how-to-set-up-appliance-vmware.md)of start de [zelfstudie van](tutorial-prepare-vmware.md) de beoordeling en implementeer het toestel tijdens de zelfstudie met een sjabloon. | Implementeren met behulp van een PowerShell-installatiescript.<br/><br/> [Instructies voor](deploy-appliance-script.md) toestelscript controleren. 
**Fysieke servers evalueren** | Geen sjabloon. | Implementeren met behulp van een PowerShell-installatiescript.<br/><br/> Controleer [de scriptinstructies van het toestel](how-to-set-up-appliance-physical.md)of start de [zelfstudie](tutorial-prepare-physical.md)van de beoordeling en implementeer het toestel tijdens de zelfstudie.
**Vm's vmware repliceren (zonder agent)** | Implementeren met een gedownloade OVA-sjabloon.<br/><br/> Als u vm's die u nabootst al hebt beoordeeld, hebt u het toestel al ingesteld tijdens de beoordeling.<br/><br/> Als u VMware VM's nabootst zonder ze te beoordelen, leest u hoe u het toestel implementeert met behulp van een sjabloon of start u de [zelfstudie met agentloze migratie](tutorial-migrate-vmware.md)en implementeert u het toestel met een sjabloon tijdens de zelfstudie. | Implementeren met behulp van een PowerShell-installatiescript. <br/><br/> [Instructies voor](deploy-appliance-script.md) toestelscript controleren. 




## <a name="next-steps"></a>Volgende stappen

[Bekijk](migrate-appliance.md) de vereisten voor het toestel.