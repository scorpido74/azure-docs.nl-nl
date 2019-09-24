---
title: Schijf versleuteling Toep assen in Azure Security Center | Microsoft Docs
description: Dit document bevat informatie over het implementeren van de Azure Security Center aanbeveling **schijf versleuteling Toep assen**.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6cc7824a-8d6b-4a5f-ab40-e3bbaebc4a91
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 0ae2b695a64e55b493453549627860872166ed25
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202498"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Schijf versleuteling Toep assen in Azure Security Center
Azure Security Center raadt u aan om schijf versleuteling toe te passen als u virtuele Windows-of Linux-schijven hebt die niet zijn versleuteld met Azure Disk Encryption. Met schijf versleuteling kunt u uw virtuele Windows-en Linux IaaS-VM-schijven versleutelen.  Versleuteling wordt aanbevolen voor het besturingssysteem en voor de gegevensvolumes op de VM.

Schijf versleuteling maakt gebruik van de industrie standaard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) -functie van Windows en de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux. Deze functies bieden besturings systeem-en gegevens versleuteling om uw gegevens te beschermen en beschermen en te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Schijf versleuteling is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om u te helpen de schijf versleutelings sleutels en geheimen in uw Key Vault-abonnement te controleren en te beheren, terwijl u ervoor zorgt dat alle gegevens in de VM-schijven op rest worden versleuteld in uw [Azure Storage](https://azure.microsoft.com/documentation/services/storage/).

> [!NOTE]
> Azure Disk Encryption wordt ondersteund op de volgende Windows Server-besturings systemen: Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2. Schijf versleuteling wordt ondersteund op de volgende besturings systemen van de Linux-server: Ubuntu, CentOS, SUSE en SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>De aanbeveling implementeren
1. Selecteer op de Blade **aanbevelingen** de optie **schijf versleuteling Toep assen**.
2. Op de Blade **schijf versleuteling Toep assen** ziet u een lijst met virtuele machines waarvoor schijf versleuteling wordt aanbevolen.
3. Volg de instructies om versleuteling toe te passen op deze Vm's.

![][1]

Voor het versleutelen van Azure-Virtual Machines die zijn geïdentificeerd door Security Center als versleuteling nodig is, raden we de volgende stappen uit:

* Installeer en configureer Azure PowerShell. Hierdoor kunt u de Power shell-opdrachten uitvoeren die zijn vereist voor het instellen van de vereiste onderdelen voor het versleutelen van Azure Virtual Machines.
* De Azure Disk Encryption vereisten Azure PowerShell script verkrijgen en uitvoeren.
* Versleutel uw virtuele machines.

[Een Windows IaaS-VM met Azure PowerShell versleutelen](../security/azure-disk-encryption-linux-powershell-quickstart.md) , wordt u door de volgende stappen geleid. In dit onderwerp wordt ervan uitgegaan dat u een Windows-client computer gebruikt van waaruit u schijf versleuteling configureert.

Er zijn veel benaderingen die kunnen worden gebruikt voor Azure Virtual Machines. Als u al goed bekend bent met Azure PowerShell of Azure CLI, maakt u mogelijk liever gebruik van een andere manier. Zie [Azure Disk Encryption](../security/azure-security-disk-encryption.md)(Engelstalig) voor meer informatie over deze andere benaderingen.

## <a name="see-also"></a>Zie ook
In dit document wordt uitgelegd hoe u de Security Center aanbeveling ' schijf versleuteling Toep assen ' implementeert. Zie het volgende voor meer informatie over schijf versleuteling:

* [Versleuteling en sleutel beheer met Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sec)--meer informatie over het gebruik van schijf versleuteling voor IaaS-Vm's en Azure Key Vault om uw gegevens te beschermen en te beveiligen.
* [Azure Disk Encryption](../security/azure-security-disk-encryption-overview.md) (document): meer informatie over het inschakelen van schijf versleuteling voor Windows-en Linux-Vm's.

Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md) --meer informatie over het configureren van beveiligings beleid.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md) --meer informatie over het controleren van de status van uw Azure-resources.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md) : Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/) : vind blog berichten over beveiliging en naleving van Azure.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
