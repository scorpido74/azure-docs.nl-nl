---
title: Over Azure Migrate | Microsoft Docs
description: Biedt een overzicht van de service Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498754"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

Dit artikel bevat een kort overzicht van Azure Migrate.

Azure Migrate helpt u bij het migreren van on-premises naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure. De hub biedt Azure-hulpprogram ma's en-services voor evaluatie en migratie, evenals onafhankelijke ISV'S-aanbiedingen (Independent Software Vendor) van derden. Azure Migrate biedt:

- **Uniform migratie platform**: een enkele portal om uw migratie traject naar Azure te starten, uit te voeren en bij te houden.
- **Bereik van hulpprogram**ma's: systeem eigen hulpprogram ma's en integratie met andere Azure-Services, evenals met ISV-hulpprogram ma's. Selecteer de juiste hulpprogram ma's voor evaluatie en migratie op basis van de vereisten van uw organisatie.
- **Werk belastingen**: Azure migrate voorziet in hulpprogram ma's voor evaluatie en migratie voor:
    - **Servers**: gebruik micro soft-hulpprogram MA'S of ISV-hulpprogram ma's voor het beoordelen en migreren van servers naar Azure-vm's.
    - **Data bases**: Maak gebruik van micro soft-en ISV-hulpprogram ma's voor de evaluatie en migratie van on-premises data bases naar Azure SQL data base of Azure SQL Managed instance.
    - **Webtoepassingen**: gebruik Azure app service-assistent om on-premises webtoepassingen te evalueren en te migreren naar Azure app service.
    - **Virtuele Bureau bladen**: gebruik ISV-hulpprogram ma's om on-premises Virtual Desktop Infrastructure (VDI) te beoordelen en migreren naar virtuele Windows-Bureau bladen in Azure.
    - **Gegevens**: gebruik de Azure data Box product familie om snel en kosten effectief gegevens naar Azure te migreren.

## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn momenteel twee versies van de Azure Migrate-service:

- **Huidige versie**: gebruik deze versie om Azure migrate projecten te maken, on-premises machines te detecteren en beoordelingen en migraties te organiseren. Meer [informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: als u de vorige versie van Azure migrate hebt gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines werd ondersteund), moet u nu de huidige versie gebruiken. U kunt niet meer Azure Migrate projecten maken met de vorige versie en u wordt aangeraden geen nieuwe detecties uit te voeren. Als u toegang wilt krijgen tot bestaande projecten, zoekt u in de Azure Portal > **alle services**naar **Azure migrate**. Op het dash board van Azure Migrate ziet u een melding en een koppeling voor toegang tot oude Azure Migrate projecten.

## <a name="isv-integration"></a>ISV-integratie

Naast systeem eigen micro soft-hulpprogram ma's, kan Azure Migrate worden geïntegreerd met een aantal ISV-aanbiedingen. 

