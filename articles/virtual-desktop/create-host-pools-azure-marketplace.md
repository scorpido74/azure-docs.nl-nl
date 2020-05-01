---
title: Windows-hostgroep voor virtuele Bureau bladen Azure Portal-Azure
description: Een Windows-hostgroep voor virtueel bureau blad maken met behulp van de Azure Portal.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: def3ed840d2886aabfce1d1081c94298083fe6d6
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611608"
---
# <a name="tutorial-create-a-host-pool-with-the-azure-portal"></a>Zelf studie: een hostgroep maken met de Azure Portal

>[!IMPORTANT]
>Deze inhoud is van toepassing op de lente 2020-update met Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)als u de versie van het Windows-bureau blad van Virtual Desktop 2019 zonder Azure Resource Manager objecten gebruikt.
>
> De Windows Virtual Desktop lente 2020-update is momenteel beschikbaar als open bare preview. Deze preview-versie is beschikbaar zonder service level agreement. het wordt niet aangeraden deze te gebruiken voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. 
> Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hostgroepen zijn een verzameling van een of meer identieke virtuele machines (Vm's) in Windows virtueel-bureaublad omgevingen. Elke hostgroep kan een app-groep bevatten waarmee gebruikers kunnen communiceren, op dezelfde manier als op een fysiek bureau blad.

Dit artikel begeleidt u stapsgewijs door het installatie proces voor het maken van een hostgroep voor een virtueel-bureaublad omgeving van Windows via de Azure Portal. Deze methode biedt een gebruikers interface op basis van een browser voor het maken van een hostgroep in het virtuele bureau blad van Windows, het maken van een resource groep met Vm's in een Azure-abonnement, het toevoegen van deze Vm's aan het domein van de Azure Active Directory (AD) en het registreren van Vm's met virtuele Windows-Bureau bladen.

## <a name="prerequisites"></a>Vereisten

U moet de volgende para meters invoeren voor het maken van een hostgroep:

- De naam van de VM-installatie kopie
- VM-configuratie
- Domein-en netwerk eigenschappen
- Eigenschappen van Windows-hostgroep voor virtueel bureau blad

U moet ook de volgende zaken kennen:

- Als de bron van de afbeelding die u wilt gebruiken, is. Is het van Azure Gallery of is het een aangepaste installatie kopie?
- Referenties voor domein deelname.

Wanneer u een virtuele Windows-bureau blad-hostgroep maakt met de Azure Resource Manager sjabloon, kunt u een virtuele machine maken vanuit de Azure-galerie, een beheerde installatie kopie of een niet-beheerde installatie kopie. Voor meer informatie over het maken van VM-installatie kopieën raadpleegt u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) en [een beheerde installatie kopie te maken van een gegeneraliseerde vm in azure](../virtual-machines/windows/capture-image-resource.md).

Als u nog geen Azure-abonnement hebt, moet u ervoor zorgen dat u [een account maakt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint met het volgen van deze instructies.

## <a name="begin-the-host-pool-setup-process"></a>Het installatie proces van de hostgroep starten

Beginnen met het maken van de nieuwe hostgroep:

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com/).

2. Voer **Windows virtueel bureau blad** in de zoek balk in, zoek en selecteer **Windows virtueel bureau blad** onder Services.

3. Selecteer op de pagina overzicht van **virtuele Windows-bureau blad** **een hostgroep maken**.

4. Op het tabblad **basis beginselen** selecteert u het juiste abonnement onder project details.

5. Selecteer **nieuwe maken** om een nieuwe resource groep te maken of selecteer een bestaande resource groep in de vervolg keuzelijst.

6. Voer een unieke naam in voor de hostgroep.

7. Selecteer in het veld locatie de regio waar u de hostgroep wilt maken in de vervolg keuzelijst.
   
   Het Azure-geografie dat is gekoppeld aan de regio's die u hebt geselecteerd, is de locatie waar de meta gegevens voor deze hostgroep en de gerelateerde objecten worden opgeslagen. Zorg ervoor dat u de regio's in de geografie kiest waarvan u wilt dat de meta gegevens van de service worden opgeslagen in.

     ![Een scherm opname van de Azure Portal het locatie veld met de locatie VS-Oost geselecteerd weer te geven. Naast het veld ziet u tekst: "meta gegevens worden opgeslagen in VS-Oost."](media/portal-location-field.png)

8. Selecteer onder Type hostgroep of uw hostgroep **persoonlijk** of **gegroepeerd**moet zijn.

    - Als u kiest voor **persoonlijk**, selecteert u vervolgens **automatisch** of **direct** in het veld toewijzings type.

      ![Een scherm afbeelding van de vervolg keuzelijst veld toewijzings type. De gebruiker heeft automatisch geselecteerd.](media/assignment-type-field.png)

