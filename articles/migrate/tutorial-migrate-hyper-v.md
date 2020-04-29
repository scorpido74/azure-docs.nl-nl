---
title: Virtuele Hyper-V-machines migreren naar Azure met Azure Migrate server migratie
description: Meer informatie over het migreren van on-premises virtuele Hyper-V-machines naar Azure met Azure Migrate server migratie
ms.topic: tutorial
ms.date: 04/15/2020
ms.custom:
- MVC
- fasttrack-edit
ms.openlocfilehash: 6b9732aab9e3fe0d26b4c572efe87c3a9d3e29f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81535346"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Hyper-V-VM's migreren naar Azure 

In dit artikel wordt beschreven hoe u on-premises virtuele Hyper-V-machines naar Azure migreert met behulp van migratie zonder agent met de Azure Migrate: hulp programma voor server migratie.

[Azure migrate](migrate-services-overview.md) biedt een centrale hub voor het bijhouden van de detectie, beoordeling en migratie van uw on-premises apps en workloads, en persoonlijke/open bare Cloud-Vm's naar Azure. De hub biedt Azure Migrate-hulpprogram ma's voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden.

Deze zelf studie is de derde in een serie die laat zien hoe u Hyper-V kunt beoordelen en migreren naar Azure met Azure Migrate server-evaluatie en server migratie. In deze zelfstudie leert u het volgende:


> [!div class="checklist"]
> * Azure en uw on-premises Hyper-V-omgeving voorbereiden
> * Stel de bron omgeving in.
> * Stel de doelomgeving in.
> * Schakel replicatie in.
> * Voer een test migratie uit om te controleren of alles werkt zoals verwacht.
> * Voer een volledige migratie naar Azure uit.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/pricing/free-trial/) aan voordat u begint.


## <a name="prerequisites"></a>Vereisten

Voordat u aan deze zelfstudie begint, dient u eerst:

