---
title: Een persoonlijk eind punt maken voor beveiligde verbindingen
titleSuffix: Azure Cognitive Search
description: In de preview-versie kunt u de toegang tot een Search service-eind punt beperken met behulp van een persoonlijk eind punt en een beveiligde VNet-verbinding.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 0ed6319a33fc3db0b3144d91f4be10b7ab6121d7
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75865010"
---
# <a name="restrict-access-to-azure-cognitive-search-using-private-endpoint-and-a-virtual-network-connection"></a>Beperk de toegang tot Azure Cognitive Search met behulp van een persoonlijk eind punt en een virtuele netwerk verbinding.

> [!IMPORTANT] 
> Ondersteuning voor persoonlijk eind punt is momenteel beschikbaar als preview-versie van beperkte toegang. Deze preview is alleen beschikbaar voor zoek services op de **laag basis**.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie. De [rest API versie 2019-10-01-preview](search-api-preview.md) biedt deze functie. Er is op dit moment geen portal-of .NET SDK-ondersteuning.

In dit artikel vindt u informatie over het maken van een nieuwe zoek service die toegankelijk is via beveiligde verbindingen, zonder toegang vanaf open bare IP-adressen. Client verbindingen zijn toegestaan vanuit virtuele Azure-machines die zijn geïmplementeerd in hetzelfde virtuele netwerk als de service.

## <a name="about-private-endpoint-support"></a>Over ondersteuning voor privé-eind punten

[Privé-eind punten](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search een client in een virtueel netwerk in staat stellen om veilig toegang te krijgen tot gegevens via een [privé-koppeling](../private-link/private-link-overview.md). Het persoonlijke eind punt gebruikt een IP-adres uit de [adres ruimte van het virtuele netwerk](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) voor uw zoek service. Netwerk verkeer tussen de client en de zoek service gaat over het virtuele netwerk en een privé koppeling op het micro soft-backbone-netwerk, waardoor de bloot stelling aan het open bare Internet wordt geëlimineerd. Raadpleeg de [sectie Beschik baarheid](../private-link/private-link-overview.md#availability) in de product documentatie voor een lijst met PaaS-services die persoonlijke koppelingen ondersteunen.

Met een persoonlijk eind punt voor uw zoek service kunt u het volgende doen:

+ Alle verbindingen op het open bare eind punt voor uw zoek service blok keren.
+ Verg root de beveiliging van het virtuele netwerk door exfiltration van gegevens van het virtuele netwerk te blok keren.
+ Maak veilig verbinding met uw zoek service vanuit on-premises netwerken die verbinding maken met het virtuele netwerk via [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [expressroutes waaraan](../expressroute/expressroute-locations.md) met privé-peering.

> [!NOTE]
> Wanneer het service-eind punt privé is, zijn sommige Portal functies uitgeschakeld. U kunt informatie over het service niveau weer geven en beheren, maar de toegang tot de portal voor het indexeren van gegevens en de verschillende onderdelen in de service, zoals de definities index, Indexer en vaardigheids, is beperkt om veiligheids redenen.

## <a name="request-access"></a>Toegang aanvragen 

Klik op [toegang aanvragen](https://aka.ms/SearchPrivateLinkRequestAccess) om u aan te melden voor deze preview-functie. Het formulier vraagt informatie over u, uw bedrijf en algemene netwerk topologie. Zodra we uw aanvraag hebben gecontroleerd, ontvangt u een bevestigings-e-mail met aanvullende instructies.

## <a name="create-a-vm"></a>Een VM maken
In deze sectie maakt u een virtueel netwerk en een subnet voor het hosten van de virtuele machine die wordt gebruikt om toegang te krijgen tot het privé-eind punt van uw zoek service.

### <a name="set-up-the-virtual-network"></a>Het virtuele netwerk instellen
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer linksboven **een resource maken** > **netwerk** > **virtueel netwerk**.
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


### <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in de linkerbovenhoek van de hoofd pagina van de portal **de optie een resource maken** > **Compute** > **virtuele machine**.

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
    | Openbare poorten voor inkomend verkeer | Laat de standaardwaarde **Geen** staan. |
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


## <a name="create-your-search-service-with-a-private-endpoint"></a>Uw zoek service maken met een persoonlijk eind punt

In deze sectie maakt u een nieuwe Azure Cognitive Search-service met een persoonlijk eind punt. 

1. Selecteer in de linkerbovenhoek van de hoofd pagina van de portal **de optie een resource maken** > **Web** > **Azure Cognitive Search**.

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
1. Zodra de service is gemaakt, bladert u naar de resource die u zojuist hebt gemaakt.
1. Selecteer **sleutels** in het menu links.
1. Kopieer de **primaire beheerders sleutel** voor gebruik in de volgende stap.

 
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

1. Er wordt mogelijk een certificaat waarschuwing weer gegeven wanneer u zich aanmeldt. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  


## <a name="access-the-search-service-privately-from-the-vm"></a>De zoek service privé openen vanuit de VM

In deze sectie controleert u de toegang van particuliere netwerken tot de zoek service en maakt u een persoonlijke verbinding met het opslag account met behulp van het persoonlijke eind punt.

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
1. Volg deze [Snelstartgids](search-get-started-postman.md) van de VM om een nieuwe zoek index in uw service te maken in postman met behulp van de rest API.

1. Probeer enkele van deze dezelfde aanvragen in postman op uw lokale werk station.

1. Als u de Snelstartgids vanaf de VM kunt volt ooien, maar u een fout bericht krijgt dat de externe server niet op uw lokale werk station aanwezig is, hebt u een persoonlijk eind punt geconfigureerd voor uw zoek service.

1. Sluit de verbinding met extern bureau blad met *myVM*. 

## <a name="clean-up-resources"></a>Resources opschonen 

Wanneer u klaar bent met het persoonlijke eind punt, het zoek service account en de virtuele machine, verwijdert u de resource groep en alle resources die deze bevat: 

1. Geef *myResourceGroup* op in het **zoekvak** boven aan de portal en selecteer *myResourceGroup* in de zoek resultaten. 
1. Selecteer **Resourcegroep verwijderen**. 
1. Voer *myResourceGroup* in om **de naam van de resource groep te typen** en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt in een virtueel netwerk en een zoek service met een persoonlijk eind punt. U hebt verbinding gemaakt met de virtuele machine via internet en u kunt deze veilig door gegeven aan de zoek service met behulp van een persoonlijke koppeling. 

> [!div class="nextstepaction"]
> [Wat is een persoonlijk Azure-eind punt?](../private-link/private-endpoint-overview.md).