9. Als u kiest voor **pooled**, voert u de volgende gegevens in:

     - Voor **maximale sessie limiet**voert u het maximum aantal gebruikers in dat door de taak verdeling voor één sessiehost moet worden gebalanceerd.
     - Kies voor de taakverdelings **algoritme**breedte-eerst of diepte-eerst op basis van uw gebruiks patroon.

       ![Een scherm afbeelding van het veld toewijzings type waarvoor ' gegroepeerd ' is geselecteerd. De gebruiker bevindt zich boven de breedte van de cursor in de vervolg keuzelijst taak verdeling.](media/pooled-assignment-type.png)

10. Selecteer **volgende: VM-Details**.

11. Als u al virtuele machines hebt gemaakt en deze wilt gebruiken in combi natie met de nieuwe hostgroep, selecteert u **Nee**. Als u nieuwe virtuele machines wilt maken en deze wilt registreren bij de nieuwe hostgroep, selecteert u **Ja**.

Nu u het eerste deel hebt voltooid, gaan we verder met het volgende deel van het installatie proces waar de virtuele machine wordt gemaakt.

## <a name="virtual-machine-details"></a>Details van virtuele machine

Nu het eerste deel wordt gebruikt, moet u uw virtuele machine instellen.

De virtuele machine instellen binnen het installatie proces van de hostgroep:

1. Onder resource groep kiest u de resource groep waarin u de virtuele machines wilt maken. Dit kan een andere resource groep zijn dan het account dat u hebt gebruikt voor de hostgroep.

2. Kies de **virtuele-machine regio** waar u de virtuele machines wilt maken. Ze kunnen hetzelfde zijn of verschillen van de regio die u hebt geselecteerd voor de hostgroep.

3. Kies vervolgens de grootte van de virtuele machine die u wilt maken. U kunt de standaard grootte blijven gebruiken of **de grootte wijzigen om de** grootte te wijzigen. Als u **grootte wijzigen**selecteert in het venster dat wordt weer gegeven, kiest u de grootte van de virtuele machine die geschikt is voor uw werk belasting.

4. Geef bij aantal Vm's het aantal Vm's op dat u voor de hostgroep wilt maken.

    >[!NOTE]
    >Het installatie proces kan Maxi maal 400 Vm's maken tijdens het instellen van uw hostgroep, en tijdens elk installatie proces van de virtuele machine worden vier objecten in de resource groep gemaakt. Omdat het quotum voor het maken van uw abonnement niet wordt gecontroleerd, controleert u of het aantal Vm's dat u invoert binnen de Azure-VM en de API-limieten voor uw resource groep en-abonnement. U kunt meer Vm's toevoegen nadat u klaar bent met het maken van de hostgroep.

5. Daarna geeft u een **naam voorvoegsel** op om de virtuele machines die het installatie proces maakt een naam te geven. Het achtervoegsel bevat `-` een nummer dat begint bij 0.

6. Kies vervolgens de installatie kopie die moet worden gebruikt om de virtuele machine te maken. U kunt kiezen uit de **Galerie** of de **opslag-BLOB**.

    - Als u **Galerie**kiest, selecteert u een van de aanbevolen installatie kopieën in de vervolg keuzelijst:

      - Windows 10 Enter prise meerdere sessies, versie 1909 + Office 365 ProPlus – gen 1
      - Windows 10 Enter prise-meerdere sessies, versie 1909 – gen 1
      - Windows Server 2019 Data Center-gen1

     Als u de gewenste installatie kopie niet ziet, selecteert u **alle afbeeldingen en schijven bladeren**, waarmee u een andere afbeelding in uw galerie of een installatie kopie van micro soft en andere uitgevers kunt selecteren.

     ![Een scherm afbeelding van de Marketplace met een lijst met installatie kopieën van micro soft weer gegeven.](media/marketplace-images.png)

     U kunt ook naar **mijn items** gaan en een aangepaste installatie kopie kiezen die u al hebt geüpload.

     ![Een scherm afbeelding van het tabblad mijn items.](media/my-items.png)

    - Als u **opslag-BLOB**kiest, kunt u gebruikmaken van uw eigen installatie kopie-build via Hyper-V of op een Azure VM. U hoeft alleen de locatie van de installatie kopie in de BLOB voor opslag in te voeren als een URI.

7. Kies welk type besturingssysteem schijven u wilt gebruiken voor uw Vm's: Standard-SSD, Premium-SSD of Standard-HDD.

