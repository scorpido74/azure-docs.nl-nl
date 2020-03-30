---
title: Privé verbinding maken met een web-app met Azure Private Endpoint
description: Privé verbinding maken met een web-app met Azure Private Endpoint
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: 2f10c7378ae7681b14df6e96b6a6f1adac832d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287812"
---
# <a name="connect-privately-to-a-web-app-using-azure-private-endpoint-preview"></a>Privé verbinding maken met een web-app met Azure Private Endpoint (Voorbeeld)

Azure Private Endpoint is de fundamentele bouwsteen voor Private Link in Azure. Hiermee u privé verbinding maken met uw web-app.
In deze Quickstart leert u hoe u een web-app met privéeindpunt implementeert en verbinding maakt met deze web-app vanaf een virtuele machine.

Zie [Privéeindpunten gebruiken voor Azure Web App voor][privatenedpointwebapp]meer informatie.

> [!Note]
>De preview is beschikbaar in de regio's Oost-VS en West-VS 2 voor alle PremiumV2 Windows- en Linux Web Apps. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="virtual-network-and-virtual-machine"></a>Virtueel netwerk en virtuele machine

In deze sectie maakt u het virtuele netwerk en het subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot uw web-app via het privéeindpunt.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en subnet.

1. Selecteer linksboven in het scherm **de** > **Networking** > optie Een**virtueel netwerk netwerknetwerk** voor bronnen maken of zoek naar **virtueel netwerk** in het zoekvak.

1. Voer in **Virtueel netwerk maken**deze informatie in of selecteer deze op het tabblad Basisbeginselen:

   > [!div class="mx-imgBorder"]
   > ![Virtueel netwerk maken][1]

1. Klik **op 'Volgende: IP-adressen >'** en voer deze informatie in of selecteer deze gegevens:

   > [!div class="mx-imgBorder"]
   >![IP-adressen configureren][2]

1. Klik in de subnetsectie op **'+ Subnet toevoegen'** en voer de volgende gegevens in en klik op **'Toevoegen'.**

   > [!div class="mx-imgBorder"]
   >![Subnet toevoegen][3]

1. Klik **op 'Controleren + maken'**

1. Nadat de validatie is geslaagd, klikt u op **'Maken'.**

### <a name="create-virtual-machine"></a>Virtuele machine maken

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een resource** > **Compute** > **Virtual-machine maken**

1. Typ of selecteer in Een virtuele machine maken - Basisprincipes de volgende gegevens:

   > [!div class="mx-imgBorder"]
   >![Basic virtuele machine][4]

1. Selecteer **'Volgende: schijven'**

   Standaardinstellingen behouden.

1. Selecteer **'Volgende: netwerken'** en selecteer deze informatie:

   > [!div class="mx-imgBorder"]
   >![Networking][5]

1. Klik **op 'Controleren + Maken'.**

1. Klik op **'Maken'** wanneer de validatie is verstreken.

## <a name="create-your-web-app-and-private-endpoint"></a>Uw web-app en privéeindpunt maken

In deze sectie maakt u een privéweb-app met behulp van een privéeindpunt.

> [!Note]
>De functie Privéeindpunt is alleen beschikbaar voor de Premium V2 SKU.

### <a name="web-app"></a>Web-app

1. Selecteer linksboven in het scherm in de Azure-portal > de optie Een**bronwebweb-app** **Create a resource** > **maken**

1. Voer in Web App maken - Basisbeginselen deze informatie in of selecteer deze:

   > [!div class="mx-imgBorder"]
   >![Basisvoor Web App][6]

1. Selecteer **'Controleren + maken'**

1. Klik op **'Maken'** wanneer de validatie is verstreken.

### <a name="create-the-private-endpoint"></a>Het privéeindpunt maken

1. Selecteer in de eigenschappen van Web App de optie > **Netwerkinstellingen** en klik op **Settings** **'Uw privéeindpuntverbindingen configureren'.**

   > [!div class="mx-imgBorder"]
   >![Web App-netwerken][7]

1. Klik in de wizard op **'+ toevoegen'.**

   > [!div class="mx-imgBorder"]
   >![Privéeindpunt voor Web App][8]

