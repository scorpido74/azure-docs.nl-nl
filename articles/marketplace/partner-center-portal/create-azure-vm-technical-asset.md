---
title: Uw technische bedrijfs middelen voor virtuele Azure-machines maken
description: Meer informatie over het maken en configureren van technische assets voor een virtuele machine (VM)-aanbieding voor Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: dannyevers
ms.author: mingshen
ms.date: 04/13/2020
ms.openlocfilehash: aee2016b422dcf447dbae047c8a77ae93bf79ab7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107152"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Uw technische bedrijfs middelen voor virtuele Azure-machines maken

In dit artikel wordt beschreven hoe u technische assets kunt maken en configureren voor een virtuele machine (VM)-aanbieding voor Azure Marketplace. Een VM bevat twee onderdelen: de virtuele harde schijf (VHD) van het besturings systeem en de optionele Vhd's met gekoppelde gegevens schijven:

* **VHD van het besturings systeem** : bevat het besturings systeem en de oplossing die met uw aanbieding wordt geïmplementeerd. Het proces van het voorbereiden van de VHD verschilt, afhankelijk van het feit of het een op Linux gebaseerde, op Windows gebaseerde of op een aangepaste virtuele machine is.
* **Gegevens schijven vhd's** : toegewezen, permanente opslag voor een virtuele machine. Gebruik niet de VHD met het besturings systeem (bijvoorbeeld station C:) om permanente gegevens op te slaan.

Een VM-installatie kopie bevat één besturingssysteem schijf en Maxi maal 16 gegevens schijven. Gebruik één VHD per gegevens schijf, zelfs als de schijf leeg is.

> [!NOTE]
> Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat nodig is voor de oplossing. Klanten kunnen geen schijven verwijderen die deel uitmaken van een installatie kopie op het moment van de implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na de implementatie.

> [!IMPORTANT]
> Elke VM-installatie kopie in een plan moet hetzelfde aantal gegevens schijven hebben.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen. Naast uw oplossings domein moet uw technische team kennis hebben van de volgende micro soft-technologieën:

