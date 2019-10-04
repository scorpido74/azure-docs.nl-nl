---
title: 'Quick Start: een persoonlijke DNS-zone van Azure maken met behulp van de Azure Portal'
description: In deze Quick Start maakt en test u een privé-DNS-zone en-record in Azure DNS. Dit is een stapsgewijze hand leiding voor het maken en beheren van uw eerste privé-DNS-zone en-record met behulp van de Azure Portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: victorh
ms.openlocfilehash: 79b626ef6beaad3a8f15da08b882fc7d596540b0
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960387"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>Quickstart: Een privé-DNS-zone met Azure maken met behulp van de Azure Portal

In deze Snelstartgids wordt u begeleid bij de stappen voor het maken van uw eerste privé-DNS-zone en-record met behulp van de Azure Portal.

Een DNS-zone wordt gebruikt om de DNS-records voor een bepaald domein te hosten. Als u uw domein wilt hosten in Azure DNS, moet u een DNS-zone maken voor die domeinnaam. Alle DNS-records voor uw domein worden vervolgens gemaakt binnen deze DNS-zone. Als u een privé-DNS-zone wilt publiceren naar uw virtuele netwerk, geeft u de lijst op met virtuele netwerken die zijn toegestaan om records in de zone om te zetten.  Deze worden *gekoppelde* virtuele netwerken genoemd. Als automatische registratie is ingeschakeld, werkt Azure DNS ook de zone records bij wanneer een virtuele machine wordt gemaakt, wordt het IP-adres gewijzigd of wordt dit verwijderd.

In deze snelstart leert u de volgende zaken:

> [!div class="checklist"]
> * Een privé-DNS-zone maken
> * Een virtueel netwerk maken
> * Het virtuele netwerk koppelen
> * Virtuele testmachines maken
> * Een extra DNS-record maken
> * De privézone testen

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u wilt, kunt u deze Snelstartgids volt ooien met behulp van [Azure PowerShell](private-dns-getstarted-powershell.md) of [Azure cli](private-dns-getstarted-cli.md).

## <a name="create-a-private-dns-zone"></a>Een privé-DNS-zone maken

In het volgende voor beeld wordt een DNS-zone met de naam **Private.contoso.com** gemaakt in een resource groep met de naam **MyAzureResourceGroup**.

Een DNS-zone bevat de DNS-vermeldingen voor een domein. Als u uw domein wilt hosten in Azure DNS, maakt u een DNS-zone voor deze domeinnaam.

![Privé-DNS zones zoeken](media/private-dns-portal/search-private-dns.png)

1. Typ **privé-DNS-zones** in het zoekvak op de portal zoek balk en druk op **Enter**.
1. Selecteer **privé-DNS zone**.
2. Selecteer **privé-DNS-zone maken**.

1. Typ of Selecteer op de pagina **privé-DNS zone maken** de volgende waarden:

   - **Resourcegroep**: Selecteer **nieuwe maken**, Voer *MyAzureResourceGroup*in en selecteer **OK**. De naam van de resourcegroep moet uniek zijn binnen het Azure-abonnement. 
   -  **Naam**: Typ *Private.contoso.com* voor dit voor beeld.
1. Selecteer voor de locatie van de **resource groep**de optie **West-Centraal VS**.

1. Selecteer **Controleren + maken**.

1. Selecteer **Maken**.

Het maken van de zone kan een paar minuten duren.

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

1. Selecteer op de pagina Portal linksboven **een resource maken**en vervolgens **netwerken**, en selecteer vervolgens **virtueel netwerk**.
2. Typ **myAzureVNet**voor **naam**.
3. Selecteer voor **resource groep**de optie **MyAzureResourceGroup**.
4. Selecteer bij **locatie**de optie **West-Centraal VS**.
5. Accepteer de andere standaard waarden en selecteer **maken**.

## <a name="link-the-virtual-network"></a>Het virtuele netwerk koppelen

Als u de privé-DNS-zone aan een virtueel netwerk wilt koppelen, maakt u een koppeling met een virtueel netwerk.

