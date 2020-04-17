---
title: Uw technische assets van Azure Virtual Machine maken
description: Meer informatie over het maken en configureren van technische elementen voor een vm-aanbieding voor virtuele machines voor Azure Marketplace.
author: dannyevers
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 46c59e8b4e60fbe17887ea0fc375b6da758ebf50
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457396"
---
# <a name="create-your-azure-virtual-machine-technical-assets"></a>Uw technische assets van Azure Virtual Machine maken

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Virtual Machine-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [Technische activa maken voor een virtuele machineaanbieding voor Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-technical-assets) om uw aanbiedingen te beheren.

In dit artikel wordt beschreven hoe u technische elementen maakt en configureert voor een vm-aanbieding voor virtuele machines voor Azure Marketplace. Een VM bevat twee componenten: de virtuele harde schijf (VHD) van het besturingssysteem en optionele gekoppelde gegevensschijven VHD's:

* **Besturingssysteem VHD** – Bevat het besturingssysteem en de oplossing die wordt geïmplementeerd met uw aanbod. Het proces van de voorbereiding van de VHD verschilt afhankelijk van of het een Linux-gebaseerde, Windows-gebaseerde of op maat gebaseerde VM is.
* **Dataschijven VHDs** - Dedicated, persistente opslag voor een VM. Gebruik het besturingssysteem VHD (bijvoorbeeld de C: drive) niet om permanente informatie op te slaan.

Een VM-afbeelding bevat één besturingssysteemschijf en maximaal 16 gegevensschijven. Gebruik één VHD per gegevensschijf, zelfs als de schijf leeg is.

> [!NOTE]
> Ongeacht welk besturingssysteem u gebruikt, voegt u alleen het minimum aantal gegevensschijven toe dat de oplossing nodig heeft. Klanten kunnen geen schijven verwijderen die deel uitmaken van een afbeelding op het moment van implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na implementatie.

> [!IMPORTANT]
> Elke VM-afbeelding in een abonnement moet hetzelfde aantal gegevensschijven hebben.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen. Naast uw oplossingsdomein moet uw engineeringteam kennis hebben van de volgende Microsoft-technologieën:

