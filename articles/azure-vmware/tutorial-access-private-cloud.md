---
title: 'Zelfstudie: Meer informatie over toegang tot uw privécloud'
description: Meer informatie over het openen van een AVS-privécloud (Azure VMware-oplossing)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 332b1b01f05ca97b03264201384ee38ac266470f
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873603"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Zelfstudie: Meer informatie over het openen van een AVS-privécloud (Azure VMware-oplossing)

Tijdens de preview kunt u met AVS geen privécloud beheren met uw on-premises vCenter. U moet via een jumpbox aanvullende instellingen en verbindingen met een lokaal vCenter-exemplaar uitvoeren. 

In deze zelfstudie maakt u een virtuele Windows-machine voor een jumpbox in de resource groep die u hebt gemaakt in de vorige zelfstudie [Zelfstudie: Netwerken configureren voor uw VMware-privécloud in Azure](tutorial-configure-networking.md) en meldt u zich aan bij vCenter. Dit is een VM in hetzelfde virtuele netwerk dat u hebt gemaakt en biedt toegang tot vCenter en NSX-beheer. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken om verbinding te maken met vCenter
> * Aanmelden bij vCenter vanaf uw virtuele machine

## <a name="create-a-new-windows-virtual-machine"></a>Een nieuwe virtuele Windows-machine maken

Selecteer in de resourcegroep **+ Toevoegen**, zoek en selecteer **Microsoft Windows 10** en klik vervolgens op **Maken**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Een nieuwe virtuele Windows 10-machine toevoegen voor een jumpbox" border="true":::

Voer de vereiste gegevens in de velden in en selecteer **Controleren + maken**. Raadpleeg de volgende tabel voor meer informatie over deze items.

| Veld | Waarde |
| --- | --- |
| **Abonnement** | Deze waarde is al ingevuld met het abonnement waartoe de resourcegroep behoort. |
| **Resourcegroep** | Deze waarde is al ingevuld voor de huidige resourcegroep. Dit moet de resourcegroep zijn die u in een voorgaande zelfstudie hebt gemaakt. |
| **Naam van virtuele machine** | Voer een unieke naam voor de VM in. |
| **Regio** | Selecteer de geografische locatie van de virtuele machine. |
| **Beschikbaarheidsopties** | Laat de standaardwaarde ingeschakeld. |
| **Installatiekopie** | Selecteer de VM-installatiekopie. |
| **Grootte** | Laat de waarde van de standaardgrootte staan. |
| **Verificatietype**  | Selecteer **Wachtwoord**. |
| **Gebruikersnaam** | Voer de gebruikersnaam in voor aanmelding bij de virtuele machine. |
| **Wachtwoord** | Voer het wachtwoord in om u aan te melden bij de virtuele machine. |
| **Wachtwoord bevestigen** | Voer het wachtwoord in om u aan te melden bij de virtuele machine. |
| **Openbare binnenkomende poorten** | Selecteer **Geen**. Als u Geen selecteert, kunt u [JIT-toegang](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-) gebruiken om de toegang tot de virtuele machine alleen te beheren wanneer u toegang wilt.  |

Nadat u de juiste informatie hebt ingevoerd, klikt u op **Controleren + maken**. Wanneer de validatie is geslaagd, selecteert u **Maken** om het proces voor het maken van de virtuele machine te starten.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Een nieuwe virtuele Windows 10-machine voor een jumpbox maken" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Verbinding maken met het lokale vCenter van uw privécloud

Meld u vanuit de jumpbox aan bij de vSphere-client met eenmalige aanmelding voor VMware vCenter. Meld u bij de vSphere-client aan met een gebruikersnaam van een cloudbeheerder. Accepteer het beveiligingsrisico en ga door wanneer er een waarschuwing over een mogelijk beveiligingsrisico wordt weergegeven. Meld u bij VMware vCenter aan met eenmalige aanmelding en controleer of de gebruikersinterface wordt weergegeven.

Selecteer in de Azure-portal uw privécloud en selecteer vervolgens in de weergave **Overzicht** de optie **Identiteit > Standaard**. De URL's en aanmeldingsreferenties voor vCenter en NSX-T-beheer van de privécloud worden weergegeven.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="URL's en referenties weergeven van vCenter en NSX-beheer van de privécloud" border="true":::

Ga naar de virtuele machine die u in de vorige stap hebt gemaakt en maak verbinding met de virtuele machine. Zie [Verbinding maken met een virtuele machine](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) voor gedetailleerde stappen voor het maken van een verbinding met de virtuele machine

Open een browser in de virtuele Windows-machine en navigeer op twee tabbladen naar de URL's van vCenter en NSX-T-beheer. Voer op het tabblad vCenter de `cloudadmin@vmcp.local`-gebruikersreferenties in uit de vorige stap.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Aanmelden bij vCenter van de privécloud" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portal" border="true":::

Meld u op het tweede tabblad van de browser aan bij NSX-T-beheer.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Lokale startpagina van NSX-beheer voor privécloud" border="true":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken om verbinding te maken met vCenter
> * Aanmelden bij vCenter vanaf uw virtuele machine

Ga verder met de volgende zelfstudie om te leren hoe u uw AVS-privécloud kunt schalen.

> [!div class="nextstepaction"]
> [Een AVS-privécloud maken](tutorial-scale-private-cloud.md)
