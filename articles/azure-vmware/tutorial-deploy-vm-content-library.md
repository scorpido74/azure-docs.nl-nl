---
title: "Zelfstudie: Een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution"
description: In deze zelfstudie over Azure VMware Solution maakt u een inhoudsbibliotheek voor het implementeren van een virtuele machine in een Azure VMware Solution-privécloud.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: 3abaafac0dbd6f3537d2ca30a093627230780eb5
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750549"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Zelfstudie: Een inhoudsbibliotheek maken voor het implementeren van VM's in Azure VMware Solution

In een inhoudsbibliotheek wordt inhoud in de vorm van bibliotheekitems opgeslagen en beheerd. Eén bibliotheekitem bestaat uit een of meer bestanden die u gebruikt voor het implementeren van virtuele machines (VM's). 
 
In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Maak een inhoudsbibliotheek
> * Upload een ISO-installatiekopie naar de inhoudsbibliotheek
> * Implementeer een virtuele machine met behulp van een ISO in de inhoudsbibliotheek

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie zijn een NSX-T-segment voor logische switches en een beheerde DHCP-service vereist.  Raadpleeg [Hoe beheert u DHCP in Azure VMware Solution Preview](manage-dhcp.md) voor meer informatie.

## <a name="create-a-content-library"></a>Maak een inhoudsbibliotheek

1. Selecteer op de on-premises vSphere-client **Menu > Inhoudsbibliotheken**.

   ![Selecteer Menu-> Inhoudsbibliotheken](./media/content-library/vsphere-menu-content-libraries.png)

1. Klik op de knop **Toevoegen** om een nieuwe inhoudsbibliotheek te maken.

   ![Klik op de knop Toevoegen om een nieuwe inhoudsbibliotheek te maken.](./media/content-library/create-new-content-library.png)

1. Geef een naam op en bevestig het IP-adres van de vCenter-server en selecteer **Volgende**.

   ![Geef een naam en opmerkingen van uw keuze op en selecteer Volgende.](./media/content-library/new-content-library-step1.png)

1. Selecteer de **Lokale inhoudsbibliotheek** en selecteer **Volgende**.

   ![In dit voorbeeld gaan we een lokale inhoudsbibliotheek maken. Selecteer volgende.](./media/content-library/new-content-library-step2.png)

1. Selecteer het gegevensarchief waarin uw inhoudsbibliotheek wordt opgeslagen en selecteer vervolgens **Volgende**.

   ![Selecteer het gegevensarchief dat u als host voor uw inhoudsbibliotheek wilt laten fungeren en selecteer volgende.](./media/content-library/new-content-library-step3.png)

1. Controleer en verifieer de instellingen voor de inhoudsbibliotheek en selecteer **Voltooien**.

   ![Controleer uw instellingen en selecteer Voltooien.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Upload een ISO-installatiekopie naar de inhoudsbibliotheek

Nu de inhoudsbibliotheek is gemaakt, kunt u een ISO-installatiekopie toevoegen om een virtuele machine te implementeren in een privécloudcluster. 

1. Selecteer op de vSphere-client **Menu > Inhoudsbibliotheken**.

1. Klik met de rechtermuisknop op de inhoudsbibliotheek die u wilt gebruiken voor de nieuwe ISO en selecteer **Item importeren**.

1. Voer een van de volgende handelingen uit om een bibliotheekitem voor de Bron te importeren en selecteer vervolgens **Importeren**:
   1. Selecteer URL en geef een URL op om een ISO te downloaden.

   1. Selecteer **Lokaal bestand** dat u van het lokale systeem wilt uploaden.

   > [!TIP]
   > Optioneel, u kunt een aangepaste itemnaam en opmerkingen voor het Doel definiëren.

1. Open de bibliotheek en selecteer het tabblad **Andere typen** om te controleren of de ISO succesvol is geüpload.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Een virtuele machine implementeren in een privécloudcluster

1. Selecteer op de vSphere-client **Menu > Hosts en clusters**.

1. Vouw in het linkerdeelvenster de structuur uit en selecteer een cluster.

1. Selecteer **Acties > Nieuwe virtuele machine**.

1. Ga door met de wizard en wijzig de gewenste instellingen.

1. Selecteer **Nieuw CD/DVD-station > Clientapparaat > Inhoudsbibliotheek ISO-bestand**.

1. Selecteer de in de vorige sectie geladen ISO en selecteer **OK**.

1. Schakel het selectievakje **Verbinding maken** in zodat de ISO wordt gekoppeld op het moment van inschakeling.

1. Selecteer **Nieuw netwerk > Selecteer vervolgkeuze > Bladeren**.

1. Selecteer de **logische switch (segment)** en selecteer **OK**.

1. Wijzig andere hardware-instellingen en selecteer **Volgende**.

1. Controleer de instellingen en selecteer **Voltooien**.


## <a name="next-steps"></a>Volgende stappen

Als u van plan bent een Hybrid Cloud Extension (HCX) te gebruiken om VM-workloads naar uw privécloud te migreren, gebruikt u de [HCX voor Azure VMware Solution installeren](hybrid-cloud-extension-installation.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
