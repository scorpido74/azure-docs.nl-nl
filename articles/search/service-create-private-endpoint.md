---
title: Een privéeindpunt maken voor een beveiligde verbinding
titleSuffix: Azure Cognitive Search
description: Een privéeindpunt instellen in een virtueel netwerk voor een beveiligde verbinding met een Azure Cognitive Search-service
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 2664b1abd4131cf1dca186c7b044e338bf1efa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945829"
---
# <a name="create-a-private-endpoint-for-a-secure-connection-to-azure-cognitive-search-preview"></a>Een privéeindpunt maken voor een beveiligde verbinding met Azure Cognitive Search (Voorbeeld)

Gebruik in dit artikel de portal om een nieuw Azure Cognitive Search-serviceexemplaar te maken dat niet toegankelijk is via een openbaar IP-adres. Configureer vervolgens een virtuele Azure-machine in hetzelfde virtuele netwerk en gebruik deze om toegang te krijgen tot de zoekservice via een privéeindpunt.

> [!Important]
> Ondersteuning voor privéeindpuntvoor Azure Cognitive Search is [op aanvraag](https://aka.ms/SearchPrivateLinkRequestAccess) beschikbaar als voorbeeld voor beperkte toegang. Preview-functies worden geleverd zonder overeenkomst op serviceniveau en worden niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. 
>
> Zodra u toegang hebt gekregen tot de preview, u privéeindpunten voor uw service configureren met behulp van de Azure-portal of de [Management REST API-versie 2019-10-06-Preview](https://docs.microsoft.com/rest/api/searchmanagement/).
>   

## <a name="why-use-private-endpoint-for-secure-access"></a>Waarom private endpoint gebruiken voor beveiligde toegang?

[Met privéeindpunten](../private-link/private-endpoint-overview.md) voor Azure Cognitive Search kan een client in een virtueel netwerk veilig toegang krijgen tot gegevens in een zoekindex via een [privékoppeling.](../private-link/private-link-overview.md) Het privéeindpunt gebruikt een IP-adres van de [virtuele netwerkadresruimte](../virtual-network/virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) voor uw zoekservice. Netwerkverkeer tussen de client en de zoekservice loopt via het virtuele netwerk en een privékoppeling op het Microsoft-backbonenetwerk, waardoor blootstelling van het openbare internet wordt geëlimineerd. Voor een lijst met andere PaaS-services die Private Link ondersteunen, raadpleegt u de [beschikbaarheidssectie](../private-link/private-link-overview.md#availability) in de productdocumentatie.

Met privéeindpunten voor uw zoekservice u:

- Blokkeer alle verbindingen op het openbare eindpunt voor uw zoekservice.
- Verhoog de beveiliging van het virtuele netwerk door exfiltratie van gegevens uit het virtuele netwerk te blokkeren.
- Maak veilig verbinding met uw zoekservice via on-premises netwerken die verbinding maken met het virtuele netwerk via [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoutes](../expressroute/expressroute-locations.md) met private-peering.

> [!NOTE]
> Er zijn momenteel enkele beperkingen in de preview die u moet weten van:
> * Alleen beschikbaar voor zoekservices op de **basislaag.** 
> * Beschikbaar in de regio West US 2, West Central US, East US, South Central US, Australia East en Australia Southeast.
> * Wanneer het serviceeindpunt privé is, zijn sommige portalfuncties uitgeschakeld. U informatie op serviceniveau bekijken en beheren, maar de toegang tot de portal tot indexgegevens en de verschillende onderdelen in de service, zoals de index-, indexer- en skillsetdefinities, is om veiligheidsredenen beperkt.
> * Wanneer het serviceeindpunt privé is, moet u de [API Voor zoekrest](https://docs.microsoft.com/rest/api/searchservice/) gebruiken om documenten naar de index te uploaden.
> * U moet de volgende koppeling gebruiken om de optie voor ondersteuning van privéeindpunten in de Azure-portal te bekijken:https://portal.azure.com/?feature.enablePrivateEndpoints=true



## <a name="request-access"></a>Toegang aanvragen 

Klik [op toegang tot aanvragen](https://aka.ms/SearchPrivateLinkRequestAccess) om u aan te melden voor deze voorbeeldfunctie. Het formulier vraagt om informatie over u, uw bedrijf en algemene netwerktopologie. Zodra we je verzoek hebben beoordeeld, ontvang je een bevestigingsmail met aanvullende instructies.

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en subnet om de VM te hosten die wordt gebruikt om toegang te krijgen tot het privéeindpunt van uw zoekservice.

1. Selecteer op het tabblad Start van de Azure-portal de optie Een virtueel**Networking** > **netwerknetwerk voor** **bronnen** > maken .

1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | *MijnVirtueelnetwerk invoeren* |
    | Adresruimte | Voer *10.1.0.0/16 in* |
    | Abonnement | Selecteer uw abonnement|
    | Resourcegroep | Selecteer **Nieuw maken,** *mijnResourcegroep*invoeren en selecteer **OK** |
    | Locatie | Selecteer **West US** of welke regio u ook gebruikt|
    | Subnet - Naam | *MijnSubnet* invoeren |
    | Subnet - adresbereik | Voer *10.1.0.0/24 in* |
    |||

1. Laat de rest als standaard en selecteer **Maken**.


## <a name="create-a-search-service-with-a-private-endpoint"></a>Een zoekservice maken met een privéeindpunt

In deze sectie maakt u een nieuwe Azure Cognitive Search-service met een privéeindpunt. 

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een bron** > **web** > **Azure Cognitive Search maken**.

1. Voer **in De nieuwe zoekservice - Basisbeginselen**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | **EXEMPLAARDETAILS** |  |
    | URL | Voer een unieke naam in. |
    | Locatie | Selecteer het gebied dat u hebt opgegeven bij het aanvragen van toegang tot deze voorbeeldfunctie. |
    | Prijscategorie | Selecteer **Prijscategorie wijzigen** en kies **Basic**. Deze laag is vereist voor de preview. |
    |||
  
1. Selecteer **Volgende: Schaal**.

1. Laat de waarden als standaard staan en selecteer **Volgende: Netwerken**.

1. Selecteer **Privé** voor **Endpoint-connectiviteit(gegevens)** in **Nieuwe zoekservice - Netwerken.**

1. Selecteer in **nieuwe zoekservice - Netwerken**de optie + **Toevoegen** onder **Privéeindpunt**. 

1. Voer in **Privéeindpunt maken**deze gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.|
    | Locatie | Selecteer **VS - west**.|
    | Name | Voer *mijnPrivéEindpunt in*.  |
    | Subresource target | Verlaat de **standaardsearchService**. |
    | **Networking** |  |
    | Virtueel netwerk  | Selecteer *MyVirtualNetwork* uit resourcegroep *myResourceGroup*. |
    | Subnet | Selecteer *mySubnet*. |
    | **INTEGRATIE VAN PRIVÉ-DNS** |  |
    | Integreren met private DNS-zone  | Laat de standaard **ja .** |
    | Privé-DNS-zone  | Laat de standaard ** (Nieuw) privatelink.search.windows.net**. |
    |||

1. Selecteer **OK**. 

1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert. 

1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**. 

1. Zodra de inrichting van uw nieuwe service is voltooid, bladert u naar de bron die u zojuist hebt gemaakt.

1. Selecteer **Toetsen** in het linkerinhoudsmenu.

1. Kopieer de **primaire beheersleutel** voor later, wanneer u verbinding maakt met de service.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer linksboven in het scherm in de Azure-portal de optie **Een resource** > **Compute** > **Virtual-machine maken**.

1. Typ of selecteer in **Een virtuele machine maken - Basisprincipes** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | **PROJECTGEGEVENS** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Selecteer **myResourceGroup**. U hebt dit in de vorige sectie gemaakt.  |
    | **EXEMPLAARDETAILS** |  |
    | Naam van de virtuele machine | Voer *myVm*in . |
    | Regio | Selecteer **West US** of welke regio u ook gebruikt. |
    | Beschikbaarheidsopties | Laat de standaardwaarde **Geen infrastructuurredundantie vereist** staan. |
    | Installatiekopie | Selecteer **Windows Server 2019-datacenter**. |
    | Grootte | Laat de standaardwaarde **Standard DS1 v2** staan. |
    | **ADMINISTRATOR-ACCOUNT** |  |
    | Gebruikersnaam | Voer een gebruikersnaam van uw keuze in. |
    | Wachtwoord | Voer een wachtwoord naar keuze in. Het wachtwoord moet minstens 12 tekens lang zijn en moet voldoen aan de [gedefinieerde complexiteitsvereisten](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in. |
    | **REGELS VOOR BINNENKOMENDE POORT** |  |
    | Openbare poorten voor inkomend verkeer | Verlaat de standaard **geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Laat de standaard **RDP (3389)**. |
    | **GELD BESPAREN** |  |
    | Hebt u al een Windows-licentie? | Laat de standaardwaarde **Nee** staan. |
    |||

1. Selecteer **Volgende: Schijven**.

1. In **Een virtuele machine maken - Schijven**laat u de standaardinstellingen achter en selecteer **Volgende: Netwerken**.

1. Selecteer in **Een virtuele machine maken - Netwerken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Virtueel netwerk | Laat de standaard **MyVirtualNetwork**.  |
    | Adresruimte | Laat de **standaardwaarde 10.1.0.0/24 .**|
    | Subnet | Laat het standaard **mySubnet (10.1.0.0/24)** achter .|
    | Openbare IP | Handhaaf de standaardinstelling **(new) myVm-ip**. |
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **HTTP** en **RDP**.|
    ||

1. Selecteer **Controleren + maken**. U wordt naar de **pagina Controleren + maken geleid,** waar Azure uw configuratie valideert.

1. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**. 


## <a name="connect-to-the-vm"></a>Verbinding maken met de virtuele machine

Download en maak vervolgens als volgt verbinding met de VM *myVm:*

1. Voer *myVm*in in de zoekbalk van het portaal.

1. Selecteer de knop **Verbinding maken**. Na het selecteren van de knop **Verbinden** wordt **Verbinden met virtuele machine** geopend.

1. Selecteer **RDP-bestand downloaden**. In Azure wordt een *RDP*-bestand (Remote Desktop Protocol) gemaakt en het bestand wordt gedownload naar de computer.

1. Open het bestand downloaded.rdp*.

    1. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

    1. Voer de gebruikersnaam en het wachtwoord in dat u hebt opgegeven bij het maken van de vm.

        > [!NOTE]
        > Mogelijk moet u **Meer opties** > selecteren**Gebruik een ander account**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt.

1. Selecteer **OK**.

1. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Als er een certificaatwaarschuwing wordt weergegeven, selecteert u **Ja** of **Doorgaan**.

1. Wanneer het VM-bureaublad wordt weergegeven, minimaliseert u het om terug te gaan naar het lokale bureaublad.  


## <a name="test-connections"></a>Testverbindingen

In deze sectie controleert u de toegang van privénetwerken tot de zoekservice en maakt u privé verbinding met het privéeindpunt.

Terugroepen uit de inleiding dat alle interacties met de zoekservice de [Search REST API](https://docs.microsoft.com/rest/api/searchservice/)vereisen. De portal en .NET SDK worden niet ondersteund in deze preview.

1. Open PowerShell in het extern bureaublad van *myVM.*

1. Voer 'nslookup [zoekservicenaam].search.windows.net' in

    Je ontvangt een bericht dat vergelijkbaar is met dit:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    [search service name].privatelink.search.windows.net
    Address:  10.0.0.5
    Aliases:  [search service name].search.windows.net
    ```

1. Maak vanaf de VM verbinding met de zoekservice en maak een index. U deze [quickstart](search-get-started-postman.md) volgen om een nieuwe zoekindex in uw service in Postman te maken met behulp van de REST API. Voor het instellen van aanvragen van Postman is het eindpunt van de zoekservice (https://[zoekservicenaam].search.windows.net) en de beheer-api-toets die u in een vorige stap hebt gekopieerd, vereist.

1. Het voltooien van de quickstart van de VM is uw bevestiging dat de service volledig operationeel is.

1. Sluit de verbinding met extern bureaublad met *myVM*. 

1. Als u wilt controleren of uw service niet toegankelijk is op een openbaar eindpunt, opent u Postman op uw lokale werkstation en probeert u de eerste verschillende taken in de quickstart. Als er een fout optreedt dat de externe server niet bestaat, hebt u een privéeindpunt voor uw zoekservice geconfigureerd.

## <a name="clean-up-resources"></a>Resources opschonen 
Wanneer u klaar bent met het privéeindpunt, de zoekservice en de vm, verwijdert u de brongroep en alle bronnen die deze bevat:
1. Typ *myResourceGroup* in het vak **Zoeken** boven aan de portal en selecteer *myResourceGroup* in de zoekresultaten. 
1. Selecteer **Resourcegroep verwijderen**. 
1. Typ *myResourceGroup* voor **TYPE DE NAAM VAN DE BRONGROEP** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een VM gemaakt voor een virtueel netwerk en een zoekservice met een privéeindpunt. U hebt vanaf het internet verbinding gemaakt met de VM en veilig gecommuniceerd met de zoekservice via Private Link. Zie [Wat is Azure Private Endpoint voor](../private-link/private-endpoint-overview.md)meer informatie over privéeindpunt?
