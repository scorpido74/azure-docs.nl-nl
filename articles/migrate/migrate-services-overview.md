---
title: Over Azure Migrate
description: Meer informatie over de evaluatie en migratie van servers met de Azure Migrate-service.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 54aea9fac946713ee6c1709e952e3962bb7288e9
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951765"
---
# <a name="about-azure-migrate"></a>Over Azure Migrate

Dit artikel bevat een kort overzicht van Azure Migrate.

Azure Migrate helpt u bij het migreren van uw onderneming van on-premises naar Azure. Azure Migrate biedt een gecentraliseerde hub voor het bijhouden van detectie, beoordeling en migratie van on-premises infra structuur, toepassingen en gegevens naar Azure.  Azure Migrate biedt:

- **Uniform migratie platform**: een enkele portal om uw migratie traject naar Azure te starten, uit te voeren en bij te houden.
- **Bereik van hulpprogram ma's**: de hub biedt Azure migrate hulpprogram ma's voor evaluatie en migratie, en kan worden geïntegreerd met andere Azure-Services, evenals andere hulpprogram MA'S en ISV-aanbiedingen (Independent Software Vendor).
- **Werk belastingen**: Azure migrate biedt evaluatie en migratie voor:
    - **Servers**: gebruik Azure migrate server evaluatie, Azure migrate server migratie en andere hulpprogram ma's, voor de evaluatie en migratie van servers naar Azure-vm's.
    - **Data bases**: Maak gebruik van micro soft-en ISV-hulpprogram ma's voor de evaluatie en migratie van on-premises data bases naar Azure SQL data base of Azure SQL Managed instance.
    - **Webtoepassingen**: gebruik Azure app service-assistent om on-premises webtoepassingen te evalueren en te migreren naar Azure app service.
    - **Virtuele Bureau bladen**: gebruik ISV-hulpprogram ma's om on-premises Virtual Desktop Infrastructure (VDI) te beoordelen en migreren naar virtuele Windows-Bureau bladen in Azure.
    - **Gegevens**: gebruik de Azure data Box product familie om snel en rendabel grote hoeveel heden gegevens naar Azure te migreren.

## <a name="azure-migrate-versions"></a>Azure Migrate versies

Er zijn momenteel twee versies van de Azure Migrate-service:

- **Huidige versie**: gebruik deze versie om Azure migrate projecten te maken, on-premises machines te detecteren en beoordelingen en migraties te organiseren. Meer [informatie](whats-new.md) over wat er nieuw is in deze versie.
- **Vorige versie**: als u de vorige versie van Azure migrate hebt gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines werd ondersteund), moet u nu de huidige versie gebruiken. U kunt niet meer Azure Migrate projecten maken met de vorige versie en u wordt aangeraden geen nieuwe detecties uit te voeren. Als u toegang wilt krijgen tot bestaande projecten, zoekt en selecteert u in de Azure Portal **Azure migrate**. Op het dash board van **Azure migrate** ziet u een melding en een koppeling voor toegang tot oude Azure migrate projecten.



## <a name="isv-integration"></a>ISV-integratie

Naast de systeem eigen Azure-hulpprogram ma's, kan Azure Migrate worden geïntegreerd met een aantal ISV-aanbiedingen. 

