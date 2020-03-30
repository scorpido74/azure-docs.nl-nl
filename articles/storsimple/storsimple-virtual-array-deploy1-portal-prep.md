---
title: Portal prep voor StorSimple Virtual Array
description: De eerste zelfstudie voor het implementeren van de virtuele array van StorSimple omvat het voorbereiden van de Azure-portal
author: alkohli
ms.assetid: 68a4cfd3-94c9-46cb-805c-46217290ce02
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7f2c7a6f9c1146627f81dc20386c59a91724b245
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254532"
---
# <a name="deploy-storsimple-virtual-array---prepare-the-azure-portal"></a>StorSimple Virtual Array implementeren - De Azure-portal voorbereiden

![](./media/storsimple-virtual-array-deploy1-portal-prep/getstarted4.png)


## <a name="overview"></a>Overzicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Dit is het eerste artikel in de reeks implementatiezelfstudies die nodig zijn om uw virtuele array volledig te implementeren als bestandsserver of een iSCSI-server met behulp van het Resource Manager-model. In dit artikel wordt de voorbereiding beschreven die nodig is om uw StorSimple Device Manager-service te maken en te configureren voordat u een virtuele array indient. In dit artikel wordt ook links naar een checklist voor implementatieconfiguratie en configuratievereisten gekoppeld.

U hebt beheerdersbevoegdheden nodig om het installatie- en configuratieproces uit te voeren. We raden u aan de checklist voor implementatieconfiguratie te bekijken voordat u begint. Het voorbereiden van de portal duurt minder dan 10 minuten.

De informatie die in dit artikel wordt gepubliceerd, is van toepassing op de implementatie van StorSimple Virtual Arrays in de Azure-portal en Microsoft Azure Government Cloud.

### <a name="get-started"></a>Aan de slag
De implementatieworkflow bestaat uit het voorbereiden van de portal, het inrichten van een virtuele array in uw gevirtualiseerde omgeving en het voltooien van de installatie. Als u aan de slag wilt met de implementatie van StorSimple Virtual Array als bestandsserver of iSCSI-server, moet u verwijzen naar de volgende getabuleerde bronnen.

#### <a name="deployment-articles"></a>Implementatieartikelen

Als u uw StorSimple Virtual Array wilt implementeren, raadpleegt u de volgende artikelen in de voorgeschreven reeks.

| **#** | **In deze stap** | **Je doet dit ...** | **En gebruik deze documenten.** |
| --- | --- | --- | --- |
| 1. |**De Azure-portal instellen** |Maak en configureer uw StorSimple Device Manager-service voordat u een StorSimple Virtual Array indient. |[De portal voorbereiden](storsimple-virtual-array-deploy1-portal-prep.md) |
| 2. |**De virtuele array inrichten** |Voor Hyper-V u een StorSimple Virtual Array inrichten en verbinden op een hostsysteem met Hyper-V op Windows Server 2012 R2, Windows Server 2012 of Windows Server 2008 R2. <br></br> <br></br> Voor VMware u een StorSimple Virtual Array inrichten en verbinden op een hostsysteem met VMware ESXi 5.0, 5.5, 6.0 of 6.5.<br></br> |[Een virtuele array inrichten in Hyper-V](storsimple-virtual-array-deploy2-provision-hyperv.md) <br></br> <br></br> [Een virtuele array invmware inrichten](storsimple-virtual-array-deploy2-provision-vmware.md) |
| 3. |**De virtuele array instellen** |Voer voor uw bestandsserver de eerste installatie uit, registreer uw StorSimple-bestandsserver en voltooide de apparaatinstelling. Vervolgens kunt u SMB-shares inrichten. <br></br> <br></br> Voer voor uw iSCSI-server de eerste installatie uit, registreer uw StorSimple iSCSI-server en voltooi de apparaatinstelling. U vervolgens iSCSI-volumes inrichten. |[Virtuele array instellen als bestandsserver](storsimple-virtual-array-deploy3-fs-setup.md)<br></br> <br></br>[Virtuele array instellen als iSCSI-server](storsimple-virtual-array-deploy3-iscsi-setup.md) |

U kunt nu Azure Portal gaan instellen.

## <a name="configuration-checklist"></a>Configuratiechecklist

De configuratiechecklist beschrijft de informatie die u moet verzamelen voordat u de software configureert op uw StorSimple Virtual Array. Door deze informatie van tevoren voor te bereiden, wordt het implementatieproces van het StorSimple-apparaat in uw omgeving gestroomlijnd. Afhankelijk van of uw StorSimple Virtual Array wordt geïmplementeerd als bestandsserver of iSCSI-server, hebt u een van de volgende checklists nodig.

