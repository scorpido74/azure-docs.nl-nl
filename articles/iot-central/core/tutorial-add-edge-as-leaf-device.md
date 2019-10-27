---
title: Een Azure IoT Edge apparaat toevoegen aan Azure IoT Central | Microsoft Docs
description: Als operator kunt u een Azure IoT Edge apparaat toevoegen aan uw Azure-IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 568e0a9c21a9d5d9a5186f0a7311c659b8573b6e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957046"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Zelf studie: een Azure IoT Edge apparaat toevoegen aan uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie leert u hoe u een *Azure IoI edge-apparaat* kunt toevoegen en configureren voor uw Microsoft Azure IOT Central-toepassing. In deze zelf studie hebben we een Azure IoT Edge ingeschakelde Linux-VM in azure Marketplace gekozen.

Deze zelfstudie bestaat uit twee delen:

* Eerst leert u hoe u in de Cloud het eerst kunt inrichten voor een Azure IoT Edge apparaat.
* Vervolgens leert u hoe u het apparaat eerst moet inrichten voor een Azure IoT Edge apparaat.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuw Azure IoT Edge-apparaat toevoegen
> * Het Azure IoT Edge-apparaat configureren om te helpen inrichten met SAS-sleutel
> * Dash boards weer geven, status van module in IoT Central
> * Opdrachten verzenden naar een module die wordt uitgevoerd op het Azure IoT Edge apparaat
> * Eigenschappen instellen voor een module die wordt uitgevoerd op het Azure IoT Edge apparaat

## <a name="prerequisites"></a>Vereisten

U hebt een Azure IoT Central-toepassing nodig om deze zelfstudie te voltooien. Volg deze Snelstartgids om [een Azure IOT Central-toepassing te maken](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge registratie groep inschakelen
Schakel SAS-sleutels voor Azure IoT Edge registratie groep in via de beheer pagina.

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Cloud eerst Azure IoT Edge apparaat inrichten   
In deze sectie maakt u een nieuw Azure IoT Edge apparaat met behulp van de **omgevings sensor sjabloon** en richt u een apparaat in. Klik op apparaten in de linkernavigatiebalk en klik op omgevings sensor sjabloon. 

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Klik op **+ Nieuw** en voer een apparaat-id en naam in die bij u past. 

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Het apparaat wordt naar de **geregistreerde** modus verplaatst.

![Apparaatprofiel-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Een door Azure IoT Edge ingeschakelde Linux-VM implementeren

>Opmerking: u kunt ervoor kiezen om een computer of apparaat te gebruiken. Besturings systeem: Linux of Windows)

Voor deze zelf studie hebben we een Azure IoT-VM met Linux-functionaliteit gekozen, die op Azure kan worden gemaakt. U wordt naar [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) geleid en klikt op de knop **nu downloaden** . 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Klik op **door gaan**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


U wordt geAzure Portal. Klik op de knop **maken**

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selecteer abonnement, maak bij voor keur een nieuwe resource groep, selecteer vs West 2 voor de beschik baarheid van VM'S, voer gebruikers en wacht woord in. Gebruiker onthouden, wacht woord is vereist voor de volgende stappen. Klik op **beoordeling + maken**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Zodra de validatie is uitgevoerd, klikt u op **maken**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Het duurt enkele minuten om de resources te maken. Klik op Ga naar **resource**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>VM inrichten als Azure IoT Edge apparaat 

Klik onder ondersteuning en probleem oplossing in het navigatie deel venster aan de linkerkant op Seriële console

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Er wordt een scherm zoals hieronder weer gegeven

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Druk op ENTER en geef de gebruikers naam en het wacht woord op als daarom wordt gevraagd en druk op ENTER. 

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Als u een opdracht wilt uitvoeren als Administrator/root, voert u de volgende opdracht uit: **sudo su –**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Controleer de runtime versie van Azure IoT Edge. Huidige GA-versie is 1.0.8

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installeer de vim-editor of gebruik nano als dit uw voor keur is. 

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Azure IoT Edge config. yaml-bestand bewerken

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Schuif naar beneden en commentaar uit connection string gedeelte van het yaml-bestand. 

**Bij**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Na** (druk op ESC en druk op onderste case a om te beginnen met bewerken)

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Verwijder de opmerking symmetrisch sleutel gedeelte van het yaml-bestand. 

**Bij**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**After**

![Ubuntu-VM](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Ga naar IoT Central en haal de bereik-id, de apparaat-ID en de symmetrische sleutel van het Azure IoT Edge apparaat ![apparaat verbinding](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Ga naar het Linux-vak en vervang de scope-ID, de registratie-ID met de apparaat-ID en de symmetrische sleutel

Druk op **ESC** en typ **: wq!** en druk op **Enter** om uw wijzigingen op te slaan

Start Azure IoT Edge opnieuw op om uw wijzigingen te verwerken en druk op **Enter**

![Apparaat verbinden](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Type: **iotedge-lijst**, het duurt enkele minuten voordat er drie modules worden geïmplementeerd

![Apparaat verbinden](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Device Explorer IoT Central 

In IoT Central uw apparaat wordt verplaatst naar de ingerichte status

![Apparaat verbinden](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Op het tabblad modules wordt de status van het apparaat en de module weer gegeven op IoT Central 

![Apparaat verbinden](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Cloud eigenschappen worden weer gegeven in een formulier (van het apparaatprofiel dat u in de vorige stappen hebt gemaakt). Voer waarden in en klik op **Opslaan**. 

![Apparaat verbinden](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Dashboard tegel

![Apparaat verbinden](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

In deze zelfstudie hebt u het volgende geleerd:

* Een nieuw Azure IoT Edge-apparaat toevoegen
* Het Azure IoT Edge-apparaat configureren om te helpen inrichten met SAS-sleutel
* Dash boards weer geven, status van module in IoT Central
* Opdrachten verzenden naar een module die wordt uitgevoerd op het Azure IoT Edge apparaat
* Eigenschappen instellen voor een module die wordt uitgevoerd op het Azure IoT Edge apparaat

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u Azure IoT Edge-apparaten beheren in IoT Central kunt gebruiken, volgt u de voorgestelde volgende stap:

<!-- Next how-tos in the sequence -->

Een transparante gateway configureren: Volg deze zelf studie

> [!div class="nextstepaction"]
> [Transparante gateway configureren](../../iot-edge/how-to-create-transparent-gateway.md)
