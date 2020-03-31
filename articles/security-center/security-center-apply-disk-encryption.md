---
title: Schijfversleuteling toepassen in Azure Security Center | Microsoft Documenten
description: In dit document ziet u hoe u de aanbeveling van het Azure Security Center **implementeert Schijfversleuteling toepassen.**
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
ms.openlocfilehash: d0f96fe758966a435f8fb8e448e75cbb18b85122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604528"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Schijfversleuteling toepassen in Azure Security Center
Azure Security Center adviseert om schijfversleuteling toe te passen als u schijven in virtuele Windows- of Linux-machines hebt die niet zijn versleuteld met Azure Disk Encryption. U kunt schijfversleuteling gebruiken voor schijven van IaaS-VM's voor Windows en Linux.  Versleuteling wordt aanbevolen voor het besturingssysteem en voor de gegevensvolumes op de VM.

Disk Encryption maakt gebruik van de industrie standaard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) functie van Windows en de [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux. Deze functies bieden os- en gegevensversleuteling om uw gegevens te beschermen en te beschermen en te voldoen aan uw verplichtingen op het gebied van organisatiebeveiliging en naleving. Schijfversleuteling is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om u te helpen de schijfversleutelingssleutels en -geheimen in uw Key Vault-abonnement te beheren en te beheren, terwijl u ervoor zorgt dat alle gegevens in de VM-schijven in rust worden versleuteld in uw Azure [Storage.](https://azure.microsoft.com/documentation/services/storage/)

> [!NOTE]
> Azure Disk Encryption wordt ondersteund op de volgende Windows-serverbesturingssystemen - Windows Server 2008 R2, Windows Server 2012 en Windows Server 2012 R2. Schijfversleuteling wordt ondersteund op de volgende Linux-serverbesturingssystemen - Ubuntu, CentOS, SUSE en SUSE Linux Enterprise Server (SLES).
>
>

## <a name="implement-the-recommendation"></a>De aanbeveling uitvoeren
1. Selecteer **schijfversleuteling toepassen**in het blad **Aanbevelingen** .
2. In het **versleutelingsblad Voor schijfversleuteling** toepassen ziet u een lijst met VM's waarvoor schijfversleuteling wordt aanbevolen.
3. Volg de instructies om versleuteling toe te passen op deze VM's.

![][1]

Als u Azure Virtual Machines wilt versleutelen die door Security Center zijn geïdentificeerd als versleuteling, raden we de volgende stappen aan:

* Installeer en configureer Azure PowerShell. Hiermee u de PowerShell-opdrachten uitvoeren die nodig zijn om de vereiste vereisten in te stellen om Azure Virtual Machines te versleutelen.
* Het Azure Disk Encryption Prerequisites Azure PowerShell-script verkrijgen en uitvoeren.
* Versleutel uw virtuele machines.

[Versleutel een Windows IaaS VM met Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) leidt u door deze stappen. In dit onderwerp wordt ervan uitgegaan dat u een Windows-clientmachine gebruikt van waaruit u schijfversleuteling configureert.

Er zijn veel benaderingen die kunnen worden gebruikt voor Azure Virtual Machines. Als u al goed bekend bent met Azure PowerShell of Azure CLI, maakt u mogelijk liever gebruik van een andere manier. Zie [Azure-schijfversleuteling](../security/fundamentals/encryption-overview.md)voor meer informatie over deze andere benaderingen.

## <a name="see-also"></a>Zie ook
In dit document ziet u hoe u de aanbeveling 'Schijfversleuteling toepassen' van het Beveiligingscentrum implementeren. Zie het volgende voor meer informatie over schijfversleuteling:

* [Versleuteling en sleutelbeheer met Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sec) - Meer informatie over het gebruik van schijfversleutelingsbeheer voor IaaS VM's en Azure Key Vault om uw gegevens te beschermen en te beveiligen.
* [Azure-schijfversleuteling](../security/fundamentals/encryption-overview.md) (document) - Meer informatie over het inschakelen van schijfversleuteling voor Windows- en Linux-VM's.

Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Beveiligingsbeleid instellen in Azure Security Center:](tutorial-security-policy.md) meer informatie over het configureren van beveiligingsbeleid.
* [Bewaking van de beveiligingsstatus in Azure Security Center:](security-center-monitoring.md) meer informatie over het bewaken van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center:](security-center-managing-and-responding-alerts.md) meer informatie over het beheren en reageren op beveiligingswaarschuwingen.
* [Beveiligingsaanbevelingen beheren in Azure Security Center:](security-center-recommendations.md) lees hoe aanbevelingen u helpen uw Azure-bronnen te beschermen.
* [Azure Security-blog](https://blogs.msdn.com/b/azuresecurity/) : zoek blogberichten over Azure-beveiliging en -naleving.

<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png
