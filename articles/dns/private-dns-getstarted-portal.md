---
title: Snelstart - Een Azure private DNS-zone maken met de Azure-portal
description: In deze quickstart maakt en test u een privé-DNS-zone en neemt u op in Azure DNS. Dit is een stapsgewijze handleiding voor het maken en beheren van uw eerste privé-DNS-zone en opnemen met behulp van de Azure-portal.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: rohink
ms.openlocfilehash: 52bf9e061eb57c7ce6ea698b7468b5ba5e11b4e8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78244988"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Snelstart: een Azure private DNS-zone maken met de Azure-portal

Deze quickstart leidt u door de stappen om uw eerste privé-DNS-zone te maken en op te nemen met behulp van de Azure-portal.

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Als u een privé-DNS-zone wilt publiceren naar uw virtuele netwerk, geeft u de lijst op met virtuele netwerken die zijn toegestaan om records in de zone om te zetten.  Deze worden *gekoppelde* virtuele netwerken genoemd. Wanneer automatische registratie is ingeschakeld, werkt Azure DNS ook de zonerecords bij wanneer een virtuele machine wordt gemaakt, het IP-adres ervan wijzigt of wordt verwijderd.

In deze snelstart leert u de volgende zaken:

> [!div class="checklist"]
> * Een privé-DNS-zone maken
> * Een virtueel netwerk maken
> * Het virtuele netwerk koppelen
> * Virtuele testmachines maken
> * Een extra DNS-record maken
> * De privézone testen

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

Als u dat liever hebt, u deze quickstart voltooien met [Azure PowerShell](private-dns-getstarted-powershell.md) of [Azure CLI.](private-dns-getstarted-cli.md)

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

In het volgende voorbeeld wordt een DNS-zone **private.contoso.com** in een resourcegroep genaamd **MyAzureResourceGroup**.

Een DNS-zone bevat de DNS-vermeldingen voor een domein. Als u uw domein wilt hosten in Azure DNS, maakt u een DNS-zone voor deze domeinnaam.

![Zoeken naar privé-DNS-zones](media/private-dns-portal/search-private-dns.png)

1. Typ op de zoekbalk van de portal **privé-dns-zones** in het tekstvak zoeken en druk op **Enter**.
1. Selecteer **Privé DNS-zone**.
2. Selecteer **Privé-dns-zone maken**.

1. Typ of selecteer op de pagina **Privé-DNS-zone maken** de volgende waarden:

   - **Resourcegroep**: Selecteer **Nieuw maken,** voer *MyAzureResourceGroup*in en selecteer **OK**. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement.
   -  **Naam**: Type *private.contoso.com* voor dit voorbeeld.
1. Selecteer West Central **US**voor **locatie resourcegroep**.

1. Selecteer **Controleren + maken**.

1. Selecteer **Maken**.

Het maken van de zone kan een paar minuten duren.

## <a name="virtual-network-and-parameters"></a>Virtueel netwerk en parameters

In deze sectie moet u de volgende parameters in de stappen vervangen door de onderstaande informatie:

| Parameter                   | Waarde                |
|-----------------------------|----------------------|
| **\<resourcegroepnaam>**  | MyAzureResourceGroup (Bestaande resourcegroep selecteren) |
| **\<>van de naam van het virtuele netwerk** | MyAzureVNet          |
| **\<regionaam>**          | VS - west-centraal      |
| **\<IPv4-adresruimte>**   | 10.2.0.0\16          |
| **\<subnetnaam>**          | MyAzureSubnet        |
| **\<subnet-adresbereik>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

Als u de privé-DNS-zone wilt koppelen aan een virtueel netwerk, maakt u een virtuele netwerkkoppeling.

