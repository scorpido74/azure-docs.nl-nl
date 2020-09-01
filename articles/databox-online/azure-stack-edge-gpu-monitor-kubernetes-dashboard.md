---
title: Uw Azure Stack edge-apparaat bewaken via Kubernetes-dash board | Microsoft Docs
description: Hierin wordt beschreven hoe u het Kubernetes-dash board opent en gebruikt om uw Azure Stack edge-apparaat te bewaken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 9224888a38c86e35df9ad516c761fd7012824c15
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084043"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Kubernetes-dash board gebruiken om uw Azure Stack Edge GPU-apparaat te bewaken

In dit artikel wordt beschreven hoe u het Kubernetes-dash board opent en gebruikt om uw Azure Stack Edge GPU-apparaat te bewaken. Als u uw apparaat wilt bewaken, kunt u grafieken gebruiken in Azure Portal, het Kubernetes-dash board weer geven of `kubectl` opdrachten uitvoeren via de Power shell-interface van het apparaat. 

Dit artikel richt zich alleen op de bewakings taken die kunnen worden uitgevoerd op het Kubernetes-dash board.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * Toegang tot het Kubernetes-dash board op uw apparaat
> * Modules weer geven die op uw apparaat zijn geïmplementeerd
> * IP-adres ophalen voor toepassingen die op uw apparaat zijn geïmplementeerd
> * Container logboeken weer geven voor modules die op uw apparaat zijn geïmplementeerd


## <a name="about-kubernetes-dashboard"></a>Over Kubernetes-dash board

Kubernetes dash board is een webinterface die u kunt gebruiken voor het oplossen van problemen met in containers geplaatste toepassingen. Het Kubernetes-dash board is een alternatief voor de Kubernetes- `kubectl` opdracht regel. 

Op uw Azure Stack edge-apparaat kunt u het Kubernetes-dash board in de modus alleen-lezen gebruiken om een overzicht te krijgen van de toepassingen die worden uitgevoerd op uw Azure Stack edge-apparaat, de status van Kubernetes-cluster bronnen weer geven en eventuele fouten op het apparaat te bekijken.

## <a name="access-dashboard"></a>Access-dash board

Het Kubernetes-dash board is alleen-lezen en wordt uitgevoerd op het Kubernetes-hoofd knooppunt op poort 31000. Volg deze stappen om toegang te krijgen tot het dash board: 

1. Ga in de lokale gebruikers interface van uw apparaat naar **apparaat** en ga vervolgens naar **eind punten**van het apparaat. Selecteer de URL van het Kubernetes-dash board om het dash board in een browser te openen.

    ![URL van Kubernetes-dash board op de pagina apparaat in lokale gebruikers interface](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Selecteer op de aanmeldings pagina van het **Kubernetes-dash board** **token**. 
1. Geef een token op. 
    1. [Maak een verbinding via de Power shell-interface van uw apparaat](azure-stack-edge-gpu-connect-powershell-interface.md)om het token op te halen.
    1. Voer de volgende opdracht uit:  `Get-HcsKubernetesDashboardToken`
    
    1. Kopieer de token teken reeks die bij de prompt wordt weer gegeven. Hier volgt een voorbeeld van uitvoer:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Selecteer **Aanmelden**.

    ![Aanmelden bij Kubernetes-dash board](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. U kunt nu het Kubernetes-dash board weer geven voor uw Azure Stack edge-apparaat in de modus alleen-lezen.

    ![Hoofd pagina van Kubernetes-dash board](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Module status weer geven

Compute-modules zijn containers waarvoor een bedrijfs logica is geïmplementeerd. U kunt het dash board gebruiken om te controleren of een compute-module is geïmplementeerd op uw Azure Stack edge-apparaat.

Volg deze stappen in het dash board om de module status weer te geven:

1. Ga in het linkerdeel venster van het dash board naar de **naam ruimte**. Filter op de naam ruimte waar IoT Edge modules worden weer gegeven, in dit geval **iotedge**.
1. Ga in het linkerdeel venster naar **workloads > implementaties**.
1. In het rechterdeel venster ziet u alle modules die op uw apparaat zijn geïmplementeerd. In dit geval is er een GettingStartedWithGPU-module geïmplementeerd op de Azure Stack-rand. U kunt zien dat de module is geïmplementeerd.

    ![Module-implementatie weer geven](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>IP-adres voor services of modules ophalen

U kunt het dash board gebruiken om de IP-adressen op te halen van de services of modules die u buiten het Kubernetes-cluster wilt weer geven. 

U wijst het IP-bereik voor deze externe services toe via de lokale web-UI van het apparaat op de pagina instellingen van het **berekenings netwerk** . Nadat u IoT Edge-modules hebt geïmplementeerd, kunt u het IP-adres ophalen dat is toegewezen aan een specifieke module of service. 

Voer de volgende stappen uit op het dash board om het IP-adres op te halen:

1. Ga in het linkerdeel venster van het dash board naar de **naam ruimte**. Filter op de naam ruimte waarin een externe service is geïmplementeerd, in dit geval **iotedge**.
1. Ga in het linkerdeel venster naar detectie- **en taak verdeling > Services**.
1. In het rechterdeel venster ziet u alle services die worden uitgevoerd in de `iotedge` naam ruimte op uw Azure stack edge-apparaat.

    ![IP ophalen voor externe services](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Containerlogboeken ophalen

Er zijn exemplaren waarvoor u de container logboeken wilt weer geven. U kunt het dash board gebruiken om logboeken op te halen voor een specifieke container die u hebt geïmplementeerd op uw Kubernetes-cluster.

Als u de container logboeken wilt weer geven, voert u de volgende stappen uit op het dash board:

1. Ga in het linkerdeel venster van het dash board naar de **naam ruimte**. Filter op de naam ruimte waarin de IoT Edge-modules zijn geïmplementeerd, in dit geval **iotedge**.
1. Ga in het linkerdeel venster naar **werk belastingen > peul**.
1. In het rechterdeel venster ziet u alle op het apparaat uitgevoerde peulen. Zoek de pod die de module uitvoert waarvoor u de logboeken wilt weer geven. Selecteer de verticale beletsel tekens voor de pod die u hebt geïdentificeerd en selecteer **Logboeken**in het context menu.

    ![Container logboeken weer geven 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. De logboeken worden weer gegeven in een logboeken viewer die is ingebouwd in het dash board. U kunt de logboeken ook downloaden.

    ![Container logboeken weer geven 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van problemen met Kubernetes <!--insert link-->.