1. [Controleer](hyper-v-migration-architecture.md) de Hyper-V-migratie architectuur.
2. [Controleren](migrate-support-matrix-hyper-v-migration.md#hyper-v-hosts) De vereisten voor de hyper-v-host en de Azure-Url's die de Hyper-V-hosts moeten openen.
3. [Bekijk](migrate-support-matrix-hyper-v-migration.md#hyper-v-vms) de vereisten voor virtuele Hyper-V-machines die u wilt migreren. Virtuele Hyper-V-machines moeten voldoen aan de vereisten van de [Azure-VM](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).
2. U wordt aangeraden de vorige zelf studies in deze serie te volt ooien. De [eerste zelf studie](tutorial-prepare-hyper-v.md) laat zien hoe u Azure en Hyper-V instelt voor migratie. De tweede zelf studie laat zien hoe u [Hyper-V-Vm's kunt beoordelen] (zelf studie-evaluatie-hyper-v.md vóór migratie met behulp van Azure Migrate: Server beoordeling. 
    > [!NOTE]
    > We raden u aan om een evaluatie uit te voeren, maar u hoeft geen beoordeling te verrichten voordat u virtuele machines migreert.
    > Voor de migratie van Hyper-V-Vm's, Azure Migrate: Server migratie voert software agents (Microsoft Azure Site Recovery provider en Microsoft Azure Recovery Service agent) op Hyper-V-hosts of cluster knooppunten uit om gegevens te organiseren en te repliceren naar Azure Migrate. Het [Azure migrate apparaat](migrate-appliance.md) wordt niet gebruikt voor de migratie van Hyper-V.

3. Zorg ervoor dat aan uw Azure-account de rol Inzender voor virtuele machines is toegewezen, zodat u gemachtigd bent om:

    - Het maken van een VM in de geselecteerde resourcegroep.
    - Het maken van een VM in het geselecteerde virtuele netwerk.
    - Schrijven naar een door Azure beheerde schijf.
4. [Stel een Azure-netwerk](../virtual-network/manage-virtual-network.md#create-a-virtual-network)in. Wanneer u migreert naar Azure, worden de gemaakte Azure-Vm's toegevoegd aan een Azure-netwerk dat u opgeeft wanneer u de migratie instelt.

## <a name="add-the-azure-migrateserver-migration-tool"></a>Voeg het Azure Migrate: hulp programma voor server migratie toe

Voeg het Azure Migrate: hulp programma voor server migratie toe.

- Als u de tweede zelf studie hebt gevolgd om [VMware-vm's te beoordelen](/tutorial-assess-hyper-v.md), hebt u al een Azure migrate project ingesteld en kunt u het hulp programma nu toevoegen.
- Als u de tweede zelf studie niet hebt gevolgd[, volgt u deze instructies](how-to-add-tool-first-time.md) om een Azure migrate project in te stellen. U voegt de Azure Migrate: hulp programma voor server migratie toe wanneer u het project maakt.

Als u een project hebt ingesteld, voegt u het hulp programma als volgt toe:

1. Klik in het Azure Migrate project op **overzicht**. 
2. Klik in **servers detecteren, evalueren en migratie**op **servers beoordelen en migreren**.
3. In **migratie hulpprogramma's**selecteert u **hier om een migratie hulpmiddel toe te voegen wanneer u klaar bent om te migreren**.

    ![Selecteer een hulp programma](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. Selecteer in de lijst met hulpprogram ma's **Azure migrate:** > **hulp programma** server migratie toevoegen

    ![Hulpprogramma voor de migratie van servers](./media/tutorial-migrate-hyper-v/server-migration-tool.png)

## <a name="prepare-hyper-v-hosts"></a>Hyper-V-hosts voorbereiden


1. Klik in het Azure Migrate project > **servers**in **Azure migrate: Server migratie**op **ontdekken**.
2. In **Discover-machines** > **zijn uw machines gevirtualiseerd?**, selecteert u **Ja, met Hyper-V**.
3. Selecteer in **doel regio**de Azure-regio waarnaar u de machines wilt migreren.
6. Selecteer **controleren of de doel regio voor de migratie de regio naam is**.
7. Klik op **resources maken**. Hiermee maakt u een Azure Site Recovery kluis op de achtergrond.
    - Als u migratie al hebt ingesteld met Azure Migrate server migratie, wordt deze optie niet weer gegeven omdat er eerder resources zijn ingesteld.
    - U kunt de doel regio voor dit project niet wijzigen nadat u op deze knop hebt geklikt.
    - Alle volgende migraties zijn naar deze regio.
    
8. In de voor **bereide hyper-v-hostservers**downloadt u de hyper-v-replicatie provider en het registratie sleutel bestand.
    - De registratie sleutel is vereist om de Hyper-V-host te registreren bij Azure Migrate server migratie.
    - De sleutel blijft vijf dagen na het genereren ervan geldig.

    ![Provider en sleutel downloaden](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Kopieer het installatie bestand van de provider en het registratie sleutel bestand naar elke Hyper-V-host (of cluster knooppunt) waarop de Vm's worden uitgevoerd die u wilt repliceren.
5. Voer het installatie bestand van de provider uit op elke host, zoals beschreven in de volgende procedure.
6. Nadat u de provider op hosts hebt geïnstalleerd, klikt u in **computers detecteren**op **registratie volt ooien**.

    ![Registratie volt ooien](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Het kan tot vijf tien minuten duren nadat de registratie is voltooid totdat gedetecteerde Vm's worden weer gegeven in Azure Migrate server migratie. Naarmate er Vm's worden gedetecteerd, nemen het aantal **gedetecteerde servers** toe.

![Gedetecteerde servers](./media/tutorial-migrate-hyper-v/discovered-servers.png)


## <a name="replicate-hyper-v-vms"></a>Virtuele Hyper-V-machines repliceren

Als de detectie is voltooid, kunt u beginnen met de replicatie van virtuele Hyper-V-machines naar Azure.

> [!NOTE]
> U kunt Maxi maal 10 computers tegelijk repliceren. Als u meer wilt repliceren, repliceert u ze tegelijkertijd in batches van 10.

1. Klik in het Azure Migrate project > **servers**, **Azure migrate: Server migratie**op **repliceren**.
2. In **repliceren**, > **bron instellingen** > **zijn uw machines gevirtualiseerd?**, selecteert u **Ja, met Hyper-V**. Klik vervolgens op **volgende: virtuele machines**.
3. Selecteer in **Virtuele machines** de machines die u wilt repliceren.
    - Als u een evaluatie voor de VM's hebt uitgevoerd, kunt u aanbevelingen voor de VM-grootte en het schijftype (premium/standard) toepassen vanuit de resultaten. Als u dit wilt doen, selecteert u in **Wilt u de migratie-instellingen van een Azure Migrate-evaluatie importeren?** de optie **Ja**.
    - Als u geen evaluatie hebt uitgevoerd of als u de instellingen daarvan niet wilt gebruiken, selecteert u de optie **Nee**.
    - Als u hebt geselecteerd dat u de evaluatie wilt gebruiken, selecteert u de VM-groep en vervolgens de naam van de evaluatie.

        ![Evaluatie selecteren](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. Zoek in **Virtuele machines** naar eigen inzicht naar VM's en controleer elke VM die u wilt migreren. Klik vervolgens op **volgende: doel instellingen**.

    ![Vm's selecteren](./media/tutorial-migrate-hyper-v/select-vms.png)

5. Selecteer in **doel instellingen**de doel regio waarnaar u wilt migreren, het abonnement en de resource groep waarin de virtuele Azure-machines na de migratie zich bevinden.
7. Selecteer in **replicatie opslag account**het Azure Storage account waarin gerepliceerde gegevens worden opgeslagen in Azure.
8. **Virtual Network**selecteert u het Azure VNet/subnet waaraan de Azure-vm's na de migratie moeten worden toegevoegd.
9. In **Azure Hybrid Benefit**:

    - Selecteer **Nee** als u Azure Hybrid Benefit niet wilt toepassen. Klik vervolgens op **volgende**.
    - Selecteer **Ja** als u Windows Server-computers hebt die worden gedekt met actieve softwareverzekering of Windows Server-abonnementen en u het voordeel wilt toepassen op de machines die u migreert. Klik vervolgens op **Volgende**.

    ![Doel instellingen](./media/tutorial-migrate-hyper-v/target-settings.png)

10. Controleer in **Compute** de naam, de grootte, het schijftype van het besturingssysteem en de beschikbaarheidsset van de VM. VM's moeten voldoen aan de [Azure-vereisten](migrate-support-matrix-hyper-v-migration.md#azure-vm-requirements).

    - **VM-grootte**: als u evaluatie aanbevelingen gebruikt, bevat de vervolg keuzelijst VM-grootte de aanbevolen grootte. Anders kiest Azure Migrate een grootte op basis van de dichtstbijzijnde overeenkomst in het Azure-abonnement. U kunt ook handmatig een grootte kiezen in **Azure VM-grootte**. 
    - **Besturingssysteem schijf**: Geef de opstart schijf van het besturings systeem voor de virtuele machine op. De besturingssysteemschijf is de schijf die de bootloader en het installatieprogramma van het besturingssysteem bevat. 
    - **Beschikbaarheidsset**: als de virtuele machine na de migratie moet worden besteld in een Azure-beschikbaarheidsset, geeft u de set op. De set moet zich bevinden in de doelresourcegroep die u voor de migratie opgeeft.

    ![Computer instellingen voor de virtuele machine](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. Geef in **Schijven** op of de VM-schijven moeten worden gerepliceerd in Azure en selecteer het schijftype (standaard SSD/HDD of premium beheerde schijven) in Azure. Klik vervolgens op **Volgende**.
    - U kunt schijven uitsluiten van replicatie.
    - Als u schijven uitsluit, zijn deze na migratie niet beschikbaar in de Azure-VM. 

    ![Disks](./media/tutorial-migrate-hyper-v/disks.png)

10. Controleer in **Replicatie controleren en beginnen** de instellingen en klik op **Repliceren** om de eerste replicatie van de servers te beginnen.

> [!NOTE]
> U kunt replicatie-instellingen op elk gewenst moment bijwerken voordat de replicatie wordt gestart, in**replicerende machines** **beheren** > . De instellingen kunnen niet meer worden gewijzigd nadat de replicatie is begonnen.

## <a name="provisioning-for-the-first-time"></a>De eerste keer inrichten

Als dit de eerste VM is die u repliceert in het Azure Migrate-project, wordt Azure Migrate: door server migratie worden deze resources in dezelfde resource groep automatisch als het project ingericht.

- **Service Bus**: Azure migrate: Server migratie gebruikt de service bus voor het verzenden van replicatie-indelings berichten naar het apparaat.
- **Gateway-opslag account**: Azure migrate: Server migratie gebruikt het opslag account van de gateway om status informatie op te slaan over de virtuele machines die worden gerepliceerd.
- **Opslag account voor logboek registratie**: het Azure migrate apparaat uploadt replicatie logboeken voor vm's naar een logboek opslag account. Azure Migrate past de replicatie gegevens toe op de replica-beheerde schijven.
- **Sleutel kluis**: het Azure migrate-apparaat gebruikt de sleutel kluis voor het beheren van verbindings reeksen voor de service bus en toegangs sleutels voor de opslag accounts die worden gebruikt voor replicatie. U moet de machtigingen instellen die de sleutel kluis nodig heeft om toegang te krijgen tot het opslag account wanneer u [Azure voor bereidingen](tutorial-prepare-hyper-v.md#prepare-azure) voor Hyper-V VM-evaluatie en-migratie maakt. 


## <a name="track-and-monitor"></a>Bijhouden en controleren


- Wanneer **u op replicatie** starten klikt, begint de taak. 
- Wanneer de taak replicatie starten is voltooid, beginnen de machines hun initiële replicatie naar Azure.
- Nadat de initiële replicatie is voltooid, begint de replicatie van verschillen. Incrementele wijzigingen van on-premises schijven worden periodiek gerepliceerd naar Azure.

U kunt de taak status volgen in de portal meldingen.

U kunt de replicatie status controleren door te klikken op **replicerende servers** in **Azure migrate: Server migratie**.
![Replicatie controleren](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Een testmigratie uitvoeren


Wanneer Delta replicatie begint, kunt u een test migratie voor de virtuele machines uitvoeren voordat u een volledige migratie naar Azure uitvoert. We raden u ten zeerste aan om dit ten minste één keer te doen voor elke machine voordat u deze migreert.

- Bij het uitvoeren van een test migratie wordt gecontroleerd of de migratie werkt zoals verwacht, zonder dat dit van invloed is op de on-premises machines, die operationeel blijven, en door gaan met repliceren. 
- Met test migratie wordt de migratie gesimuleerd door een Azure VM te maken met behulp van gerepliceerde gegevens (die meestal worden gemigreerd naar een niet-productie-Azure VNet in uw Azure-abonnement).
- U kunt de gerepliceerde virtuele machine van Azure gebruiken om de migratie te valideren, apps te testen en problemen op te lossen voordat u de volledige migratie uitvoert.

Ga als volgt te werk om een test migratie uit te voeren:


1. In **migratie doelen** > **servers** > **Azure migrate: Server migratie**klikt u op **gemigreerde servers testen**.

     ![Gemigreerde servers testen](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Klik met de rechtermuisknop op de te testen VM en klik vervolgens op **Migratie testen**.

    ![Migratie testen](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. Selecteer bij **test migratie**het virtuele Azure-netwerk waarin de Azure VM na de migratie moet worden gevonden. U kunt het beste een niet-productie virtueel netwerk gebruiken.
4. De taak **Migratie testen** wordt gestart. Houd de taak in portalmeldingen in de gaten.
5. Nadat de migratie is voltooid, bekijkt u de gemigreerde Azure-VM in **Virtuele machines** in de Azure-portal. De machinenaam heeft het achtervoegsel **-Test**.
6. Nadat de test is afgerond, klikt u met de rechtermuisknop op de Azure-VM in **Machines repliceren** en klikt u op **Testmigratie opschonen**.

    ![Migratie opschonen](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Virtuele machines migreren

Nadat u hebt gecontroleerd of de test migratie werkt zoals verwacht, kunt u de on-premises machines migreren.

1. Klik in het Azure migrate project > **servers** > **Azure migrate: Server migratie**op **servers repliceren**.

    ![Servers repliceren](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. Klik in **Machines repliceren** met de rechtermuisknop op de VM > **Migreren**.
3. Selecteer **Migrate** >  **Yes**Ja > **OK**bij het migreren van**virtuele machines afsluiten en het uitvoeren van een geplande migratie zonder gegevens verlies**.
    - Azure Migrate sluit standaard de on-premises VM af en voert de replicatie op aanvraag uit om VM-wijzigingen die sinds de laatste replicatie zijn opgetreden, te synchroniseren. Zo gaan er geen gegevens verloren.
    - Als u de VM niet wilt afsluiten, selecteert u **Nee**
4. Er wordt een migratietaak gestart voor de VM. Volg de taak in Azure-meldingen.
5. Nadat de taak is afgerond, kunt u de VM bekijken en beheren vanaf de pagina **Virtuele machines**.

## <a name="complete-the-migration"></a>Migratie voltooien

1. Nadat de migratie is voltooid, klikt u met de rechter muisknop op de virtuele machine > de **migratie stoppen**. Er gebeurt nu het volgende:
    - Hiermee wordt de replicatie voor de on-premises computer gestopt.
    - Hiermee verwijdert u de computer van het aantal **replicerende servers** in azure migrate: Server migratie.
    - Hiermee wordt de replicatie status informatie voor de virtuele machine opgeschoond.
2. Installeer de Azure VM [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) -of [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) -agent op de gemigreerde computers.
3. Voer correcties van de app uit na de migratie, zoals updates van de databaseverbindingsreeksen en webserverconfiguraties.
4. Voer acceptatietesten van de toepassing en de migratie uit op de gemigreerde toepassing die nu wordt uitgevoerd in Azure.
5. Verdeel het verkeer naar het gemigreerde Azure VM-exemplaar.
6. Verwijder de on-premises VM's uit uw lokale VM-inventaris.
7. Verwijder de on-premises VM's uit de lokale back-ups.
8. Werk eventuele interne documentatie bij met de nieuwe locatie en het nieuwe IP-adres van de Azure VM's. 

## <a name="post-migration-best-practices"></a>Best practices na de migratie

- Voor grotere flexibiliteit:
    - Houd uw gegevens veilig door back-ups van virtuele Azure VM‘s te maken met behulp van de Azure Backup-service. [Meer informatie](../backup/quick-backup-vm-portal.md).
    - Houd workloads continu beschikbaar door Azure VM‘s naar een secundaire regio te repliceren met Site Recovery. [Meer informatie](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Voor betere beveiliging:
    - Vergrendel en beperk de toegang tot inkomend verkeer met [Azure Security Center-just-in-time-beheer](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Beperk het netwerkverkeer naar beheereindpunten met [Netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implementeer [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) om schijven te beveiligen en gegevens te beschermen tegen diefstal en onbevoegde toegang.
    - Lees meer informatie over [IaaS-resources beveiligen](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) en bezoek het [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Voor controle en beheer:
-  Overweeg de implementatie van [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) om uw resourcegebruik en uitgaven te bewaken.


## <a name="next-steps"></a>Volgende stappen

Onderzoek de [reis voor Cloud migratie](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) in het Azure Cloud-acceptatie Framework.
