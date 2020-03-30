---
title: De portal gebruiken om Extern bureaublad in te schakelen voor een rol
titleSuffix: Azure Cloud Services
description: Uw azure cloud service-toepassing configureren om verbinding met extern bureaublad toe te staan
services: cloud-services
documentationcenter: ''
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: d65f4b55be317234c10a0e90cfe413d9e38a6a90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247460"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Verbinding met extern bureaublad inschakelen voor een rol in Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure-portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Met Extern bureaublad heeft u toegang tot het bureaublad van een rol die in Azure wordt uitgevoerd. U een verbinding met Extern bureaublad gebruiken om problemen met uw toepassing op te lossen en te diagnosticeren terwijl deze wordt uitgevoerd.

U tijdens de ontwikkeling een verbinding met extern bureaublad in uw rol inschakelen door de Extern bureaublad-modules op te nemen in uw servicedefinitie of u ervoor kiezen extern bureaublad in te schakelen via de extensie Extern bureaublad. De voorkeursbenadering is om de extensie Extern bureaublad te gebruiken, omdat u Extern bureaublad inschakelen, zelfs nadat de toepassing is geïmplementeerd zonder dat u uw toepassing opnieuw hoeft te implementeren.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Extern bureaublad configureren vanuit de Azure-portal

De Azure-portal maakt gebruik van de benadering van Extern bureaublad-extensie, zodat u Extern bureaublad inschakelen, zelfs nadat de toepassing is geïmplementeerd. Met de **instellingen voor Extern bureaublad** voor uw cloudservice u Extern bureaublad inschakelen, het lokale administratoraccount wijzigen dat wordt gebruikt om verbinding te maken met de virtuele machines, het certificaat dat wordt gebruikt in verificatie en de vervaldatum instellen.

1. Klik **op CloudServices,** selecteer de naam van de cloudservice en selecteer **Extern bureaublad**.

    ![Extern bureaublad voor cloudservices](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Kies of u Extern bureaublad wilt inschakelen voor een afzonderlijke rol of voor alle rollen en wijzig vervolgens de waarde van de switcher in **Ingeschakeld**.

3. Vul de vereiste velden in voor gebruikersnaam, wachtwoord, vervaldatum en certificaat.

    ![Extern bureaublad voor cloudservices](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alle rolinstanties worden opnieuw gestart wanneer u Extern bureaublad voor het eerst inschakelt en **OK** (vinkje) selecteert. Om een herstart te voorkomen, moet het certificaat dat wordt gebruikt om het wachtwoord te versleutelen, in de rol worden geïnstalleerd. Als u een herstart wilt voorkomen, [uploadt u een certificaat voor de cloudservice](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) en gaat u terug naar dit dialoogvenster.

4. Selecteer **in Rollen**de rol die u wilt bijwerken of selecteer **Alles** voor alle rollen.

5. Wanneer u uw configuratie-updates hebt voltooid, selecteert u **Opslaan**. Het duurt even voordat uw rolexemplaren klaar zijn om verbindingen te ontvangen.

## <a name="remote-into-role-instances"></a>Op afstand in rolexemplaren

Zodra Extern bureaublad is ingeschakeld voor de rollen, u rechtstreeks vanuit de Azure-portal een verbinding starten:

1. Klik **op Instanties** om de instellingen van Instanties **te** openen.
2. Selecteer een rolinstantie waarop Extern bureaublad is geconfigureerd.
3. Klik **op Verbinding maken** om een RDP-bestand voor de rolinstantie te downloaden.

    ![Extern bureaublad voor cloudservices](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klik **op Openen** en maak vervolgens **verbinding** om de verbinding met extern bureaublad te starten.

>[!NOTE]
> Als uw cloudservice achter een NSG zit, moet u mogelijk regels maken waarmee verkeer op poorten **3389** en **20000**mogelijk is.  Extern bureaublad maakt gebruik van poort **3389**.  Cloud Service-exemplaren zijn load balanced, dus u niet direct bepalen met welke instantie u verbinding wilt maken.  De *RemoteForwarder-* en *RemoteAccess-agents* beheren RDP-verkeer en stellen de client in staat om een RDP-cookie te verzenden en een afzonderlijke instantie op te geven waarmee u verbinding moet maken.  De *RemoteForwarder-* en *RemoteAccess-agents* vereisen dat poort **20000*** open is, die kan worden geblokkeerd als u een NSG hebt.

## <a name="additional-resources"></a>Aanvullende bronnen

[Cloud Services configureren](cloud-services-how-to-configure-portal.md)