* Download de [checklist voor configuratie van de StorSimple Virtual Array File Server.](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayFileServerConfigurationChecklist.pdf)
* Download de [storSimple Virtual Array iSCSI-serverconfiguratiechecklist.](https://download.microsoft.com/download/E/E/6/EE690BB0-B442-4B84-8165-4731EE727ACF/MicrosoftAzureStorSimpleVirtualArrayiSCSIServerConfigurationChecklist.pdf)

## <a name="prerequisites"></a>Vereisten

Hier vindt u de configuratievereisten voor uw StorSimple Device Manager-service, uw StorSimple Virtual Array en het datacenternetwerk.

### <a name="for-the-storsimple-device-manager-service"></a>Voor de StorSimple-apparaatbeheerfunctie

Zorg voordat u begint voor het volgende:

* U hebt een Microsoft-account met toegangsreferenties.
* U hebt een Microsoft Azure Storage-account met toegangsreferenties.
* Uw Microsoft Azure-abonnement moet zijn ingeschakeld voor de StorSimple Device Manager-service.

### <a name="for-the-storsimple-virtual-array"></a>Voor de StorSimple Virtual Array

Voordat u een virtuele array implementeert, moet u ervoor zorgen dat:

* U hebt toegang tot een hostsysteem met Hyper-V op Windows Server 2008 R2 of hoger of VMware (ESXi 5.0, 5.5, 6.0 of 6.5) dat kan worden gebruikt voor een inrichting van een apparaat.
* Het hostsysteem kan de volgende resources besteden aan het inrichten van uw virtuele array:
  
  * Minimaal 4 kerngeheugens.
  * Ten minste 8 GB RAM-geheugen. Als u van plan bent de virtuele array als bestandsserver te configureren, ondersteunt 8 GB 2 miljoen bestanden. Je hebt 16 GB RAM nodig om 2 - 4 miljoen bestanden te ondersteunen.
  * Eén netwerkinterface.
  * Een virtuele schijf van 500 GB voor systeemgegevens.

### <a name="for-the-datacenter-network"></a>Voor datacenternetwerk

Zorg voordat u begint voor het volgende:

* Het netwerk in uw datacenter is geconfigureerd volgens de netwerkvereisten voor uw StorSimple-apparaat. Zie voor meer informatie de [StorSimple Virtual Array System Requirements](storsimple-ova-system-requirements.md).
* Uw StorSimple Virtual Array heeft een speciale 5 Mbps internetbandbreedte (of meer) beschikbaar te allen tijde. Deze bandbreedte mag niet worden gedeeld met andere toepassingen.

## <a name="step-by-step-preparation"></a>Stapsgewijze voorbereiding

Gebruik de volgende stapsgewijze instructies om uw portal voor te bereiden op de StorSimple Device Manager-service.

## <a name="step-1-create-a-new-service"></a>Stap 1: een nieuwe service maken

Een enkele instantie van de StorSimple Device Manager-service kan meerdere StorSimple Virtual Arrays beheren. Voer de volgende stappen uit om een exemplaar van de StorSimple-apparaatbeheerfunctie uit te voeren. Als u een bestaande StorSimple Device Manager-service hebt om uw virtuele arrays te beheren, slaat u deze stap over en gaat u naar [Stap 2: Download de serviceregistratiesleutel](#step-2-get-the-service-registration-key).

[!INCLUDE [storsimple-virtual-array-create-new-service](../../includes/storsimple-virtual-array-create-new-service.md)]

> [!IMPORTANT]
> Als u de service niet hebt ingeschakeld om automatisch een opslagaccount te maken, moet u minimaal één opslagaccount maken nadat u een service hebt gemaakt.
> 
> * Als u niet automatisch een opslagaccount hebt gemaakt, gaat u naar [Een nieuw opslagaccount voor de service configureren](#optional-step-configure-a-new-storage-account-for-the-service) voor gedetailleerde instructies.
> * Als u het automatisch maken van een opslagaccount hebt ingeschakeld, gaat u naar [Stap 2: de serviceregistratiesleutel ophalen](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Stap 2: de serviceregistratiesleutel ophalen

Wanneer de StorSimple-apparaatbeheerfunctie bedrijfsklaar is, moet u de serviceregistratiesleutel ophalen. Deze sleutel wordt gebruikt om het StorSimple-apparaat te registreren en te verbinden met de service.

Voer de volgende stappen uit in [Azure Portal](https://portal.azure.com/).

[!INCLUDE [storsimple-virtual-array-get-service-registration-key](../../includes/storsimple-virtual-array-get-service-registration-key.md)]

> [!NOTE]
> De serviceregistratiesleutel wordt gebruikt om alle StorSimple Device Manager-apparaten te registreren die moeten worden geregistreerd bij uw StorSimple Device Manager-service.
> 
> 

## <a name="step-3-download-the-virtual-array-image"></a>Stap 3: Download de virtuele arrayafbeelding

Nadat u de serviceregistratiesleutel hebt, moet u de juiste virtuele arrayafbeelding downloaden om een virtuele array op uw hostsysteem in te richten. De virtuele arrayafbeeldingen zijn specifiek voor het besturingssysteem en kunnen worden gedownload van de pagina Snel starten in de Azure-portal.

> [!IMPORTANT]
> De software die op de StorSimple Virtual Array wordt uitgevoerd, mag alleen worden gebruikt met de StorSimple Device Manager-service.
> 
> 

Voer de volgende stappen uit in [Azure Portal](https://portal.azure.com/).

#### <a name="to-get-the-virtual-array-image"></a>De virtuele arrayafbeelding krijgen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/). 
2. Klik in de Azure-portal op **Bladeren > StorSimple-apparaatbeheerders**.
3. Selecteer een bestaande StorSimple Device Manager-service. Klik in het **storsimple-apparaatbeheer** op **Snel starten**. 
4. Klik op de koppeling die overeenkomt met de afbeelding die u wilt downloaden vanuit het Microsoft Downloadcentrum. De installatiekopieën zijn ongeveer 4,8 GB.
   
   * VHDX voor Hyper-V op Windows Server 2012 en hoger
   * VHD voor Hyper-V op Windows Server 2008 R2 en hoger
   * VMDK voor VMWare ESXi 5.0, 5.5, 6.0 of 6.5
5. Download het bestand en pak het uit op een lokale schijf, en onthoud waar het zipbestand is uitgepakt.

## <a name="optional-step-configure-a-new-storage-account-for-the-service"></a>Optionele stap: een nieuw opslagaccount voor de service configureren

Deze stap is optioneel en mag alleen worden uitgevoerd als u het automatisch aanmaken van een opslagaccount bij uw service niet hebt ingeschakeld.

Zie [Een opslagaccount maken](../storage/common/storage-account-create.md) voor stapsgewijze instructies als u een Azure-opslagaccount in een andere regio wilt maken.

Voer de volgende stappen uit in de [Azure-portal](https://ms.portal.azure.com/) op de servicepagina van StorSimple Device Manager om een bestaand Microsoft Azure-opslagaccount toe te voegen.

#### <a name="to-add-a-storage-account-credential-that-has-the-same-azure-subscription-as-the-device-manager-service"></a>Een opslagaccountreferentie toevoegen met hetzelfde Azure-abonnement als de Service Apparaatbeheer

1. Navigeer naar de service Apparaatbeheer en dubbelklik erop. Hiermee wordt het **overzichtsblad** geopend.
2. Selecteer **Accountreferenties opslaan** in de sectie **Configuratie.**
3. Klik op**toevoegen**.
4. Ga als volgt te werk in het blad **Een opslagaccount toevoegen:**
   
   1. Selecteer **Huidig** **voor Abonnement**.
   
   2. Geef de naam op van uw Azure-opslagaccount.
   
   3. Selecteer **Inschakelen** om een beveiligd kanaal te maken voor netwerkcommunicatie tussen uw StorSimple-apparaat en de cloud. Selecteer **Alleen uitschakelen** als u binnen een privécloud werkt.
   
   4. Klik op**toevoegen**. U wordt hiervan op de hoogte gesteld nadat het opslagaccount is gemaakt.<br></br>
   
      ![Een bestaand opslagaccountreferentie toevoegen](./media/storsimple-virtual-array-manage-storage-accounts/ova-add-storageacct.png)

## <a name="next-step"></a>Volgende stap

De volgende stap is het inrichten van een virtuele machine voor uw StorSimple Virtual Array. Raadpleeg hier de specifieke instructies voor het besturingssysteem van uw host:

* [Een StorSimple Virtual Array inhyper-V inrichten](storsimple-virtual-array-deploy2-provision-hyperv.md)
* [Een StorSimple Virtual Array inrichten in VMware](storsimple-virtual-array-deploy2-provision-vmware.md)

