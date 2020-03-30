---
title: Capaciteit plannen voor herstel van hyperv's na spoed met Azure Site Recovery
description: Gebruik dit artikel om de capaciteit te schatten bij het instellen van noodherstel met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 843d5da26d6791cea880e5dfb654fe27b74f5d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73936048"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Capaciteit plannen voor herstel van Hyper-V VM-ramp 

De [Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) voor Hyper-V naar Azure-implementatie biedt het volgende:

* Beoordeling van de geschiktheid van vm's op basis van het aantal schijven, schijfgrootte, IOPS, churn en enkele VM-kenmerken
* Vereiste netwerkbandbreedte versus RPO-evaluatie
* Vereisten voor Azure-infrastructuur
* On-premises infrastructuurvereisten
* Hulp bij eerste replicatie via batch
* Geschatte totale kosten voor herstel van rampen voor Azure


Azure Site Recovery Capacity Planner helpt u bij het bepalen van uw capaciteitsvereisten wanneer u Hyper-V Vm's nabootst met Azure Site Recovery.

Gebruik Site Recovery Capacity Planner om uw bronomgeving en workloads te analyseren. Hiermee u de bandbreedtebehoeften, de serverbronnen die u nodig hebt voor de bronlocatie en de resources (zoals VM's en opslag) inschatten die u op de doellocatie nodig hebt.

U het gereedschap in twee modi uitvoeren:

* **Snelle planning:** biedt netwerk- en serverprojecties op basis van een gemiddeld aantal VM's, schijven, opslag en wijzigingssnelheid.
* **Gedetailleerde planning**: Geeft details over elke werkbelasting op VM-niveau. Analyseer VM-compatibiliteit en ontvang netwerk- en serverprojecties.

## <a name="before-you-start"></a>Voordat u begint

* Verzamel informatie over uw omgeving, inclusief VM's, schijven per VM, opslag per schijf.
* Identificeer uw dagelijkse wijzigingssnelheid (churn) voor gerepliceerde gegevens. Download de [Hyper-V capaciteitsplanningstool](https://www.microsoft.com/download/details.aspx?id=39057) om de wijzigingssnelheid te krijgen. [Meer informatie](site-recovery-capacity-planning-for-hyper-v-replication.md) over dit hulpprogramma. We raden u aan deze tool gedurende een week uit te voeren om gemiddelden vast te leggen.


## <a name="run-the-quick-planner"></a>De Snelplanner uitvoeren
1. Download en open [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Je moet macro's uitvoeren. Wanneer u wordt gevraagd, maakt u selecties om bewerking en inhoud in te schakelen.

2. Selecteer **Snelplanner**in het vak Lijst **met een plannertype** selecteren .

   ![Aan de slag](./media/site-recovery-capacity-planner/getting-started.png)

3. Voer op het werkblad **Capaciteitsplanner** de vereiste gegevens in. Vul alle velden in rood in de volgende schermafbeelding:

   a. Kies **in Uw scenario**selecteren de optie **Hyper-V naar Azure** of **VMware/Physical naar Azure**.

   b. Voer **in De gemiddelde dagelijkse snelheid van gegevenswijziging (%)** de informatie in die u verzamelt met behulp van het hulpprogramma voor [capaciteitsplanning van Hyper V](site-recovery-capacity-planning-for-hyper-v-replication.md) of Site Recovery Deployment [Planner](./site-recovery-deployment-planner.md).

   c. De **compressie-instelling** wordt niet gebruikt wanneer u Hyper-V VM's repliceert naar Azure. Gebruik voor compressie een apparaat van derden, zoals Riverbed.

   d. Geef **in Bewaar in dagen**op hoe lang het bewaren van replica's duurt.

   e. Voer **in aantal uren waarin de eerste replicatie voor de batch van virtuele machines moet worden voltooid** en aantal virtuele machines per **initiële replicatiebatch,** instellingen in die worden gebruikt om de eerste replicatievereisten te berekenen. Wanneer Siteherstel wordt geïmplementeerd, wordt de volledige oorspronkelijke gegevensset geüpload.

   ![Invoer](./media/site-recovery-capacity-planner/inputs.png)

4. Nadat u de waarden voor de bronomgeving hebt ingevoerd, omvat de weergegeven uitvoer:

   * **Bandbreedte die nodig is voor deltareplicatie (in Megabits/sec):** Netwerkbandbreedte voor deltareplicatie wordt berekend op basis van de gemiddelde dagelijkse gegevenswijzigingssnelheid.
   * **Bandbreedte die nodig is voor de eerste replicatie (in Megabits/sec):** Netwerkbandbreedte voor de eerste replicatie wordt berekend op basis van de initiële replicatiewaarden die u invoert.
   * **Vereiste opslag (in GB's):** de totale Azure-opslag die nodig is.
   * **Totaal IOPS over standaardopslag:** het nummer wordt berekend op basis van de grootte van de 8K IOPS-eenheid op de totale standaardopslagaccounts. Voor de Quick Planner wordt het getal berekend op basis van alle bron-VM-schijven en de dagelijkse gegevenswijzigingssnelheid. Voor de gedetailleerde planner wordt het getal berekend op basis van het totale aantal VM's dat is toegewezen aan standaard Azure VM's en de gegevenswijzigingssnelheid op die VM's.
   * **Aantal vereiste standaardopslagaccounts:** het totale aantal standaardopslagaccounts dat nodig is om de VM's te beschermen. Een standaard opslagaccount kan maximaal 20.000 IOPS bevatten voor alle VM's in standaardopslag. Per schijf wordt maximaal 500 IOPS ondersteund.
   * **Aantal Blob-schijven vereist:** het aantal schijven dat is gemaakt op Azure-opslag.
   * **Aantal vereiste premiumaccounts**: het totale aantal premium-opslagaccounts dat nodig is om de VM's te beschermen. Een bron-VM met een hoge IOPS (meer dan 20.000) heeft een premium opslagaccount nodig. Een premium opslagaccount kan maximaal 80.000 IOPS bevatten.
   * **Totaal IOPS over premium opslag:** Het aantal wordt berekend op basis van de grootte van 256K IOPS-eenheden op de totale premium-opslagrekeningen. Voor de Quick Planner wordt het getal berekend op basis van alle bron-VM-schijven en de dagelijkse gegevenswijzigingssnelheid. Voor de gedetailleerde planner wordt het getal berekend op basis van het totale aantal VM's dat is toegewezen aan premium Azure VM's (DS- en GS-reeksen) en de gegevenswijzigingssnelheid op die VM's.
   * **Aantal configuratieservers vereist:** geeft aan hoeveel configuratieservers nodig zijn voor de implementatie.
   * **Aantal extra processervers vereist:** geeft aan of er extra processervers nodig zijn, naast de processerver die standaard op de configuratieserver wordt uitgevoerd.
   * **100% extra opslag op de bron:** geeft aan of extra opslag nodig is in de bronlocatie.

      ![Uitvoer](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>De gedetailleerde planner uitvoeren

1. Download en open [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel). Je moet macro's uitvoeren. Wanneer u wordt gevraagd, maakt u selecties om bewerking en inhoud in te schakelen.

2. Selecteer **Gedetailleerde planner** in **Een plannertype**selecteren in het keuzelijstvak.

   ![Handleiding Aan de slag](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Voer op het werkblad **Werkbelastingkwalificatie** de vereiste gegevens in. U moet alle gemarkeerde velden invullen.

   a. Geef in **Processorcores**het totale aantal cores op een bronserver op.

   b. Geef in **Geheugentoewijzing (in GB's)** de RAM-grootte van een bronserver op.

   c. Geef **in Aantal NIC's**het aantal netwerkadapters op een bronserver op.

   d. Geef **in Totaal opslag (in GB)** de totale grootte van de VM-opslag op. Als de bronserver bijvoorbeeld drie schijven heeft met elk 500 GB, is de totale opslaggrootte 1.500 GB.

   e. Geef **in Aantal gekoppelde schijven**het totale aantal schijven van een bronserver op.

   f. Geef **in Schijfcapaciteitsbenutting (%)** de gemiddelde benutting op.

   g. Geef in **de dagelijkse snelheid van gegevenswijziging (%)** de dagelijkse gegevenswijzigingssnelheid van een bronserver op.

   h. Voer in **Azure VM-grootte toewijzen**de Azure VM-grootte in die u wilt toewijzen. Als u dit niet handmatig wilt doen, selecteert u **Compute IaaS VM's**. Als u een handmatige instelling invoert en vervolgens **IaaS VM's berekenen**selecteert, kan de handmatige instelling worden overschreven. Het rekenproces identificeert automatisch de beste overeenkomst op Azure VM-grootte.

   ![Werkblad Werkbelastingkwalificatie](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Als u **Compute IaaS VM's selecteert,** gaat het hier om:

   * Valideert de verplichte ingangen.
   * Berekent IOPS en stelt de beste Azure VM-grootteovereenkomst voor voor elke VM die in aanmerking komt voor replicatie naar Azure. Als een geschikte Azure VM niet kan worden gedetecteerd, wordt er een fout weergegeven. Als het aantal gekoppelde schijven bijvoorbeeld 65 is, wordt een fout weergegeven omdat de hoogste grootte voor een Azure-vm 64 is.
   * Stelt een opslagaccount voor dat kan worden gebruikt voor een Azure VM.
   * Berekent het totale aantal standaardopslagaccounts en premium opslagaccounts dat nodig is voor de werkbelasting. Schuif omlaag om het Azure-opslagtype en het opslagaccount weer te geven dat kan worden gebruikt voor een bronserver.
   * Hiermee vult en sorteert u de rest van de tabel op basis van het vereiste opslagtype (standaard of premium) dat is toegewezen voor een VM en het aantal gekoppelde schijven. Voor alle VM's die voldoen aan de vereisten voor Azure, is de kolom **Is VM gekwalificeerd?** toont **Ja**. Als er geen back-up van een vm kan worden gemaakt naar Azure, wordt er een fout weergegeven.

Kolommen AA naar AE worden uitgevoerd en geven informatie voor elke vm.

![Uitvoerkolommen AA naar AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Voorbeeld
Voor zes VM's met de waarden in de tabel berekent en wijst het hulpprogramma bijvoorbeeld de beste Azure VM-match en de Azure-opslagvereisten toe.

![Opdrachten voor werkbelastingkwalificatie](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Noteer in het voorbeelduitvoer het volgende:

  * De eerste kolom is een validatiekolom voor de VM's, schijven en verloop.
  * Voor vijf VM's zijn twee standaardopslagaccounts en één premium opslagaccount nodig.
  * VM3 komt niet in aanmerking voor bescherming omdat een of meer schijven meer dan 1 TB zijn.
  * VM1 en VM2 kunnen het eerste standaard opslagaccount gebruiken
  * VM4 kan het tweede standaardopslagaccount gebruiken.
  * VM5 en VM6 hebben een premium opslagaccount nodig en beide kunnen één account gebruiken.

    > [!NOTE]
    > IOPS op standaard- en premiumopslag worden berekend op VM-niveau en niet op schijfniveau. Een standaard VM kan tot 500 IOPS per schijf aan. Als IOPS voor een schijf groter is dan 500, hebt u premium opslag nodig. Als IOPS voor een schijf meer dan 500 is, maar IOPS voor de totale VM-schijven binnen de standaard Azure VM-limieten voor ondersteuning valt, kiest de planner een standaard VM en niet de DS- of GS-reeks. (De Azure VM-limieten zijn VM-grootte, aantal schijven, aantal adapters, CPU en geheugen.) U moet de azure-groottecel handmatig bijwerken met de juiste DS- of GS-serie VM.


Nadat alle informatie is ingevoerd, selecteert u **Gegevens verzenden naar de plannertool** om Capaciteitsplanner te openen. Workloads worden gemarkeerd om aan te geven of ze in aanmerking komen voor bescherming.

### <a name="submit-data-in-capacity-planner"></a>Gegevens verzenden in Capaciteitsplanner
1. Wanneer u het werkblad **Capaciteitsplanner** opent, wordt het ingevuld op basis van de opgegeven instellingen. Het woord 'Werkbelasting' wordt weergegeven in de **broncel Infra-invoer** om aan te geven dat de invoer het werkblad **Werkbelastingkwalificatie** is.

2. Als u wijzigingen wilt aanbrengen, moet u het werkblad **Werkbelastingkwalificatie** wijzigen. Selecteer vervolgens **Gegevens opnieuw verzenden naar het planner-hulpprogramma.**

   ![Capaciteitsplanner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over het uitvoeren van](site-recovery-capacity-planning-for-hyper-v-replication.md) het hulpprogramma voor capaciteitsplanning.
