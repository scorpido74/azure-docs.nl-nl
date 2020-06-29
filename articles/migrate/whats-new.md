---
title: Nieuwe functies in Azure Migrate
description: Meer informatie over nieuwe en recente updates in de Azure Migrate-service.
ms.topic: overview
ms.date: 04/19/2020
ms.custom: mvc
ms.openlocfilehash: be617590ce0c96c3ec8ea705732534c8395ac0ce
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052328"
---
# <a name="whats-new-in-azure-migrate"></a>Nieuwe functies in Azure Migrate

[Azure Migrate](migrate-services-overview.md) helpt u bij het detecteren, evalueren en migreren van on-premises servers, apps en gegevens naar de Microsoft Azure-cloud. In dit artikel vindt u een overzicht van nieuwe releases en functies in Azure Migrate.

## <a name="update-june-2020"></a>Update (juni 2020)

- Ondersteuning voor meerdere referenties op een apparaat voor detectie van fysieke servers.
- Ondersteuning voor het toestaan van Azure-aanmelding vanaf een apparaat voor tenants, waarbij een tenantbeperking is geconfigureerd.
- Nieuwe Azure Migrate-apparaatversies, zoals hieronder vermeld.

### <a name="azure-public-cloud"></a>Openbare Azure-cloud

**Scenario** | **Downloaden** | **SHA256**
--- | --- | ---
Hyper-V (8,93 GB) | [Nieuwste versie](https://aka.ms/migrate/appliance/hyperv) |  572be425ea0aca69a9aa8658c950bc319b2bdbeb93b440577264500091c846a1
VMware (10,9 GB) | [Nieuwste versie](https://aka.ms/migrate/appliance/vmware) | cacbdaef927fe5477fa4e1f494fcb7203cbd6b6ce7402b79f234bc0fe69663dd
Physical (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2105112) | 0a27adf13cc5755e4b23df0c05732c6ac08d1fe8850567cb57c9906fbc3b85a0


### <a name="azure-government"></a>Azure Government

**Scenario** | **Downloaden** | **SHA256**
--- | --- | ---
Hyper-V (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120200&clcid=0x409) |  2c5e73a1e5525d4fae468934408e43ab55ff397b7da200b92121972e683f9aa3
VMware (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120300&clcid=0x409 ) | 3d5822038646b81f458d89d706832c0a2c0e827bfa9b0a55cc478eaf2757a4de
Physical (63,1 MB) | [Nieuwste versie](https://go.microsoft.com/fwlink/?linkid=2120100&clcid=0x409) | 93dfef131026e70acdfad2769cd208ff745ab96a96f013cdf3f9e1e61c9b37e1



## <a name="update-april-2020"></a>Update (april 2020)

Azure Migrate ondersteunt implementaties in Azure Government. 

- U kunt VMware-VM's, Hyper-V-VM's en fysieke servers detecteren en evalueren.
- U kunt VMware-VM's, Hyper-V-VM's en fysieke servers migreren naar Azure.
- Voor VMware-migratie kunt u gebruikmaken van migratie met of zonder agent. [Meer informatie](server-migrate-overview.md).
- [Beoordeel](migrate-support-matrix.md#supported-geographies-azure-government) ondersteunde locaties en regio's voor Azure Government.
- [Op agent gebaseerde afhankelijkheidsanalyse](concepts-dependency-visualization.md#agent-based-analysis) wordt niet ondersteund in Azure Government.
- Functies in preview worden ondersteund in Azure Government, te weten de [afhankelijkheidsanalyse zonder agent](concepts-dependency-visualization.md#agentless-analysis) en [toepassingsdetectie](how-to-discover-applications.md).


## <a name="update-march-2020"></a>Update (maart 2020)

Er is nu installatie op basis van een script beschikbaar voor het instellen van het [Azure Migrate-apparaat](migrate-appliance.md):

- De installatie op basis van een script is een alternatief voor de OVA-installatie (VMware)/VHD (Hyper-V) van het apparaat.
- Deze biedt een PowerShell-installatiescript dat kan worden gebruikt om het apparaat in te stellen voor VMware/Hyper-V op een bestaande computer met Windows Server 2016.

## <a name="update-november-2019"></a>Update (november 2019)

Er zijn een aantal nieuwe functies toegevoegd aan Azure Migrate:

- **Evaluatie van fysieke servers**. Evaluatie van on-premises fysieke servers wordt nu ondersteund, naast de fysieke servermigratie die al wordt ondersteund.
- **Evaluatie op basis van import**. Evaluatie van computers die gebruikmaken van metagegevens en prestatiegegevens die worden geleverd in een CSV-bestand, wordt nu ondersteund.
- **Toepassingsdetectie**: Azure Migrate ondersteunt nu de detectie op toepassingsniveau van apps, rollen en functies met behulp van het Azure Migrate-apparaat. Dit wordt momenteel alleen ondersteund voor VMware-VM's en is beperkt tot detectie (evaluatie wordt momenteel niet ondersteund). [Meer informatie](how-to-discover-applications.md)
- **Visualisatie van afhankelijkheden zonder agent**: U hoeft niet meer expliciet agents te installeren voor visualisatie van afhankelijkheden. Zowel zonder agent als met agent wordt nu ondersteund.
- **Virtueel bureaublad**: Gebruik ISV-hulpprogramma's om on-premises VDI (Virtual Desktop Infrastructure) te evalueren en te migreren naar Windows Virtual Desktop in Azure.
- **Web-app**: De Azure App Service Migration Assistant, die wordt gebruikt voor het evalueren en migreren van web-apps, is nu geïntegreerd in Azure Migrate.

Er zijn nieuwe hulpprogramma's voor evaluatie en migratie toegevoegd aan Azure Migrate:

- **Rackware**: biedt cloudmigratie.
- **Movere**: biedt evaluatie.

[Meer informatie](migrate-services-overview.md) over het gebruik van hulpprogramma's en ISV-aanbiedingen voor evaluatie en migratie in Azure Migrate.

## <a name="azure-migrate-current-version"></a>Huidige versie van Azure Migrate

De huidige versie van Azure Migrate (uitgebracht in juli 2019) biedt een aantal nieuwe functies:

- **Geïntegreerd migratieplatform**: Azure Migrate biedt nu één portal om de beleving van uw migratie naar Azure te centraliseren, te beheren en bij te houden, met een verbeterde implementatiestroom en portalervaring.
- **Hulpprogramma's voor evaluatie en migratie**: Azure Migrate biedt systeemeigen hulpprogramma's en is te integreren met andere Azure-services, evenals met hulpprogramma's van onafhankelijke softwareleveranciers (ISV's). [Meer informatie](migrate-services-overview.md#isv-integration) over ISV-integratie.
- **Azure Migrate-evaluatie**: Met het hulpprogramma Azure Migrate Server Assessment kunt u VMware-VM's en Hyper-V-VM's evalueren voor migratie naar Azure. U kunt ook evalueren voor migratie met behulp van andere Azure-services en met ISV-hulpprogramma's.
- **Azure Migrate-migratie**: Met behulp van het hulpprogramma Azure Migrate Server Migration kunt u on-premises VMware-VM's en Hyper-V-VM's migreren naar Azure, evenals fysieke servers, andere gevirtualiseerde servers en VM's in een privécloud/openbare cloud. Daarnaast kunt u migreren naar Azure met behulp van ISV-hulpprogramma's.
- **Azure Migrate-apparaat**: Azure Migrate implementeert een licht apparaat voor detectie en evaluatie van on-premises VMware-VM's en Hyper-V-VM's.
    - Dit apparaat wordt gebruikt door Azure Migrate Server Assessment en Azure Migrate Server Migration voor migratie zonder agent.
    - Het apparaat detecteert voortdurend metagegevens en prestatiegegevens van de server ten behoeve van evaluatie en migratie.  
- **Migratie van VMware-VM's**:  Azure Migrate Server Migration biedt een aantal methoden voor het migreren van on-premises VMware-VM's naar Azure.  Een migratie zonder agent met behulp van het Azure Migrate-apparaat en een migratie op basis van een agent die gebruikmaakt van een replicatieapparaat en een agent implementeert op elke VM die u wilt migreren. [Meer informatie](server-migrate-overview.md)
 - **Database-evaluatie en -migratie**: Vanuit Azure Migrate kunt u on-premises databases evalueren voor migratie naar Azure met behulp van de Azure Database Migration Assistant. U kunt databases migreren met behulp van de Azure Database Migration Service.
- **Migratie van web-apps**: U kunt web-apps evalueren met behulp van een openbare eindpunt-URL met de Azure App Service. Voor de migratie van interne NET-apps kunt u de App Service Migration Assistant downloaden en uitvoeren.
- **Data Box**: Importeer grote hoeveelheden offlinegegevens in Azure met behulp van Azure Data Box in Azure Migrate.

## <a name="azure-migrate-previous-version"></a>Vorige versie van Azure Migrate

Als u de vorige versie van Azure Migrate gebruikt (alleen evaluatie van on-premises VMware-VM's werd ondersteund), kunt u nu beter de huidige versie gaan gebruiken. In de vorige versie kunt u geen nieuwe Azure Migrate-projecten meer maken of nieuwe detecties uitvoeren. U houdt toegang tot bestaande projecten. Ga hiertoe in Azure Portal naar **Alle services** en zoek naar **Azure Migrate**. De Azure Migrate-meldingen bevatten een koppeling voor toegang tot oude Azure Migrate-projecten.



## <a name="next-steps"></a>Volgende stappen

- Meer informatie over prijzen voor Azure Migrate vindt u [hier](https://azure.microsoft.com/pricing/details/azure-migrate/).
- [Bekijk de veelgestelde vragen](resources-faq.md) over Azure Migrate.
- Probeer onze zelfstudies om [VMware-VM's](tutorial-assess-vmware.md) en [Hyper-V-VM's](tutorial-assess-hyper-v.md) te evalueren.
