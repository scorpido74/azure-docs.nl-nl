---
title: De configuratieserver instellen voor noodherstel van fysieke servers naar Azure met Azure Site Recovery | Microsoft Docs'
description: In dit artikel wordt beschreven hoe u de on-premises configuratieserver instelt voor noodherstel van on-premises fysieke servers in Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257873"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>De configuratieserver instellen voor noodherstel van fysieke servers in Azure

In dit artikel wordt beschreven hoe u uw on-premises omgeving instelt om fysieke servers met Windows of Linux te repliceren naar Azure.

## <a name="prerequisites"></a>Vereisten

Het artikel gaat ervan uit dat u al:
- Een kluis van Recovery Services in de [Azure-portal](https://portal.azure.com "Azure Portal").
- Een fysieke computer waarop de configuratieserver kan worden geïnstalleerd.
- Als u TLS 1.0 hebt uitgeschakeld op de machine waarop u de configuratieserver installeert, moet u ervoor zorgen dat TLs 1.2 is ingeschakeld en dat de .NET Framework-versie 4.6 of hoger op de machine is geïnstalleerd (met sterke cryptografie ingeschakeld). [Meer informatie](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimale vereisten voor configuratieserver
In de volgende tabel worden de minimale hardware-, software- en netwerkvereisten voor een configuratieserver weergegeven.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-gebaseerde proxyservers worden niet ondersteund door de configuratieserver.

## <a name="choose-your-protection-goals"></a>Kies uw beschermingsdoelen

1. Ga in de Azure-portal naar het kluizenblad **van Recovery Services** en selecteer uw kluis.
2. Klik in het menu **Resource** van de kluis op **Aan de slag** > **siteherstelstap** > **1: Infrastructuurbeveiliging** > **voorbereiden**.

    ![Doelstellingen kiezen](./media/physical-azure-set-up-source/choose-goals.png)
3. Selecteer **in Het doel Beveiliging**De optie Voor **Azure** en **Niet gevirtualiseerd/Overig**en klik op **OK**.

    ![Doelstellingen kiezen](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

1. Als u geen configuratieserver hebt, klikt u in **Bron voorbereiden**als u geen configuratieserver hebt, klikt u op **+Configuratieserver** om er een toe te voegen.

   ![Bron instellen](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Controleer **in** het blade Server toevoegen of **configuratieserver** wordt weergegeven in **Servertype**.
4. Download het installatiebestand Site Recovery Unified Setup.
5. Download de registratiesleutel voor de kluis. U hebt de registratiesleutel nodig wanneer u Unified Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Bron instellen](./media/physical-azure-set-up-source/set-source2.png)
6. Voer **Azure Site Recovery Unified Setup** uit op de machine die u als configuratieserver gebruikt om de configuratieserver, de processerver en de hoofddoelserver te installeren.

#### <a name="run-azure-site-recovery-unified-setup"></a>Installatie van Azure Site Recovery Unified uitvoeren

> [!TIP]
> Configuratieserverregistratie mislukt als de tijd op de systeemklok van uw computer meer dan vijf minuten verwijderd is van de lokale tijd. Synchroniseer uw systeemklok met een [tijdserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) voordat u met de installatie begint.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> De configuratieserver kan worden geïnstalleerd via een opdrachtregel. [Meer informatie](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Algemene problemen

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Volgende stappen

De volgende stap is [het instellen van uw doelomgeving](physical-azure-set-up-target.md) in Azure.