* Basiskennis van [Azure Services](https://azure.microsoft.com/services/)
* [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
* Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage) en Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
* Werkkennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
* Werkkennis van [JSON](https://www.json.org/)

## <a name="suggested-tools--optional"></a>Voorgestelde gereedschappen – optioneel

Overweeg een van de volgende scriptomgevingen te gebruiken om VM's en VHD's te beheren:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-CLI](https://code.visualstudio.com/)

Overweeg bovendien de volgende tools toe te voegen aan uw ontwikkelomgeving:

* [Azure Opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/)
  * Extensie: [Azure Resource Manager-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  * Uitbreiding: [Verfraaien](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  * Uitbreiding: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Bekijk de beschikbare hulpprogramma's op de pagina [Azure Developer Tools](https://azure.microsoft.com/product-categories/developer-tools/) en, als u Visual Studio gebruikt, de Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-a-vm-image-using-an-approved-base"></a>Een VM-afbeelding maken met een goedgekeurde basis

> [!NOTE]
> Als u uw technische elementen voor virtuele machines wilt maken met behulp van een afbeelding die u op uw eigen locatie hebt gemaakt, gaat u naar [Een virtuele machine maken met uw eigen afbeelding.](#create-a-vm-using-your-own-image)

In deze sectie worden verschillende aspecten beschreven van het gebruik van een goedgekeurde basis, zoals het gebruik van het Extern bureaublad-protocol (RDP), het selecteren van een grootte voor de VM, het installeren van de nieuwste Windows-updates en het generaliseren van de VHD-afbeelding.

De volgende secties richten zich voornamelijk op windows-gebaseerde VHD's. Zie [Linux voor distributies](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)die door Azure zijn goedgekeurd voor meer informatie over het maken van Op Linux gebaseerde VHD's.

> [!WARNING]
> Volg de richtlijnen in dit onderwerp om Azure te gebruiken om een VM te maken met een vooraf geconfigureerd, onderschreven besturingssysteem. Als dit niet compatibel is met uw oplossing, is het mogelijk om een on-premises VM te maken en te configureren met behulp van een goedgekeurd besturingssysteem. U deze vervolgens configureren en voorbereiden op uploaden zoals beschreven in [Een Windows VHD of VHDX voorbereiden om te uploaden naar Azure.](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)

### <a name="select-an-approved-base"></a>Een goedgekeurde basis selecteren

Selecteer het Windows-besturingssysteem of Linux als basis.

#### <a name="windows"></a>Windows

Het besturingssysteem VHD voor uw VM-afbeelding in Windows moet gebaseerd zijn op een door Azure goedgekeurde basisafbeelding die Windows Server of SQL Server bevat. Maak om te beginnen een VM van een van de volgende afbeeldingen van de Azure-portal:

* Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [Datacenter 2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
* [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
* [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!NOTE]
> Als u de huidige Azure-portal of Azure PowerShell gebruikt, worden Windows Server-afbeeldingen die op 8 september 2014 en hoger zijn gepubliceerd, goedgekeurd.

#### <a name="linux"></a>Linux

Azure biedt een reeks goedgekeurde Linux-distributies. Zie Linux voor een huidige lijst [op distributies die zijn goedgekeurd door Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)

### <a name="create-vm-in-the-azure-portal"></a>VM maken in de Azure-portal

Volg deze stappen om de basis-VM-afbeelding in de [Azure-portal](https://ms.portal.azure.com/)te maken:

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/) met het Microsoft-account dat is gekoppeld aan het Azure-abonnement dat u wilt gebruiken om uw VM-aanbieding te publiceren.
2. Maak een nieuwe resourcegroep en geef de naam van uw **resourcegroep,** **abonnement**en **locatie van de resourcegroep op**. Zie Resources [beheren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)voor meer informatie .
3. Selecteer **Virtuele machines** aan de linkerkant om de pagina Virtuele machines details weer te geven.
4. Selecteer **+ Toevoegen** om de ervaring Een virtuele machine maken te **openen**.
5. Selecteer de afbeelding in de vervolgkeuzelijst of klik op **Bladeren door alle openbare en privéafbeeldingen** bladeren om alle beschikbare afbeeldingen van virtuele machines te zoeken of door te bladeren.
6. Selecteer de grootte van de VM die u wilt implementeren met behulp van de volgende aanbevelingen:
    * Als u van plan bent om de VHD on-premises te ontwikkelen, maakt de grootte niet uit. Overweeg het gebruik van een van de kleinere VM's.
    * Als u van plan bent de afbeelding in Azure te ontwikkelen, u overwegen een van de aanbevolen VM-formaten voor de geselecteerde afbeelding te gebruiken.

7. Vouw in de sectie **Schijven** de sectie **Geavanceerd** uit en stel de optie **Beheerde schijven gebruiken** in **op Nee**.
8. Geef de andere vereiste gegevens op om de VM te maken.
9. Selecteer **Controleren + maken** om uw keuzes te bekijken. Wanneer u het **doorgegeven bericht Validatie ziet,** selecteert u **Maken**.

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. U de voortgang ervan bijhouden door het tabblad **Virtuele machines** aan de linkerkant te selecteren. Nadat de status is gemaakt, wordt de status gewijzigd in **Uitvoeren**.

Als u problemen ondervindt bij het maken van uw nieuwe VHD op Azure-basis, raadpleegt u [Veelvoorkomende problemen tijdens VHD-creatie (veelgestelde vragen).](https://aka.ms/VHDcreationIssues)

### <a name="connect-to-your-azure-vm"></a>Verbinding maken met uw Azure VM

In deze sectie wordt uitgelegd hoe u verbinding maken met en u aanmelden bij de VM die u op Azure hebt gemaakt. Nadat u verbinding hebt gemaakt, u met de VM werken alsof u lokaal bent aangemeld bij de hostserver.

#### <a name="connect-to-a-windows-based-vm"></a>Verbinding maken met een windows-vm

Gebruik de externe desktopclient om verbinding te maken met de vm die op Azure wordt gehost op Windows. De meeste versies van Windows bevatten native ondersteuning voor het Remote Desktop Protocol (RDP). Voor andere besturingssystemen vindt u meer informatie over clients in [Extern bureaublad-clients.](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)

In dit artikel wordt beschreven hoe u de ingebouwde Windows RDP-ondersteuning gebruiken om verbinding te maken met uw VM: [Verbinding maken en inloggen op een virtuele Azure-machine met Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> U tijdens het proces beveiligingswaarschuwingen krijgen. Waarschuwingen zoals 'Het RDP-bestand is bijvoorbeeld van een onbekende uitgever' of 'Uw gebruikersreferenties kunnen niet worden geverifieerd'. Het is veilig om deze waarschuwingen te negeren.

#### <a name="connect-to-a-linux-based-vm"></a>Verbinding maken met een op Linux gebaseerde VM

Om verbinding te maken met een Op Linux gebaseerde VM, hebt u een Secure Shell Protocol (SSH) client nodig. De volgende stappen maken gebruik van de gratis [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminal.

1. Ga naar de [Azure-portal.](https://ms.portal.azure.com/)
2. Zoeken naar virtuele machines en selecteer **deze**.
3. Selecteer de VM waarmee u verbinding wilt maken.
4. Start de VM als deze nog niet actief is.
5. Selecteer de naam van de virtuele machine om de **pagina Overzicht** te openen.
6. Let op de ip-adres en dns-naam van uw vm (als deze waarden niet zijn ingesteld, moet u [een netwerkinterface maken).](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)
7. Open de PuTTY-toepassing.
8. Voer in het dialoogvenster PuTTY-configuratie het IP-adres of de DNS-naam van uw vm in.

    :::image type="content" source="media/avm-putty.png" alt-text="Illustreert de PuTTY-terminalinstellingen. de hostnaam of het IP-adres en de poortvakken worden gemarkeerd.":::

9. Selecteer **Openen** om een PuTTY-terminal te openen.
10. Voer desgevraagd de accountnaam en het wachtwoord van uw Linux VM-account in.

Als u verbindingsproblemen hebt, raadpleegt u de documentatie voor uw SSH-client. [Bijvoorbeeld hoofdstuk 10: algemene foutmeldingen](https://www.ssh.com/ssh/putty/putty-manuals).

Zie [Extern bureaublad installeren en configureren om verbinding te maken met een Linux-vm in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/use-remote-desktop)voor meer informatie, waaronder het toevoegen van een bureaublad aan een ingerichte Linux-vm.

## <a name="create-a-vm-using-your-own-image"></a>Een VM maken met uw eigen afbeelding

In deze sectie wordt beschreven hoe u een door de gebruiker geleverde virtuele machine (VM)-afbeelding maken en implementeren. U dit doen door VHD-beelden van een door Azure geïmplementeerde virtuele harde schijf (VHD) te voorzien van vhd-opnamen van het besturingssysteem en de gegevensschijf.

> [!NOTE]
> Als u optioneel een goedgekeurde basisafbeelding wilt gebruiken, volgt u de instructies in [Een VM-afbeelding maken met een goedgekeurde basis.](#create-a-vm-image-using-an-approved-base)

1. Upload uw afbeeldingen naar het Azure Storage-account.
2. De VM-afbeelding implementeren.
3. Leg de VM-afbeelding vast.

### <a name="upload-your-images-to-an-azure-storage-account"></a>Uw afbeeldingen uploaden naar een Azure-opslagaccount

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Upload uw gegeneraliseerde besturingssysteem VHD- en dataschijf-VHD's naar uw Azure-opslagaccount.

### <a name="deploy-your-image"></a>Uw afbeelding implementeren

Maak uw afbeelding met de Azure-portal of Azure PowerShell.

#### <a name="deploy-using-the-azure-portal"></a>Implementeren met de Azure-portal

1. Selecteer op de startpagina **Een resource maken,** zoek naar 'Sjabloonimplementatie' en selecteer **Maken**.
2. Kies **Uw eigen sjabloon maken in de editor**.

    :::image type="content" source="media/avm-custom-deployment.png" alt-text="Hiermee wordt de pagina Aangepaste implementatie geïllustreerd.":::

3. Plak deze [JSON-sjabloon](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-deploy-json-template) in de editor en selecteer **Opslaan**.
4. Geef de parameterwaarden op voor de weergegeven aangepaste eigenschapspagina's. **Custom deployment**

    | Parameter | Beschrijving |
    | ------------ | ------------- |
    | Gebruikersnaam accountnaam | Inhoud van cel 2 |
    | Naam van gebruikersopslagcontainer | Naam van het opslagaccount waar de gegeneraliseerde VHD zich bevindt |
    | DNS-naam voor openbaar IP | Openbare IP DNS-naam. Definieer de DNS-naam voor het openbare IP-adres in de Azure-portal nadat de aanbieding is geïmplementeerd. |
    | Gebruikersnaam beheerder | Gebruikersnaam van het beheerdersaccount voor nieuwe vm |
    | Beheerderswachtwoord | Het wachtwoord van het beheerdersaccount voor nieuwe vm |
    | Het type besturingssysteem | VM-besturingssysteem: Windows of Linux |
    | Abonnements-id | Id van het geselecteerde abonnement |
    | Locatie | Geografische locatie van de implementatie |
    | VM-grootte | [Azure VM-grootte](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), bijvoorbeeld Standard_A2 |
    | Naam van openbaar IP-adres | Naam van uw openbare IP-adres |
    | VM-naam | Naam van de nieuwe VM |
    | Naam virtueel netwerk | Naam van het virtuele netwerk dat door de VM wordt gebruikt |
    | NIC-naam | Naam van de netwerkinterfacekaart waarop het virtuele netwerk wordt uitgevoerd |
    | VHD-URL | Url van OS Disk VHD voltooien |
    |  |  |

5. Nadat u deze waarden hebt opgedaan, selecteert u **Kopen**.

Azure begint met de implementatie. Hiermee wordt een nieuwe VM gemaakt met de opgegeven onbeheerde VHD in het opgegeven bekeeringsaccountpad voor opslag. U de voortgang in de Azure-portal bijhouden door **virtuele machines** aan de linkerkant van de portal te selecteren. Wanneer de vm wordt gemaakt, verandert de status van Starten naar Uitvoeren.

#### <a name="deploy-using-azure-powershell"></a>Implementeren met Azure PowerShell

```powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```

### <a name="capture-the-vm-image"></a>De VM-afbeelding vastleggen

Gebruik de volgende instructies die overeenkomen met uw aanpak:

* Azure PowerShell: [een onbeheerde VM-afbeelding maken van een Azure-vm](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
* Azure CLI: [een afbeelding van een virtuele machine of VHD maken](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
* API: [Virtuele machines - Vastleggen](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

## <a name="configure-the-virtual-machine"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure VM vergroten, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden om te worden geïmplementeerd op Azure Marketplace.

### <a name="sizing-the-vhds"></a>Grootte van de VHD's

Als u een van de VM's hebt geselecteerd die vooraf zijn geconfigureerd met een besturingssysteem (en eventueel aanvullende services), hebt u al een standaard Azure VM-grootte gekozen. Het starten van uw oplossing met een vooraf geconfigureerd besturingssysteem is de aanbevolen aanpak. Als u een besturingssysteem echter handmatig installeert, moet u uw primaire VHD in uw VM-afbeelding vergroten:

* Voor Windows moet het besturingssysteem VHD worden gemaakt als een VHD met een vast formaat van 127-128 GB.
* Voor Linux moet deze VHD worden gemaakt als een 30-50 GB fixed-formaat VHD.

Als de fysieke grootte minder dan 127-128 GB is, moet de VHD uitbreidbaar zijn (schaars/dynamisch). De basis-Windows- en SQL Server-afbeeldingen die worden geleverd, voldoen al aan deze vereisten, dus wijzig het formaat of de grootte van de VHD niet.

Gegevensschijven kunnen zo groot zijn als 1 TB. Houd er bij de beslissing over de grootte van bij dat klanten het formaat van VHD's in een afbeelding op het moment van implementatie niet kunnen wijzigen. Datadisk VHD's moeten worden gemaakt als VHD's met een vaste indeling. Ze moeten ook uitbreidbaar (schaars / dynamisch). Gegevensschijven kunnen in eerste instantie leeg zijn of gegevens bevatten.

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

De basisafbeeldingen van vm's van het besturingssysteem moeten de laatste updates bevatten tot hun gepubliceerde datum. Voordat u het door u gemaakte besturingssysteem VHD publiceert, moet u ervoor zorgen dat u het besturingssysteem en alle geïnstalleerde services bijwerkt met de nieuwste beveiligings- en onderhoudspatches.

Voer voor Windows Server de opdracht **Controleren op updates** uit.

Voor Linux-distributies worden updates vaak gedownload en geïnstalleerd via een command-line tool of een grafisch hulpprogramma. Ubuntu Linux biedt bijvoorbeeld de [apt-get-opdracht](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) en de [Update Manager-tool](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) voor het bijwerken van het besturingssysteem.

### <a name="perform-additional-security-checks"></a>Extra beveiligingscontroles uitvoeren

Houd een hoog beveiligingsniveau voor uw oplossingsafbeeldingen in de Azure Marketplace. In het volgende artikel vindt u een checklist met beveiligingsconfiguraties en -procedures om u te helpen: [beveiligingsaanbevelingen voor Azure Marketplace-afbeeldingen.](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images) Sommige van deze aanbevelingen zijn specifiek voor op Linux gebaseerde afbeeldingen, maar de meeste zijn van toepassing op elke VM-afbeelding.

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie- en geplande taken uitvoeren

Als er extra configuratie nodig is, gebruikt u een geplande taak die wordt uitgevoerd bij het opstarten om eventuele definitieve wijzigingen in de VM aan te brengen nadat deze is geïmplementeerd. Houd ook rekening met de volgende aanbevelingen:

* Als het een run-once-taak is, moet de taak zichzelf verwijderen nadat deze is voltooid.
* Configuraties mogen niet afhankelijk zijn van andere stations dan C of D, omdat alleen deze twee schijven altijd gegarandeerd bestaan (schijf C is de schijf van het besturingssysteem en station D is de tijdelijke lokale schijf).

Zie [Virtuele machine-extensies en -functies voor Linux voor](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)meer informatie over Linux-aanpassingen.

## <a name="generalize-the-image"></a>De afbeelding generaliseren

Alle afbeeldingen in de Azure Marketplace moeten op een algemene manier opnieuw kunnen worden gebruikt. Om dit te bereiken moet het besturingssysteem VHD worden gegeneraliseerd, een bewerking die alle instantiespecifieke id's en softwarestuurprogramma's van een VM verwijdert.

### <a name="windows"></a>Windows

Windows OS-schijven worden gegeneraliseerd met het [sysprep-hulpprogramma](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Als u het besturingssysteem vervolgens bijwerkt of opnieuw configureert, moet u sysprep opnieuw uitvoeren.

> [!WARNING]
> Omdat updates automatisch kunnen worden uitgevoerd, schakelt u de virtuele machine uit nadat u sysprep hebt uitgevoerd totdat deze is geïmplementeerd. Deze afsluiting voorkomt latere updates van het aanbrengen van instantiespecifieke wijzigingen in het besturingssysteem of geïnstalleerde services. Zie [Stappen om een VHD te generaliseren voor](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)meer informatie over het uitvoeren van sysprep.

### <a name="linux"></a>Linux

Het volgende proces generaliseert een Linux VM en herschikt deze als een afzonderlijke VM. Zie Een [afbeelding van een virtuele machine of VHD maken](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)voor meer informatie. U stoppen wanneer u de sectie 'Een vm maken van de vastgelegde afbeelding' bereikt.

1. **De Azure Linux-agent verwijderen**

    1. Maak verbinding met uw Linux-VM via een SSH-client.
    2. Voer in het SSH-venster `sudo waagent -deprovision+user`de volgende opdracht in: .
    3. Typ **Y** om door te gaan (u de parameter **-force** toevoegen aan de vorige opdracht om de bevestigingsstap te vermijden).
    d. Nadat de opdracht is voltooid, typt u **Afsluiten** om de SSH-client te sluiten.

2. **Virtuele machine stoppen**

    1. Selecteer in de Azure-portal uw resourcegroep (RG) en detoewijzing van de VM.
    2. Uw VHD is nu gegeneraliseerd en u een nieuwe VM maken met behulp van deze VHD.

## <a name="next-steps"></a>Volgende stappen

Als u problemen hebt ondervonden bij het maken van uw nieuwe VHD op Azure-gebaseerde, raadpleegt u [Veelvoorkomende problemen tijdens het maken van VHD.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues)

Anders:

* [In het certificeren van uw VM-afbeelding](https://docs.microsoft.com/azure/marketplace/partner-center-portal/get-sas-uri) wordt uitgelegd hoe u een VM-afbeelding voor Azure Marketplace-certificering testen en verzenden, inclusief waar u het *hulpprogramma voor certificeringstest voor Azure Certified* krijgen en hoe u deze gebruiken om uw VM-afbeelding te certificeren.
