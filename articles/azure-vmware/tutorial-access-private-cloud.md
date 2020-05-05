---
title: Zelf studie-informatie over toegang tot uw privécloud
description: Meer informatie over het openen van een Azure VMware-oplossing (AVS) privécloud
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: fcee332818c89d9c8b00795dca9e74f68260eefd
ms.sourcegitcommit: d9cd51c3a7ac46f256db575c1dfe1303b6460d04
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/04/2020
ms.locfileid: "82740190"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Zelf studie: meer informatie over het openen van een Azure VMware-oplossing (AVS) privécloud

Tijdens de preview-versie kunt u met AVS geen persoonlijke Cloud beheren met uw on-premises vCenter. U moet aanvullende instellingen en verbindingen met een lokale vCenter-instantie uitvoeren via een Jump box. 

In deze zelf studie maakt u een virtuele Windows-machine voor een Jump box in de resource groep die u in de vorige zelf studie zelf studie hebt gemaakt [: netwerken configureren voor uw VMware-privécloud in azure](tutorial-configure-networking.md) en aanmelden bij vCenter. Dit is een VM in hetzelfde virtuele netwerk dat u hebt gemaakt en biedt toegang tot vCenter en NSX Manager. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken die moet worden gebruikt om verbinding te maken met vCenter
> * Meld u aan bij vCenter vanaf uw virtuele machine

## <a name="create-a-new-windows-virtual-machine"></a>Een nieuwe Windows-VM maken

Selecteer in de resource groep **+ toevoegen** en vervolgens zoeken. Selecteer **micro soft Windows 10**en klik vervolgens op **maken**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Een nieuwe Windows 10-VM toevoegen voor een JumpBox" border="true":::

Voer de vereiste gegevens in de velden in en selecteer vervolgens **controleren + maken**. Zie de volgende tabel voor meer informatie over de velden.

| Veld | Waarde |
| --- | --- |
| **Abonnement** | Deze waarde is al ingevuld met het abonnement waarvan de resource groep deel uitmaakt. |
| **Resourcegroep** | Deze waarde is al ingevuld voor de huidige resource groep. Dit moet de resource groep zijn die u in een voor gaande zelf studie hebt gemaakt. |
| **Naam van de virtuele machine** | Voer een unieke naam in voor de virtuele machine. |
| **Regio** | Selecteer de geografische locatie van de virtuele machine. |
| **Beschikbaarheidsopties** | Zorg dat de standaard waarde is geselecteerd. |
| **Installatiekopie** | Selecteer de VM-installatie kopie. |
| **Grootte** | Wijzig de waarde voor standaard grootte. |
| **Verificatie type**  | Selecteer **wacht woord**. |
| **Gebruikers** | Voer de gebruikers naam in voor aanmelding bij de virtuele machine. |
| **Wachtwoord** | Voer het wacht woord in om u aan te melden bij de virtuele machine. |
| **Wacht woord bevestigen** | Voer het wacht woord in om u aan te melden bij de virtuele machine. |
| **Open bare binnenkomende poorten** | Selecteer **Geen**. Als u geen selecteert, kunt u [JIT-toegang](../security-center/security-center-just-in-time.md#configure-jit-access-from-an-azure-vms-page-) gebruiken om de toegang tot de virtuele machine alleen te beheren wanneer u deze wilt openen.  |

Nadat u de benodigde gegevens hebt ingevoerd, klikt u op **controleren + maken**. Wanneer de validatie is geslaagd, selecteert u **maken** om het proces voor het maken van de virtuele machine te starten.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Een nieuwe Windows 10-VM maken voor een JumpBox" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Verbinding maken met de lokale vCenter van uw privécloud

Meld u aan bij de vSphere-client met VMware vCenter SSO vanuit het vak Jump box. Meld u aan bij de vSphere-client met behulp van een gebruikers naam voor de Cloud beheerder. Accepteer het beveiligings risico en blijf door gaan wanneer er een waarschuwing over een mogelijk beveiligings risico wordt weer gegeven. Meld u aan bij VMware vCenter met eenmalige aanmelding en controleer of de gebruikers interface wordt weer gegeven.

In de Azure Portal selecteert u uw privécloud en selecteert u vervolgens in de weer gave **overzicht** de optie **identiteit > standaard**. De Url's en aanmeldings referenties voor Private Cloud vCenter en NSX-T Manager worden weer gegeven.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Url's en referenties van de Private Cloud vCenter en NSX Manager weer geven" border="true":::

Ga naar de virtuele machine die u in de vorige stap hebt gemaakt en maak verbinding met de virtuele machine. Zie [verbinding maken met een virtuele machine](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) voor gedetailleerde stappen voor het maken van verbinding met de virtuele machine.

Open in de Windows-VM een browser en navigeer naar de videovcenter-en NSX-beheer-Url's op twee tabbladen. Voer op het tabblad vCenter de `cloudadmin@vmcp.local` gebruikers referenties in van de vorige stap.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Aanmelden bij de privécloud-vCenter" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-Portal" border="true":::

Meld u op het tweede tabblad van de browser aan bij NSX-T-beheer.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Lokale privé Cloud NSX Manager-start pagina" border="true":::

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een virtuele Windows-machine maken die moet worden gebruikt om verbinding te maken met vCenter
> * Meld u aan bij vCenter vanaf uw virtuele machine

Ga verder met de volgende zelf studie om te leren hoe u de privécloud van uw AVS kunt schalen.

> [!div class="nextstepaction"]
> [Een persoonlijke cloud van een AVS schalen](tutorial-scale-private-cloud.md)