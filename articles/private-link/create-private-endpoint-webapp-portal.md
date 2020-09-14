---
title: Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt (preview-versie)
description: In dit artikel wordt uitgelegd hoe u een persoonlijke verbinding maakt met een web-app met behulp van Azure private endpoint (preview).
author: ericgre
ms.assetid: b8c5c7f8-5e90-440e-bc50-38c990ca9f14
ms.topic: how-to
ms.date: 09/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ccbcdbe9204120e1cf181136f566556ec30be871
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2020
ms.locfileid: "90054531"
---
# <a name="connect-privately-to-a-web-app-by-using-azure-private-endpoint-preview"></a>Privé verbinden met een web-app met behulp van een persoonlijk Azure-eind punt (preview-versie)

Persoonlijk Azure-eind punt (preview) is de fundamentele bouw steen voor persoonlijke Azure-koppelingen. Door een persoonlijk eind punt te gebruiken, kunt u een persoonlijke verbinding maken met uw web-app. In dit artikel leert u hoe u een web-app implementeert met behulp van een persoonlijk eind punt en vervolgens verbinding maakt met de web-app vanaf een virtuele machine (VM).

Zie [Private-eind punten gebruiken voor een Azure-web-app][privateendpointwebapp]voor meer informatie.

> [!Note]
> Persoonlijk eind punt (preview) is beschikbaar in open bare regio's voor PremiumV2 Windows Web apps, Linux-web-apps en het Premium-abonnement van Azure Functions (ook wel het elastische Premium-abonnement genoemd). 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [de Azure Portal](https://portal.azure.com)voordat u begint.

## <a name="create-a-virtual-network-and-virtual-machine"></a>Een virtueel netwerk en een virtuele machine maken

In deze sectie maakt u een virtueel netwerk en een subnet voor het hosten van een virtuele machine die u gaat gebruiken voor toegang tot een web-app via een persoonlijk eind punt.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

Ga als volgt te werk om het virtuele netwerk en subnet te maken:

1. Selecteer in het linkerdeel venster **een resource**  >  **netwerk**  >  **virtueel netwerk**maken.

1. Selecteer in het deel venster **virtueel netwerk maken** het tabblad **basis beginselen** en voer vervolgens de informatie in die hier wordt weer gegeven:

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het deel venster ' maken Virtual Network ' in de Azure Portal.][1]

1. Selecteer het tabblad **IP-adressen** en voer vervolgens de informatie in die hier wordt weer gegeven:

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het tabblad ' IP-adressen ' in het deel venster virtueel netwerk maken.][2]

1. Selecteer **subnet toevoegen**in het gedeelte **subnet** , voer de informatie in die hier wordt weer gegeven en selecteer vervolgens **toevoegen**.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het deel venster ' subnet toevoegen '.][3]

1. Selecteer **Controleren + maken**.

1. Selecteer **maken**nadat de validatie is voltooid.

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

Ga als volgt te werk om de virtuele machine te maken:

1. Selecteer in de Azure Portal in het linkerdeel venster de optie voor het **maken van een**  >  **Compute**  >  **virtuele machine**voor het berekenen van een resource.

1. Voer in het deel venster **een virtuele machine maken-basis beginselen** de informatie in die hier wordt weer gegeven:

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het deel venster ' een virtuele machine maken '.][4]

1. Selecteer **Volgende: Schijven**.

1. Behoud de standaard instellingen in het deel venster **schijven** en selecteer vervolgens **volgende: netwerken**.

1. Voer in het deel venster **netwerken** de informatie in die hier wordt weer gegeven:

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van het tabblad netwerken in het deel venster een virtuele machine maken.][5]

1. Selecteer **Controleren + maken**.

1. Selecteer **maken**nadat de validatie is voltooid.

## <a name="create-a-web-app-and-a-private-endpoint"></a>Een web-app en een persoonlijk eind punt maken

In deze sectie maakt u een persoonlijke web-app die gebruikmaakt van een persoonlijk eind punt.

> [!Note]
> De functie privé-eind punt is alleen beschikbaar voor de PremiumV2-laag.

### <a name="create-the-web-app"></a>De web-app maken

1. Selecteer in de Azure Portal in het linkerdeel venster de optie **een resource**web-web-  >  **Web**  >  **app**maken.

1. Op het deel venster **Web-app** selecteert u het tabblad **basis beginselen** en voert u vervolgens de informatie in die hier wordt weer gegeven:

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van het tabblad ' basis informatie ' in het deel venster Web-app.][6]

1. Selecteer **Controleren + maken**.

1. Selecteer **maken**nadat de validatie is voltooid.

### <a name="create-the-private-endpoint"></a>Het persoonlijke eind punt maken

1. Selecteer in de eigenschappen van de web-app onder **instellingen**de optie **netwerken**en selecteer vervolgens onder **Private endpoint Connections (preview)** **uw particuliere endpoint-verbindingen configureren**.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van de koppeling ' uw persoonlijke eindpunt verbindingen configureren ' in het deel venster Web-app-netwerk.][7]

1. Selecteer in de wizard **verbindingen met privé-eind punten (preview)** de optie **toevoegen**.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de knop toevoegen in de wizard Persoonlijke eind punt verbindingen (preview).][8]

1. Selecteer de juiste gegevens in de vervolg keuzelijst **abonnement**, het **virtuele netwerk**en het **subnet** en selecteer vervolgens **OK**.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van het deel venster ' persoonlijk eind punt toevoegen (preview) '.][9]

1. De voortgang van het maken van een persoonlijk eind punt bewaken.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van de voortgang van het toevoegen van het persoonlijke eind punt. ][10]
   >  ![ Scherm opname van het zojuist gemaakte persoonlijke eind punt.][11]

