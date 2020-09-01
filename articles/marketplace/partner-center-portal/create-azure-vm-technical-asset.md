---
title: Technische assets van virtuele Azure-machines maken
description: Meer informatie over het maken en configureren van technische assets voor een virtuele machine (VM)-aanbieding voor Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 08/14/2020
ms.openlocfilehash: 496cd4aeb96ca1849e950331658014d91dc6d6ba
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2020
ms.locfileid: "89143876"
---
# <a name="create-azure-virtual-machine-technical-assets"></a>Technische assets van virtuele Azure-machines maken

Bij het publiceren van uw VM-installatie kopieën naar Azure Marketplace, valideert het Azure-team de VM-installatie kopie om te zorgen voor de opstart baarheid, beveiliging en Azure-compatibiliteit. Als een van de tests van hoge kwaliteit mislukt, mislukt de publicatie met een bericht met daarin de fout en mogelijke [rectificaties tappen](https://docs.microsoft.com/azure/marketplace/partner-center-portal/vm-certification-issues-solutions).

In dit artikel wordt beschreven hoe u technische assets kunt maken en configureren voor een virtuele machine (VM)-aanbieding voor Azure Marketplace. Een VM bevat twee onderdelen: de virtuele harde schijf (VHD) van het besturings systeem en de optionele Vhd's met gekoppelde gegevens schijven:

1. **VHD van het besturings systeem** : bevat het besturings systeem en de oplossing die met uw aanbieding wordt geïmplementeerd. Het proces van het voorbereiden van de VHD verschilt, afhankelijk van het feit of het een op Linux gebaseerde, op Windows gebaseerde of een op een maatwerk gebaseerde VM is.

2. **Vhd's met gegevens schijf** : toegewezen, permanente opslag voor een virtuele machine. Gebruik niet de VHD met het besturings systeem (bijvoorbeeld station C:) om permanente gegevens op te slaan.

Een VM-installatie kopie bevat één besturingssysteem schijf en Maxi maal 16 gegevens schijven. Gebruik één VHD per gegevens schijf, zelfs als de schijf leeg is.

> [!NOTE]
> Ongeacht welk besturings systeem u gebruikt, voegt u alleen het minimum aantal gegevens schijven toe dat nodig is voor de oplossing. Klanten kunnen geen schijven verwijderen die deel uitmaken van een installatie kopie op het moment van de implementatie, maar ze kunnen altijd schijven toevoegen tijdens of na de implementatie.

> [!IMPORTANT]
> Elke VM-installatie kopie in een plan moet hetzelfde aantal gegevens schijven hebben.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen. Naast uw oplossings domein moet uw technische team kennis hebben van de volgende micro soft-technologieën:

- Basis informatie over [Azure-Services](https://azure.microsoft.com/services/)
- Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
- Praktische kennis van [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
- Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktische kennis van [JSON](https://www.json.org/)

### <a name="optional-suggested-tools"></a>Optionele aanbevolen hulpprogram ma's

Overweeg het gebruik van een van de volgende script omgevingen om Vm's en Vhd's te beheren:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure-CLI](https://code.visualstudio.com/)

Daarnaast kunt u de volgende hulpprogram ma's toevoegen aan uw ontwikkel omgeving:

- [Azure-opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/)

## <a name="create-a-vm-image-using-an-approved-base"></a>Een VM-installatie kopie maken met behulp van een goedgekeurde basis

Zie [een VM-installatie kopie maken met behulp van een goedgekeurde basis](#create-a-vm-image-using-an-approved-base) hieronder voor het maken van de technische assets van uw virtuele machines met behulp van een installatie kopie die u hebt gemaakt op uw eigen locatie.

In deze sectie worden verschillende aspecten van het gebruik van een goedgekeurde basis beschreven, zoals het gebruik van de Remote Desktop Protocol (RDP), het selecteren van een grootte voor de virtuele machine, het installeren van de meest recente Windows-updates en het generaliseren van de VHD-installatie kopie.

Volg de richt lijnen in dit artikel om Azure te gebruiken voor het maken van een virtuele machine met een vooraf geconfigureerd, goedgekeurd besturings systeem. Als dit niet compatibel is met uw oplossing, is het mogelijk een on-premises VM te maken en te configureren met behulp van een goedgekeurd besturings systeem. U kunt deze vervolgens configureren en voorbereiden voor het uploaden, zoals wordt beschreven in [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).

### <a name="select-an-approved-base-image"></a>Een goedgekeurde basis installatie kopie selecteren

Selecteer het Windows-besturings systeem of Linux als basis.

De VHD van het besturings systeem voor uw VM-installatie kopie moet gebaseerd zijn op een door Azure goedgekeurde basis installatie kopie die Windows Server of SQL Server bevat.

Wanneer u een aanvraag indient om uw installatie kopie opnieuw te publiceren met updates, kan de test voor de verificatie van het onderdeel nummer mislukken. In dat geval wordt uw installatie kopie niet goedgekeurd.

Deze fout treedt op wanneer u een basis installatie kopie gebruikt die deel uitmaakt van een andere uitgever en u de installatie kopie hebt bijgewerkt. In dit geval is het niet toegestaan om uw installatie kopie te publiceren.

U kunt dit probleem oplossen door uw nieuwste installatie kopie op te halen van Azure Marketplace en wijzigingen aan te brengen in de installatie kopie. Raadpleeg het volgende voor het weer geven van goedgekeurde basis installatie kopieën waarin u kunt zoeken naar uw installatie kopie:

#### <a name="windows"></a>Windows

- Windows Server ([2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2016](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 R2 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Data Center](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
- SQL Server 2019 ([Enter prise](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Standard](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [Web](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview))
- SQL Server 2014 ([Enter prise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))
- SQL Server 2012 SP2 ([Enter prise](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Standard](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance), [Web](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance))

#### <a name="linux"></a>Linux

Azure biedt een scala aan goedgekeurde Linux-distributies. Zie voor een actuele lijst [Linux op distributies die zijn goedgekeurd door Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="create-vm-on-the-azure-portal"></a>Een VM maken op het Azure Portal

Volg deze stappen om de installatie kopie van de basis-VM te maken op de [Azure Portal](https://ms.portal.azure.com/):

1. Meld u aan bij de [Azure Portal](https://ms.portal.azure.com/) met de Microsoft-account die is gekoppeld aan het Azure-abonnement dat u wilt gebruiken om uw VM-aanbieding te publiceren.
2. Maak een nieuwe resource groep en geef de locatie van de **resource groep**, het **abonnement**en de **resource groep**op. Zie [resources beheren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal)voor meer informatie.

    :::image type="content" source="media/vm/create-resource-group.png" alt-text="Toont het begin van het maken van een resource groep.":::

3. Selecteer **virtuele machines** in de linkerkant om de pagina Details van de virtuele machines weer te geven.
4. Selecteer **+ toevoegen** om de **ervaring een virtuele machine maken**te openen.
5. Selecteer de installatie kopie in de vervolg keuzelijst of selecteer **Bladeren in alle open bare en persoonlijke installatie kopieën** om te zoeken of bladeren in alle beschik bare installatie kopieën van virtuele machines. Voorbeeld:

    :::image type="content" source="media/vm/create-resource-group-example.png" alt-text="Toont een voor beeld van een VM-installatie kopie.":::

6. Selecteer de grootte van de virtuele machine die u wilt implementeren met behulp van de volgende aanbevelingen:
    1. Als u van plan bent om de VHD on-premises te ontwikkelen, is de grootte niet van belang. Overweeg het gebruik van een van de kleinere Vm's.
    2. Als u van plan bent de installatie kopie in azure te ontwikkelen, kunt u een van de aanbevolen VM-grootten voor de geselecteerde installatie kopie gebruiken.

    :::image type="content" source="media/vm/create-virtual-machine.png" alt-text="De selectie van de VM-grootte weer geven.":::

7. Vouw in de sectie **schijven** het gedeelte **Geavanceerd** uit en stel de optie **Managed disks gebruiken** in op **Nee**.

    :::image type="content" source="media/vm/use-managed-disks.png" alt-text="Hiermee wordt de optie voor het gebruik van beheerde schijven weer gegeven.":::

8. Geef de overige vereiste gegevens op om de virtuele machine te maken.
9. Selecteer **beoordeling + maken** om uw keuzes te controleren. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Azure begint met het inrichten van de virtuele machine die u hebt opgegeven. U kunt de voortgang volgen door op het tabblad **virtual machines** aan de linkerkant te klikken. Nadat deze is gemaakt, wordt de status gewijzigd in **actief**.

### <a name="create-a-generation-2-vm-on-the-azure-portal"></a>Een virtuele machine van de tweede generatie maken op de Azure Portal

Maak een Gen2-VM (Generation 2) in Azure Portal.

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).
2. Selecteer **Een resource maken**.
3. Selecteer **alles weer geven** in azure Marketplace aan de linkerkant.
4. Selecteer een installatie kopie die Gen2 ondersteunt.
5. Selecteer **Maken**.
6. Selecteer op het tabblad **Geavanceerd** onder de sectie **VM-generatie** de optie **generatie 2** .
7. Ga naar het tabblad **basis** informatie en klik onder **Details van exemplaar**op **grootte** en open de Blade **VM-grootte selecteren** .
8. Selecteer een aanbevolen grootte voor de VM en grootte van de [ondersteunde generatie](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2#generation-2-vm-sizes) .
9. Ga door met de stroom voor het maken van een virtuele machine in [Azure Portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) .

    :::image type="content" source="media/vm/vm-generation.png" alt-text="Hiermee wordt de optie voor het genereren van virtuele machines geselecteerd.":::

## <a name="connect-to-your-azure-vm"></a>Verbinding maken met uw Azure VM

In deze sectie wordt uitgelegd hoe u verbinding maakt en zich aanmeldt bij de virtuele machine die u hebt gemaakt in Azure. Nadat u verbinding hebt gemaakt, kunt u met de virtuele machine werken alsof u lokaal bent aangemeld bij de hostserver.

### <a name="connect-to-a-windows-based-vm"></a>Verbinding maken met een VM op basis van Windows

Gebruik de extern bureau blad-client om verbinding te maken met de op Windows gebaseerde VM die wordt gehost op Azure. De meeste versies van Windows bevatten systeem eigen ondersteuning voor Remote Desktop Protocol (RDP). Voor andere besturings systemen kunt u meer informatie vinden over clients in [extern bureaublad-clients](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

In dit artikel wordt beschreven hoe u de ingebouwde Windows RDP-ondersteuning gebruikt om verbinding te maken met uw virtuele [machine: verbinding maken en aanmelden bij een virtuele Azure-computer met Windows](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).

> [!TIP]
> Tijdens het proces kunnen er beveiligings waarschuwingen worden weer gegeven. Bijvoorbeeld: waarschuwingen zoals ' het RDP-bestand is van een onbekende uitgever ' of ' uw gebruikers referenties kunnen niet worden geverifieerd '. Het is veilig om deze waarschuwingen te negeren.

### <a name="connect-to-a-linux-based-vm"></a>Verbinding maken met een op Linux gebaseerde VM

Als u verbinding wilt maken met een VM op basis van Linux, hebt u een SSH-client (Secure Shell Protocol) nodig. In de volgende stappen wordt de Free [putty](https://www.ssh.com/ssh/putty/) shh-terminal gebruikt.

1. Ga naar [Azure Portal](https://ms.portal.azure.com/).
2. Zoek en selecteer virtuele machines.
3. Selecteer de virtuele machine waarmee u verbinding wilt maken.
4. Start de virtuele machine als deze nog niet wordt uitgevoerd.
5. Selecteer de naam van de virtuele machine om de pagina overzicht te openen.
6. Noteer het open bare IP-adres en de DNS-naam van uw virtuele machine (als deze waarden niet zijn ingesteld, moet u [een netwerk interface maken](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface).
7. Open de PuTTy-toepassing.
8. Voer in het dialoog venster PuTTy-configuratie het IP-adres of de DNS-naam van uw virtuele machine in.

    :::image type="content" source="media/vm/putty-configuration.png" alt-text="Illustreert de PuTTy-Terminal instellingen en markeert de velden hostnaam en poort.":::

9. Selecteer **openen** om een putty-Terminal te openen.
10. Wanneer u hierom wordt gevraagd, voert u de account naam en het wacht woord in van uw Linux-VM-account.

## <a name="configure-the-virtual-machine"></a>De virtuele machine configureren

In deze sectie wordt beschreven hoe u een Azure-VM kunt verg Roten of verkleinen, bijwerken en generaliseren. Deze stappen zijn nodig om uw VM voor te bereiden voor implementatie op Azure Marketplace.

### <a name="sizing-the-vhds"></a>Grootte van de Vhd's aanpassen

De volgende regels zijn voor beperkingen op de besturingssysteem schijf grootte. Wanneer u een aanvraag indient, moet u ervoor zorgen dat de schijf grootte van het besturings systeem binnen de limiet voor Linux of Windows valt.

| Besturingssysteem | Aanbevolen grootte voor VHD |
| --- | --- |
| Linux | 30 tot 1023 GB |
| Windows | 30 tot 250 GB |

Als Vm's toegang bieden tot het onderliggende besturings systeem, moet u ervoor zorgen dat de VHD-grootte groot genoeg is voor de VHD. Omdat schijven niet kunnen worden uitgevouwen zonder uitval tijd, gebruikt u een schijf grootte tussen 30 en 50 &nbsp; GB.

| VHD-grootte | Werkelijke grootte | Oplossing |
| --- | --- | --- |
| >500 TB | N.v.t. | Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring. |
| 250-500 TB | >200 GB wijkt af van de grootte van de BLOB | Neem contact op met het ondersteunings team voor een uitzonderings goedkeuring. |

### <a name="install-the-most-current-updates"></a>De meest recente updates installeren

De basis installatie kopieën van virtuele machines van het besturings systeem moeten de meest recente updates bevatten tot de publicatie datum. Voordat u de VHD van het besturings systeem publiceert die u hebt gemaakt, moet u ervoor zorgen dat u de besturings systemen en alle geïnstalleerde services bijwerkt met alle nieuwste patches voor beveiliging en onderhoud.

- Voor Windows Server voert u de opdracht controleren op updates uit.
- Voor Linux-distributies worden updates meestal gedownload en geïnstalleerd via een opdracht regel programma of een grafisch hulp programma. Ubuntu Linux biedt bijvoorbeeld de opdracht [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) en het hulp programma [Update beheer](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) voor het bijwerken van het besturings systeem.

#### <a name="perform-additional-security-checks"></a>Aanvullende beveiligings controles uitvoeren

Behoud een hoog beveiligings niveau voor uw oplossings installatie kopieën in de Azure Marketplace. Zie [beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)voor een controle lijst met beveiligings configuraties en-procedures. Sommige van deze aanbevelingen zijn specifiek voor installatie kopieën op basis van Linux, maar zijn het meest van toepassing op elke VM-installatie kopie.

#### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie en geplande taken uitvoeren

Als u aanvullende configuratie nodig hebt, gebruikt u een geplande taak die wordt uitgevoerd bij het opstarten om definitieve wijzigingen aan te brengen in de virtuele machine nadat deze is geïmplementeerd. Houd ook rekening met het volgende:

- Als het een Run-Once-taak is, moet de taak zichzelf verwijderen nadat deze is voltooid.
- Configuraties mogen niet afhankelijk zijn van C of D, omdat alleen deze twee schijven altijd gegarandeerd bestaan (station C is de schijf met het besturings systeem en station D is de tijdelijke lokale schijf).

Zie [extensies en functies van virtuele machines voor Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)voor meer informatie over Linux-aanpassingen.

## <a name="generalize-the-image"></a>De installatie kopie generaliseren

Alle installatie kopieën in azure Marketplace moeten op een algemene manier opnieuw worden gebruikt. Om dit te verhelpen, moet de VHD van het besturings systeem worden gegeneraliseerd, een bewerking waarbij alle instantie-specifieke id's en software stuur Programma's van een virtuele machine worden verwijderd.

### <a name="for-windows"></a>Voor Windows

Windows-besturingssysteem schijven worden gegeneraliseerd met het hulp programma [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Als u het besturings systeem later bijwerkt of opnieuw configureert, moet u Sysprep opnieuw uitvoeren.

> [!WARNING]
> Nadat u Sysprep hebt uitgevoerd, schakelt u de virtuele machine uit totdat deze is geïmplementeerd, omdat updates mogelijk automatisch worden uitgevoerd. Als u dit afsluit, voor komt u dat latere updates exemplaren van specifieke wijzigingen aanbrengen in het besturings systeem of de geïnstalleerde services. Zie [stappen voor het generaliseren van een VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)voor meer informatie over het uitvoeren van Sysprep.

### <a name="for-linux"></a>Voor Linux

Het volgende proces generaliseert een Linux-VM en implementeert deze opnieuw als een afzonderlijke VM. Zie [een installatie kopie van een virtuele machine of VHD maken](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)voor meer informatie. U kunt stoppen wanneer u de sectie met de naam ' een virtuele machine maken vanuit de vastgelegde installatie kopie ' bereikt.

1. De Azure Linux-agent verwijderen

    1. Verbinding maken met uw virtuele Linux-machine met behulp van een SSH-client
    2. Voer in het SSH-venster de volgende opdracht in: `sudo waagent –deprovision+user` .
    3. Typ j om door te gaan (u kunt de para meter-Force toevoegen aan de vorige opdracht om te voor komen dat de bevestigings stap wordt uitgevoerd).
    4. Nadat de opdracht is voltooid, typt u exit om de SSH-client te sluiten.

2. Virtuele machine stoppen

    1. Selecteer in de Azure Portal uw resource groep (RG) en de virtuele machine opnieuw toe te wijzen.
    2. Uw VHD is nu gegeneraliseerd en u kunt een nieuwe virtuele machine maken met behulp van deze VHD.

## <a name="next-steps"></a>Volgende stappen

- Als u problemen ondervindt bij het maken van uw nieuwe VHD op basis van Azure, raadpleegt u [algemene problemen tijdens het maken](common-issues-during-vhd-creation.md)van de VHD.
- Als dat niet het geval is, wordt in de [test virtuele machine (VM) die is geïmplementeerd vanaf VHD](azure-vm-image-certification.md) wordt uitgelegd hoe u een VM-installatie kopie kunt testen en verzenden voor Azure Marketplace-certificering, met inbegrip van de locatie waar u het hulp programma voor certificerings test kunt verkrijgen voor Azure Certified en hoe u dit gebruikt om uw
