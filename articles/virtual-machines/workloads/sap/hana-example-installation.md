---
title: HANA installeren op SAP HANA op Azure (Grote exemplaren) | Microsoft Documenten
description: HANA installeren op SAP HANA op Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 023f32fce01ffbd974b182fa89fd604e62332936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617214"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>HANA installeren op SAP HANA op Azure (grote exemplaren)

Als u HANA op SAP HANA op Azure (Large Instances) wilt installeren, moet u eerst het volgende doen:
- U verstrekt Microsoft alle gegevens die u implementeren op een SAP HANA Large Instance.
- U ontvangt de SAP HANA Large Instance van Microsoft.
- U maakt een virtueel Azure-netwerk dat is verbonden met uw on-premises netwerk.
- U verbindt het ExpressRoute-circuit voor HANA Large Instances met hetzelfde virtuele Azure-netwerk.
- U installeert een Virtuele Azure-machine die u gebruikt als springbox voor HANA Large Instances.
- Je zorgt ervoor dat je vanuit de jump box verbinding maken met je HANA Large Instance unit, en vice versa.
- U controleert of alle benodigde pakketten en patches zijn geïnstalleerd.
- U leest de SAP notities en documentatie over HANA installatie op het besturingssysteem dat u gebruikt. Zorg ervoor dat de HANA release naar keuze wordt ondersteund op de release van het besturingssysteem.

De volgende sectie toont een voorbeeld van het downloaden van de HANA installatiepakketten naar de jump box virtuele machine. In dit geval is het besturingssysteem Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Download de SAP HANA installatiebits
De HANA Large Instance units zijn niet direct verbonden met het internet. U de installatiepakketten van SAP niet direct downloaden naar de virtuele machine van HANA Large Instance. In plaats daarvan download je de pakketten naar de virtuele machine van de jump box.

Je hebt een SAP S-gebruiker of andere gebruiker nodig, waarmee je toegang hebt tot de SAP Marketplace.

1. Meld u aan en ga naar [SAP Service Marketplace.](https://support.sap.com/en/index.html) Selecteer**Software-installaties** >  **downloaden** > en upgraden**op alfabetische index**. Selecteer vervolgens **Onder H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Installatie**. Download de bestanden in de volgende schermafbeelding.

   ![Schermafbeelding van de bestanden die u wilt downloaden](./media/hana-installation/image16_download_hana.PNG)

2. In dit voorbeeld hebben we SAP HANA 2.0 installatiepakketten gedownload. Vouw op de virtuele machine van de Azure jump box de zelf-extraherende archieven uit in de map zoals hieronder wordt weergegeven.

   ![Schermafbeelding van zelf-uitpakkend archief](./media/hana-installation/image17_extract_hana.PNG)

3. Als de archieven worden geëxtraheerd, kopieer de directory gemaakt door de extractie (in dit geval, 51052030). Kopieer de map van de HANA Large Instance unit /hana/shared volume naar een directory die u hebt gemaakt.

   > [!Important]
   > Kopieer de installatiepakketten niet naar de root of boot LUN, omdat de ruimte beperkt is en ook door andere processen moet worden gebruikt.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>SAP HANA installeren op de HANA Large Instance unit
Als u SAP HANA wilt installeren, meldt u zich aan als gebruikersroot. Alleen root heeft genoeg machtigingen om SAP HANA te installeren. Machtigingen instellen voor de map die u hebt gekopieerd naar /hana/gedeeld.

```
chmod –R 744 <Installation bits folder>
```

Als u SAP HANA wilt installeren met behulp van de grafische gebruikersinterface-instelling, moet het gtk2-pakket worden geïnstalleerd op HANA Large Instances. Voer de volgende opdracht uit om te controleren of deze is geïnstalleerd:

```
rpm –qa | grep gtk2
```

(In latere stappen tonen we de SAP HANA-setup met de grafische gebruikersinterface.)

Ga naar de installatiemap en navigeer naar de submap HDB_LCM_LINUX_X86_64. 

Ga uit die map:

```
./hdblcmgui 
```
Op dit punt, je vooruitgang door middel van een reeks van schermen waarin u de gegevens voor de installatie. In dit voorbeeld installeren we de SAP HANA-databaseserver en de SAP HANA-clientcomponenten. Daarom is onze selectie **SAP HANA Database.**

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, met SAP HANA-database geselecteerd](./media/hana-installation/image18_hana_selection.PNG)