1. Vul de informatie over het abonnement, VNet en Subnet en klik op **'OK'**

   > [!div class="mx-imgBorder"]
   >![Web App-netwerken][9]

1. Bekijk de creatie van het privéeindpunt

   > [!div class="mx-imgBorder"]
   >![Overzicht][10]
   >![Eindweergave van het privéeindpunt][11]

## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

1. Voer **myVm** in de zoekbalk van de portal in
1. Selecteer de **knop Verbinding maken**. Nadat u de knop Verbinding maken hebt geselecteerd, opent Verbinding maken met virtuele machine, selecteert u **RDP**

   > [!div class="mx-imgBorder"]
   >![Knop RDP][12]

1. Azure maakt een Extern bureaublad-protocol (.rdp)-bestand en downloadt het naar uw computer nadat u op **RDP-bestand downloaden** hebt geklikt

   > [!div class="mx-imgBorder"]
   >![RDP-bestand downloaden][13]

1. Open het bestand downloaded.rdp.

- Selecteer Verbinding maken wanneer hierom wordt gevraagd.
- Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

> [!Note]
> Mogelijk moet u Meer opties selecteren > Een ander account gebruiken om de referenties op te geven die u hebt ingevoerd toen u de vm maakte.

- Selecteer OK.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u Ja of Doorgaan.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.

## <a name="access-web-app-privately-from-the-vm"></a>Web-app privé openen vanaf de VM

In deze sectie maakt u privé verbinding met de web-app via het privéeindpunt.

1. Download het privé-IP-adres van uw privéeindpunt in de zoekbalktype **Privékoppeling**en selecteer Privékoppeling

   > [!div class="mx-imgBorder"]
   >![Private Link][14]

1. Selecteer **privéeindpunten** in het Privé-verbindingscentrum om al uw privéeindpunten weer te geven

   > [!div class="mx-imgBorder"]
   >![Privé-linkcentrum][15]

1. Selecteer de privé-eindpuntkoppeling naar uw web-app en uw subnet

   > [!div class="mx-imgBorder"]
   >![Eigenschappen voor privéeindpunt][16]

1. Kopieer het privé-IP-adres van uw privéeindpunt en de FQDN van uw web-app, in ons geval webappdemope.azurewebsites.net 10.10.2.4

1. Controleer in de myVM of de web-app niet toegankelijk is via het openbare IP-adres. Een browser openen en de naam van de web-app plakken, moet u een 403 verboden foutpagina hebben

   > [!div class="mx-imgBorder"]
   >![Verboden][17]

> [!Important]
> Aangezien deze functie in preview is, moet u de DNS-vermelding handmatig beheren.

1. De hostinvoer maken, verkenner openen en het hosts-bestand zoeken

   > [!div class="mx-imgBorder"]
   >![Hosts-bestand][18]

1. Een vermelding toevoegen met het privé-IP-adres en de openbare naam van uw web-app door het hosts-bestand met notitieblok te bewerken

   > [!div class="mx-imgBorder"]
   >![Host inhoud][19]

1. Sla het bestand op.

1. Een browser openen en de url van uw web-app typen

   > [!div class="mx-imgBorder"]
   >![Website met PE][20]

1. U hebt toegang tot uw webapp via het privéeindpunt

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het privéeindpunt, Web App en de VM, verwijdert u de brongroep en alle bronnen die deze bevat:

1. Voer kant-en-klaar in het vak Zoeken boven aan de portal en selecteer kant-en-klare in de zoekresultaten.
1. Selecteer Resourcegroep verwijderen.
1. Voer kant-en-klare gegevens in voor TYPE DE NAAM VAN DE RESOURCEGROEP en selecteer Verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Quickstart hebt u een VM gemaakt op een virtueel netwerk, een web-app en een privéeindpunt. U hebt vanaf internet verbinding gemaakt met een virtuele machine en veilig gecommuniceerd met de web-app via Private Link. Zie [Wat is Azure Private Endpoint][privateendpoint]voor meer informatie over privéeindpunt .

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