**ISV** | **Functie**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Evalueren
[Apparaat 42](https://docs.device42.com/) | Evalueren
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Evalueren
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Evalueren
[Cohuur technologie](https://www.corenttech.com/AzureMigrate/) | Beoordelen en migreren
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migreren
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Evalueren



### <a name="selecting-an-isv-tool"></a>Een ISV-hulp programma selecteren

U kunt het hulp programma identificeren dat u nodig hebt en dit toevoegen aan een Azure Migrate-project.

- Nadat u een ISV-hulp programma hebt toegevoegd, kunt u aan de slag met het verkrijgen van een licentie of het aanmelden voor een gratis proef versie, conform het ISV-beleid. Licentie verlening voor ISV-hulpprogram ma's is in overeenstemming met het ISV-licentie model.
- In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie van het hulp programma om verbinding te maken met het hulp programma met Azure Migrate. L
- U kunt uw migratie traject centraal volgen vanuit het Azure Migrate project, in Azure-en ISV-hulpprogram ma's.


## <a name="azure-migrate-server-assessment-tool"></a>Hulp programma Azure Migrate server-evaluatie

Azure Migrate: het hulp programma Server bepaling detecteert en evalueert on-premises VMware-Vm's, Hyper-V-Vm's en fysieke servers voor migratie naar Azure. Het helpt u bij het identificeren van het volgende:

- **Azure-gereedheid:** Bepaal of on-premises machines gereed zijn voor migratie naar Azure.
- **Azure-grootte:** De geschatte grootte van virtuele Azure-machines na de migratie.
- **Schatting van de kosten van Azure:** Geschatte kosten voor het uitvoeren van on-premises servers in Azure.
- **Afhankelijkheids visualisatie:** Afhankelijkheden tussen servers (als afhankelijkheids visualisatie is ingeschakeld) en optimale manieren om afhankelijke servers naar Azure te verplaatsen.

Server assessment maakt gebruik van een licht gewicht dat u on-premises implementeert en registreert bij Server evaluatie.

- Het apparaat detecteert on-premises machines.
- Er wordt verbinding gemaakt met server evaluatie en de meta gegevens en prestatie gegevens van machines worden voortdurend naar Azure Migrate verzonden.
- De detectie van het apparaat is zonder agent. Er hoeft niets te worden geïnstalleerd op gedetecteerde computers.
- Na de detectie verzamelt u gedetecteerde computers in groepen. Doorgaans verzamelt u machines die u samen wilt migreren.
- U maakt een evaluatie voor een groep. U kunt de evaluatie vervolgens analyseren om uw migratie strategie te bepalen.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate-servermigratiehulpprogramma

Azure Migrate: hulp programma voor Server Migratie helpt u bij het migreren van on-premises virtuele VMware-machines, Hyper-V-Vm's, fysieke servers, andere gevirtualiseerde machines en open bare Cloud-Vm's naar Azure. U kunt machines migreren nadat u ze hebt geëvalueerd, of zonder een evaluatie.


## <a name="database-assessment"></a>Data base-evaluatie

Azure Migrate integreert met Microsoft Data Migration Assistant (DMA) om on-premises SQL Server data bases te evalueren voor migratie naar Azure SQL DB, Azure SQL Managed instance of Azure-Vm's waarop SQL Server wordt uitgevoerd. DMA biedt informatie over mogelijke blokkerings problemen voor migratie. Deze bevat niet-ondersteunde functies, evenals nieuwe functies waarvan u na de migratie kunt profiteren en helpt u bij het identificeren van het juiste pad voor database migratie. [Meer informatie](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Databasemigratie

Azure Migrate integreert met de Azure Database Migration Service (DMS) om on-premises data bases naar Azure te migreren. Gebruik DMS om on-premises data bases te migreren naar Azure-Vm's waarop SQL, Azure SQL DB en Azure SQL Managed instances worden uitgevoerd. [Meer informatie](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Migratie van de web-app

Azure Migrate integreert met de Azure App Service Migration Assistant. Vanuit de Azure Migrate hub kunt u on-premises web-apps met behulp van de-assistent als volgt beoordelen en migreren naar Azure:

- **Online web apps evalueren**: gebruik Azure app service migration assistant om on-premises websites te evalueren voor migratie naar Azure app service.
- **Web-apps migreren**: Migreer .net-en php-web-apps naar Azure met behulp van Azure app Service Migration Assistant.

Meer [informatie](https://appmigration.microsoft.com/) over de assistent.

## <a name="offline-data-migration"></a>Offline gegevens migratie

U kunt de Azure Data Box-producten gebruiken om grote hoeveel heden gegevens offline naar Azure te verplaatsen. [Meer informatie](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Volgende stappen

- Probeer onze zelf studies voor het beoordelen van virtuele [VMware-machines](tutorial-assess-vmware.md) en [virtuele Hyper-V-machines](tutorial-assess-hyper-v.md).
- Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
