---
title: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt
description: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 06/02/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 1b3ac4c79ce92f591e74821a9f355717e4b22ea4
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737390"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt (preview-versie)

Persoonlijk Azure-eind punt is de fundamentele bouw steen voor privé-koppeling in Azure. Hiermee kunt u privé verbinding maken met uw web-app.
In deze Quick Start leert u hoe u een web-app implementeert met een persoonlijk eind punt en vanaf een virtuele machine verbinding maakt met deze web-app.

Zie voor meer informatie [privé-eind punten gebruiken voor Azure-web-app][privatenedpointwebapp].

> [!Note]
>Het voor beeld is beschikbaar in open bare regio's voor PremiumV2 voor Windows en Linux Web Apps-en elastische Premium-functies. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Virtueel netwerk en virtuele machine

In deze sectie maakt u het virtuele netwerk en het subnet voor het hosten van de virtuele machine die wordt gebruikt voor toegang tot uw web-app via het persoonlijke eind punt.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en een subnet.

1. Selecteer in de linkerbovenhoek van het scherm de optie **een resource**  >  **netwerken**  >  **virtueel netwerk** maken of zoeken naar **virtueel netwerk** in het zoekvak.

1. In **virtueel netwerk maken**typt of selecteert u deze informatie op het tabblad basis beginselen:

   > [!div class="mx-imgBorder"]
   > ![Virtual Network maken][1]

1. Klik op **volgende: IP-adressen >** en typ of Selecteer deze gegevens:

   > [!div class="mx-imgBorder"]
   >![IP-adressen configureren][2]

1. Klik in het gedeelte subnet op **+ subnet toevoegen** en voer de volgende informatie in en klik op **toevoegen** .

   > [!div class="mx-imgBorder"]
   >![Subnet toevoegen][3]

1. Klik op **' bekijken + maken '**

1. Nadat de validatie is voltooid, klikt u op **maken** .

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een**  >  **Compute**  >  **virtuele machine** voor het berekenen van een resource maken

1. Typ of selecteer in Een virtuele machine maken - Basisprincipes de volgende gegevens:

   > [!div class="mx-imgBorder"]
   >![Basis van virtuele machines][4]

1. Selecteer **volgende: schijven**

   Behoud de standaard instellingen.

1. Selecteer **volgende: netwerken**en selecteer deze gegevens:

   > [!div class="mx-imgBorder"]
   >![Networking][5]

1. Klik op **' bekijken + maken '**

1. Wanneer het bericht met de validatie is door gegeven, klikt u op **maken**

## <a name="create-your-web-app-and-private-endpoint"></a>Een web-app en een persoonlijk eind punt maken

In deze sectie maakt u een persoonlijke web-app met behulp van een persoonlijk eind punt.

> [!Note]
>De functie privé-eind punt is alleen beschikbaar voor de Premium v2-SKU.

### <a name="web-app"></a>Web-app

1. Selecteer in de linkerbovenhoek van het scherm in de Azure Portal **een resource maken**  >  **Web**  >  **Web app** .

1. Voer in web-app maken de volgende gegevens in of Selecteer deze:

   > [!div class="mx-imgBorder"]
   >![Web-app-basis][6]

1. Selecteer **' controleren + maken '**

1. Wanneer het bericht met de validatie is door gegeven, klikt u op **maken**

### <a name="create-the-private-endpoint"></a>Het persoonlijke eind punt maken

1. Selecteer in de eigenschappen van de web-app **instellingen**  >  **netwerken** en klik op **uw particuliere endpoint-verbindingen configureren** .

   > [!div class="mx-imgBorder"]
   >![Web-app-netwerken][7]

1. Klik in de wizard op **+ toevoegen**

   > [!div class="mx-imgBorder"]
   >![Persoonlijk eind punt voor web-app][8]

1. Vul de gegevens voor het abonnement, VNet en subnet in en klik op **OK**

   > [!div class="mx-imgBorder"]
   >![Web-app-netwerken][9]