**ISV** | **Functie**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Servers migreren
[Cloudamize](https://www.cloudamize.com/platform) | Servers beoordelen
[Cohuur technologie](https://www.corenttech.com/AzureMigrate/) | Servers beoordelen en migreren
[Apparaat 42](https://docs.device42.com/) | Servers beoordelen
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI beoordelen
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Servers migreren
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Servers beoordelen
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Servers en data bases beoordelen

## <a name="azure-tool-integration"></a>Integratie van Azure-hulpprogram ma's

De tabel bevat een overzicht van andere hulpprogram ma's die in Azure Migrate zijn geïntegreerd.

**Hulpprogramma** | **Details**
--- | ---
Azure Migrate: Server evaluatie | Servers beoordelen
Azure Migrate: Server migratie | Servers migreren
Data base-Migration Assistant (DMA) | Data bases beoordelen
Database Migration Service (DMS) | Databases migreren
Movere | Servers beoordelen
Migration Assistant van web-app | Web-apps beoordelen en migreren



### <a name="selecting-a-tool"></a>Een hulp programma selecteren

Bepaal het hulp programma dat u nodig hebt en voeg het toe aan een Azure Migrate-project.

- Als u een ISV-hulp programma of een indrijfprogramma toevoegt:
    - Ga aan de slag door een licentie te verkrijgen of u aan te melden voor een gratis proef versie, in overeenstemming met het hulp programma beleid. Licentie verlening voor hulpprogram ma's is in overeenstemming met het ISV-of hulp programma licentie model.
    - In elk hulp programma is er een optie om verbinding te maken met Azure Migrate. Volg de instructies en documentatie van het hulp programma om het hulp programma te verbinden met Azure Migrate.
- U kunt uw migratie traject centraal volgen vanuit het Azure Migrate project, over Azure en andere hulpprogram ma's.



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


## <a name="database-migration-assistant"></a>Migration Assistant data base

Azure Migrate integreert met Microsoft Data Migration Assistant (DMA) om on-premises SQL Server data bases te evalueren voor migratie naar Azure SQL DB, Azure SQL Managed instance of Azure-Vm's waarop SQL Server wordt uitgevoerd. DMA biedt informatie over mogelijke blokkerings problemen voor migratie. Deze bevat niet-ondersteunde functies, evenals nieuwe functies waarvan u na de migratie kunt profiteren en helpt u bij het identificeren van het juiste pad voor database migratie. [Meer informatie](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Database Migration Service

Azure Migrate integreert met de Azure Database Migration Service (DMS) om on-premises data bases naar Azure te migreren. Gebruik DMS om on-premises data bases te migreren naar Azure-Vm's waarop SQL, Azure SQL DB en Azure SQL Managed instances worden uitgevoerd. [Meer informatie](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
De verhuis wagen is een SaaS-platform dat business intelligence verbetert door nauw keurig volledige IT-omgevingen binnen één dag te presen teren. Naarmate organisaties groeien, wijzigen en digitaal optimaliseren, biedt de oplossing ondernemingen met het vertrouwen dat ze nodig hebben om inzicht te hebben in hun omgeving, ongeacht het platform, de toepassing of de geografie. De aandrijf periode is door micro soft [verworven](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) en wordt niet meer verkocht als een zelfstandige aanbieding.  De verhuis wagen is beschikbaar via de micro soft-oplossing voor de evaluatie van oplossingen en Cloud economie. [Meer informatie](https://www.movere.io) over indrijfen. Als u vragen hebt, verzendt u deze naar: movereq@microsoft.com of neemt u contact op met uw micro soft-vertegenwoordiger.

We raden u aan om ook Azure Migrate, onze ingebouwde migratie service, te bekijken. Azure Migrate biedt een centrale hub om uw migratie naar de cloud te vereenvoudigen. De hub biedt uitgebreide ondersteuning voor verschillende werk belastingen, zoals fysieke en virtuele servers, data bases en toepassingen. End-to-end-zicht baarheid maakt het eenvoudig om de voortgang te volgen tijdens de detectie, evaluatie en migratie. Met zowel Azure-als partner-ISV-hulpprogram ma's die zijn ingebouwd, is Azure Migrate ook voorzien van een uitgebreide reeks functies, waaronder de detectie van virtuele en fysieke servers, het op prestaties gebaseerde formaat, kosten planning, op import gebaseerde beoordelingen en toepassingen zonder agent afhankelijkheids analyse. Als u op zoek bent naar deskundige hulp om aan de slag te gaan, heeft micro soft een ervaren [Azure expert managed service provider](https://azure.microsoft.com/partners) om u over de reis te leiden. Bekijk de [Azure migrate-website](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Migration Assistant van web-app

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
