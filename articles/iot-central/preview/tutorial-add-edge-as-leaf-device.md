---
title: Een Azure IoT Edge apparaat toevoegen aan Azure IoT Central | Microsoft Docs
description: Als operator kunt u een Azure IoT Edge apparaat toevoegen aan uw Azure IoT Central-toepassing
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979067"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Zelf studie: een Azure IoT Edge apparaat toevoegen aan uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In deze zelf studie wordt uitgelegd hoe u een Azure IoT Edge apparaat toevoegt en configureert voor uw Azure IoT Central-toepassing. In deze zelf studie hebben we een Linux-VM met IoT Edge-functionaliteit gekozen uit Azure Marketplace.

Deze zelfstudie bestaat uit twee delen:

* Eerst leert u hoe u in de Cloud het eerst kunt inrichten voor een IoT Edge apparaat.
* Vervolgens leert u hoe u ' apparaat eerst ' kunt inrichten voor een IoT Edge apparaat.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuw IoT Edge-apparaat toevoegen
> * Het IoT Edge-apparaat configureren om in te richten met behulp van een SAS-sleutel (Shared Access Signature)
> * Dash boards en module status in IoT Central weer geven
> * Opdrachten verzenden naar een module die wordt uitgevoerd op het IoT Edge apparaat
> * Eigenschappen instellen voor een module die wordt uitgevoerd op het IoT Edge apparaat

## <a name="prerequisites"></a>Vereisten

U hebt een Azure IoT Central-toepassing nodig om deze zelfstudie te voltooien. Volg [deze Snelstartgids om een Azure IOT Central-toepassing te maken](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Azure IoT Edge registratie groep inschakelen
Schakel op de pagina **beheer** SAS-sleutels in voor Azure IOT Edge registratie groep.

![Scherm afbeelding van de beheer pagina, waarbij de verbinding met het apparaat is gemarkeerd](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Een "Cloud First"-Azure IoT Edge apparaat inrichten  
In deze sectie maakt u een nieuw IoT Edge apparaat met behulp van de omgevings sensor sjabloon en richt u een apparaat in. Selecteer **apparaten** > **omgevings sensor sjabloon**. 

![Scherm afbeelding van de pagina apparaten, met omgevings sensor sjabloon gemarkeerd](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Selecteer **+ Nieuw**en voer een apparaat-id en de naam van uw keuze in. Selecteer **Maken**.

![Scherm afbeelding van het dialoog venster nieuw apparaat maken, met apparaat-ID en gemarkeerd maken](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Het apparaat wordt naar de **geregistreerde** modus verplaatst.

![Scherm afbeelding van sjabloon pagina voor omgevings sensor, met de apparaatstatus gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Een door IoT Edge ingeschakelde Linux-VM implementeren

> [!NOTE]
> U kunt ervoor kiezen om een computer of apparaat te gebruiken. Het besturings systeem kan Linux of Windows zijn.

Voor deze zelf studie gebruiken we een Azure IoT-VM met Linux-functionaliteit, gemaakt in Azure. Selecteer **nu ophalen**in [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview). 

![Scherm opname van Azure Marketplace, met nu downloaden gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Selecteer **Doorgaan**.

![Scherm afbeelding van het dialoog venster deze app maken in azure, waarbij door gaan is gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


U wordt naar de Azure Portal geleid. Selecteer **Maken**.

![Scherm afbeelding van de Azure Portal, met geaccentueerd gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Selecteer **abonnement**, maak een nieuwe resource groep en selecteer **(VS) West VS 2** voor Beschik baarheid van virtuele machines. Voer vervolgens gebruikers-en wachtwoord gegevens in. Deze zijn vereist voor toekomstige stappen. Vergeet dus niet. Selecteer **Controleren + maken**.

![Scherm afbeelding van de pagina Details van virtuele machine maken, waarbij verschillende opties zijn gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Na validatie selecteert u **maken**.

![Scherm afbeelding van het maken van een virtuele-machine pagina, waarbij validatie is voltooid en gemarkeerd maken](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Het duurt enkele minuten om de resources te maken. Selecteer **Ga naar resource**.

![Scherm afbeelding van de pagina implementatie volt ooien, met naar gemarkeerde resource](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>VM inrichten als een IoT Edge apparaat 

Selecteer **seriële console**onder **ondersteuning en probleem oplossing**.

![Scherm opname van ondersteuning en probleemoplossings opties, met Seriële console gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Er wordt een scherm weer gegeven dat lijkt op het volgende:

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Druk op ENTER, geef de gebruikers naam en het wacht woord op als daarom wordt gevraagd en druk nogmaals op ENTER. 

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Als u een opdracht wilt uitvoeren als Administrator (gebruiker "root"), voert u het volgende in: **sudo su –**

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Controleer de runtime versie van IoT Edge. Op het moment van schrijven is de huidige GA-versie 1.0.8.

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Installeer de vim-editor of gebruik nano als u dat wilt. 

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Bewerk het IoT Edge config. yaml-bestand.

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Schuif omlaag en noteer het connection string gedeelte van het yaml-bestand. 

**Voor**

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

Klik **na** (druk op ESC en druk op kleine letter a om te beginnen met bewerken).

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Verwijder de opmerking over het symmetrische-sleutel gedeelte van het yaml-bestand. 

**Voor**

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Na**

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Ga naar IoT Central. Haal de scope-ID, de apparaat-ID en de symmetrische sleutel van het IoT Edge apparaat op.
Scherm opname van IoT Central ![, waarbij verschillende verbindings opties voor apparaten zijn gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Ga naar de Linux-computer en vervang de scope-ID en registratie-ID door de apparaat-ID en de symmetrische sleutel.

Druk op ESC en typ **: wq!** . Druk op ENTER om uw wijzigingen op te slaan.

Start IoT Edge opnieuw op om de wijzigingen te verwerken en druk op ENTER.

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Type **iotedge-lijst**. Na enkele minuten ziet u drie modules die zijn geïmplementeerd.

![Scherm opname van de console](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>Device Explorer IoT Central 

Uw apparaat wordt in IoT Central verplaatst naar de ingerichte status.

![Scherm opname van IoT Central apparaten opties, met de apparaatstatus gemarkeerd](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Op het tabblad **modules** ziet u de status van het apparaat en de module op IOT Central. 

![Scherm afbeelding van IoT Central tabblad modules](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


U ziet Cloud eigenschappen in een formulier, van de apparaat sjabloon die u in de vorige stappen hebt gemaakt. Voer waarden in en selecteer **Opslaan**. 

![Scherm afbeelding van het formulier voor het Linux edge-apparaat](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Hier ziet u een weer gave in de vorm van een dashboard tegel.

![Scherm afbeelding van de tegels van een dash board voor Linux edge-apparaten](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u IoT Edge apparaten in IoT Central kunt gebruiken en beheren, volgt u de voorgestelde volgende stap:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Transparante gateway configureren](../../iot-edge/how-to-create-transparent-gateway.md)
