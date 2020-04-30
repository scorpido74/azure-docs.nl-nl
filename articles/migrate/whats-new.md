---
title: Wat is er nieuw in Azure Migrate
description: Meer informatie over nieuwe en recente updates in de Azure Migrate-service.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: ba85b0a4ed30320099388ccb48dd91ad6445efd5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81677250"
---
# <a name="whats-new-in-azure-migrate"></a>Wat is er nieuw in Azure Migrate

[Azure migrate](migrate-services-overview.md) helpt u bij het detecteren, evalueren en migreren van on-premises servers, apps en gegevens naar de Microsoft Azure Cloud. In dit artikel vindt u een overzicht van nieuwe releases en functies in Azure Migrate.


## <a name="update-april-2020"></a>Update (april 2020)

Azure Migrate ondersteunt implementaties in Azure Government. 

- U kunt virtuele VMware-machines, virtuele Hyper-V-machines en fysieke servers detecteren en beoordelen.
- U kunt virtuele VMware-machines, virtuele Hyper-V-machines en fysieke servers naar Azure migreren.
- Voor VMware-migratie kunt u gebruikmaken van zonder agents of migratie op basis van een agent. [Meer informatie](server-migrate-overview.md).
- [Bekijk](migrate-support-matrix.md#supported-geographies-azure-government) ondersteunde geographs en regio's voor Azure Government.
- [Afhankelijkheids analyse op basis](concepts-dependency-visualization.md#agent-based-analysis) van een agent wordt niet ondersteund in azure Government.
- Functies in de preview-versie worden ondersteund in Azure Government, met name [afhankelijkheids analyse zonder agents](concepts-dependency-visualization.md#agentless-analysis)en [toepassings detectie](how-to-discover-applications.md).

## <a name="update-march-2020"></a>Update (maart 2020)

Er is nu een installatie op basis van een script beschikbaar om het [Azure migrate apparaat](migrate-appliance.md)in te stellen:

- De installatie op basis van een script is een alternatief voor de. EICELLEN (VMware)/VHD (Hyper-V) installatie van het apparaat.
- Het bevat een Power shell-installatie script dat kan worden gebruikt om het apparaat voor VMware/Hyper-V in te stellen op een bestaande computer met Windows Server 2016.

## <a name="update-november-2019"></a>Update (november 2019)

Er zijn een aantal nieuwe functies toegevoegd aan Azure Migrate:

- **Fysieke server beoordeling**. De evaluatie van on-premises fysieke servers wordt nu ondersteund, naast de fysieke server migratie die al wordt ondersteund.
- **Evaluatie op basis van import**. De evaluatie van computers die gebruikmaken van meta gegevens en prestatie gegevens die zijn opgenomen in een CSV-bestand, wordt nu ondersteund.
- **Application Discovery**: Azure migrate ondersteunt nu de detectie op toepassings niveau van apps, rollen en functies met behulp van het Azure migrate apparaat. Dit wordt momenteel alleen ondersteund voor virtuele VMware-machines en is beperkt tot detectie (de evaluatie wordt momenteel niet ondersteund). [Meer informatie](how-to-discover-applications.md)
- **Visualisatie zonder agent**: u hoeft geen agents meer expliciet te installeren voor de visualisatie van afhankelijkheden. Zowel zonder agent als op basis van een agent worden nu ondersteund.
- **Virtueel bureau blad**: gebruik ISV-hulpprogram ma's om on-premises Virtual Desktop Infrastructure (VDI) te beoordelen en migreren naar Windows Virtual Desktop in Azure.
- **Web-app**: de Azure app Service Migration Assistant, die wordt gebruikt voor het evalueren en migreren van web-apps, is nu geïntegreerd in azure Migrate.

Er zijn nieuwe hulpprogram ma's voor evaluatie en migratie toegevoegd aan Azure Migrate:

- **Rackware**: biedt Cloud migratie.
- **Overbelasting:** aanbiedings beoordeling.

Meer [informatie](migrate-services-overview.md) over het gebruik van hulpprogram MA'S en ISV-aanbiedingen voor evaluatie en migratie in azure Migrate.

## <a name="azure-migrate-current-version"></a>Azure Migrate huidige versie

De huidige versie van Azure Migrate (uitgebracht in juli 2019) biedt een aantal nieuwe functies:

- **Uniform migratie platform**: Azure migrate biedt nu één portal om uw migratie naar Azure te centraliseren, beheren en bij te houden, met een verbeterde implementatie stroom en Portal ervaring.
- **Hulpprogram ma's voor evaluatie en migratie**: Azure migrate voorziet in systeem eigen hulpprogram ma's en kan worden geïntegreerd met andere Azure-Services, evenals met ISV-hulpprogram ma's (Independent Software Vendor). Meer [informatie](migrate-services-overview.md#isv-integration) over ISV-integratie.
- **Azure migrate beoordeling**: met het hulp programma Azure migrate server Assessment kunt u virtuele VMware-machines en virtuele Hyper-V-machines beoordelen voor migratie naar Azure. U kunt ook evalueren voor migratie met behulp van andere Azure-Services en ISV-hulpprogram ma's.
- **Azure migrate migratie**: met het hulp programma Azure migrate server-migratie kunt u on-premises virtuele VMware-machines en virtuele Hyper-V-machines migreren naar Azure, evenals fysieke servers, andere gevirtualiseerde servers en persoonlijke/open bare Cloud-vm's. Daarnaast kunt u migreren naar Azure met behulp van ISV-hulpprogram ma's.
- **Azure migrate apparaat**: Azure migrate implementeert een licht gewicht apparaat voor detectie en evaluatie van on-premises virtuele VMware-machines en virtuele Hyper-V-machines.
    - Dit apparaat wordt gebruikt door Azure Migrate server-evaluatie en Azure Migrate server migratie voor migratie zonder agent.
    - Het apparaat detecteert voortdurend meta gegevens en prestatie gegevens van de server voor de evaluatie en migratie.  
- **Migratie van virtuele VMware**-machines: Azure migrate server migratie biedt een aantal methoden voor het migreren van on-premises VMware-Vm's naar Azure.  Een migratie zonder agent met behulp van het Azure Migrate apparaat en een migratie op basis van een agent die gebruikmaakt van een replicatie apparaat en een agent implementeert op elke virtuele machine die u wilt migreren. [Meer informatie](server-migrate-overview.md)
 - **Data Base-evaluatie en migratie**: vanuit Azure migrate kunt u on-premises data bases voor migratie naar Azure evalueren met behulp van de Azure data base-migration assistant. U kunt data bases migreren met behulp van de Azure Database Migration Service.
- **Web-app-migratie**: u kunt web-apps met behulp van een open bare eind punt-URL evalueren met de Azure app service. Voor de migratie van interne .NET-apps kunt u de App Service-Migration Assistant downloaden en uitvoeren.
- **Data Box**: Importeer grote hoeveel heden offline gegevens in azure met behulp van Azure Data Box in azure Migrate.

## <a name="azure-migrate-previous-version"></a>Azure Migrate vorige versie

Als u de vorige versie van Azure Migrate gebruikt (alleen de evaluatie van on-premises virtuele VMware-machines wordt ondersteund), moet u nu de huidige versie gebruiken. In de vorige versie kunt u geen nieuwe Azure Migrate projecten meer maken of nieuwe detecties uitvoeren. U kunt nog steeds toegang krijgen tot bestaande projecten. Als u dit wilt doen in het Azure Portal > **alle services**, zoekt u naar **Azure migrate**. In de Azure Migrate meldingen is er een koppeling naar het openen van oude Azure Migrate projecten.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
- Probeer onze zelf studies voor het beoordelen van virtuele [VMware-machines](tutorial-assess-vmware.md) en [virtuele Hyper-V-machines](tutorial-assess-hyper-v.md).