8. Onder netwerk en beveiliging selecteert u het virtuele netwerk en het subnet waar u de virtuele machines wilt plaatsen die u maakt. Zorg ervoor dat het virtuele netwerk verbinding kan maken met de domein controller omdat u de virtuele machines in het virtuele netwerk moet toevoegen aan het domein. Selecteer vervolgens of u een openbaar IP-adres voor de virtuele machines wilt of niet. We raden u aan om **Nee**te selecteren, omdat een persoonlijk IP-adres veiliger is.

9. Selecteer wat voor soort beveiligings groep u wilt: **Basic**, **Advanced**of **none**.

    Als u **Basic**selecteert, moet u aangeven of u een binnenkomende poort wilt openen. Als u **Ja**selecteert, kiest u in de lijst met standaard poorten om binnenkomende verbindingen toe te staan.

    >[!NOTE]
    >Voor een betere beveiliging wordt u aangeraden geen open bare binnenkomende poorten te openen.

    ![Een scherm opname van de pagina beveiligings groep waarop een lijst met beschik bare poorten in een vervolg keuzelijst wordt weer gegeven.](media/available-ports.png)
    
    Als u **Geavanceerd**kiest, selecteert u een bestaande netwerk beveiligings groep die u al hebt geconfigureerd.

10. Selecteer daarna of u wilt dat de virtuele machines worden gekoppeld aan een specifiek domein en een organisatie-eenheid. Als u **Ja**kiest, geeft u het domein op dat u wilt toevoegen. U kunt ook een specifieke organisatie-eenheid toevoegen waarin u wilt dat de virtuele machines zich bevindt.

11. Voer onder Administrator-account de referenties in voor de Active Directory-domein beheerder van het virtuele netwerk dat u hebt geselecteerd.

12. Selecteer een **werk ruimte**.

Met deze optie kunt u de volgende fase voor het instellen van uw hostgroep nu starten: uw app-groep registreren bij een werk ruimte.

## <a name="workspace-information"></a>Werkruimtegegevens

Met het installatie proces van de hostgroep wordt standaard een bureaublad toepassings groep gemaakt. Als u wilt dat de hostgroep werkt zoals bedoeld, moet u deze app-groep publiceren naar gebruikers of gebruikers groepen en moet u de app-groep registreren bij een werk ruimte. 

De bureau blad-app-groep registreren bij een werk ruimte:

1. Selecteer **Ja**.

   Als u **Nee**selecteert, kunt u de app-groep later registreren, maar we raden u aan de registratie van de werk ruimte in te stellen zodra u dit kunt doen, zodat uw hostgroep goed werkt.

2. Kies vervolgens of u een nieuwe werk ruimte wilt maken of selecteren in bestaande werk ruimten. Alleen werk ruimten die zijn gemaakt op dezelfde locatie als de hostgroep, kunnen de app-groep registreren bij.

3. U kunt ook **labels**selecteren.

    Hier kunt u Tags toevoegen, zodat u de objecten kunt groeperen met meta gegevens zodat u het voor uw beheerders gemakkelijker maakt.

4. Wanneer u klaar bent, selecteert u **controleren + maken**. 

     >[!NOTE]
     >Het validatie proces controleren en maken controleert niet of uw wacht woord voldoet aan de beveiligings normen of of uw architectuur juist is. u moet daarom zelf controleren of er problemen zijn met een van deze dingen. 

5. Bekijk de informatie over uw implementatie om te controleren of alles juist is. Als u gereed bent, selecteert u **Maken**. Hiermee wordt het implementatie proces gestart, waardoor de volgende objecten worden gemaakt:

     - Uw nieuwe hostgroep.
     - Een bureau blad-app-groep.
     - Een werk ruimte, als u ervoor hebt gekozen om deze te maken.
     - Als u ervoor kiest om de groep bureau blad-apps te registreren, wordt de registratie voltooid
     - Virtuele machines, als u deze hebt gemaakt, die zijn gekoppeld aan het domein en die zijn geregistreerd bij de nieuwe hostgroep.
     - Een download koppeling voor een Azure resource management-sjabloon op basis van uw configuratie.

Daarna bent u klaar.

## <a name="next-steps"></a>Volgende stappen

Nu u uw hostgroep hebt gemaakt, kunt u deze vullen met RemoteApp-program ma's. Voor meer informatie over het beheren van apps in Windows Virtual Desktop, kop naar onze volgende zelf studie:

> [!div class="nextstepaction"]
> [Zelf studie app-groepen beheren](./manage-app-groups.md)
