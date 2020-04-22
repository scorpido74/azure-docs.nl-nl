---
title: Over Azure Migrate
description: Meer informatie over de Azure Migrate-service.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: fe6386346282cf182397f6420c541d629ba0aab3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768397"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

In dit artikel vindt u een snel overzicht van de Azure Migrate-service.

Azure Migrate biedt een gecentraliseerde hub om on-premises servers, infrastructuur, toepassingen en gegevens te beoordelen en te migreren naar Azure.

Azure Migrate biedt de volgende functies:

- **Uniform migratieplatform:** één portal om uw migratie naar Azure te starten, uit te voeren en bij te houden.
- **Reeks instrumenten**: Een reeks instrumenten voor beoordeling en migratie. Hulpprogramma's: Azure Migrate: Server Assessment en Azure Migrate: Server Migration. Azure Migrate integreert met andere Azure-services en met andere tools en onafhankelijke softwareleveranciers (ISV)-aanbiedingen.
- **Beoordeling en migratie:** In de Azure Migrate-hub u beoordelen en migreren:
    - **Servers:** Beoordeel on-premises servers en migreer ze naar virtuele Azure-machines.
    - **Databases:** Beoordeel on-premises databases en migreer ze naar Azure SQL Database of naar een azure SQL Database beheerde instantie.
    - **Webtoepassingen:** beoordeel on-premises webtoepassingen en migreer ze naar Azure App Service met behulp van de Azure App Service Migration Assistant.
    - **Virtuele bureaubladen:** beoordeel uw on-premises virtuele desktopinfrastructuur (VDI) en migreer deze naar Windows Virtual Desktop in Azure.
    - **Gegevens:** migreer snel en kosteneffectief grote hoeveelheden gegevens naar Azure met Azure Data Box-producten.

## <a name="integrated-tools"></a>Geïntegreerde gereedschappen

De Azure Migrate-hub bevat de volgende hulpprogramma's:

**Hulpprogramma** | **Beoordelen en migreren** | **Details**
--- | --- | ---
**Azure Migreren: serverbeoordeling** | Beoordeel servers. | Ontdek en beoordeel on-premises VM's vMware, Hyper-V VM's en fysieke servers ter voorbereiding op migratie naar Azure.
**Azure Migreren: servermigratie** | Servers migreren. | Migreer VMware VM's, Hyper-V VM's, fysieke servers, andere gevirtualiseerde machines en openbare cloud VM's naar Azure.
**Assistent gegevensmigratie** | On-premises SQL Server-databases beoordelen op migratie naar Azure SQL Database, een Azure SQL Database-beheerde instantie of Azure VM's met SQL Server. | Data Migration Assistant helpt bij het lokaliseren van mogelijke problemen die migratie blokkeren. Het identificeert niet-ondersteunde functies, nieuwe functies die u kunnen profiteren na migratie en het juiste pad voor databasemigratie. [Meer informatie](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Azure Database Migration Service** | Migreer on-premises databases naar Azure VM's met SQL Server, Azure SQL Database of Azure SQL Database managed instances. | [Meer informatie](https://docs.microsoft.com/azure/dms/dms-overview) over Database Migration Service.
**Movere** | Beoordeel servers. | [Meer informatie](#movere) over Movere.
**Migratieassistent voor web-apps** | Beoordeel on-premises webapps en migreer ze naar Azure. |  Gebruik Azure App Service Migration Assistant om on-premises websites te beoordelen op migratie naar Azure App Service.<br/><br/> Gebruik Migratieassistent om .NET- en PHP-webapps te migreren naar Azure. [Meer informatie](https://appmigration.microsoft.com/) over Azure App Service Migration Assistant.
**Azure Data Box** | Offlinegegevens migreren. | Gebruik Azure Data Box-producten om grote hoeveelheden offline gegevens naar Azure te verplaatsen. [Meer informatie](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Als u zich in Azure Government bevindt, kunnen externe geïntegreerde hulpprogramma's en ISV-aanbiedingen geen gegevens verzenden naar Azure Migrate-projecten. U tools onafhankelijk van elkaar gebruiken.

## <a name="isv-integration"></a>ISV-integratie

Azure Migrate integreert met verschillende ISV-aanbiedingen. 

**Isv**    | **Functie**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servers migreren.
[Cloudamize](https://www.cloudamize.com/platform) | Beoordeel servers.
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Servers beoordelen en migreren.
[Device42](https://docs.device42.com/) | Beoordeel servers.
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Beoordeel VDI.
[RackWare (RackWare)](https://go.microsoft.com/fwlink/?linkid=2102735) | Servers migreren.
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Beoordeel servers.
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Beoordeel servers en databases.

## <a name="azure-migrate-server-assessment-tool"></a>Azure Migreren: hulpprogramma voor serverbeoordeling

Het hulpprogramma Azure Migrate: Server Assessment detecteert en beoordeelt on-premises Vm's vMware, Hyper-V VM's en fysieke servers voor migratie naar Azure.

Dit is wat de tool doet:

- **Azure-gereedheid**: beoordeelt of on-premises machines klaar zijn voor migratie naar Azure.
- **Azure-formaat:** schat de grootte van Azure VM's na migratie.
- **Schatting van azure-kosten:** schat de kosten voor het uitvoeren van on-premises servers in Azure.
- **Afhankelijkheidsanalyse**: identificeert afhankelijkheden van meerdere servers en optimalisatiestrategieën voor het verplaatsen van onderling afhankelijke servers naar Azure. Meer informatie over serverbeoordeling met [afhankelijkheidsanalyse](concepts-dependency-visualization.md).

Server assessment maakt gebruik van een lichtgewicht [Azure Migrate-toestel](migrate-appliance.md) dat u on-premises implementeert.

- Het toestel draait op een VM of fysieke server. U het eenvoudig installeren met behulp van een gedownloade sjabloon.
- Het apparaat detecteert on-premises machines. Het stuurt ook voortdurend machinemetadata en prestatiegegevens naar Azure Migrate.
- Toesteldetectie is agentloos. Er is niets geïnstalleerd op ontdekte machines.
- Na het ontdekken van het apparaat u gedetecteerde machines in groepen verzamelen en beoordelingen uitvoeren voor elke groep.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migreren: hulpprogramma voor servermigratie

Het hulpprogramma Azure Migreren: Servermigratie helpt u te migreren naar Azure:

- On-premises VMware VM's
- Virtuele Hyper-V-machines
- Fysieke servers
- Andere gevirtualiseerde machines
- VM's in de openbare cloud

U machines migreren nadat u ze hebt beoordeeld of zonder beoordeling migreren.

Voor agentloze migratie van Vm's zonder vmware en migratie van Hyper V VM's maakt servermigratie gebruik van een Azure Migrate-toestel dat u on-premises implementeert. Het toestel wordt ook gebruikt als u serverbeoordeling instelt. Het is beschreven in het vorige gedeelte.

## <a name="selecting-assessment-and-migration-tools"></a>Beoordelings- en migratietools selecteren

In de azure migrate hub selecteert u het hulpprogramma dat u wilt gebruiken voor beoordeling of migratie en voegt u het toe aan een Azure Migrate-project. Als u een ISV-gereedschap of Movere toevoegt:

- Om aan de slag te gaan, u een licentie verkrijgen of u aanmelden voor een gratis proefperiode door de gereedschapsinstructies te volgen. Elke ISV of tool specificeert tool licenties.
- Elk hulpprogramma heeft een optie om verbinding te maken met Azure Migrate. Volg de gereedschapsinstructies om verbinding te maken.
- Houd uw migratie bij voor alle hulpprogramma's binnen het Azure Migrate-project.

## <a name="movere"></a>Movere

Movere is een software as a service (SaaS) platform. Het verhoogt de business intelligence door volledige IT-omgevingen binnen één dag nauwkeurig te presenteren. Organisaties en ondernemingen groeien, veranderen en optimaliseren digitaal. Terwijl ze dit doen, biedt Movere hen het nodige vertrouwen om hun omgevingen te zien en te controleren, ongeacht het platform, de toepassing of de geografie.

Microsoft heeft Movere [overgenomen](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) en wordt niet langer verkocht als een zelfstandig aanbod. Movere is beschikbaar via Microsoft Solution Assessment en Microsoft Cloud Economics Program. [Meer informatie](https://www.movere.io) over Movere.

We raden u aan om ook te kijken naar Azure Migrate, onze ingebouwde migratieservice. Azure Migrate biedt een centrale hub om uw cloudmigratie te vereenvoudigen. De hub ondersteunt volledig workloads zoals fysieke en virtuele servers, databases en toepassingen. Met end-to-end zichtbaarheid u eenvoudig de voortgang bijhouden tijdens detectie, beoordeling en migratie.

Met zowel Azure- als partner-ISV-hulpprogramma's ingebouwd, heeft Azure Migrate een uitgebreid scala aan functies, waaronder:

- Ontdekking van virtuele en fysieke servers.
- Op prestaties gebaseerde rechten voorbehouden.
- Kostenplanning.
- Beoordelingen op basis van import.
- Afhankelijkheidsanalyse van agentless toepassingen.

Als u op zoek bent naar deskundige hulp om aan de slag te gaan, heeft Microsoft ervaren [Azure Expert Managed Service Providers](https://azure.microsoft.com/partners) om u te begeleiden. Bekijk de [Azure Migrate-website](https://azure.microsoft.com/services/azure-migrate/). 

## <a name="azure-migrate-versions"></a>Azure-migratieversies

Er zijn twee versies van de Azure Migrate-service.

- **Huidige versie**: Gebruik deze versie om Azure Migrate-projecten te maken, on-premises machines te ontdekken en beoordelingen en migraties te orkestreren. [Meer informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: De vorige versie van Azure Migrate ondersteunt alleen beoordeling van on-premises VMware VM's. Als u de vorige versie hebt gebruikt, moet u nu de huidige versie gebruiken. U azure migrate-projecten niet meer maken met behulp van de vorige versie. En we raden je aan er geen nieuwe ontdekkingen mee te doen.

    Als u toegang wilt krijgen tot bestaande projecten in de Azure-portal, zoekt en selecteert u **Azure Migreren**. Het **Azure Migrate-dashboard** heeft een melding en een koppeling om toegang te krijgen tot oude Azure Migrate-projecten.

## <a name="next-steps"></a>Volgende stappen

- Probeer onze tutorials om [VMware VM's,](tutorial-prepare-vmware.md) [Hyper-V VM's](tutorial-prepare-hyper-v.md)of [fysieke servers](tutorial-prepare-physical.md)te beoordelen.
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
