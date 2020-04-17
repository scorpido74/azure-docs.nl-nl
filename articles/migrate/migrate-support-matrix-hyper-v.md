---
title: Ondersteuning voor Hyper-V-beoordeling in Azure Migrate
description: Meer informatie over ondersteuning voor Hyper-V-beoordeling met Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 990d5026d9621c144c31635fabac4416eb9d20e6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538168"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Ondersteuningsmatrix voor Hyper-V-beoordeling

In dit artikel worden de vereisten en ondersteuningsvereisten samengevat wanneer u Hyper-V VM's beoordeelt op migratie naar Azure, met behulp van het hulpprogramma [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Als u Hyper-V VM's naar Azure wilt migreren, controleert u de [matrix voor migratieondersteuning](migrate-support-matrix-hyper-v-migration.md).

Als u een Hyper-V VM-beoordeling wilt instellen, maakt u een Azure Migrate-project en voegt u het hulpprogramma voor serverbeoordeling toe aan het project. Nadat het hulpprogramma is toegevoegd, implementeert u het [Azure Migrate-toestel](migrate-appliance.md). Het toestel detecteert continu on-premises machines en stuurt machinemetadata en prestatiegegevens naar Azure. Nadat detectie is voltooid, verzamelt u gedetecteerde machines in groepen en voert u een beoordeling uit voor een groep.


## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelingslimieten** | U maximaal 35.000 Hyper-V VM's ontdekken en beoordelen in één [Azure Migrate-project.](migrate-support-matrix.md#azure-migrate-projects)
**Projectlimieten** | U meerdere projecten maken in een Azure-abonnement. Naast Hyper-V VM's kan een project VMware VM's en fysieke servers bevatten, tot aan de beoordelingslimieten voor elk project.
**Detectie** | Het Azure Migrate-toestel kan maximaal 5000 Hyper-V VM's ontdekken.<br/><br/> Het apparaat kan verbinding maken met maximaal 300 Hyper-V-hosts.
**Beoordeling** | U maximaal 35.000 machines in één groep toevoegen.<br/><br/> U maximaal 35.000 VM's beoordelen in één beoordeling voor een groep.

[Meer informatie](concepts-assessment-calculation.md) over beoordelingen.



## <a name="hyper-v-host-requirements"></a>Hyper-V-hostvereisten

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Hyper-V-host**       | De Hyper-V-host kan zelfstandig zijn of in een cluster worden geïmplementeerd.<br/><br/> De Hyper-V-host kan Windows Server 2019, Windows Server 2016 of Windows Server 2012 R2 uitvoeren.<br/> U kunt geen VM's beoordelen die zich bevinden op Hyper-V-hosts waarop Windows Server 2012 wordt uitgevoerd.
| **Machtigingen**           | U hebt beheerdersmachtigingen nodig voor de Hyper-V-host. <br/> Als u geen beheerdersmachtigingen wilt toewijzen, maakt u een lokaal of domeingebruikersaccount en voegt u het gebruikersaccount toe aan deze groepen: gebruikers van extern beheer, Hyper-V-beheerders en gebruikers van prestatiemonitorgebruikers. |
| **Externe communicatie van powershell**   | [PowerShell remoting](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/enable-psremoting?view=powershell-7) moet zijn ingeschakeld op elke Hyper-V host. |
| **Hyper-V Replica**       | Als u Hyper-V-replica gebruikt (of als u meerdere VM's met dezelfde VM-id's hebt) en u zowel de originele als de gerepliceerde VM's ontdekt met Azure Migrate, is de beoordeling die door Azure Migrate wordt gegenereerd mogelijk niet nauwkeurig. |


## <a name="hyper-v-vm-requirements"></a>Hyper-V VM-vereisten

| **Ondersteuning**                  | **Details**               
| :----------------------------- | :------------------- |
| **Besturingssysteem** | Alle [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) en Linux-besturingssystemen. [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) |
| **Integratieservices**       | [Hyper-V Integration Services](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/integration-services) moet draaien op VM's die u beoordeelt om informatie over het besturingssysteem vast te leggen. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure Migrate-toestel](migrate-appliance.md) voor detectie en beoordeling. U het toestel implementeren met een gecomprimeerde Hyper-V VHD die u van de portal downloadt of een [PowerShell-script gebruikt.](deploy-appliance-script.md)

