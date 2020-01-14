---
title: Een persoonlijk eind punt maken voor een beveiligde verbinding
titleSuffix: Azure Cognitive Search
description: Een persoonlijk eind punt in een virtueel netwerk instellen voor een beveiligde verbinding met een Azure Cognitive Search-service
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: cfa8db0d00f351f5ab2bda96744305ca83cccb19
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922457"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Een persoonlijk eind punt maken voor een beveiligde verbinding met Azure Cognitive Search (preview-versie)

[Privé-eind punten](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search een client in een virtueel netwerk in staat stellen om veilig toegang te krijgen tot gegevens in een zoek index via een [persoonlijke koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de [adres ruimte van het virtuele netwerk](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) voor uw zoek service. Netwerk verkeer tussen de client en de zoek service gaat over het virtuele netwerk en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt geëlimineerd. Raadpleeg de [sectie Beschik baarheid](../private-link/private-link-overview.md#availability) in de product documentatie voor een lijst met andere PaaS-services die persoonlijke koppelingen ondersteunen.

> [!Important]
> Ondersteuning voor privé-eind punten voor Azure Cognitive Search is beschikbaar als preview-versie van beperkte toegang en is momenteel niet bedoeld voor productie gebruik. Vul het formulier voor de [toegangs aanvraag](https://aka.ms/SearchPrivateLinkRequestAccess) in en verzend het als u toegang wilt tot de preview. Het formulier vraagt informatie over u, uw bedrijf en algemene toepassings architectuur. Zodra we uw aanvraag hebben gecontroleerd, ontvangt u een bevestigings-e-mail met aanvullende instructies.
>
> Zodra u toegang tot de preview hebt gekregen, kunt u persoonlijke eind punten voor uw service configureren met behulp van de Azure Portal en REST API versie [2019-10-06-preview](search-api-preview.md).
>   

Met persoonlijke eind punten voor uw zoek service kunt u het volgende doen:

- Alle verbindingen op het open bare eind punt voor uw zoek service blok keren.
- Verbeter de beveiliging van het virtuele netwerk door u in staat te stellen exfiltration van gegevens van het virtuele netwerk te blok keren.
- Maak veilig verbinding met uw zoek service vanuit on-premises netwerken die verbinding maken met het virtuele netwerk via [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../expressroute/expressroute-locations.md) met privé-peering.

> [!NOTE]
> Er zijn momenteel enkele beperkingen in de preview-versie waarvan u rekening moet houden:
> * Alleen beschikbaar voor zoek services op de laag **basis** . 
> * Beschikbaar in de Verenigde Staten West 2, VS-West-Centraal, VS-Oost, Zuid-Centraal, Australië-oost en Australië-zuidoost regio's.
> * Wanneer het service-eind punt privé is, zijn sommige Portal functies uitgeschakeld. U kunt informatie over het service niveau weer geven en beheren, maar de toegang tot de portal voor het indexeren van gegevens en de verschillende onderdelen in de service, zoals de definities index, Indexer en vaardigheids, is beperkt om veiligheids redenen.
> * Wanneer het service-eind punt privé is, moet u de zoek-API gebruiken om documenten te uploaden naar de index.
> * U moet de volgende koppeling gebruiken om de ondersteunings optie voor privé-eind punten weer te geven in de Azure Portal: https://portal.azure.com/?feature.enablePrivateEndpoints=true

In dit artikel leert u hoe u de portal kunt gebruiken om een nieuw exemplaar van Azure Cognitive Search service te maken dat niet toegankelijk is via een openbaar IP-adres, hoe u een virtuele machine van Azure kunt configureren in hetzelfde virtuele netwerk en hoe u deze kunt gebruiken om toegang te krijgen tot de zoek service via een persoonlijke endpoints.


## <a name="create-a-vm"></a>Een VM maken
In deze sectie maakt u een virtueel netwerk en een subnet voor het hosten van de virtuele machine die wordt gebruikt om toegang te krijgen tot het privé-eind punt van uw zoek service.

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

1. Selecteer op het tabblad Start Azure Portal **een resource maken** > **netwerk** > **virtueel netwerk**.

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | *MyVirtualNetwork* invoeren |
    | Adresruimte | Voer *10.1.0.0/16* in |
    | Abonnement | Selecteer uw abonnement|
    | Resourcegroep | Selecteer **nieuwe maken**, Voer *myResourceGroup*in en selecteer **OK** . |
    | Locatie | Selecteer **VS-West** of welke regio u gebruikt|
    | Subnet - Naam | *MySubnet* invoeren |
    | Subnet - adresbereik | Voer *10.1.0.0/24* in |
    |||

1. Laat de rest als standaard en selecteer **maken**.


## <a name="create-your-search-service-with-a-private-endpoint"></a>Uw zoek service maken met een persoonlijk eind punt

In deze sectie maakt u een nieuwe Azure Cognitive Search-service met een persoonlijk eind punt. 

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **Web** > **Azure Cognitive Search**.

1. In **nieuwe Search service basis principes**voert u de volgende gegevens in of selecteert u deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | **EXEMPLAARDETAILS** |  |
    | URL | Voer een unieke naam in. |
    | Locatie | Selecteer de regio die u hebt opgegeven bij het aanvragen van toegang tot deze preview-functie. |
    | Prijscategorie | Selecteer **prijs categorie wijzigen** en kies **basis**. Deze laag is vereist voor de preview-versie. |
    |||
  
1. Selecteer **volgende: schalen**.

1. Wijzig de waarden als standaard en selecteer **volgende: netwerken**.

1. Selecteer in **nieuwe Search service netwerken** **persoonlijke** voor **endpoint Connectivity (gegevens)** .

1. In **nieuwe Search service-netwerken**selecteert u **+ toevoegen** onder **persoonlijk eind punt**. 

1. Voer in **persoonlijk eind punt maken**de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.|
    | Locatie | Selecteer **VS-West**.|
    | Name | Voer *myPrivateEndpoint*in.  |
    | Doel-subresource | De standaard **searchService**behouden. |
    | **INBEL** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* in de resource groep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PARTICULIERE DNS** |  |
    | Integreren met een privé-DNS-zone  | Accepteer de standaard waarde **Ja**. |
    | Privé-DNS-zone  | Wijzig de standaard waarde * * (nieuw) privatelink.search.windows.net * *. |
    |||

1. Selecteer **OK**. 

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert. 

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**. 

1. Zodra de inrichting van de nieuwe service is voltooid, bladert u naar de resource die u zojuist hebt gemaakt.

1. Selecteer **sleutels** in het menu links.

1. Kopieer de **primaire Administrator sleutel** voor later.

### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in de linkerbovenhoek van het scherm in het Azure Portal **een resource maken** > **reken** > **virtuele machine**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit gemaakt in de vorige sectie.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in. |
    | Regio | Selecteer **VS-West** of de regio die u gebruikt. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | afbeelding | Selecteer **Windows Server 2019 Data Center**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikers naam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Laat de standaard **geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Behoud de standaard **-RDP (3389)** . |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **volgende: schijven**.

1. In **een virtuele machine maken-schijven**, de standaard instellingen behouden en **volgende selecteren: netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | De standaard **MyVirtualNetwork**behouden.  |
    | Adresruimte | De standaard **10.1.0.0/24**behouden.|
    | Subnet | Behoud de standaard **mySubnet (10.1.0.0/24)** .|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    ||

1. Selecteer **Controleren + maken**. U gaat naar de pagina **controleren en maken** waar Azure uw configuratie valideert.

1. Wanneer u het bericht **door gegeven validatie** ziet, selecteert u **maken**. 


## <a name="connect-to-a-vm-from-the-internet"></a>Verbinding maken met een virtuele machine via internet

Maak als volgt verbinding met de VM- *myVm* van het Internet:

1. Voer in de zoek balk van de portal *myVm*in.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het bestand gedownloade. rdp *.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikers naam en het wacht woord in die u hebt opgegeven bij het maken van de virtuele machine.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > **Een ander account gebruiken** selecteren om de referenties op te geven die u hebt ingevoerd tijdens het maken van de VM.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  


## <a name="access-the-search-service-privately-from-the-vm"></a>De zoek service privé openen vanuit de VM

In deze sectie gaat u de persoonlijke netwerk toegang tot de zoek service controleren en een persoonlijke verbinding maken met het gebruik van het persoonlijke eind punt.

1. Open Power shell in de Extern bureaublad van *myVM*.

1. Voer ' nslookup [naam zoek service ']. Search. Windows. net ' in

    U ontvangt een bericht dat er ongeveer als volgt uitziet:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```
1. Volg deze [Snelstartgids](search-get-started-postman.md) van de VM om een nieuwe zoek index in uw service te maken in postman met behulp van de rest API.  Gebruik de sleutel die u in een vorige stap hebt gekopieerd om u te verifiëren bij de service.

1. Probeer enkele van deze dezelfde aanvragen in postman op uw lokale werk station.

1. Als u de Snelstartgids vanaf de VM kunt volt ooien, maar u een fout bericht krijgt dat de externe server niet op uw lokale werk station aanwezig is, hebt u een persoonlijk eind punt geconfigureerd voor uw zoek service.

1. Sluit de verbinding met extern bureau blad met *myVM*. 


## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het persoonlijke eind punt, de zoek service en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat:
1. Geef *myResourceGroup* op in het **zoekvak** boven aan de portal en selecteer *myResourceGroup* in de zoek resultaten. 
1. Selecteer **Resourcegroep verwijderen**. 
1. Voer *myResourceGroup* in om **de naam van de resource groep te typen** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een VM gemaakt in een virtueel netwerk en een zoek service met een persoonlijk eind punt. U hebt verbinding gemaakt met de virtuele machine via internet en u kunt deze veilig door gegeven aan de zoek service met behulp van een persoonlijke koppeling. Zie [Wat is Azure private endpoint?](../private-link/private-endpoint-overview.md)voor meer informatie over privé-eind punten.
