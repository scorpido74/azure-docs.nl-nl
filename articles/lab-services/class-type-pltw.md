---
title: Stel project in voor de manier waarop Labs met Azure Lab Services
description: Meer informatie over het instellen van Labs om project leider te leren hoe klassen.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: e3783ae4fa07bf783841022903c4bcf3ab6fbe23
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648001"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Labs instellen voor project leider de manier waarop klassen

[Project leider (PLTW)](https://www.pltw.org/) is een non-profit organisatie die PreK-12-leer plan biedt over de Verenigde Staten in computer wetenschappen, engineering en biomedische wetenschap.  In elke PLTW-klasse gebruiken studenten diverse software toepassingen als onderdeel van hun praktijk ervaring.  Voor veel van de software toepassingen is een snelle CPU of in sommige gevallen een GPU vereist.  Dit artikel laat u zien hoe u Labs kunt instellen voor de volgende PLTW-klassen die doorgaans aan studenten worden aangeboden in kwaliteiten 9-12:

- **Inleiding tot technisch ontwerp**

    Studenten worden geïntroduceerd in het proces van technisch ontwerp, waaronder het gebruik [van de inventaris computer-aided design (CAD)](https://www.autodesk.com/products/inventor/new-features) -software van auto Desk voor 3D-model lering.

- **Principes van engineering**
    
    Studenten leren meer over technische mechanismen, structural\materiale kracht en automatisering.  Deze klasse maakt gebruik van software zoals [MD solids](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [westelijke pointer Bridge Designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)en [de leger simulatie van Amerika](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Civiele techniek en architectuur**

    Studenten zijn het bouwen en ontwerpen en ontwikkelen van sites met behulp [van de ontwerp software van de Revit](https://www.autodesk.com/products/revit/overview) -architectuur van auto Desk voor het maken van 3D-gegevens modellering (BIM).

- **Computer geïntegreerde productie**

    Studenten verkennen moderne productie processen waarbij Robotics en automatisering betrokken zijn.   In deze klasse gebruiken studenten [de Inventory CAD van auto Desk](https://www.autodesk.com/products/inventor/new-features) en de software voor [inventaris van computer-aided Manufacturing (CAM)](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Digitale elektronica**

    Studenten onderzoeken elektronische-Logic-circuits en-apparaten met behulp van de MultiSim simulatie en circuit ontwerp software [van het National instrument](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) .

- **Technisch ontwerp en-ontwikkeling**

    Studenten dragen bij aan een end-to-end oplossing voor het combi neren van onderzoek, ontwerp en tests die ze aanbieden aan een panel van technici.  In deze klasse gebruiken studenten [de Inventory CAD-software van auto Desk](https://www.autodesk.com/products/inventor/new-features) .

- **Essentiële computer wetenschappen**

    Studenten worden geïntroduceerd op reken kundige concepten en hulpprogram ma's.  Ze beginnen met Program meren op basis van een blok kering en vervolgens verplaatsen naar op tekst gebaseerde code ring met code omgevingen zoals [VEXcode V5-blokken](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principes van computer wetenschappen**
    
    Studenten groeien hun programmeer kennis met [python](https://www.python.org/) met behulp [van de Visual Studio-ontwikkel omgeving van micro soft](https://code.visualstudio.com/). 

- **Computer wetenschappen A**

    Studenten nemen hun programmeer expertise in deze klasse verder door de ontwikkeling van mobiele apps te leren.  In deze klasse leren ze [Java](https://www.java.com/) met behulp [van de Visual Studio code Development Environment van micro soft](https://code.visualstudio.com/).  Studenten gebruiken ook een emulator waarmee ze hun mobiele app-code kunnen uitvoeren en testen.  Voor informatie over het instellen van een emulator in azure Labs, neemt u contact met ons op azlabspilot@microsoft.com .

Raadpleeg de site van PLTW voor de [volledige lijst met software](https://www.pltw.org/pltw-software) voor elke klasse.

## <a name="lab-configuration"></a>Lab-configuratie
Als u Labs wilt instellen voor PLTW, hebt u een Azure-abonnement en een Lab-account nodig om aan de slag te gaan. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. Zodra u een Azure-abonnement hebt ontvangen, kunt u een nieuw Lab-account maken in Azure Lab Services. Zie de zelf studie over het [instellen van een Lab-account](./tutorial-setup-lab-account.md)voor meer informatie over het maken van een nieuw Lab-account. U kunt ook een bestaand Lab-account gebruiken.

Zodra u een Lab-account hebt, moet u afzonderlijke Labs maken voor elke sessie van een PLTW-klasse die uw school biedt.  We raden u ook aan om afzonderlijke installatie kopieën te maken voor elk type PLTW-klasse.  Lees het blog bericht: [overstappen van een fysiek lab naar Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)voor meer informatie over het structureren van uw Labs en installatie kopieën.

### <a name="lab-account-settings"></a>Instellingen van Lab-account
Schakel de instellingen die worden beschreven in de onderstaande tabel voor het lab-account in. Voor meer informatie over het inschakelen van Marketplace-installatie kopieën raadpleegt u het artikel over het [opgeven van Marketplace-installatie kopieën die beschikbaar zijn voor Lab-makers](./specify-marketplace-images.md).

| Account instelling Lab | Instructies |
| -------------------- | ----- |
| Marketplace-installatiekopie | Schakel de Windows 10 Pro-installatie kopie in voor gebruik binnen uw Lab-account. |

### <a name="lab-settings"></a>Lab-instellingen
De grootte van de virtuele machine die wordt gebruikt voor PLTW-klassen is afhankelijk van de typen werk belastingen die uw studenten in de klasse uitvoeren.  Voor de bovenstaande klassen wordt u aangeraden grote en kleine GPU-VM-grootten (visualisatie) te gebruiken.  Raadpleeg de richt lijnen in de onderstaande tabel voor het instellen van Labs voor uw PLTW-klassen.

| Lab-instelling | Waarde/instructies |
| ------------ | ------------------ |
|Grootte van de virtuele machine| **Kleine GPU (visualisatie)**.  Deze VM is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX.  We raden u aan deze grootte te gebruiken voor de volgende PLTW-klassen: civiele techniek en architectuur, digitale elektronica, computer geïntegreerde productie; en technisch ontwerp en ontwikkeling.
|Grootte van de virtuele machine| **Groot**.  Deze grootte is het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches.  We raden u aan deze grootte te gebruiken voor de volgende PLTW-klassen: inleiding op technisch ontwerp, principes van technische ontwikkeling, computer wetenschappen, beginselen van computer wetenschappen en computer wetenschappen A.

### <a name="licensing-server"></a>Licentie server
De meeste software die wordt gebruikt in de bovenstaande PLTW-klassen *_hebben geen toegang_* tot een licentie server.  U hebt echter wel toegang tot een licentie server als u van plan bent om het netwerk licentie model van auto Desk te gebruiken voor de volgende software:
-   Revit
-   Vind
-   Voor raden CAM

[PLTW biedt gedetailleerde stappen](https://www.pltw.org/pltw-software) voor het installeren van de licentie Manager van auto Desk op uw licentie server om netwerk licenties te gebruiken met de software van auto Desk.  Deze licentie server bevindt zich doorgaans in uw on-premises netwerk of wordt gehost op een virtuele Azure-machine (VM) in azure Virtual Network (VNet).

Nadat de licentie server is ingesteld, moet u [het VNet](./how-to-connect-peer-virtual-network.md) koppelen aan uw [Lab-account](./tutorial-setup-lab-account.md). De peering van het netwerk moet worden uitgevoerd _before * het lab maken zodat de virtuele machines van het lab toegang hebben tot de licentie server en de andere manier.

Met de gegenereerde licentie bestanden van auto Desk wordt het MAC-adres van de licentie server Inge sloten.  Als u besluit uw licentie server te hosten met behulp van een Azure-VM, is het belang rijk om ervoor te zorgen dat het MAC-adres van uw licentie server niet wordt gewijzigd.   Als het MAC-adres wordt gewijzigd, moeten uw licentie bestanden opnieuw worden gegenereerd.  Volg deze tips om te voor komen dat uw MAC-adres wordt gewijzigd:

- [Stel een statisch privé-IP en Mac-adres](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) in voor de virtuele Azure-machine die als host fungeert voor uw licentie server.
- Zorg ervoor dat u het lab-account en het VNet van de licentie server in een region\location met voldoende VM-capaciteit hebt ingesteld, zodat u deze resources niet later hoeft te verplaatsen naar een nieuwe region\location.

Lees ook het artikel over het [instellen van een licentie server als een gedeelde bron](./how-to-create-a-lab-with-shared-resource.md) voor meer informatie.

### <a name="template-machine"></a>Sjabloon machine
Sommige van de installatie bestanden die u nodig hebt voor PLTW, zijn groot en nemen veel tijd in beslag om te kopiëren wanneer u deze downloadt naar de sjabloon machine van een test omgeving.

In plaats van installatie bestanden te downloaden naar de sjabloon machine en alles daar te installeren, raden we u aan om uw PLTW-installatie kopieën in uw fysieke omgeving te maken.  Vervolgens kunt u de installatie kopieën in de galerie met gedeelde installatie kopieën importeren, zodat u deze aangepaste installatie kopieën kunt gebruiken om uw Labs te maken.  Lees het volgende artikel voor meer informatie: [een aangepaste installatie kopie uploaden naar de galerie met gedeelde afbeeldingen](./upload-custom-image-shared-image-gallery.md).

Dit zijn de belangrijkste taken voor het instellen van een Lab:

1. Maak de installatie kopie voor de klasse in uw fysieke omgeving.

    a.  Gebruik de gedetailleerde stappen van PLTW voor het downloaden van installatie bestanden en het installeren van de vereiste software.

    > [!NOTE]    
    > Wanneer u de toepassingen van auto Desk installeert, moet de computer waarop u auto Desk installeert, kunnen communiceren met uw licentie server (in de installatie wizard van auto Desk wordt u gevraagd de computer naam op te geven van de computer waarop de licentie server wordt gehost).  Als u uw licentie server op een virtuele Azure-machine host, moet u mogelijk wachten op de installatie van auto Desk op de sjabloon machine van het Lab zodat de wizard installatie van auto Desk toegang heeft tot uw licentie server

    b.  OneDrive (of andere back-upopties die uw school kan gebruiken) [installeren en configureren](./how-to-prepare-windows-template.md#install-and-configure-onedrive) .
    
    c.  [Windows-updates installeren en configureren](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Upload de aangepaste installatie kopie naar de [Galerie met gedeelde afbeeldingen die is gekoppeld aan uw Lab-account](./how-to-attach-detach-shared-image-gallery.md).

1.  Maak een lab en selecteer de aangepaste installatie kopie die u in de vorige stap hebt geüpload.

1.  Nadat het lab is gemaakt, start en maakt u verbinding met de sjabloon machine om te controleren of de installatie kopie naar verwachting werkt.

1.  Ten slotte publiceert u de sjabloon machine om de Vm's van de studenten te maken.

## <a name="student-devices"></a>Studenten apparaten
Uw studenten kunnen verbinding maken met hun Lab-Vm's van Windows\Mac computers en Chromebooks.  Hier vindt u koppelingen naar instructies voor elk van deze opties:

- [Verbinding maken vanaf Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Verbinding maken vanaf Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Verbinding maken vanaf Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Kosten
Laten we een mogelijke schatting van de kosten voor de bovenstaande PLTW-klassen best uren.  Deze schatting omvat niet de kosten voor het uitvoeren van een licentie server of voor het gebruik van de galerie voor gedeelde installatie kopieën.  We gebruiken een klasse van 25 studenten.  Er zijn 20 uur geplande tijd voor de klasse.  Daarnaast krijgt elke student tien uur quota voor huis werk of toewijzingen buiten de geplande tijd.  Raadpleeg de onderstaande kosten ramingen voor de grootte van de **grote** en **kleine GPU (visualisatie)** .

- **Grote VM**

    25 studenten x (20 geplande uren + 10 quotum uren) x 70 Lab-eenheden x 0,01 USD per uur = 525,00 USD

- **Kleine GPU (visualisatie)**

    25 studenten x (20 geplande uren + 10 quotum uren) x 160 Lab-eenheden x 0,01 USD per uur = 1200,00 USD

> [!IMPORTANT] 
> De schatting van de kosten is alleen bedoeld als voor beeld.  Zie [Azure Lab Services prijzen](https://azure.microsoft.com/pricing/details/lab-services/)voor actuele Details over prijzen.

> [!NOTE] 
> Veel van de PLTW-klassen gebruiken toepassingen die toegankelijk zijn via een browser, zoals MIT app Inventory.  Deze browser toepassingen vereisen geen snelle CPU of GPU en kunnen worden geopend vanaf elk apparaat met een Internet verbinding.  Wanneer studenten gebruikmaken van deze typen toepassingen, raden we u aan de browser op hun fysieke apparaat te gebruiken in plaats van de browser op hun Lab-Vm's te gebruiken.  Zo kunt u kosten besparen door alleen de Lab-Vm's te gebruiken voor toepassingen waarvoor een snelle CPU of GPU is vereist.

## <a name="next-steps"></a>Volgende stappen
De volgende stappen zijn gebruikelijk voor het instellen van elk lab:

- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users).