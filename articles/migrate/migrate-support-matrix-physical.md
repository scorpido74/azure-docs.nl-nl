---
title: Ondersteuning voor fysieke serverbeoordeling in Azure Migreren
description: Meer informatie over ondersteuning voor fysieke serverbeoordeling met Azure Migrate Server Assessment
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 4bf7af74be35a521cdaa02e9209a7d7c0b91184f
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389457"
---
# <a name="support-matrix-for-physical-server-assessment"></a>Ondersteuningsmatrix voor fysieke serverbeoordeling 

In dit artikel worden de vereisten en ondersteuningsvereisten samengevat wanneer u fysieke servers beoordeelt op migratie naar Azure met behulp van het hulpprogramma [Azure Migrate:Server Assessment.](migrate-services-overview.md#azure-migrate-server-assessment-tool) Als u fysieke servers naar Azure wilt migreren, controleert u de [matrix voor migratieondersteuning](migrate-support-matrix-physical-migration.md).


Als u fysieke servers wilt beoordelen, maakt u een Azure Migrate-project en voegt u het hulpprogramma voor serverbeoordeling toe aan het project. Nadat het hulpprogramma is toegevoegd, implementeert u het [Azure Migrate-toestel](migrate-appliance.md). Het toestel detecteert continu on-premises machines en stuurt machinemetadata en prestatiegegevens naar Azure. Nadat detectie is voltooid, verzamelt u gedetecteerde machines in groepen en voert u een beoordeling uit voor een groep.


## <a name="limitations"></a>Beperkingen

**Ondersteuning** | **Details**
--- | ---
**Beoordelingslimieten** | U maximaal 35.000 fysieke servers in één [Azure Migrate-project](migrate-support-matrix.md#azure-migrate-projects)ontdekken en beoordelen.
**Projectlimieten** | U meerdere projecten maken in een Azure-abonnement. Naast fysieke servers kan een project VMware VM's en Hyper-V VM's bevatten, tot de beoordelingslimieten voor elk project.
**Detectie** | Het Azure Migrate-toestel kan maximaal 250 fysieke servers detecteren.
**Beoordeling** | U maximaal 35.000 machines in één groep toevoegen.<br/><br/> U tot 35.000 machines in één beoordeling beoordelen.

[Meer informatie](concepts-assessment-calculation.md) over beoordelingen.

## <a name="physical-server-requirements"></a>Vereisten voor fysieke servers

| **Ondersteuning**                | **Details**               
| :-------------------       | :------------------- |
| **Fysieke serverimplementatie**       | De fysieke server kan zelfstandig zijn of in een cluster worden geïmplementeerd. |
| **Machtigingen**           | **Windows:** U hebt een lokaal of domeingebruikersaccount nodig op alle Windows-servers die u wilt ontdekken. Het gebruikersaccount moet aan deze groepen worden toegevoegd: gebruikers van Extern bureaublad, gebruikers van prestatiemonitorgebruikers en gebruikers van prestatielogboeken. <br/><br/> **Linux:** Je hebt een root-account nodig op de Linux-servers die je wilt ontdekken. |
| **Besturingssysteem** | Alle [Windows-](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) en [Linux-besturingssystemen](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) die worden ondersteund door Azure, met uitzondering van Windows Server 2003 en SUSE Linux.|


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate-apparaatvereisten

Azure Migrate gebruikt het [Azure Migrate-toestel](migrate-appliance.md) voor detectie en beoordeling. Het toestel voor fysieke servers kan worden uitgevoerd op een VM of een fysieke machine. U stelt het toestel in met een PowerShell-script dat u downloadt van de Azure-portal.

- Meer informatie over [toestelvereisten](migrate-appliance.md#appliance---physical) voor fysieke servers.
- Meer informatie over [URL's](migrate-appliance.md#url-access) waartoe het toestel toegang nodig heeft.

## <a name="port-access"></a>Poorttoegang

In de volgende tabel worden de poortvereisten voor beoordeling samengevat.

**Apparaat** | **Verbinding**
--- | ---
**Toestel** | Binnenkomende verbindingen op TCP-poort 3389, om externe desktopverbindingen met het toestel mogelijk te maken.<br/><br/> Binnenkomende verbindingen op poort 44368, om op afstand toegang te krijgen tot de app voor toestelbeheer met behulp van de URL:``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Uitgaande verbindingen op poorten 443 (HTTPS) om detectie- en prestatiemetagegevens naar Azure Migrate te verzenden.
**Fysieke servers** | **Windows:** Binnenkomende verbindingen op WinRM-poorten 5985 (HTTP) en 5986 (HTTPS) om configuratie- en prestatiemetagegevens van Windows-servers te halen. <br/><br/> **Linux:**  Binnenkomende verbindingen op poort 22 (UDP), om configuratie- en prestatiemetagegevens van Linux-servers te halen. |

## <a name="agent-based-dependency-analysis-requirements"></a>Vereisten voor afhankelijkheidsanalyse op basis van agent

[Afhankelijkheidsanalyse](concepts-dependency-visualization.md) helpt u om afhankelijkheden te identificeren tussen on-premises machines die u wilt beoordelen en migreren naar Azure. De tabel geeft een overzicht van de vereisten voor het instellen van afhankelijkheidsanalyse op basis van agent. Momenteel wordt alleen op agent gebaseerde afhankelijkheidsanalyse ondersteund voor fysieke servers.

**Vereiste** | **Details** 
--- | --- 
**Vóór implementatie** | U moet een Azure Migrate-project hebben geïnstalleerd, met het hulpprogramma Serverbeoordeling dat aan het project is toegevoegd.<br/><br/>  U implementeert afhankelijkheidsvisualisatie na het instellen van een Azure Migrate-toestel om uw on-premises machines te ontdekken<br/><br/> [Meer informatie over het](create-manage-projects.md) maken van een project voor de eerste keer.<br/> [Meer informatie over het](how-to-assess.md) toevoegen van een beoordelingstool aan een bestaand project.<br/> Meer informatie over het instellen van het Azure Migrate-toestel voor beoordeling van [Hyper-V,](how-to-set-up-appliance-hyper-v.md) [VMware](how-to-set-up-appliance-vmware.md)of fysieke servers.
**Azure Government** | Afhankelijkheidsvisualisatie is niet beschikbaar in Azure Government.
**Logboekanalyse** | Azure Migrate gebruikt de [Service Map-oplossing](../operations-management-suite/operations-management-suite-service-map.md) in [Azure Monitor-logboeken](../log-analytics/log-analytics-overview.md) voor afhankelijkheidsvisualisatie.<br/><br/> U koppelt een nieuwe of bestaande Log Analytics-werkruimte aan een Azure Migrate-project. De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd. <br/><br/> De werkruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> De werkruimte moet zich in de regio's Oost-VS, Zuidoost-Azië of West-Europa bevinden. Werkruimten in andere regio's kunnen niet aan een project worden gekoppeld.<br/><br/> De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)<br/><br/> In Log Analytics wordt de werkruimte die is gekoppeld aan Azure Migrate getagd met de toets Migratieproject en de projectnaam.
**Vereiste agenten** | Installeer op elke machine die u wilt analyseren de volgende agents:<br/><br/> De [Microsoft Monitoring agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).<br/> De [afhankelijkheidsagent.](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> Als on-premises machines niet zijn verbonden met internet, moet u de Log Analytics-gateway erop downloaden en installeren.<br/><br/> Meer informatie over het installeren van de [afhankelijkheidsagent](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) en [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics-werkruimte** | De werkruimte moet zich in hetzelfde abonnement bevinden als het Azure Migrate-project.<br/><br/> Azure Migrate ondersteunt werkruimten die zich bevinden in de regio's Oost-VS, Zuidoost-Azië en West-Europa.<br/><br/>  De werkruimte moet zich in een gebied bevinden waarin [servicekaart wordt ondersteund.](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)<br/><br/> De werkruimte voor een Azure Migrate-project kan niet worden gewijzigd nadat deze is toegevoegd.
**Kosten** | De oplossing Servicekaart brengt geen kosten met zich mee voor de eerste 180 dagen (vanaf de dag dat u de werkruimte Log Analytics koppelt aan het Azure Migrate-project)/<br/><br/> Na 180 dagen gelden de standaardkosten voor Log Analytics.<br/><br/> Als u een andere oplossing dan Service Map in de bijbehorende Werkruimte Log Analytics gebruikt, worden [standaardkosten](https://azure.microsoft.com/pricing/details/log-analytics/) voor Log Analytics in rekening gebracht.<br/><br/> Wanneer het Azure Migrate-project wordt verwijderd, wordt de werkruimte niet samen met het project verwijderd. Na het verwijderen van het project is het gebruik van servicekaarten niet gratis en wordt elk knooppunt in rekening gebracht volgens de betaalde laag loganalytics-werkruimte/<br/><br/>Als u projecten hebt die u hebt gemaakt voordat Azure Algemene beschikbaarheid migreert (GA- 28 februari 2018), hebt u mogelijk extra servicekosten in rekening gebracht. Om ervoor te zorgen dat u na 180 dagen wordt betaald, raden we u aan een nieuw project te maken, omdat bestaande werkruimten vóór GA nog steeds in rekening worden gebracht.
**Beheer** | Wanneer u agents registreert in de werkruimte, gebruikt u de id en de sleutel die worden geleverd door het Azure Migrate-project.<br/><br/> U de werkruimte Log Analytics buiten Azure Migreren gebruiken.<br/><br/> Als u het bijbehorende Azure Migrate-project verwijdert, wordt de werkruimte niet automatisch verwijderd. [Verwijder het handmatig](../azure-monitor/platform/manage-access.md).<br/><br/> Verwijder de werkruimte die is gemaakt door Azure Migrate niet, tenzij u het Azure Migrate-project verwijdert. Als u dit doet, werkt de functionaliteit voor afhankelijkheidsvisualisatie niet zoals verwacht.
**Verbinding met internet** | Als machines niet zijn verbonden met internet, moet u de Log Analytics-gateway erop installeren.

## <a name="next-steps"></a>Volgende stappen

[Bereid u voor op de beoordeling van fysieke servers.](tutorial-prepare-physical.md)
