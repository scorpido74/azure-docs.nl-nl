---
title: De door Microsoft Azure gehoste VM configureren voor de Azure Marketplace
description: Hiermee wordt uitgelegd hoe u een VM die op Azure wordt gehost, vergroten, bijwerken en generaliseren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: f20beced7d977668d12c06375ceb8a2554c6d335
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273423"
---
# <a name="configure-the-azure-hosted-vm"></a>De door Azure gehoste VM configureren

> [!IMPORTANT]
> Vanaf 13 april 2020 beginnen we met het verplaatsen van het beheer van uw Azure Virtual Machine-aanbiedingen naar Partner Center. Na de migratie maak en beheer je je aanbiedingen in partnercentrum. Volg de instructies in [Uw technische assets van Azure Virtual Machine maken](https://aka.ms/AzureVMTechAsset) om uw gemigreerde aanbiedingen te beheren.

In dit artikel wordt uitgelegd hoe u een virtuele machine (VM) vergroten, bijwerken en generaliseren die op Azure wordt gehost.  Deze stappen zijn nodig om uw VM voor te bereiden om te worden geïmplementeerd vanuit de Azure Marketplace.

## <a name="sizing-the-vhds"></a>Grootte van de VHD's

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Als u een van de VM's hebt geselecteerd die vooraf zijn geconfigureerd met een besturingssysteem (en eventueel aanvullende services), hebt u al een standaard Azure VM-grootte gekozen, zoals beschreven op [het tabblad SKU's voor virtuele machines.](./cpp-skus-tab.md)  Het starten van uw oplossing met een vooraf geconfigureerd besturingssysteem is de aanbevolen aanpak.  Als u echter een besturingssysteem handmatig installeert, moet u uw primaire VHD in uw VM-afbeelding vergroten:

- Voor Windows moet het besturingssysteem VHD worden gemaakt als een VHD met een vast formaat van 127-128 GB. 
- Voor Linux moet deze VHD worden gemaakt als een 30-50 GB fixed-formaat VHD.

Als de fysieke grootte minder dan 127-128 GB is, moet de VHD schaars zijn. De basis-Windows- en SQL Server-afbeeldingen die al aan deze vereisten voldoen, wijzigen dus niet het formaat of de grootte van de verkregen VHD. 

Gegevensschijven kunnen zo groot zijn als 1 TB. Houd er bij de beslissing over hoe groot ze zijn, bedenk dan dat klanten het formaat van VHD's in een afbeelding op het moment van implementatie niet kunnen wijzigen. Datadisk VHD's moeten worden gemaakt als VHD's met een vaste indeling. Ze moeten ook schaars zijn. Gegevensschijven kunnen in eerste instantie leeg zijn of gegevens bevatten.


## <a name="install-the-most-current-updates"></a>De meest recente updates installeren

De basisafbeeldingen van besturingssysteem VM's bevatten de laatste updates tot hun gepubliceerde datum. Voordat u het door u gemaakte besturingssysteem VHD publiceert, moet u ervoor zorgen dat u het besturingssysteem en alle geïnstalleerde services bijwerkt met de nieuwste beveiligings- en onderhoudspatches.

Voer voor Windows Server 2016 de opdracht **Controleren op updates** uit.  Zie Voor oudere versies van Windows, zie [Hoe u een update ontvangen via Windows Update.](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update)  Windows update installeert automatisch de nieuwste kritieke en belangrijke beveiligingsupdates.

Voor Linux-distributies worden updates vaak gedownload en geïnstalleerd via een command-line tool of een grafisch hulpprogramma.  Ubuntu Linux biedt bijvoorbeeld de [apt-get-opdracht](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) en de [Update Manager-tool](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) voor het bijwerken van het besturingssysteem.


## <a name="perform-additional-security-checks"></a>Extra beveiligingscontroles uitvoeren