- Meer informatie over [toestelvereisten](migrate-appliance.md#appliance---hyper-v) voor Hyper-V.
- Meer informatie over URL's die het toestel moet openen in [openbare](migrate-appliance.md#public-cloud-urls) en [overheidsclouds.](migrate-appliance.md#government-cloud-urls)
- In Azure Government moet u het toestel implementeren met behulp van het script.

## <a name="port-access"></a>Poorttoegang

In de volgende tabel worden de poortvereisten voor beoordeling samengevat.

**Apparaat** | **Verbinding**
--- | ---
**Toestel** | Binnenkomende verbindingen op TCP-poort 3389 om externe desktopverbindingen met het toestel mogelijk te maken.<br/><br/> Binnenkomende verbindingen op poort 44368 om op afstand toegang te krijgen tot de app voor toestelbeheer met behulp van de URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Uitgaande verbindingen op poorten 443 (HTTPS) om detectie- en prestatiemetagegevens naar Azure Migrate te verzenden.
**Hyper-V-host/cluster** | Binnenkomende verbindingen op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS), om metagegevens en prestatiegegevens voor Hyper-V VM's te verzamelen met behulp van een CIM-sessie (Common Information Model).

## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheidsanalyse op basis van agent

[Afhankelijkheidsanalyse](concepts-dependency-visualization.md) helpt u om afhankelijkheden te identificeren tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel geeft een overzicht van de vereisten voor het instellen van afhankelijkheidsanalyse op basis van agent. Hyper-V ondersteunt momenteel alleen op agentgebaseerde afhankelijkheidsafhankelijkheidsvisualisatie. 

**Vereiste** | **Details** 
--- | --- 
**Vóór implementatie** | U moet een Azure Migrate-project hebben geïnstalleerd, met het hulpprogramma Serverbeoordeling dat aan het project is toegevoegd.<br/><br/>  U implementeert afhankelijkheidsvisualisatie na het instellen van een Azure Migrate-toestel om uw on-premises machines te ontdekken<br/><br/> [Meer informatie over het](create-manage-projects.md) maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een beoordelingstool aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-toestel voor beoordeling van [Hyper V VM's.](how-to-set-up-appliance-hyper-v.md)
**Azure Government** | Afhankelijkheidsvisualisatie is niet beschikbaar in Azure Government.
**Log Analytics** | Azure Migrate gebruikt de [Service Map-oplossing](../operations-management-suite/operations-management-suite-service-map.md) in [Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) voor afhankelijkheidsvisualisatie.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werkruimte aan een Azure Migrate-project. De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werkruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werkruimte moet zich in de regio's Oost-VS, Zuidoost-Azië of West-Europa bevinden. Werkruimten in andere regio's kunnen niet aan een project worden gekoppeld.<br/><br/> De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> In Log Analytics wordt de werkruimte die is gekoppeld aan Azure Migrate getagd met de toets Migratieproject en de projectnaam.
**Vereiste agenten** | Installeer op elke machine die u wilt analyseren de volgende agents:<br/><br/> De [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> De [afhankelijkheidsagent.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Als on-premises machines niet zijn verbonden met internet, moet u de Log Analytics-gateway erop downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [afhankelijkheidsagent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-werkruimte** | De werkruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werkruimten die zich bevinden in de regio's Oost-VS, Zuidoost-Azië en West-Europa.<br/><br/>  De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De oplossing Servicekaart brengt geen kosten met zich mee voor de eerste 180 dagen (vanaf de dag dat u de werkruimte Log Analytics koppelt aan het Azure Migrate-project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Als u een andere oplossing dan Service Map in de bijbehorende Werkruimte Log Analytics gebruikt, worden [standaardkosten](https://azure.microsoft.com/pricing/details/log-analytics/) voor Log Analytics in rekening gebracht.<br/><br/> Wanneer het Azure Migrate-project wordt verwijderd, wordt de werkruimte niet samen met het project verwijderd. Na het verwijderen van het project is het gebruik van servicekaarten niet gratis en wordt elk knooppunt in rekening gebracht volgens de betaalde laag loganalytics-werkruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt voordat Azure Algemene beschikbaarheid migreert (GA- 28 februari 2018), hebt u mogelijk extra servicekosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen wordt betaald, raden we u aan een nieuw project te maken, omdat bestaande werkruimten vóór GA nog steeds in rekening worden gebracht.
**Beheer** | Wanneer u agents registreert in de werkruimte, gebruikt u de id en de sleutel die worden geleverd door het Azure Migrate-project.<br/><br/> U de werkruimte Log Analytics buiten Azure Migreren gebruiken.<br/><br/> Als u het bijbehorende Azure Migrate-project verwijdert, wordt de werkruimte niet automatisch verwijderd. [Verwijder het handmatig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werkruimte die is gemaakt door Azure Migrate niet, tenzij u het Azure Migrate-project verwijdert. Als u dit doet, werkt de functionaliteit voor afhankelijkheidsvisualisatie niet zoals verwacht.
**Verbinding met internet** | Als machines niet zijn verbonden met internet, moet u de Log Analytics-gateway erop installeren.
**Azure Government** | Afhankelijkheidsanalyse op basis van agenten wordt niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

[Voorbereiden op Hyper-V VM-beoordeling](tutorial-prepare-hyper-v.md)
