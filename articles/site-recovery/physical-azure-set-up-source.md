---
title: De configuratie server instellen voor herstel na nood gevallen van fysieke servers naar Azure met behulp van Azure Site Recovery | Microsoft Docs '
description: In dit artikel wordt beschreven hoe u de on-premises configuratie server instelt voor herstel na nood gevallen van on-premises fysieke servers naar Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 902c14211e91a1500c6b50cd790b347e337c4f70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84699920"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>De configuratie server instellen voor herstel na nood gevallen van fysieke servers naar Azure

In dit artikel wordt beschreven hoe u uw on-premises omgeving instelt om fysieke servers met Windows of Linux te repliceren naar Azure.

## <a name="prerequisites"></a>Vereisten

In het artikel wordt ervan uitgegaan dat u al beschikt over:
- Een Recovery Services kluis in de [Azure Portal](https://portal.azure.com "Azure Portal").
- Een fysieke computer waarop de configuratie server moet worden geïnstalleerd.
- Als u TLS 1,0 hebt uitgeschakeld op de computer waarop u de configuratie server installeert, moet u ervoor zorgen dat TLs 1,2 is ingeschakeld en dat de .NET Framework versie 4,6 of hoger op de computer is geïnstalleerd (waarbij sterke crypto grafie is ingeschakeld). [Meer informatie](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1).

### <a name="configuration-server-minimum-requirements"></a>Minimale vereisten voor configuratie server
De volgende tabel bevat de minimale hardware-, software-en netwerk vereisten voor een configuratie server.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> Op HTTPS gebaseerde proxy servers worden niet ondersteund door de configuratie server.

## <a name="choose-your-protection-goals"></a>Uw beveiligings doelen kiezen

1. Ga in het Azure Portal naar de Blade **Recovery Services** kluizen en selecteer uw kluis.
2. Klik in het menu **resource** van de kluis op **aan de slag**  >  **site Recovery**  >  **stap 1: infrastructuur**  >  **beveiliging**voorbereiden.

    ![Doelstellingen kiezen](./media/physical-azure-set-up-source/choose-goals.png)
3. In **beveiligings doel**selecteert **u naar Azure** en **niet gevirtualiseerd/Overig**en klikt u vervolgens op **OK**.

    ![Doelstellingen kiezen](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>De bronomgeving instellen

1. Als u geen configuratie server hebt, klikt u in **bron voorbereiden**op **+ configuratie** server om er een toe te voegen.

   ![Bron instellen](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Controleer op de Blade **server toevoegen** of de **Configuratie server** wordt weer gegeven bij **server type**.
4. Down load het installatie bestand voor de Site Recovery Unified Setup.
5. Download de registratiesleutel voor de kluis. U hebt de registratie sleutel nodig wanneer u Unified Setup uitvoert. De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Bron instellen](./media/physical-azure-set-up-source/set-source2.png)
6. Voer **Azure site Recovery Unified Setup** uit op de computer die u als de configuratie server gebruikt om de configuratie server, de proces server en de hoofddoel server te installeren.

#### <a name="run-azure-site-recovery-unified-setup"></a>Run Azure Site Recovery Unified Setup

> [!TIP]
> Registratie van de configuratie server mislukt als de tijd van de systeem klok van de computer meer dan vijf minuten van de lokale tijd is. Synchroniseer de systeem klok met een [tijd server](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) voordat u begint met de installatie.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> De configuratie server kan worden geïnstalleerd via een opdracht regel. [Meer informatie](physical-manage-configuration-server.md#install-from-the-command-line).


## <a name="common-issues"></a>Algemene problemen

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Volgende stappen

De volgende stap omvat [het instellen van uw doel omgeving](physical-azure-set-up-target.md) in Azure.