Selecteer nieuw systeem **installeren**in het volgende scherm .

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, waarbij Nieuw systeem installeren is geselecteerd](./media/hana-installation/image19_select_new.PNG)

Selecteer vervolgens een aantal extra onderdelen die u installeren.

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, met lijst met extra componenten](./media/hana-installation/image20_select_components.PNG)

Hier kiezen we voor de SAP HANA Client en de SAP HANA Studio. We installeren ook een scale-up exemplaar. Kies vervolgens **Single-Host System**. 

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, waarbij single hostsysteem is geselecteerd](./media/hana-installation/image21_single_host.PNG)

Geef vervolgens wat gegevens op.

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, met de velden systeemeigenschappen om te definiëren](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Als HANA System ID (SID) moet u dezelfde SID leveren als microsoft toen u de HANA Large Instance-implementatie hebt besteld. Als u een andere SID kiest, mislukt de installatie als gevolg van toegangsmachtigingsproblemen op de verschillende volumes.

Gebruik voor het installatiepad de /hana/shared directory. In de volgende stap geef je de locaties voor de HANA databestanden en de HANA logbestanden.


![Schermafbeelding van het scherm SAP HANA Lifecycle Management, met velden gegevens en logboekgebieden](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> De SID die u hebt opgegeven wanneer u systeemeigenschappen (twee schermen geleden) hebt gedefinieerd, moet overeenkomen met de SID van de bevestigingspunten. Als er een mismatch is, ga dan terug en pas de SID aan de waarde die je hebt op de mount punten.

Bekijk in de volgende stap de hostnaam en corrigeer deze uiteindelijk. 

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, met hostnaam](./media/hana-installation/image24_review_host_name.PNG)

In de volgende stap moet u ook gegevens ophalen die u aan Microsoft hebt gegeven toen u de HANA Large Instance-implementatie hebt besteld. 

![Schermafbeelding van SAP HANA Lifecycle Management, met systeembeheerdersvelden om te definiëren](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Geef dezelfde **gebruikers-id** en **id van de gebruikersgroep als** u aan Microsoft hebt verstrekt, terwijl u de implementatie van de eenheid bestelt. Anders mislukt de installatie van SAP HANA op de HANA Large Instance-eenheid.

De volgende twee schermen worden hier niet getoond. Hiermee u het wachtwoord opgeven voor de systeemgebruiker van de SAP HANA-database en het wachtwoord voor de sapadm-gebruiker. Dit laatste wordt gebruikt voor de SAP Host Agent die wordt geïnstalleerd als onderdeel van de SAP HANA database instance.

Nadat u het wachtwoord hebt gedefinieerd, ziet u een bevestigingsscherm. controleer alle vermelde gegevens en ga verder met de installatie. U bereikt een voortgangsscherm dat de voortgang van de installatie documenteert, zoals deze:

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, met indicatoren voor de voortgang van de installatie](./media/hana-installation/image27_show_progress.PNG)

Als de installatie is voltooid, ziet u een scherm als dit:

![Schermafbeelding van het scherm SAP HANA Lifecycle Management, waarin wordt aangegeven dat de installatie is voltooid](./media/hana-installation/image28_install_finished.PNG)

De SAP HANA-instantie moet nu operationeel zijn en klaar zijn voor gebruik. Je moet in staat zijn om verbinding te maken met het van SAP HANA Studio. Zorg er ook voor dat u de nieuwste updates controleert en toepast.


## <a name="next-steps"></a>Volgende stappen

- [SAP HANA Large Instances hoge beschikbaarheid en disaster recovery op Azure](hana-overview-high-availability-disaster-recovery.md)