* Basis informatie over [Azure-Services](https://azure.microsoft.com/services/)
* Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
* Praktische kennis van [Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
* Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Praktische kennis van [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Voorgestelde hulpprogram ma's – optioneel

Overweeg het gebruik van een van de volgende script omgevingen om Vm's en Vhd's te beheren:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-CLI](https://code.visualstudio.com/)

Daarnaast kunt u de volgende hulpprogram ma's toevoegen aan uw ontwikkel omgeving:

* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Bekijk de beschik bare hulpprogram ma's op de pagina [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/product-categories/developer-tools/) en als u Visual Studio, de [Visual Studio Marketplace](https://marketplace.visualstudio.com/), gebruikt.

## <a name="create-a-vm-image-using-an-approved-base"></a>Een VM-installatie kopie maken met behulp van een goedgekeurde basis

> [!NOTE]
> Als u uw technische bedrijfs middelen voor virtuele machines wilt maken met een installatie kopie die u hebt gemaakt op uw eigen locatie, gaat u naar [een virtuele machine maken met uw eigen installatie kopie](#create-a-vm-using-your-own-image).

In deze sectie worden verschillende aspecten van het gebruik van een goedgekeurde basis beschreven, zoals het gebruik van de Remote Desktop Protocol (RDP), het selecteren van een grootte voor de virtuele machine, het installeren van de meest recente Windows-updates en het generaliseren van de VHD-installatie kopie.

De volgende secties richten zich voornamelijk op op Windows gebaseerde Vhd's. Zie [Linux op distributies die zijn goedgekeurd door Azure](../../virtual-machines/linux/endorsed-distros.md)voor meer informatie over het maken van op Linux gebaseerde virtuele harde schijven.

> [!WARNING]
> Volg de richt lijnen in dit onderwerp om Azure te gebruiken voor het maken van een virtuele machine met een vooraf geconfigureerd, goedgekeurd besturings systeem. Als dit niet compatibel is met uw oplossing, is het mogelijk een on-premises VM te maken en te configureren met behulp van een goedgekeurd besturings systeem. U kunt deze vervolgens configureren en voorbereiden voor het uploaden, zoals wordt beschreven in [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md).

### <a name="select-an-approved-base"></a>Selecteer een goedgekeurde basis

Selecteer het Windows-besturings systeem of Linux als basis.

#### <a name="windows"></a>Windows

De VHD van het besturings systeem voor uw VM-installatie kopie op Windows moet gebaseerd zijn op een door Azure goedgekeurde basis installatie kopie die Windows Server of SQL Server bevat. Als u wilt beginnen, maakt u een virtuele machine van een van de volgende installatie kopieën van de Azure Portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 data center](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2012 Data Center](https://www.microsoft.com/cloud-platform/windows-server-pricing), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enter prise, Standard, web)
* [SQL Server 2012 SP2](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance.md) (Enter prise, Standard, web)

> [!NOTE]
> Als u de huidige Azure Portal of Azure PowerShell gebruikt, worden installatie kopieën van Windows Server die zijn gepubliceerd op 8 september 2014 en later goedgekeurd.

#### <a name="linux"></a>Linux

Azure biedt een scala aan goedgekeurde Linux-distributies. Zie voor een actuele lijst [Linux op distributies die zijn goedgekeurd door Azure](../../virtual-machines/linux/endorsed-distros.md).

### <a name="create-vm-in-the-azure-portal"></a>Een virtuele machine maken in de Azure Portal

Volg deze stappen om de basis installatie kopie van de virtuele machine te maken in de [Azure Portal](https://ms.portal.azure.com/):

1. Meld u aan bij de [Azure Portal](https://ms.portal.azure.com/) met de Microsoft-account die is gekoppeld aan het Azure-abonnement dat u wilt gebruiken om uw VM-aanbieding te publiceren.
2. Maak een nieuwe resource groep en geef de locatie van de **resource groep**, het **abonnement**en de **resource groep**op. Zie [resources beheren](../../azure-resource-manager/resource-group-portal.md)voor meer informatie.
3. Selecteer **virtuele machines** aan de linkerkant om de pagina Details van virtuele machines weer te geven.
4. Selecteer **+ toevoegen** om de **ervaring een virtuele machine maken**te openen.
5. Selecteer de installatie kopie in de vervolg keuzelijst of klik op **Bladeren naar alle open bare en persoonlijke installatie kopieën** om de installatie kopieën van alle beschik bare virtuele machines te zoeken of bladeren.
6. Selecteer de grootte van de virtuele machine die u wilt implementeren met behulp van de volgende aanbevelingen:
    * Als u van plan bent om de VHD on-premises te ontwikkelen, is de grootte niet van belang. Overweeg het gebruik van een van de kleinere Vm's.
    * Als u van plan bent de installatie kopie in azure te ontwikkelen, kunt u een van de aanbevolen VM-grootten voor de geselecteerde installatie kopie gebruiken.

7. Vouw in de sectie **schijven** het gedeelte **Geavanceerd** uit en stel de optie **Managed disks gebruiken** in op **Nee**.
8. Geef de overige vereiste gegevens op om de virtuele machine te maken.
9. Selecteer **beoordeling + maken** om uw keuzes te controleren. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. U kunt de voortgang volgen door op het tabblad **virtual machines** aan de linkerkant te klikken. Nadat deze is gemaakt, wordt de status gewijzigd in **actief**.

Als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure, raadpleegt u [algemene problemen tijdens het maken van de VHD (Veelgestelde vragen)](common-issues-during-vhd-creation.md).

### <a name="connect-to-your-azure-vm"></a>Verbinding maken met uw Azure VM

In deze sectie wordt uitgelegd hoe u verbinding maakt met en hoe u zich aanmeldt bij de virtuele machine die u hebt gemaakt in Azure. Nadat u verbinding hebt gemaakt, kunt u met de virtuele machine werken alsof u lokaal bent aangemeld bij de hostserver.

#### <a name="connect-to-a-windows-based-vm"></a>Verbinding maken met een VM op basis van Windows

Gebruik de extern bureau blad-client om verbinding te maken met de op Windows gebaseerde VM die wordt gehost op Azure. De meeste versies van Windows bevatten systeem eigen ondersteuning voor Remote Desktop Protocol (RDP). Voor andere besturings systemen kunt u meer informatie vinden over clients in [extern bureaublad-clients](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

In dit artikel wordt beschreven hoe u de ingebouwde Windows RDP-ondersteuning gebruikt om verbinding te maken met uw VM: [verbinding maken en aanmelden bij een virtuele machine van Azure waarop Windows wordt uitgevoerd](../../virtual-machines/windows/connect-logon.md).

> [!TIP]
> Tijdens het proces kunnen er beveiligings waarschuwingen worden weer gegeven. Bijvoorbeeld: waarschuwingen zoals ' het RDP-bestand is van een onbekende uitgever ' of ' uw gebruikers referenties kunnen niet worden geverifieerd '. Het is veilig om deze waarschuwingen te negeren.

#### <a name="connect-to-a-linux-based-vm"></a>Verbinding maken met een op Linux gebaseerde VM

Als u verbinding wilt maken met een VM op basis van Linux, hebt u een SSH-client (Secure Shell Protocol) nodig. In de volgende stappen wordt de Free [putty](https://www.ssh.com/ssh/putty/) shh-terminal gebruikt.

1. Ga naar de [Azure Portal](https://ms.portal.azure.com/).
2. Zoek en selecteer **virtuele machines**.
3. Selecteer de virtuele machine waarmee u verbinding wilt maken.
4. Start de virtuele machine als deze nog niet wordt uitgevoerd.
5. Selecteer de naam van de virtuele machine om de pagina **overzicht** te openen.
6. Noteer het open bare IP-adres en de DNS-naam van uw virtuele machine (als deze waarden niet zijn ingesteld, moet u [een netwerk interface maken](../../virtual-network/virtual-network-network-interface.md#create-a-network-interface))).
7. Open de PuTTy-toepassing.
8. Voer in het dialoog venster PuTTy-configuratie het IP-adres of de DNS-naam van uw virtuele machine in.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustreert de PuTTy-Terminal instellingen. de vakjes host-naam of IP-adres en poort zijn gemarkeerd.":::

9. Selecteer **openen** om een putty-Terminal te openen.
10. Wanneer u hierom wordt gevraagd, voert u de account naam en het wacht woord in van uw Linux-VM-account.

Als u verbindings problemen hebt, raadpleegt u de documentatie voor uw SSH-client. Bijvoorbeeld [hoofd stuk 10: algemene fout berichten](https://www.ssh.com/ssh/putty/putty-manuals).

Zie [extern bureaublad installeren en configureren om verbinding te maken met een virtuele Linux-machine in azure](../../virtual-machines/linux/use-remote-desktop.md)voor meer informatie, inclusief het toevoegen van een bureau blad aan een ingerichte Linux VM.

## <a name="create-a-vm-using-your-own-image"></a>Een virtuele machine maken met uw eigen installatie kopie

In deze sectie wordt beschreven hoe u een installatie kopie van een door de gebruiker gedefinieerde virtuele machine (VM) maakt en implementeert. U kunt dit doen door VHD-installatie kopieën van besturings systeem en gegevens schijven te voorzien van een door Azure geïmplementeerde virtuele harde schijf (VHD).

> [!NOTE]
> Als u optioneel een goedgekeurde basis installatie kopie wilt gebruiken, volgt u de instructies in [een VM-installatie kopie maken met behulp van een goedgekeurde basis](#create-a-vm-image-using-an-approved-base).

1. Upload uw installatie kopieën naar het Azure Storage-account.
2. Implementeer de VM-installatie kopie.
3. Leg de VM-installatie kopie vast.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Uw installatie kopieën uploaden naar een Azure-opslag account

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Upload de VHD van uw gegeneraliseerde besturings systeem en de Vhd's met gegevens schijven naar uw Azure Storage-account.

### <a name="deploy-your-image"></a>Uw installatie kopie implementeren

Maak uw installatie kopie met behulp van de Azure Portal of Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implementeren met de Azure-portal

1. Op de start pagina selecteert u **een resource maken**, zoekt u naar "sjabloon implementatie" en selecteert u **maken**.
2. Kies **uw eigen sjabloon bouwen in de editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Illustreert de aangepaste implementatie pagina.":::

3. Plak deze [JSON-sjabloon](../partner-center-portal/azure-vm-image-certification.md) in de editor en selecteer **Opslaan**.
4. Geef de parameter waarden op voor de weer gegeven eigenschappen pagina's met **aangepaste implementatie** .

    | Parameter | Beschrijving |
    | ------------ | ------------- |
    | Account naam voor gebruikers opslag | Inhoud van cel 2 |
    | Container naam voor gebruikers opslag | De naam van het opslag account waarin de gegeneraliseerde VHD zich bevindt |
    | DNS-naam voor openbaar IP-adres | De naam van het open bare IP-adres. Definieer de DNS-naam voor het open bare IP-adres in de Azure Portal nadat de aanbieding is geïmplementeerd. |
    | Gebruikers naam voor de beheerder | Gebruikers naam van beheerders account voor nieuwe VM |
    | Beheerderswachtwoord | Wacht woord van beheerders account voor nieuwe VM |
    | Het type besturingssysteem | VM-besturings systeem: Windows of Linux |
    | Abonnements-id | Id van het geselecteerde abonnement |
    | Locatie | Geografische locatie van de implementatie |
    | VM-grootte | [Azure VM-grootte](../../virtual-machines/windows/sizes.md), bijvoorbeeld Standard_A2 |
    | Naam van openbaar IP-adres | Naam van uw open bare IP-adres |
    | VM-naam | De naam van de nieuwe virtuele machine |
    | Virtual Network naam | De naam van het virtuele netwerk dat wordt gebruikt door de virtuele machine |
    | NIC-naam | De naam van de netwerk interface kaart met het virtuele netwerk |
    | URL VOOR VHD | Volledige VHD-URL van de besturingssysteem schijf |
    |  |  |

5. Nadat u deze waarden hebt opgegeven, selecteert u **kopen**.

De implementatie van Azure wordt gestart. Er wordt een nieuwe virtuele machine gemaakt met de opgegeven onbeheerde VHD in het opgegeven pad van het opslag account. U kunt de voortgang van de Azure Portal volgen door **virtual machines** aan de linkerkant van de portal te selecteren. Wanneer de virtuele machine wordt gemaakt, wordt de status gewijzigd van gestart in actief.

#### <a name="deploy-using-azure-powershell"></a>Implementeren met Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>De VM-installatie kopie vastleggen

Gebruik de volgende instructies die overeenkomen met uw benadering:

* Azure PowerShell: [een installatie kopie van een niet-beheerde virtuele machine maken op basis van een Azure-VM](../../virtual-machines/windows/capture-image-resource.md)
* Azure CLI: [een installatie kopie van een virtuele machine of VHD maken](../../virtual-machines/linux/capture-image.md)
* API: [virtual machines-vastleggen](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt verg Roten of verkleinen, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden voor implementatie op Azure Marketplace.

### <a name="sizing-the-vhds"></a>Grootte van de Vhd's aanpassen

Als u een van de virtuele machines hebt geselecteerd die vooraf zijn geconfigureerd met een besturings systeem (en eventueel extra services), hebt u al een standaard Azure VM-grootte gekozen. Het is de aanbevolen methode om uw oplossing te starten met een vooraf geconfigureerd besturings systeem. Als u echter een besturings systeem hand matig installeert, moet u de grootte van uw primaire VHD in uw VM-installatie kopie aanpassen:

* Voor Windows moet de VHD met het besturings systeem worden gemaakt als VHD met een vaste indeling van 127 – 128 GB.
* Voor Linux moet deze VHD worden gemaakt als een VHD met een vaste grootte van 30:50 GB.

Als de fysieke grootte kleiner is dan 127 – 128 GB, moet de VHD uitbreidbaar (Spare/Dynamic) zijn. De basis Windows-en SQL Server-installatie kopieën die worden meegeleverd, zijn al aan deze vereisten voldoen. Wijzig de indeling of de grootte van de VHD dus niet.

Gegevens schijven kunnen Maxi maal 1 TB groot zijn. Houd er bij het bepalen van de grootte rekening mee dat klanten de grootte van Vhd's binnen een installatie kopie niet kunnen wijzigen op het moment van de implementatie. Vhd's met een gegevens schijf moeten worden gemaakt als Vhd's met vaste grootte. Ze moeten ook uitbreidbaar (sparse/Dynamic) zijn. Gegevens schijven kunnen in eerste instantie leeg zijn of gegevens bevatten.

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

De basis installatie kopieën van virtuele machines van het besturings systeem moeten de meest recente updates bevatten tot de publicatie datum. Voordat u de VHD van het besturings systeem publiceert die u hebt gemaakt, moet u ervoor zorgen dat u de besturings systemen en alle geïnstalleerde services bijwerkt met alle nieuwste patches voor beveiliging en onderhoud.

Voor Windows Server voert u de opdracht **controleren op updates** uit.

Voor Linux-distributies worden updates meestal gedownload en geïnstalleerd via een opdracht regel programma of een grafisch hulp programma. Ubuntu Linux biedt bijvoorbeeld de opdracht [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) en het hulp programma [Update beheer](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) voor het bijwerken van het besturings systeem.

### <a name="perform-additional-security-checks"></a>Aanvullende beveiligings controles uitvoeren

Behoud een hoog beveiligings niveau voor uw oplossings installatie kopieën in de Azure Marketplace. Het volgende artikel bevat een controle lijst met beveiligings configuraties en-procedures om u te helpen: [beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace](../../security/security-recommendations-azure-marketplace-images.md). Sommige van deze aanbevelingen zijn specifiek voor installatie kopieën op basis van Linux, maar zijn het meest van toepassing op elke VM-installatie kopie.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie en geplande taken uitvoeren

Als er aanvullende configuratie nodig is, gebruikt u een geplande taak die wordt uitgevoerd bij het opstarten om alle laatste wijzigingen in de virtuele machine door te voeren nadat deze is geïmplementeerd. Houd ook rekening met de volgende aanbevelingen:

* Als het een Run-Once-taak is, moet de taak zichzelf verwijderen nadat deze is voltooid.
* Configuraties mogen niet afhankelijk zijn van C of D, omdat alleen deze twee schijven altijd gegarandeerd bestaan (station C is de schijf met het besturings systeem en station D is de tijdelijke lokale schijf).

Zie [extensies en functies van virtuele machines voor Linux](../../virtual-machines/extensions/features-linux.md)voor meer informatie over Linux-aanpassingen.

## <a name="generalize-the-image"></a>De installatie kopie generaliseren

Alle installatie kopieën in azure Marketplace moeten op een algemene manier opnieuw worden gebruikt. Om dit te verhelpen, moet de VHD van het besturings systeem worden gegeneraliseerd, een bewerking waarbij alle instantie-specifieke id's en software stuur Programma's van een virtuele machine worden verwijderd.

### <a name="windows"></a>Windows

Windows-besturingssysteem schijven worden gegeneraliseerd met het [hulp programma Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Als u het besturings systeem vervolgens bijwerkt of opnieuw configureert, moet u Sysprep opnieuw uitvoeren.

> [!WARNING]
> Omdat updates mogelijk automatisch worden uitgevoerd, moet u na het uitvoeren van Sysprep de virtuele machine uitschakelen totdat deze is geïmplementeerd. Als u dit afsluit, voor komt u dat latere updates exemplaren van specifieke wijzigingen aanbrengen in het besturings systeem of de geïnstalleerde services. Zie [stappen voor het generaliseren van een VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)voor meer informatie over het uitvoeren van Sysprep.

### <a name="linux"></a>Linux

Het volgende proces generaliseert een Linux-VM en implementeert deze opnieuw als een afzonderlijke VM. Zie [een installatie kopie van een virtuele machine of VHD maken](../../virtual-machines/linux/capture-image.md)voor meer informatie. U kunt stoppen wanneer u het gedeelte ' een virtuele machine maken van de vastgelegde installatie kopie ' bereikt.

1. **De Azure Linux-agent verwijderen**

    1. Maak verbinding met uw virtuele Linux-machine met behulp van een SSH-client.
    2. Voer in het SSH-venster de volgende opdracht in: `sudo waagent -deprovision+user` .
    3. Typ **j** om door te gaan (u kunt de para meter **-Force** toevoegen aan de vorige opdracht om te voor komen dat de bevestigings stap wordt uitgevoerd).
    d. Nadat de opdracht is voltooid, typt u **Exit** om de SSH-client te sluiten.

2. **Virtuele machine stoppen**

    1. Selecteer in de Azure Portal uw resource groep (RG) en de virtuele machine opnieuw toe te wijzen.
    2. Uw VHD is nu gegeneraliseerd en u kunt een nieuwe virtuele machine maken met behulp van deze VHD.

## <a name="next-steps"></a>Volgende stappen

Als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure, raadpleegt u [algemene problemen tijdens het maken](common-issues-during-vhd-creation.md)van de VHD.

Anders:

* Een [certificerings instantie voor uw VM-installatie kopie](get-sas-uri.md) wordt uitgelegd hoe u een VM-installatie kopie kunt testen en verzenden voor Azure Marketplace-certificering, met inbegrip van de locatie waar u het *certificerings test hulpprogramma voor het hulp programma voor Azure Certified* kunt gebruiken en hoe dit kan worden gebruikt om