1. Het maken van het persoonlijke eind punt controleren

   > [!div class="mx-imgBorder"]
   >![][10]
   > ![ Definitieve weer gave van het persoonlijke eind punt controleren][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

1. Voer in de zoek balk van de portal **myVm** in
1. Selecteer de **knop verbinding maken**. Nadat u de knop verbinding maken hebt geselecteerd, maakt u verbinding met de virtuele machine openen, selecteert u **RDP**

   > [!div class="mx-imgBorder"]
   >![RDP-knop][12]

1. Er wordt door Azure een Remote Desktop Protocol-bestand (. RDP) gemaakt en gedownload naar uw computer nadat u op **RDP-bestand downloaden** hebt geklikt.

   > [!div class="mx-imgBorder"]
   >![RDP-bestand downloaden][13]

1. Open het gedownloade RDP-bestand.

- Selecteer Verbinding maken wanneer hierom wordt gevraagd.
- Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven bij het maken van de virtuele machine.

> [!Note]
> Mogelijk moet u meer opties selecteren > een ander account gebruiken om de referenties op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine.

- Selecteer OK.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u Ja of Doorgaan.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="access-web-app-privately-from-the-vm"></a>Toegang tot de web-app vanuit de VM

In deze sectie maakt u een persoonlijke verbinding met de web-app met behulp van het persoonlijke eind punt.

1. Haal het privé-IP-adres van uw persoonlijke eind punt op in de **persoonlijke koppeling**van het type zoek balk en selecteer privé-koppeling.

   > [!div class="mx-imgBorder"]
   >![Private Link][14]

1. Selecteer **privé-eind punten** in het persoonlijke koppelings centrum om alle privé-eind punten weer te geven

   > [!div class="mx-imgBorder"]
   >![Persoonlijk koppelings centrum][15]

1. Selecteer de koppeling met het persoonlijke eind punt naar uw web-app en uw subnet

   > [!div class="mx-imgBorder"]
   >![Eigenschappen van persoonlijk eind punt][16]

1. Kopieer het privé-IP-adres van uw persoonlijke eind punt en de FQDN-namen van uw web-app, in ons geval webappdemope.azurewebsites.net 10.10.2.4

1. Controleer in de myVM of de web-app niet toegankelijk is via het open bare IP-adres. Open een browser en plak de naam van de web-app. u moet een 403-verboden fout pagina hebben

   > [!div class="mx-imgBorder"]
   >![Verboden][17]

> [!Important]
> Omdat deze functie in preview is, moet u de DNS-vermelding hand matig beheren.

1. Maak de host-vermelding, open Verkenner en zoek het bestand hosts

   > [!div class="mx-imgBorder"]
   >![Bestand hosts][18]

1. Een item met het privé-IP-adres en de open bare naam van uw web-app toevoegen door het hosts-bestand te bewerken met Klad blok

   > [!div class="mx-imgBorder"]
   >![Host inhoud][19]

1. Sla het bestand op.

1. Open een browser en typ de URL van uw web-app

   > [!div class="mx-imgBorder"]
   >![Website met PE][20]

1. U hebt toegang tot uw web-app via het persoonlijke eind punt

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het persoonlijke eind punt, de web-app en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat:

1. Typ Ready-rg in het zoekvak boven aan de portal en selecteer gereed-rg in de zoek resultaten.
1. Selecteer Resourcegroep verwijderen.
1. Voer gereed-rg in als u de naam van de RESOURCE groep wilt typen en selecteer verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een VM gemaakt in een virtueel netwerk, een web-app en een persoonlijk eind punt. U hebt verbinding gemaakt met een virtuele machine van Internet en u kunt deze veilig door gegeven aan de web-app met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint][privateendpoint]? voor meer informatie over privé-eind punten.

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtualmachine.png
[5]: ./media/create-private-endpoint-webapp-portal/vmnetwork.png
[6]: ./media/create-private-endpoint-webapp-portal/webapp.png
[7]: ./media/create-private-endpoint-webapp-portal/webappnetworking.png
[8]: ./media/create-private-endpoint-webapp-portal/webapppe.png
[9]: ./media/create-private-endpoint-webapp-portal/webapppenetwork.png
[10]: ./media/create-private-endpoint-webapp-portal/inprogress.png
[11]: ./media/create-private-endpoint-webapp-portal/webapppefinal.png
[12]: ./media/create-private-endpoint-webapp-portal/rdp.png
[13]: ./media/create-private-endpoint-webapp-portal/rdpdownload.png
[14]: ./media/create-private-endpoint-webapp-portal/pl.png
[15]: ./media/create-private-endpoint-webapp-portal/plcenter.png
[16]: ./media/create-private-endpoint-webapp-portal/privateendpointproperties.png
[17]: ./media/create-private-endpoint-webapp-portal/forbidden.png
[18]: ./media/create-private-endpoint-webapp-portal/explorer.png
[19]: ./media/create-private-endpoint-webapp-portal/hosts.png
[20]: ./media/create-private-endpoint-webapp-portal/webappwithpe.png

<!--Links-->
[privatenedpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