![Virtuele netwerkkoppeling toevoegen](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Open de resource groep **MyAzureResourceGroup** en selecteer de privé zone **Private.contoso.com** .
2. Selecteer **virtuele netwerk koppelingen**in het linkerdeel venster.
3. Selecteer **Toevoegen**.
4. Typ **myLink** voor de **naam**van de koppeling.
5. Selecteer voor **virtueel netwerk** **myAzureVNet**.
6. Schakel het selectie vakje **automatische registratie inschakelen** in.
7. Selecteer **OK**.

## <a name="create-the-test-virtual-machines"></a>De virtuele testmachines maken

Maak nu twee virtuele machines, zodat u uw DNS-privézone kunt testen:

1. Selecteer op de pagina Portal linksboven **een resource maken**en selecteer vervolgens **Windows Server 2016 Data Center**.
1. Selecteer **MyAzureResourceGroup** voor de resource groep.
1. Typ **myVM01** -voor de naam van de virtuele machine.
1. Selecteer **West-Centraal VS** voor de **regio**.
1. Typ **azureadmin** voor de gebruikers naam van de beheerder.
2. Typ **Azure12345678** voor het wacht woord en bevestig het wacht woord.

5. Voor **open bare binnenkomende poorten**selecteert u **geselecteerde poorten toestaan**en vervolgens **RDP (3389)** selecteren voor **binnenkomende poorten selecteren**.
10. Accepteer de andere standaard waarden voor de pagina en klik vervolgens op **Next: Schijven >** .
11. Accepteer de standaard instellingen op de pagina **schijven** en klik vervolgens op **Next: Netwerk >** .
1. Zorg ervoor dat **myAzureVNet** is geselecteerd voor het virtuele netwerk.
1. Accepteer de andere standaard waarden voor de pagina en klik vervolgens op **Next: Beheer >** .
2. Voor **Diagnostische gegevens over opstarten**selecteert u **uit**, accepteert u de andere standaard waarden en selecteert u vervolgens **bekijken + maken**.
1. Controleer de instellingen en klik vervolgens op **maken**.

Herhaal deze stappen en maak een andere virtuele machine met de naam **myVM02**.

Het duurt enkele minuten voordat de virtuele machines zijn voltooid.

## <a name="create-an-additional-dns-record"></a>Een extra DNS-record maken

 In het volgende voor beeld wordt een record gemaakt met de relatieve naam **db** in de DNS-zone **Private.contoso.com**in de resource groep **MyAzureResourceGroup**. De volledig gekwalificeerde naam van de recordset is **db.private.contoso.com**. Het record type is ' A ', met het IP-adres van **myVM01**.

1. Open de resource groep **MyAzureResourceGroup** en selecteer de privé zone **Private.contoso.com** .
2. Selecteer **Recordset toevoegen**.
3. Typ **db**bij **naam**.
4. Voor **IP-adres**, typt u het IP-adres dat u voor **myVM01**ziet. Dit moet automatisch worden geregistreerd wanneer de virtuele machine is gestart.
5. Selecteer **OK**.

## <a name="test-the-private-zone"></a>De privézone testen

Nu kunt u de naam omzetting voor uw persoonlijke **Private.contoso.com** -zone testen.

### <a name="configure-vms-to-allow-inbound-icmp"></a>VM’s configureren voor het toestaan van inkomende ICMP

U kunt de pingopdracht gebruiken voor het testen van naamomzetting. Configureer de firewall dus op beide virtuele machines om inkomende ICMP-pakketten toe te staan.

1. Maak verbinding met myVM01 en open een Windows PowerShell-venster met beheerdersbevoegdheden.
2. Voer de volgende opdracht uit:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

Als u deze niet meer nodig hebt, verwijdert u de resource groep **MyAzureResourceGroup** om de resources te verwijderen die u in deze Quick Start hebt gemaakt.


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure DNS Private Zones scenario's](private-dns-scenarios.md)