## <a name="connect-to-the-vm-from-the-internet"></a>Verbinding maken met de virtuele machine via Internet

1. Typ **myVm**in het **zoekvak** Azure Portal.
1. Selecteer **verbinding maken**en selecteer vervolgens **RDP**.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de knop RDP in het deel venster myVM.][12]

1. Selecteer in het deel venster **verbinding maken met RDP** de optie **RDP-bestand downloaden**.  

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van de knop RDP-bestand downloaden in het deel venster verbinding maken met RDP.][13]

   Er wordt door Azure een Remote Desktop Protocol RDP-bestand gemaakt en gedownload naar uw computer.   

1. Open het gedownloade RDP-bestand.

   a. Selecteer **verbinding maken**bij de prompt.  
   b. Voer de gebruikersnaam en het wachtwoord in die u hebt opgegeven tijdens het maken van de VM.

     > [!Note]
     > Als u deze referenties wilt gebruiken, moet u mogelijk **meer keuzes**selecteren  >  **een ander account gebruiken**.

1. Selecteer **OK**.

   > [!Note]
   > Als u een certificaat waarschuwing ontvangt tijdens het aanmeldings proces, selecteert u **Ja** of **door gaan**.

1. Wanneer het VM-bureau blad wordt weer gegeven, minimaliseert u het om terug te gaan naar het lokale bureau blad.

## <a name="access-the-web-app-privately-from-the-vm"></a>De web-app privé openen vanuit de VM

In deze sectie maakt u een persoonlijke verbinding met de web-app met behulp van het persoonlijke eind punt.

1. Als u het privé-IP-adres van uw privé-eind punt wilt ophalen, typt u **privé-koppeling** in het **zoekvak** en selecteert u in de lijst met resultaten de optie **privé-koppeling**.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de koppeling ' persoonlijke koppeling ' in de lijst met zoek resultaten.][14]

1. Selecteer **privé-eind punten** in het linkerdeel venster van het persoonlijke koppelings centrum om uw privé-eind punten weer te geven.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van de lijst met persoonlijke eind punten in het persoonlijke koppelingen centrum.][15]

1. Selecteer het persoonlijke eind punt dat is gekoppeld aan uw web-app en uw subnet.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van het deel venster Eigenschappen voor een persoonlijk eind punt.][16]

1. Kopieer het privé-IP-adres van uw persoonlijke eind punt en de Fully Qualified Domain Name (FQDN) van uw web-app. In het vorige voor beeld is de persoonlijke ID *`webappdemope.azurewebsites.net 10.10.2.4`* .

1. Controleer in het deel venster **myVM** of de web-app niet toegankelijk is via het open bare IP-adres. Als u dit wilt doen, opent u een browser en plakt u de naam van de web-app. Op de pagina moet het bericht ' fout 403-verboden ' worden weer gegeven.

   > [!div class="mx-imgBorder"]
   > ![Scherm opname van de fout pagina ' fout 403-verboden '.][17]

   > [!Important]
   > Omdat deze functie in preview is, moet u de Domain Name Service-vermelding (DNS) hand matig beheren.

   Voor de DNS voert u een van de volgende handelingen uit:
 
   - Gebruik de Azure DNS private zone service.  

     a. Maak een persoonlijke DNS-zone *`privatelink.azurewebsites.net`* met de naam en koppel deze aan het virtuele netwerk.  
     b. Maak de twee A-records (dat wil zeggen, de naam van de app en de service besturings beheer-naam [SCM]) met het IP-adres van uw persoonlijke eind punt.  
     > [!div class="mx-imgBorder"]
     > ![Scherm opname van privé zone records voor DNS.][21]  

   - Het *hosts* -bestand van de virtuele machine gebruiken.  

     a. Maak de vermelding hosts, open Verkenner en zoek het bestand *hosts* .  
     > [!div class="mx-imgBorder"]
     > ![Scherm opname van het bestand hosts in Verkenner.][18]  
     b. Voeg een vermelding toe die het privé-IP-adres en de open bare naam van uw web-app bevat door het *hosts* -bestand in een tekst editor te bewerken.  
     > [!div class="mx-imgBorder"]
     > ![Scherm afbeelding van de tekst van het hosts-bestand.][19]  
     c. Sla het bestand op.

1. Typ de URL van uw web-app in een browser.

   > [!div class="mx-imgBorder"]
   > ![Scherm afbeelding van een browser waarin een web-app wordt weer gegeven.][20]

U hebt nu toegang tot uw web-app via het persoonlijke eind punt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het persoonlijke eind punt, de web-app en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat.

1. Voer in de Azure Portal in het **zoekvak** **gereed-RG**in en selecteer vervolgens **gereed-RG** in de lijst met resultaten.

1. Selecteer **Resourcegroep verwijderen**.

1. Onder **Typ de naam van de resource groep**voert u **gereed-RG**in en selecteert u vervolgens **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt in een virtueel netwerk, een web-app en een persoonlijk eind punt. U hebt verbinding gemaakt met een virtuele machine via internet en de web-app wordt veilig gecommuniceerd met behulp van een persoonlijke koppeling. 

Zie [Wat is Azure private endpoint?][privateendpoint]voor meer informatie over privé-eind punten (preview).

<!--Image references-->
[1]: ./media/create-private-endpoint-webapp-portal/createnetwork.png
[2]: ./media/create-private-endpoint-webapp-portal/ipaddresses.png
[3]: ./media/create-private-endpoint-webapp-portal/subnet.png
[4]: ./media/create-private-endpoint-webapp-portal/virtual-machine.png
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
[21]: ./media/create-private-endpoint-webapp-portal/dns-private-zone-records.png


<!--Links-->
[privateendpointwebapp]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
[privateendpoint]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview
