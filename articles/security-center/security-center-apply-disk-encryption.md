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
ms.openlocfilehash: e0677ecc63573d04e384a0104ee0e4e28b4d5e3b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473268"
---
# <a name="apply-disk-encryption-in-azure-security-center"></a>Schijfversleuteling toepassen in Azure Security Center

Azure Security Center raadt u aan Azure Disk Encryption te gebruiken op niet-versleutelde schijven op zowel Windows als Linux VM. U kunt schijfversleuteling gebruiken voor schijven van IaaS-VM's voor Windows en Linux.  Versleuteling wordt aanbevolen voor het besturingssysteem en voor de gegevensvolumes op de VM.

Disk Encryption maakt gebruik van de industrie standaard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) functie van Windows en de [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux. Deze functies bieden os- en gegevensversleuteling om uw gegevens te beschermen en te beschermen en te voldoen aan uw verplichtingen op het gebied van organisatiebeveiliging en naleving. Schijfversleuteling is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om u te helpen de schijfversleutelingssleutels en -geheimen in uw Key Vault-abonnement te beheren en te beheren, terwijl u ervoor zorgt dat alle gegevens in de VM-schijven in rust worden versleuteld in uw Azure [Storage.](https://azure.microsoft.com/documentation/services/storage/)

Zie [Ondersteunde VM's en besturingssystemen](../virtual-machines/windows/disk-encryption-overview.md#supported-vms-and-operating-systems) in de azure disk encryptie-documentatie voor de lijst met ondersteunde versies van Windows en Linux.

## <a name="implement-the-recommendation"></a>De aanbeveling uitvoeren
1. Selecteer **schijfversleuteling** op de pagina Aanbevelingen **op virtuele machines**.
2. Selecteer **in de resources Niet in orde**een VM waarvoor schijfversleuteling wordt aanbevolen.
3. Volg de instructies om versleuteling toe te passen op deze VM's.

![Schijfversleuteling toepassen](./media/security-center-apply-disk-encryption/apply-disk-encryption.png)

Als u Azure Virtual Machines wilt versleutelen die door Security Center zijn geïdentificeerd als versleuteling, raden we de volgende stappen aan:

* Installeer en configureer Azure PowerShell. Hiermee u de PowerShell-opdrachten uitvoeren die nodig zijn om de vereiste vereisten in te stellen om Azure Virtual Machines te versleutelen.
* Het Azure Disk Encryption Prerequisites Azure PowerShell-script verkrijgen en uitvoeren.
* Versleutel uw virtuele machines.

[Een Windows IaaS VM versleutelen met Azure PowerShell](../virtual-machines/windows/disk-encryption-powershell-quickstart.md) - Leidt u door deze stappen en gaat ervan uit dat u een Windows-clientmachine gebruikt van waaruit u schijfversleuteling configureren.

Er zijn veel benaderingen die kunnen worden gebruikt voor Azure Virtual Machines. Als u al goed thuis bent in Azure PowerShell of Azure CLI, u de voorkeur geven aan alternatieve benaderingen. Zie [Azure-schijfversleuteling](../security/fundamentals/encryption-overview.md)voor meer informatie over deze andere benaderingen.

## <a name="see-also"></a>Zie ook
In dit document ziet u hoe u de aanbeveling 'Schijfversleuteling toepassen' van het Beveiligingscentrum implementeren. Zie voor meer informatie over schijfversleuteling:

* [Versleuteling en sleutelbeheer met Azure Key Vault](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (video, 36 min 39 sec)-- Meer informatie over het gebruik van schijfversleutelingsbeheer voor IaaS VM's en Azure Key Vault om uw gegevens te beschermen en te beveiligen.
* [Azure-schijfversleuteling](../security/fundamentals/encryption-overview.md) (document)-- Meer informatie over het inschakelen van schijfversleuteling voor Windows- en Linux-VM's.