U moet een hoog beveiligingsniveau voor uw oplossingsafbeeldingen in de Azure Marketplace behouden.  In het volgende artikel vindt u een checklist met beveiligingsconfiguraties en -procedures om u te helpen bij deze doelstelling: [beveiligingsaanbevelingen voor Azure Marketplace-afbeeldingen](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Sommige van deze aanbevelingen zijn specifiek voor op Linux gebaseerde afbeeldingen, maar de meeste zijn van toepassing op elke VM-afbeelding. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie- en geplande taken uitvoeren

Als er extra configuratie nodig is, is de aanbevolen aanpak het gebruik van een geplande taak die wordt uitgevoerd bij het opstarten om eventuele definitieve wijzigingen in de VM aan te brengen nadat deze is geïmplementeerd.  Houd ook rekening met de volgende aanbevelingen:
- Als het een run-once-taak is, wordt aanbevolen dat de taak zichzelf verwijdert nadat deze is voltooid.
- Configuraties mogen niet afhankelijk zijn van andere stations dan C of D, omdat alleen deze twee schijven die altijd gegarandeerd bestaan. Station C is de schijf van het besturingssysteem en station D is de tijdelijke lokale schijf.

Zie [Virtuele machine-extensies en -functies voor Linux voor](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)meer informatie over Linux-aanpassingen.


## <a name="generalize-the-image"></a>De afbeelding generaliseren

Alle afbeeldingen in de Azure Marketplace moeten op een algemene manier opnieuw kunnen worden gebruikt. Om deze herbruikbaarheid te bereiken, moet het besturingssysteem VHD worden *gegeneraliseerd*, een bewerking die alle instantiespecifieke id's en softwarestuurprogramma's van een VM verwijdert.

### <a name="windows"></a>Windows

Windows OS-schijven worden gegeneraliseerd met het [sysprep-hulpprogramma](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Als u het besturingssysteem vervolgens bijwerkt of opnieuw configureert, moet u sysprep opnieuw uitvoeren. 

> [!WARNING]
>  Omdat updates automatisch kunnen worden uitgevoerd, moet u de vm uitschakelen zodra u sysprep uitvoert totdat deze is geïmplementeerd.  Deze afsluiting voorkomt latere updates van het aanbrengen van instantiespecifieke wijzigingen in het VHD-besturingssysteem of de geïnstalleerde services.

Zie [Stappen om een VHD te generaliseren voor](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) meer informatie over het uitvoeren van sysprep

### <a name="linux"></a>Linux

Het volgende proces in twee stappen generaliseert een Linux-VM en herschikt deze als een afzonderlijke VM. Deze twee stappen zijn slechts de essentie van het proces. Voor meer informatie over deze twee stappen en waarom ze moeten worden gedaan, zie [Hoe maak je een afbeelding van een virtuele machine of VHD](../../../virtual-machines/linux/capture-image.md). Met het oog op het maken van de VHD voor uw Azure Marketplace-aanbieding u stoppen wanneer u de sectie 'Een VM maken van de vastgelegde afbeelding' bereikt.

#### <a name="remove-the-azure-linux-agent"></a>De Azure Linux-agent verwijderen
1.  Maak verbinding met uw Linux-VM via een SSH-client.
2.  Typ in het SSH-venster de volgende opdracht: <br/>
    `sudo waagent -deprovision+user`
3.  Typ `y` om door te gaan. (U `-force` de parameter toevoegen aan de vorige opdracht om deze bevestigingsstap te voorkomen.)
4.  Nadat de opdracht is `exit` voltooid, typt u de SSH-client om te sluiten.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>De afbeelding vastleggen
1.  Ga naar de Azure-portal, selecteer uw resourcegroep (RG) en detoewijzing van de VM.
2.  Uw VHD is nu gegeneraliseerd en u een nieuwe VM maken met behulp van deze VHD.


## <a name="create-one-or-more-copies"></a>Een of meer kopieën maken

Het maken van kopieën van VM is vaak handig voor back-up, testen, aangepaste fail-over of load balancing, om verschillende configuraties van een oplossing aan te bieden, enzovoort. Zie voor informatie over het dupliceren en downloaden van een primaire VHD om een onbeheerde kloon te maken:

- Linux VM: [Download een Linux VHD van Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [een Windows VHD downloaden van Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Volgende stappen

Nadat uw VM is gegeneraliseerd, is deallocated en u een afbeelding van de VM hebt gemaakt, bent u klaar om een virtuele machine vanaf een virtuele harde schijf te [implementeren.](./cpp-deploy-vm-vhd.md)
