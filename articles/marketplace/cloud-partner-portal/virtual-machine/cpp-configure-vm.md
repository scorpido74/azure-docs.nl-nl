---
title: De door Microsoft Azure gehoste VM configureren voor de Azure Marketplace
description: Hierin wordt uitgelegd hoe u een virtuele machine die wordt gehost op Azure kunt aanpassen, bijwerken en generaliseren.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ce7fe49b07dc250a9f56ff73229e347b997f0cc0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824498"
---
# <a name="configure-the-azure-hosted-vm"></a>De door Azure gehoste VM configureren

In dit artikel wordt uitgelegd hoe u de grootte, update en generaliseren van een virtuele machine (VM) die wordt gehost op Azure.  Deze stappen zijn nodig om uw VM voor te bereiden voor implementatie vanuit Azure Marketplace.


## <a name="sizing-the-vhds"></a>Grootte van de Vhd's aanpassen

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Als u een van de virtuele machines hebt geselecteerd die vooraf zijn geconfigureerd met een besturings systeem (en eventueel extra services), hebt u al een standaard Azure VM-grootte gekozen, zoals beschreven op het [tabblad virtual machine sku's](./cpp-skus-tab.md).  Het is de aanbevolen methode om uw oplossing te starten met een vooraf geconfigureerd besturings systeem.  Als u echter een besturings systeem hand matig installeert, moet u de grootte van de primaire VHD in uw VM-installatie kopie aanpassen:

- Voor Windows moet de VHD met het besturings systeem worden gemaakt als VHD met een vaste indeling van 127-128 GB. 
- Voor Linux moet deze VHD worden gemaakt als een VHD met een vaste grootte van 30-50 GB.

Als de fysieke grootte kleiner is dan 127-128 GB, moet de VHD sparse zijn. De geleverde basis Windows-en SQL Server-installatie kopieën voldoen al aan deze vereisten, dus Wijzig de indeling of de grootte van de opgehaalde VHD niet. 

Gegevens schijven kunnen Maxi maal 1 TB groot zijn. Houd er bij het bepalen van de grootte rekening mee dat klanten de grootte van Vhd's binnen een installatie kopie niet kunnen wijzigen op het moment van de implementatie. Vhd's met een gegevens schijf moeten worden gemaakt als Vhd's met vaste grootte. Ze moeten ook verspreid zijn. Gegevens schijven kunnen in eerste instantie leeg zijn of gegevens bevatten.


## <a name="install-the-most-current-updates"></a>De meest recente updates installeren

De basis installatie kopieën van virtuele machines van het besturings systeem bevatten de meest recente updates tot hun publicatie datum. Voordat u de VHD van het besturings systeem publiceert die u hebt gemaakt, moet u ervoor zorgen dat u het OS en alle geïnstalleerde services bijwerkt met alle nieuwste beveiligings-en onderhouds patches.

Voor Windows Server 2016 voert u de opdracht **controleren op updates** uit.  Zie voor oudere versies van Windows voor meer [informatie over het ophalen van een update via Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  De nieuwste essentiële en belang rijke beveiligings updates worden automatisch geïnstalleerd met Windows Update.

Voor Linux-distributies worden updates meestal gedownload en geïnstalleerd via een opdracht regel programma of een grafisch hulp programma.  Ubuntu Linux biedt bijvoorbeeld de opdracht [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) en het hulp programma [Update beheer](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) voor het bijwerken van het besturings systeem.


## <a name="perform-additional-security-checks"></a>Aanvullende beveiligings controles uitvoeren

U moet een hoog beveiligings niveau voor uw oplossings installatie kopieën in de Azure Marketplace onderhouden.  Het volgende artikel bevat een controle lijst met beveiligings configuraties en-procedures om u te helpen in dit doel: [beveiligings aanbevelingen voor installatie kopieën van Azure Marketplace](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Sommige van deze aanbevelingen zijn specifiek voor installatie kopieën op basis van Linux, maar zijn het meest van toepassing op elke VM-installatie kopie. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Aangepaste configuratie en geplande taken uitvoeren

Als er aanvullende configuratie nodig is, is de aanbevolen benadering om een geplande taak uit te voeren die tijdens het opstarten wordt uitgevoerd om eventuele wijzigingen aan te brengen in de virtuele machine nadat deze is geïmplementeerd.  Houd ook rekening met de volgende aanbevelingen:
- Als het een Run-Once-taak is, is het raadzaam de taak zelf te verwijderen nadat deze is voltooid.
- Configuraties mogen niet afhankelijk zijn van C of D, omdat alleen deze twee stations altijd gegarandeerd bestaan. Station C is de schijf met het besturings systeem en station D is de tijdelijke lokale schijf.

Zie [extensies en functies van virtuele machines voor Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)voor meer informatie over Linux-aanpassingen.


## <a name="generalize-the-image"></a>Generaliseren van de installatiekopie

Alle installatie kopieën in azure Marketplace moeten op een algemene manier opnieuw worden gebruikt. Om deze herbruikbaarheid te verhelpen, moet de VHD van het besturings systeem worden *gegeneraliseerd*, een bewerking waarbij alle instantie-specifieke id's en software stuur Programma's van een virtuele machine worden verwijderd.

### <a name="windows"></a>Windows

Windows-besturingssysteem schijven worden gegeneraliseerd met het [hulp programma Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Als u het besturings systeem vervolgens bijwerkt of opnieuw configureert, moet u Sysprep opnieuw uitvoeren. 

> [!WARNING]
>  Omdat updates mogelijk automatisch worden uitgevoerd, moet u na het uitvoeren van Sysprep de virtuele machine uitschakelen totdat deze is geïmplementeerd.  Als u dit afsluit, voor komt u dat latere updates exemplaren van specifieke wijzigingen aanbrengen in het VHD-besturings systeem of de geïnstalleerde services.

Zie [stappen voor het generaliseren van een VHD](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) voor meer informatie over het uitvoeren van Sysprep

### <a name="linux"></a>Linux

Het volgende proces in twee stappen generaliseert een Linux-VM en implementeert deze opnieuw als een afzonderlijke virtuele machine. Deze twee stappen zijn alleen de basis principes van het proces. Zie [een installatie kopie van een virtuele machine of VHD maken](../../../virtual-machines/linux/capture-image.md)voor meer informatie over deze twee stappen en waarom ze moeten worden uitgevoerd. Voor het maken van de VHD voor uw Azure Marketplace-aanbieding kunt u stoppen wanneer u het gedeelte ' een VM maken van de vastgelegde installatie kopie ' bereikt.

#### <a name="remove-the-azure-linux-agent"></a>De Azure Linux-agent verwijderen
1.  Maak verbinding met uw virtuele Linux-machine met behulp van een SSH-client.
2.  Typ de volgende opdracht in het SSH-venster: <br/>
    `sudo waagent -deprovision+user`
3.  Typ `y` om door te gaan. (U kunt de para meter `-force` toevoegen aan de vorige opdracht om deze bevestigings stap te voor komen.)
4.  Nadat de opdracht is voltooid, typt u `exit` om de SSH-client te sluiten.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>De installatie kopie vastleggen
1.  Ga naar de Azure Portal, selecteer uw resource groep (RG) en de virtuele machine opnieuw toe te wijzen.
2.  Uw VHD wordt nu gegeneraliseerd en u kunt een nieuwe virtuele machine maken met behulp van deze VHD.


## <a name="create-one-or-more-copies"></a>Een of meer kopieën maken

Het maken van kopieën van VM is vaak handig voor back-ups, testen, aangepaste failover of taak verdeling, om verschillende configuraties van een oplossing te bieden, enzovoort. Zie voor informatie over het dupliceren en downloaden van een primaire VHD, voor het maken van een onbeheerde kloon:

- Linux-VM: [down load een Linux-VHD van Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows-VM: [een Windows-VHD downloaden vanuit Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Volgende stappen

Nadat uw virtuele machine is gegeneraliseerd, is de toewijzing ongedaan gemaakt en hebt u een installatie kopie van de VM. u bent klaar om een VM te [implementeren vanaf een virtuele harde schijf](./cpp-deploy-vm-vhd.md).
