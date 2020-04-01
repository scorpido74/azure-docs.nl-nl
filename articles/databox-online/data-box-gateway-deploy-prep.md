---
title: Zelfstudie over het voorbereiden van Azure Portal voor de implementatie van Data Box Gateway | Microsoft Docs
description: De eerste zelfstudie voor het implementeren van Azure Data Box Gateway omvat het voorbereiden van Azure Portal.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 74fec059bdffb91f5a7774d430e2f1897f0e863c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79474455"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Zelfstudie: Voorbereiden op het implementeren van Azure Data Box Gateway

Dit is de eerste zelfstudie in de reeks zelfstudies voor implementatie, die noodzakelijk zijn voor het voltooien van de implementatie van uw Azure Data Box Gateway. In deze zelfstudie wordt beschreven hoe u Azure Portal voorbereidt voor de implementatie van de Data Box Gateway-resource.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. Het voorbereiden van de portal duurt minder dan 10 minuten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een nieuwe resource maken
> * De installatiekopie voor het virtuele apparaat downloaden
> * De activeringssleutel ophalen

## <a name="get-started"></a>Aan de slag

Raadpleeg de volgende zelfstudies in de voorgeschreven volgorde voor het implementeren van uw Data Box Gateway.

| **#** | **In deze stap** | **Gebruikt u deze documenten** |
| --- | --- | --- | 
| 1. |**[Azure Portal voorbereiden voor Data Box Gateway](data-box-gateway-deploy-prep.md)** |Maak en configureer uw Data Box Gateway-resource voordat u een virtueel Data Box Gateway-apparaat inricht. |
| 2. |**[Data Box Gateway inrichten in Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Data Box Gateway inrichten in VMware](data-box-gateway-deploy-provision-vmware.md)**|Voor Hyper-V moet u een virtueel Data Box Gateway-apparaat inrichten en verbinden op een hostsysteem waarop Hyper-V wordt uitgevoerd in Windows Server 2016 of Windows Server 2012 R2. <br><br><br> Voor VMware moet u een virtueel Data Box Gateway-apparaat inrichten en verbinden op een hostsysteem waarop VMware ESXi 6.0, 6.5 of 6.7 wordt uitgevoerd.<br></br> |
| 3. |**[Azure Data Box Gateway verbinden, instellen en activeren](data-box-gateway-deploy-connect-setup-activate.md)** |Maak verbinding met de lokale webinterface, voltooi het instellen van het apparaat en activeer het. Vervolgens kunt u SMB-shares inrichten.  |
| 4. |**[Gegevens overdragen met Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Shares toevoegen en verbinding maken met shares via SMB of NFS. |

U kunt nu Azure Portal gaan instellen.

## <a name="prerequisites"></a>Vereisten

Hier vindt u de configuratievereisten voor uw Data Box Gateway-resource, uw Data Box Gateway-apparaat en het datacenternetwerk.

### <a name="for-the-data-box-gateway-resource"></a>Voor de Data Box Gateway-resource

Zorg voordat u begint voor het volgende:

* Uw Microsoft Azure-abonnement is ingeschakeld voor een Azure Stack Edge-bron. Zorg ervoor dat u een ondersteund abonnement hebt gebruikt, zoals [Microsoft Enterprise Agreement (EA),](https://azure.microsoft.com/overview/sales-number/) [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)of [Microsoft Azure Sponsoring.](https://azure.microsoft.com/offers/ms-azr-0036p/)
* U hebt toegang tot de eigenaar of inzender op resourcegroepniveau voor de gegevensvakrand/gegevensboxgateway, IoT-hub en Azure Storage-bronnen.
    - Als u een Data Box Edge/Data Box Gateway-bron wilt maken, moet u machtigingen hebben als bijdrager (of hoger) op resourcegroepniveau. U moet er ook `Microsoft.DataBoxEdge` voor zorgen dat de provider is geregistreerd. Ga voor informatie over hoe u zich registreren naar [Resourceprovider registreren.](data-box-gateway-manage-access-power-connectivity-mode.md#register-resource-providers)
    - Als u een bron voor een opslagaccount wilt maken, hebt u opnieuw een bijdrager of een hoger toegangsbereik nodig op het niveau van de brongroep. Azure Storage is standaard een geregistreerde resourceprovider.
- U hebt beheerders- of gebruikerstoegang tot de Microsoft Graph API. Zie [Verwijzing naar microsoft graph-machtigingen voor](https://docs.microsoft.com/graph/permissions-reference)meer informatie .
- U hebt een Microsoft Azure Storage-account met toegangsreferenties.

### <a name="for-the-data-box-gateway-device"></a>Voor het Data Box Gateway-apparaat

Voordat u een virtueel apparaat implementeert, controleert u of:

- U toegang hebt tot een hostsysteem met Hyper-V in Windows Server 2012 R2 of hoger, of VMware (ESXi 6.0, 6.5 of 6.7) dat kan worden gebruikt voor het inrichten van een apparaat.
- Het hostsysteem kan de volgende resources volledig toewijzen aan het inrichten van uw virtuele Data Box-apparaat:
  
  - Minimaal 4 virtuele processors.
  - Ten minste 8 GB RAM-geheugen.
  - Eén netwerkinterface.
  - Een besturingssysteemschijf van 250 GB
  - Een virtuele schijf van 2 TB voor systeemgegevens

### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

- Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw Data Box Gateway-apparaat. Zie de [systeemvereisten van de Data Box Gateway](data-box-gateway-system-requirements.md)voor meer informatie .

- Voor de normale bedrijfsomstandigheden van uw Data Box Gateway moet u een:

    - Een downloadbandbreedte van minimaal 10 Mbps om ervoor te zorgen dat het apparaat up-to-date blijft.
    - Een minimum van 20 Mbps speciale upload- en downloadbandbreedte om bestanden over te zetten.

## <a name="create-a-new-resource"></a>Een nieuwe resource maken

Als u al een Data Box Gateway-resource hebt voor het beheer van uw virtuele apparaten, kunt u deze stap overslaan en verdergaan met [Activeringscode ophalen](#get-the-activation-key).

Als u een Data Box Gateway-bron wilt maken, neemt u de volgende stappen in de Azure-portal.

1. Gebruik uw Microsoft Azure-referenties om u aan te melden bij:

    - De Azure-portal op [https://portal.azure.com](https://portal.azure.com)deze URL: .
    - Of de Azure Government-portal [https://portal.azure.us](https://portal.azure.us)op deze URL: . Ga voor meer informatie naar [Verbinding maken met Azure Government via de portal.](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal)

2. Selecteer in het linkerdeelvenster **+ Een resource maken**. Zoeken naar **Data Box Edge / Data Box Gateway**. Selecteer Data Box Edge / Data Box Gateway. Selecteer **Maken**.
3. Kies het abonnement dat u wilt gebruiken voor het Data Box Gateway-apparaat. Selecteer het gebied waar u de Data Box Gateway-bron wilt implementeren. Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure Stack Edge-bron beschikbaar is. Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren. Selecteer in de optie **Data Box Gateway** de optie **Maken**.

    ![Data Box Gateway-service zoeken](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Voer op het tabblad **Basisbeginselen** de volgende **projectdetails**in of selecteer deze .
    
    |Instelling  |Waarde  |
    |---------|---------|
    |Abonnement    |Dit wordt automatisch ingevuld op basis van de eerdere selectie. Abonnement is gekoppeld aan uw factureringsrekening. |
    |Resourcegroep  |Maak een nieuwe groep of selecteer een bestaande groep.<br>Meer informatie over [Azure Resource Groups](../azure-resource-manager/management/overview.md).     |

5. Voer de volgende **instantiedetails**in of selecteer deze .

    |Instelling  |Waarde  |
    |---------|---------|
    |Name   | Een beschrijvende naam om de resource aan te duiden.<br>De naam is tussen 2 en 50 tekens lang en kan letters, cijfers en afbreekstreepjes bevatten.<br> De naam begint en eindigt met een letter of cijfer.        |   
    |Regio     |Zie [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)voor een lijst met alle regio's waar de Azure Stack Edge-bron beschikbaar is. Voor de Azure-regering zijn alle overheidsregio's die in de [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/) worden vermeld, beschikbaar. <br> Kies een locatie die het dichtst bij de geografische regio ligt waar u uw apparaat wilt implementeren.|
    
    ![Data Box Gateway-resource maken](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selecteer **Controleren + maken**.
 
7. Bekijk op het tabblad **Controleren + maken** de **prijsdetails**, **gebruiksvoorwaarden**en de details voor uw resource. Selecteer **Maken**.

    ![Gegevensboxgatewaybrongegevens controleren](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

Het maken van de resource duurt enkele minuten. Nadat de resource is gemaakt en geïmplementeerd, wordt u hiervan op de hoogte gesteld. Selecteer **Ga naar resource**.

![Gegevensboxgatewaybrongegevens controleren](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>De installatiekopie voor het virtuele apparaat downloaden

Nadat u de Data Box Gateway-resource hebt gemaakt, downloadt u de juiste installatiekopie voor het virtuele apparaat om een virtueel apparaat in te richten op uw hostsysteem. De afbeeldingen van virtuele apparaten zijn specifiek voor een besturingssysteem.

> [!IMPORTANT]
> De software die wordt uitgevoerd op de Data Box Gateway kan alleen worden gebruikt met de Data Box Gateway-resource.

Volg deze stappen in de [Azure-portal](https://portal.azure.com/) om een afbeelding van een virtueel apparaat te downloaden.

1. Selecteer overzicht in de bron die u hebt gemaakt en selecteer **Overzicht**. Als u een bestaande Azure Data Box Gateway-bron hebt, selecteert u de bron en gaat u naar **Overzicht**. Selecteer **Apparaatinstelling**.

    ![Nieuwe Data Box Gateway-resource](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Selecteer op de tegel **Afbeelding downloaden** de virtuele apparaatafbeelding die overeenkomt met het besturingssysteem op de hostserver die wordt gebruikt om de vm in te richten. De afbeeldingsbestanden zijn ongeveer 5,6 GB.
   
   * [VHDX voor Hyper-V in Windows Server 2012 R2 en hoger](https://aka.ms/dbe-vhdx-2012).
   * [VMDK voor VMWare ESXi 6.0, 6.5 of 6.7](https://aka.ms/dbe-vmdk).

    ![Afbeelding van het virtuele apparaat van Data Box Gateway downloaden](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Download het bestand en pak het uit op een lokale schijf, en onthoud waar het zipbestand is uitgepakt.


## <a name="get-the-activation-key"></a>De activeringssleutel ophalen

Nadat de Data Box Gateway-bron actief is, moet u de activeringssleutel ophalen. Deze sleutel wordt gebruikt om uw Data Box Gateway-apparaat te activeren en te verbinden met de resource. U kunt deze sleutel nu ophalen, terwijl u Azure Portal geopend hebt.

1. Selecteer de resource die u hebt gemaakt en selecteer vervolgens **Overzicht**. Ga **in**de installatie apparaat naar de tegel **Configureren en activeren.**

    ![Tegel configureren en activeren](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selecteer **Sleutel genereren** om een activeringssleutel te maken. Selecteer het kopieerpictogram om de sleutel te kopiëren en op te slaan voor later gebruik.

    ![Activeringssleutel ophalen](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - De activeringssleutel verloopt drie dagen nadat deze is gegenereerd.
> - Als de sleutel is verlopen, genereert u een nieuwe sleutel. De oudere toegangssleutel is niet langer geldig.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Gateway, zoals:

> [!div class="checklist"]
> * Een nieuwe resource maken
> * De installatiekopie voor het virtuele apparaat downloaden
> * De activeringssleutel ophalen

Ga naar de volgende zelfstudie om te lezen hoe u een virtuele machine kunt inrichten voor uw Data Box Gateway. Raadpleeg hier de specifieke instructies voor het besturingssysteem van uw host:

> [!div class="nextstepaction"]
> [Een Azure Data Box Gateway inrichten in Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OF

> [!div class="nextstepaction"]
> [Een Azure Data Box Gateway inrichten in VMware](./data-box-gateway-deploy-provision-vmware.md)


