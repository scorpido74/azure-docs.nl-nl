---
title: Gedetailleerde probleemoplossing voor extern bureaublad in Azure | Microsoft Documenten
description: Gedetailleerde stappen voor het oplossen van problemen controleren voor externe bureaubladfouten wanneer u niet naar een virtuele Windows-machine in Azure gaan
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: kan geen verbinding maken met extern bureaublad, problemen met extern bureaublad oplossen, extern bureaublad kan geen verbinding maken, externe desktopfouten, problemen met extern bureaublad, problemen met extern bureaublad
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: ea448b87f9e6954abecead2934bfb7f4ed04a9c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920141"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Gedetailleerde stappen voor probleemoplossing voor problemen met Extern bureaublad met Windows-VM's in Azure
In dit artikel vindt u gedetailleerde stappen voor het oplossen van problemen om complexe externe desktopfouten voor op Windows gebaseerde Azure-virtuele machines te diagnosticeren en op te lossen.

> [!IMPORTANT]
> Als u de meest voorkomende fouten op extern bureaublad wilt elimineren, moet u [het basisartikel voor probleemoplossing voor Extern bureaublad](troubleshoot-rdp-connection.md) lezen voordat u verdergaat.

Mogelijk komt u een foutbericht voor extern bureaublad tegen dat niet lijkt op een van de specifieke foutberichten die worden weergegeven in [de basishandleiding voor het oplossen](troubleshoot-rdp-connection.md)van problemen met extern bureaublad. Volg deze stappen om te bepalen waarom de Extern bureaublad-client (RDP) geen verbinding kan maken met de RDP-service op de Azure VM.


Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure Support-site](https://azure.microsoft.com/support/options/) en klik op **Ondersteuning krijgen**. Lees de [veelgestelde vragen](https://azure.microsoft.com/support/faq/)over Microsoft Azure Support voor informatie over het gebruik van Azure Support.

## <a name="components-of-a-remote-desktop-connection"></a>Onderdelen van een verbinding met extern bureaublad
De volgende onderdelen zijn betrokken bij een RDP-verbinding:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Voordat u verdergaat, kan het helpen om mentaal te bekijken wat er is veranderd sinds de laatste succesvolle verbinding met extern bureaublad met de VM. Bijvoorbeeld:

* Het openbare IP-adres van de VM of de cloudservice met de VM (ook wel het virtuele IP-adres [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)genoemd) is gewijzigd. De RDP-fout kan zijn omdat in uw DNS-clientcache nog steeds het *oude IP-adres* is geregistreerd voor de DNS-naam. Spoel uw DNS-clientcache door en probeer de VM opnieuw aan te sluiten. Of probeer direct verbinding te maken met de nieuwe VIP.
* U gebruikt een toepassing van derden om uw Extern bureaublad-verbindingen te beheren in plaats van de verbinding te gebruiken die door de Azure-portal wordt gegenereerd. Controleer of de toepassingsconfiguratie de juiste TCP-poort bevat voor het Extern bureaublad-verkeer. U deze poort controleren op een klassieke virtuele machine in de [Azure-portal](https://portal.azure.com)door te klikken op de instellingen van de VM > Eindpunten.

## <a name="preliminary-steps"></a>Voorbereidende stappen
Voordat u overgaat tot de gedetailleerde probleemoplossing,

* Controleer de status van de virtuele machine in de Azure-portal op voor de hand liggende problemen.
* Volg de [stappen voor snelle oplossing voor veelvoorkomende RDP-fouten in de basishandleiding voor probleemoplossing.](troubleshoot-rdp-connection.md#quick-troubleshooting-steps)
* Voor aangepaste afbeeldingen moet u ervoor zorgen dat uw VHD goed is voorbereid voordat u deze uploadt. Zie [Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure voor](../windows/prepare-for-upload-vhd-image.md)meer informatie.


Probeer na deze stappen opnieuw verbinding te maken met de virtuele machine via Extern bureaublad.

## <a name="detailed-troubleshooting-steps"></a>Gedetailleerde stappen voor het oplossen van problemen
De Extern bureaublad-client kan mogelijk de Extern bureaublad-service op de Azure VM niet bereiken vanwege problemen bij de volgende bronnen:

* [Extern bureaublad-clientcomputer](#source-1-remote-desktop-client-computer)
* [Randapparaat van het intranet van de organisatie](#source-2-organization-intranet-edge-device)
* [Lijst met eindpunten en toegangscontrole voor cloudservices (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Netwerkbeveiligingsgroepen](#source-4-network-security-groups)
* [Windows-gebaseerde Azure VM](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Bron 1: Extern bureaublad-clientcomputer
Controleer of uw computer verbinding met extern bureaublad kan maken met een andere on-premises, Windows-computer.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Als u dat niet, controleert u op de volgende instellingen op uw computer:

* Een lokale firewall-instelling die extern bureaublad-verkeer blokkeert.
* Lokaal geïnstalleerde clientproxysoftware die Extern bureaublad-verbindingen voorkomt.
* Lokaal geïnstalleerde netwerkbewakingssoftware die verbindingen met Extern bureaublad verhindert.
* Andere typen beveiligingssoftware die het verkeer controleren of specifieke typen verkeer toestaan/weigeren die verbindingen op extern bureaublad verhinderen.

Schakel in al deze gevallen de software tijdelijk uit en probeer via Extern bureaublad verbinding te maken met een on-premises computer. Als u op deze manier de werkelijke oorzaak achterhalen, werkt u samen met uw netwerkbeheerder om de software-instellingen te corrigeren om verbindingen met extern bureaublad toe te staan.

## <a name="source-2-organization-intranet-edge-device"></a>Bron 2: Apparaat voor intranetedge van de organisatie
Controleer of een computer die rechtstreeks met internet is verbonden, verbinding met Extern bureaublad kan maken met uw virtuele Azure-machine.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Als u geen computer hebt die rechtstreeks met internet is verbonden, maakt en test u met een nieuwe virtuele Azure-machine in een brongroep of cloudservice. Zie [Een virtuele machine maken met Windows maken in Azure](../virtual-machines-windows-hero-tutorial.md)voor meer informatie. U de virtuele machine en de resourcegroep of de cloudservice na de test verwijderen.

Als u een verbinding met Extern bureaublad maken met een computer die rechtstreeks aan het internet is gekoppeld, controleert u het randapparaat van uw organisatie op het intranet op:

* Een interne firewall die HTTPS-verbindingen met internet blokkeert.
* Een proxyserver die verbindingen met Extern bureaublad voorkomt.
* Inbraakdetectie of netwerkbewakingssoftware die wordt uitgevoerd op apparaten in uw edge-netwerk die verbindingen met extern bureaublad verhindert.

Werk samen met uw netwerkbeheerder om de instellingen van het intranetrandapparaat van uw organisatie te corrigeren om op HTTPS gebaseerde extern bureaublad-verbindingen met internet toe te staan.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Bron 3: Eindpunt van cloudservices en ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Controleer voor VM's die zijn gemaakt met het klassieke implementatiemodel of een andere Azure-vm die zich in dezelfde cloudservice of het virtuele netwerk bevindt, Verbinding met Extern bureaublad kan maken met uw Azure VM.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Ga naar [Bron 4: Netwerkbeveiligingsgroepen](#source-4-network-security-groups)voor virtuele machines die zijn gemaakt in Resource Manager.

Als u geen andere virtuele machine in dezelfde cloudservice of hetvirtuele netwerk hebt, maakt u er een. Volg de stappen in [Een virtuele machine maken met Windows in Azure](../virtual-machines-windows-hero-tutorial.md). Verwijder de virtuele testmachine nadat de test is voltooid.

Als u via Extern bureaublad verbinding maken met een virtuele machine in dezelfde cloudservice of hetvirtuele netwerk, controleert u op de volgende instellingen:

* De eindpuntconfiguratie voor Extern bureaublad-verkeer op de doel-VM: de privé-TCP-poort van het eindpunt moet overeenkomen met de TCP-poort waarop de extern bureaublad-service van de VM luistert (standaard is 3389).
* Met het ACL-eindpunt voor het eindpunt extern bureaublad-verkeer op de doel-VM: ACL's u toegestaan of geweigerd binnenkomend verkeer van internet opgeven op basis van het IP-adres van de bron. Verkeerd geconfigureerde ACL's kunnen binnenkomend Extern bureaublad-verkeer naar het eindpunt voorkomen. Controleer uw ACL's om ervoor te zorgen dat binnenkomend verkeer van uw openbare IP-adressen van uw proxy of andere randserver is toegestaan. Zie [Wat is een Lijst met netwerktoegangsbeheer (ACL)?](../../virtual-network/virtual-networks-acl.md)

Als u wilt controleren of het eindpunt de bron van het probleem is, verwijdert u het huidige eindpunt en maakt u een nieuw eindpunt, waarbij u een willekeurige poort in het bereik 49152-65535 voor het externe poortnummer kiest. Zie [Eindpunten instellen op een virtuele machine voor](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)meer informatie.

## <a name="source-4-network-security-groups"></a>Bron 4: Netwerkbeveiligingsgroepen
Netwerkbeveiligingsgroepen bieden meer gedetailleerde controle over toegestaan inkomend en uitgaand verkeer. U regels maken voor subnetten en cloudservices in een virtueel Azure-netwerk.

Gebruik [IP-stroom controleren](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) om te bevestigen of verkeer naar of van een virtuele machine wordt geblokkeerd door een regel in een netwerkbeveiligingsgroep. U ook effectieve regels voor beveiligingsgroepen controleren om ervoor te zorgen dat de NSG-regel 'Toestaan' bestaat en prioriteit krijgt voor rdp-poort (standaard389). Zie [Effectieve beveiligingsregels gebruiken om de vm-verkeersstroom op te lossen voor](../../virtual-network/diagnose-network-traffic-filter-problem.md)meer informatie .

## <a name="source-5-windows-based-azure-vm"></a>Bron 5: Windows-gebaseerde Azure VM
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Volg de instructies in [dit artikel](../windows/reset-rdp.md). In dit artikel wordt de extern bureaublad-service op de virtuele machine opnieuw ingesteld:

* Schakel de standaardregel 'Extern bureaublad' van Windows Firewall (TCP-poort 3389) in.
* Extern bureaublad-verbindingen inschakelen door de registerwaarde HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections in te stellen op 0.

Probeer de verbinding vanaf uw computer opnieuw. Als u nog steeds geen verbinding maken via Extern bureaublad, controleert u op de volgende mogelijke problemen:

* De Extern bureaublad-service wordt niet uitgevoerd op de doel-VM.
* De Remote Desktop-service luistert niet op TCP-poort 3389.
* Windows Firewall of een andere lokale firewall heeft een uitgaande regel die extern bureaublad-verkeer verhindert.
* Inbraakdetectie of netwerkbewakingssoftware die op de virtuele Azure-machine wordt uitgevoerd, voorkomt verbindingen met extern bureaublad.

Voor VM's die zijn gemaakt met behulp van het klassieke implementatiemodel, u een externe Azure PowerShell-sessie gebruiken voor de virtuele Azure-machine. Eerst moet u een certificaat installeren voor de hostingcloudservice van de virtuele machine. Ga naar [Secure Remote PowerShell Access to Azure Virtual Machines configureren](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) en download het **scriptbestand InstallWinRMCertAzureVM.ps1** naar uw lokale computer.

Installeer vervolgens Azure PowerShell als u dat nog niet hebt gedaan. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview).

Open vervolgens een opdrachtprompt voor Azure PowerShell en wijzig de huidige map in de locatie van het **scriptbestand InstallWinRMCertAzureVM.ps1.** Als u een Azure PowerShell-script wilt uitvoeren, moet u het juiste uitvoeringsbeleid instellen. Voer de opdracht **Uitvoeringbeleid** uitvoeren om uw huidige beleidsniveau te bepalen. Zie [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx)voor informatie over het instellen van het juiste niveau.

Vul vervolgens de naam van uw Azure-abonnement, de naam van de cloudservice en de naam van uw virtuele machine in (het verwijderen van de < en > tekens) en voer deze opdrachten uit.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

U de juiste abonnementsnaam ophalen in de eigenschap *SubscriptionName* van de weergave van de opdracht **Get-AzureSubscription.** U de naam van de cloudservice voor de virtuele machine ophalen in de kolom *ServiceName* in de weergave van de opdracht **Get-AzureVM.**

Controleer of u het nieuwe certificaat hebt. Open een module certificaten voor de huidige gebruiker en kijk in de map **Vertrouwde basiscertificeringsinstanties\Certificaten.** U ziet een certificaat met de DNS-naam van uw cloudservice in de kolom Uitgegeven Aan (voorbeeld: cloudservice4testing.cloudapp.net).

Start vervolgens een externe Azure PowerShell-sessie met behulp van deze opdrachten.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Nadat u geldige beheerdersreferenties hebt ingevoerd, ziet u iets dat lijkt op de volgende Azure PowerShell-prompt:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Het eerste deel van deze prompt is uw naam van de cloudservice die de doel-VM bevat, die kan verschillen van 'cloudservice4testing.cloudapp.net'. U nu Azure PowerShell-opdrachten voor deze cloudservice uitvoeren om de genoemde problemen te onderzoeken en de configuratie te corrigeren.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>De TCP-poort voor Extern bureaublad-services handmatig corrigeren
Voer deze opdracht uit bij de externe Azure PowerShell-sessieprompt.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

De eigenschap PortNumber geeft het huidige poortnummer weer. Wijzig indien nodig het poortnummer van extern bureaublad terug naar de standaardwaarde (3389) met deze opdracht.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Controleer met deze opdracht of de poort is gewijzigd in 3389.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Sluit de externe Azure PowerShell-sessie af met deze opdracht.

```powershell
Exit-PSSession
```

Controleer of het eindpunt voor Extern bureaublad voor de Azure VM ook TCP-poort 3398 als interne poort gebruikt. Start de Azure VM opnieuw en probeer de verbinding extern bureaublad opnieuw.

## <a name="additional-resources"></a>Aanvullende bronnen
[Een wachtwoord of de Extern bureaublad-service voor virtuele Windows-apparaten opnieuw instellen](../windows/reset-rdp.md)

[Azure PowerShell installeren en configureren](/powershell/azure/overview)

[Problemen met Secure Shell-verbindingen (SSH) oplossen met een op Linux gebaseerde Azure-virtuele machine](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Problemen oplossen met toegang tot een toepassing die wordt uitgevoerd op een virtuele Azure-machine](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

