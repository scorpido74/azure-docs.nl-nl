---
title: 'Zelfstudie: Meer informatie over toegang tot uw privécloud'
description: Meer informatie over het openen van een Azure VMware Solution-privécloud
ms.topic: tutorial
ms.date: 08/21/2020
ms.openlocfilehash: 73226c6aa567dc5fbe18251bed4812637664a02c
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750530"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-private-cloud"></a>Zelfstudie: Meer informatie over het openen van een Azure VMware Solution-privécloud

Tijdens de preview kunt u met Azure VMware Solution geen privécloud beheren met uw on-premises vCenter. U moet via een jumpbox aanvullende instellingen en verbindingen met een lokaal vCenter-exemplaar uitvoeren. 

In deze zelfstudie maakt u een virtuele Windows-machine voor een jumpbox in de resource groep die u hebt gemaakt in de vorige zelfstudie [Zelfstudie: Netwerken configureren voor uw VMware-privécloud in Azure](tutorial-configure-networking.md) en meldt u zich aan bij vCenter. Dit is een VM in hetzelfde virtuele netwerk dat u hebt gemaakt en biedt toegang tot vCenter en NSX-beheer. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken om verbinding te maken met vCenter
> * Aanmelden bij vCenter vanaf uw virtuele machine

## <a name="create-a-new-windows-virtual-machine"></a>Een nieuwe virtuele Windows-machine maken

1. Selecteer in de resourcegroep **+ Toevoegen**, zoek en selecteer **Microsoft Windows 10** en klik vervolgens op **Maken**.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Een nieuwe virtuele Windows 10-machine toevoegen voor een jumpbox." border="true":::

1. Voer de vereiste gegevens in de velden in en selecteer **Controleren + maken**. Raadpleeg de volgende tabel voor meer informatie over deze items.

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
   | **Openbare binnenkomende poorten** | Selecteer **Geen**. Als u Geen selecteert, kunt u [JIT-toegang](../security-center/security-center-just-in-time.md#jit-configure) gebruiken om de toegang tot de virtuele machine alleen te beheren wanneer u toegang wilt.  |

1. Nadat u de juiste informatie hebt ingevoerd, klikt u op **Controleren + maken**. 
1. Wanneer de validatie is geslaagd, selecteert u **Maken** om het proces voor het maken van de virtuele machine te starten.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Een nieuwe virtuele Windows 10-machine maken voor een jumpbox." border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Verbinding maken met het lokale vCenter van uw privécloud

1. Meld u vanuit de jumpbox aan bij de vSphere-client met eenmalige aanmelding voor VMware vCenter. Meld u bij de vSphere-client aan met een gebruikersnaam van een cloudbeheerder. Accepteer het beveiligingsrisico en ga door wanneer er een waarschuwing over een mogelijk beveiligingsrisico wordt weergegeven. Meld u bij VMware vCenter aan met eenmalige aanmelding en controleer of de gebruikersinterface wordt weergegeven.

1. Selecteer in de Azure-portal uw privécloud en selecteer vervolgens in de weergave **Overzicht** de optie **Identiteit > Standaard**. De URL's en aanmeldingsreferenties voor vCenter en NSX-T-beheer van de privécloud worden weergegeven.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="URL's en referenties weergeven van vCenter en NSX-beheer van de privécloud." border="true":::

1. Ga naar de virtuele machine die u in de vorige stap hebt gemaakt en maak verbinding met de virtuele machine. Zie [Verbinding maken met een virtuele machine](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) voor gedetailleerde stappen voor het maken van een verbinding met de virtuele machine

1. Open een browser in de virtuele Windows-machine en navigeer op twee tabbladen naar de URL's van vCenter en NSX-T-beheer. Voer op het tabblad vCenter de `cloudadmin@vmcp.local`-gebruikersreferenties in uit de vorige stap.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Aanmelden bij vCenter van de privécloud." border="true":::

   :::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portal." border="true":::

1. Meld u op het tweede tabblad van de browser aan bij NSX-T-beheer.

   :::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Meld u op het tweede tabblad van de browser aan bij NSX-T-beheer." border="true":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken om verbinding te maken met vCenter
> * Aanmelden bij vCenter vanaf uw virtuele machine

Ga verder met de volgende zelfstudie om te leren hoe u uw Azure VMware Solution-privécloud kunt schalen.

> [!div class="nextstepaction"]
> [Een Azure VMware Solution-privécloud schalen](tutorial-scale-private-cloud.md)
