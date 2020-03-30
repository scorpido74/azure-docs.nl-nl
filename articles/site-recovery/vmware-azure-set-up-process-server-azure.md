---
title: Een processerver VMware/fysieke failback instellen in Azure Site Recovery
description: In dit artikel wordt beschreven hoe u een processerver in Azure instelt om Azure VM's naar VMware te failbacken.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: ramamill
ms.openlocfilehash: 16214fa07b14c8db2f32fa2c69739b7fa3dc1907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083954"
---
# <a name="set-up-a-process-server-in-azure-for-failback"></a>Een processerver voor failback in Azure instellen

Nadat u vmware-vm's of fysieke servers naar Azure hebt mislukt met [siteherstel,](site-recovery-overview.md)u deze weer naar de on-premises site laten gaan wanneer deze weer actief is. Als u niet terug wilt werken, moet u een tijdelijke processerver in Azure instellen om replicatie van Azure naar on-premises af te handelen. U deze vm verwijderen nadat failback is voltooid.

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over het [proces voor herbescherming](vmware-azure-reprotect.md) en [failback.](vmware-azure-failback.md)

[!INCLUDE [site-recovery-vmware-process-server-prerequ](../../includes/site-recovery-vmware-azure-process-server-prereq.md)]


## <a name="deploy-a-process-server-in-azure"></a>Een processerver implementeren in Azure

1. Selecteer in de kluis > Configuratieservers voor het beheer van > **de**> **siteherstelinfrastructuur**de configuratieserver. **Site Recovery Infrastructure**
2. Klik op de serverpagina op **+ Processerver**
3. Selecteer **in De pagina Processerver toevoegen** en selecteer om de processerver in Azure te implementeren.
4. Geef de Azure-instellingen op, inclusief het abonnement dat wordt gebruikt voor failover, een resourcegroep, het Azure-gebied dat wordt gebruikt voor failover en het virtuele netwerk waarin de Azure VM's zich bevinden. Als u meerdere Azure-netwerken hebt gebruikt, hebt u in elk van deze netwerken een processerver nodig.

   ![Galerieitem processerver toevoegen](./media/vmware-azure-set-up-process-server-azure/add-ps-page-1.png)

4. Geef in **Servernaam**, **Gebruikersnaam**en **Wachtwoord**een naam op voor de processerver en referenties die beheerdersmachtigingen op de server krijgen toegewezen.
5. Geef een opslagaccount op dat moet worden gebruikt voor de vm-schijven van de server, het subnet waarin de processerver-vm zich bevindt en het IP-adres van de server dat wordt toegewezen wanneer de VM wordt gestart.
6. Klik op de knop **OK** om de vm van de processerver te implementeren. De processerver wordt geïmplementeerd op Standard_A8_v2 SKU. Controleer of deze VM SKU beschikbaar is voor uw abonnement.

>

## <a name="registering-the-process-server-running-in-azure-to-a-configuration-server-running-on-premises"></a>De processerver registreren (uitgevoerd in Azure) op een configuratieserver (on-premises uitgevoerd)

Nadat de vm van de processerver operationeel is, moet u deze als volgt registreren bij de on-premises configuratieserver:

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]