![Virtuele netwerkkoppeling toevoegen](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Open de resourcegroep **MyAzureResourceGroup** en selecteer de **private.contoso.com** privézone.
2. Selecteer **virtuele netwerkkoppelingen**in het linkerdeelvenster .
3. Selecteer **Toevoegen**.
4. Typ **myLink** voor de **naam Koppeling**.
5. Selecteer **myAzureVNet**voor **virtueel netwerk**.
6. Schakel het selectievakje **Automatisch registreren inschakelen** in.
7. Selecteer **OK**.

## <a name="create-the-test-virtual-machines"></a>De virtuele testmachines maken

Maak nu twee virtuele machines, zodat u uw DNS-privézone kunt testen:

1. Selecteer linksboven op de portalpagina **Een resource maken**en selecteer vervolgens Windows Server **2016 Datacenter**.
1. Selecteer **MyAzureResourceGroup** voor de resourcegroep.
1. Typ **myVM01** - voor de naam van de virtuele machine.
1. Selecteer **West Central US** voor de **regio**.
1. Voer een naam in voor de gebruikersnaam van de beheerder.
2. Voer een wachtwoord in en bevestig het wachtwoord.
5. Selecteer Voor **Openbare binnenkomende poorten**de optie **Geselecteerde poorten toestaan**en selecteer vervolgens **RDP (3389)** voor **Binnenkomende poorten selecteren**.
10. Accepteer de andere standaardinstellingen voor de pagina en klik op **Volgende: Schijven >**.
11. Accepteer de standaardinstellingen op de pagina **Schijven** en klik vervolgens op **Volgende: >netwerken **.
1. Zorg ervoor dat **myAzureVNet** is geselecteerd voor het virtuele netwerk.
1. Accepteer de andere standaardinstellingen voor de pagina en klik op **Volgende: beheer >**.
2. Selecteer **Voor Diagnostische gegevens,** selecteer **Uit,** accepteer de andere standaardinstellingen en selecteer **Vervolgens Controleren + maken**.
1. Controleer de instellingen en klik op **Maken**.

Herhaal deze stappen en maak een andere virtuele machine met de naam **myVM02**.

Het duurt een paar minuten voor beide virtuele machines te voltooien.

## <a name="create-an-additional-dns-record"></a>Een extra DNS-record maken

 In het volgende voorbeeld wordt een record met de relatieve naam **db** in de DNS-zone **private.contoso.com**, in resourcegroep **MyAzureResourceGroup**. De volledig gekwalificeerde naam van het record is **db.private.contoso.com**. Het recordtype is "A", met het IP-adres van **myVM01**.

1. Open de resourcegroep **MyAzureResourceGroup** en selecteer de **private.contoso.com** privézone.
2. Selecteer **Recordset toevoegen**.
3. Typ **db voor Naam**. **db**
4. Typ voor **IP-adres**het IP-adres dat u voor **myVM01**ziet. Dit moet automatisch worden geregistreerd wanneer de virtuele machine is gestart.
5. Selecteer **OK**.

## <a name="test-the-private-zone"></a>De privézone testen

Nu u de naamresolutie testen voor uw **private.contoso.com** privézone.

### <a name="configure-vms-to-allow-inbound-icmp"></a>VM’s configureren voor het toestaan van inkomende ICMP

U kunt de pingopdracht gebruiken voor het testen van naamomzetting. Configureer de firewall dus op beide virtuele machines om inkomende ICMP-pakketten toe te staan.

1. Maak verbinding met myVM01 en open een Windows PowerShell-venster met beheerdersbevoegdheden.
2. Voer de volgende opdracht uit:

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

Herhaal voor myVM02.

### <a name="ping-the-vms-by-name"></a>De VM's pingen op naam

1. Ping vanuit de myVM02 Windows PowerShell-opdrachtprompt myVM01 met de naam van de automatisch geregistreerde hostnaam:
   ```
   ping myVM01.private.contoso.com
   ```
   De uitvoer ziet er ongeveer als volgt uit:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Ping nu de **db**-naam die u eerder hebt gemaakt:
   ```
   ping db.private.contoso.com
   ```
   De uitvoer ziet er ongeveer als volgt uit:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Alle resources verwijderen

Wanneer u niet langer nodig bent, verwijdert u de resourcegroep **MyAzureResourceGroup** om de bronnen te verwijderen die in deze snelstart zijn gemaakt.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Scenario's voor Azure DNS-privézones](private-dns-scenarios.md)

