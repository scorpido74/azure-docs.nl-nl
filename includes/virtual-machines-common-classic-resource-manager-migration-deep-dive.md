---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: tanmaygore
ms.openlocfilehash: 215057640dd08d9ea524d8f6b3bed8b03a8b5b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77068412"
---
## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>IaaS-resources migreren van het klassieke implementatiemodel naar Azure Resource Manager
Ten eerste is het belangrijk om het verschil te begrijpen tussen data-plane en management-plane operaties op de infrastructure as a service (IaaS) resources.

* *Beheer/controlevlak* beschrijft de aanroepen die in het beheer/controlevlak of de API komen voor het wijzigen van resources. Bewerkingen zoals het maken van een VM, opnieuw starten van een VM en het bijwerken van een virtueel netwerk met een nieuw subnet beheren de actieve resources. Ze hebben geen directe invloed op het verbinden met de VM's.
* *Gegevensvlak* (toepassing) beschrijft de runtime van de toepassing zelf en omvat interactie met instanties die niet door de Azure API gaan. Toegang tot uw website of het ophalen van gegevens uit een sql server-exemplaar of een MongoDB-server zijn bijvoorbeeld gegevensvlak- of toepassingsinteracties. Andere voorbeelden zijn het kopiëren van een blob vanuit een opslagaccount en het openen van een openbaar IP-adres om Remote Desktop Protocol (RDP) of Secure Shell (SSH) in de virtuele machine te gebruiken. Deze bewerkingen zorgen ervoor dat de toepassing uitgevoerd blijft worden bij het berekenen, het netwerken en de opslag.

Het gegevensvlak is hetzelfde tussen het klassieke implementatiemodel en de stapels Resource Manager. Het verschil is dat Microsoft tijdens het migratieproces de weergave van de resources van het klassieke implementatiemodel vertaalt naar die in de stack Resource Manager. Als gevolg hiervan moet u nieuwe hulpprogramma's, API's en SDK's gebruiken om uw resources in de resourcemanager-stack te beheren.

![Diagram met het verschil tussen beheer/controlevlak en gegevensvlak](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> In sommige scenario's voor migratie stopt het Azure-platform, maakt de toewijzingen ongedaan en start uw virtuele machines opnieuw op. Dit veroorzaakt een korte downtime van het datavlak.
>

## <a name="the-migration-experience"></a>De migratie-ervaring
Voordat u met de migratie begint:

* Zorg ervoor dat de resources die u wilt migreren geen niet-ondersteunde functies of configuraties gebruiken. Meestal detecteert het platform deze problemen en genereert een fout.
* Als u VM's hebt die zich niet in een virtueel netwerk bevinden, worden ze gestopt en worden ze toegewezen als onderdeel van de voorbereidingsbewerking. Als u het openbare IP-adres niet wilt verliezen, u overwegen het IP-adres te reserveren voordat u de voorbereidingsbewerking activeert. Als de VM's zich in een virtueel netwerk bevinden, worden ze niet gestopt en gelokaliseerd.
* Plan de migratie buiten kantooruren zodat u de tijd hebt om onverwachte fouten die kunnen ontstaan tijdens de migratie op te lossen.
* Download de huidige configuratie van uw virtuele machines met behulp van PowerShell, CLI-opdrachten (opdrachtregelinterface) of REST API's om de validatie eenvoudiger te maken nadat de voorbereidingsstap is voltooid.
* Werk uw automatiserings- en operationalisatiescripts bij om het implementatiemodel van Resource Manager te verwerken voordat u de migratie start. Desgewenst kunt u GET-bewerkingen uitvoeren wanneer de resources de status Voorbereid hebben.
* Evalueer het RBAC-beleid (Role-Based Access Control) dat is geconfigureerd op de IaaS-resources in het klassieke implementatiemodel en plan voor nadat de migratie is voltooid.

De migratieworkflow ziet er als volgt uit:

![Diagram met de migratiewerkstroom](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> De operaties beschreven in de volgende secties zijn allemaal idempotent. Als u een ander probleem hebt dan een niet-ondersteunde functie of een configuratiefout, probeert u de bewerking voorbereiden, afbreken of vastleggen opnieuw. Azure probeert de actie opnieuw.
>
>

### <a name="validate"></a>Valideren
De bewerking Valideren is de eerste stap in het migratieproces. Het doel van deze stap is het analyseren van de status van de resources die u wilt migreren in het klassieke implementatiemodel. De bewerking evalueert of de resources kunnen worden gemigratie (succes of mislukking).

U selecteert het virtuele netwerk of een cloudservice (als deze zich niet in een virtueel netwerk bevindt) die u wilt valideren voor migratie. Als de bron niet kan worden gemigratie, geeft Azure een lijst van de redenen waarom.

#### <a name="checks-not-done-in-the-validate-operation"></a>Controles die niet zijn uitgevoerd in de valideringsbewerking

De validatiebewerking analyseert alleen de status van de resources in het klassieke implementatiemodel. Het kan controleren op alle fouten en niet-ondersteunde scenario's als gevolg van verschillende configuraties in het klassieke implementatiemodel. Het is niet mogelijk om te controleren op alle problemen die de Azure Resource Manager-stack tijdens de migratie op de resources kan opleggen. Deze problemen worden alleen gecontroleerd wanneer de resources worden transformatie ondergaan in de volgende stap van migratie (de voorbereidingsbewerking). In de volgende tabel worden alle problemen weergegeven die niet zijn aangevinkt in de valideringsbewerking:


|Netwerkcontroles die niet in de validatiebewerking zijn uitgevoerd|
|-|
|Een virtueel netwerk met zowel ER- als VPN-gateways.|
|Een verbinding met een virtuele netwerkgateway in een verbroken status.|
|Alle ER-circuits zijn vooraf gemigreerd naar Azure Resource Manager-stack.|
|Azure Resource Manager-quota controleert op netwerkbronnen. Bijvoorbeeld: statisch openbaar IP, dynamische openbare IP,, load balancer, netwerkbeveiligingsgroepen, routetabellen en netwerkinterfaces. |
| Alle regels voor load balancer zijn geldig voor implementatie en het virtuele netwerk. |
| Conflicterende privé-IP's tussen stop-deallocated VM's in hetzelfde virtuele netwerk. |

### <a name="prepare"></a>Voorbereiden
De bewerking Voorbereiden is de tweede stap in het migratieproces. Het doel van deze stap is het simuleren van de transformatie van de IaaS-resources van het klassieke implementatiemodel naar resourcebeheerbronnen. Verder, de voorbereiding operatie presenteert deze side-by-side voor u om te visualiseren.

> [!NOTE] 
> Uw resources in het klassieke implementatiemodel worden tijdens deze stap niet gewijzigd. Het is een veilige stap om uit te voeren als je migratie uitprobeert. 

U selecteert het virtuele netwerk of de cloudservice (als het geen virtueel netwerk is) dat u wilt voorbereiden op migratie.

* Als de bron niet kan worden gemigratie, stopt Azure het migratieproces en geeft het de reden weer waarom de voorbereidingsbewerking is mislukt.
* Als de bron kan worden gemigratie, vergrendelt Azure de beheervlakbewerkingen voor de resources die worden migratie. U kunt bijvoorbeeld geen gegevensschijf toevoegen aan een virtuele machine die wordt gemigreerd.

Azure start vervolgens de migratie van metagegevens van het klassieke implementatiemodel naar Resourcebeheer voor de migrerende resources.

Nadat de voorbereidingsbewerking is voltooid, u de resources in zowel het klassieke implementatiemodel als Resource Manager visualiseren. Voor elke cloudservice in het klassieke implementatiemodel maakt het Azure-platform een resourcegroepnaam met de indeling `cloud-service-name>-Migrated`.

> [!NOTE]
> Het is niet mogelijk om de naam te selecteren van een resourcegroep die is gemaakt voor gemigreerde resources (dat wil zeggen 'Gemigreerd'). Nadat de migratie is voltooid, u echter de verhuisfunctie van Azure Resource Manager gebruiken om resources te verplaatsen naar elke gewenste resourcegroep. Zie voor meer informatie [Resources verplaatsen naar een nieuwe resourcegroep of een nieuw abonnement](../articles/resource-group-move-resources.md).

De volgende twee screenshots tonen het resultaat na een succesvolle voorbereidingsbewerking. De eerste toont een resourcegroep die de oorspronkelijke cloudservice bevat. De tweede toont de nieuwe brongroep 'Gemigreerd' die de gelijkwaardige Azure Resource Manager-resources bevat.

![Schermafbeelding van de oorspronkelijke cloudservice](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Schermafbeelding van Azure Resource Manager-bronnen in de voorbereidingsbewerking](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Hier is een kijkje achter de schermen bij uw bronnen na de voltooiing van de voorbereidingsfase. Houd er rekening mee dat de bron in het gegevensvlak hetzelfde is. Het is vertegenwoordigd in zowel het beheervlak (klassiek implementatiemodel) als het besturingsvlak (Resource Manager).

![Diagram van de voorbereidingsfase](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> VM's die zich niet in een virtueel netwerk in het klassieke implementatiemodel bevinden, worden gestopt en in deze fase van migratie gedeald.
>

### <a name="check-manual-or-scripted"></a>Controleren (handmatig of gepland)
In de controlestap hebt u de optie om de configuratie die u eerder hebt gedownload te gebruiken om te valideren dat de migratie er correct uitziet. U zich ook aanmelden bij de portal en de eigenschappen en bronnen ter plaatse controleren om te valideren dat de migratie met gegevens metagegevens er goed uitziet.

Als u een virtueel netwerk migreert, wordt de configuratie van virtuele machines meestal niet opnieuw opgestart. Voor toepassingen op deze VM's u valideren dat de toepassing nog steeds wordt uitgevoerd.

U uw monitoring- en operationele scripts testen om te zien of de VM's werken zoals verwacht en of uw bijgewerkte scripts correct werken. Alleen GET-bewerkingen worden ondersteund wanneer de resources de status Voorbereid hebben.

Er is geen vast tijdsvenster waarvoor u de migratie moet vastleggen. U kunt zoveel tijd nemen als u wilt in deze fase. Het beheervlak is echter wel vergrendeld voor deze resources totdat u de migratie afbreekt of doorvoert.

Als er problemen zijn, kunt u altijd de migratie altijd afbreken en terugkeren naar het klassieke implementatiemodel. Nadat u terug bent gegaan, opent Azure de beheervlakbewerkingen op de resources, zodat u de normale bewerkingen van deze VM's in het klassieke implementatiemodel hervatten.

### <a name="abort"></a>Afbreken
Dit is een optionele stap als u uw wijzigingen wilt terugdraaien naar het klassieke implementatiemodel en de migratie wilt stoppen. Met deze bewerking worden de metagegevens van Resource Manager (gemaakt in de voorbereidingsstap) voor uw resources verwijderd. 

![Diagram van de afbreekstap](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Deze bewerking kan niet worden uitgevoerd nadat u de commit-bewerking hebt geactiveerd.     
>

### <a name="commit"></a>Doorvoeren
Nadat de validatie is voltooid, kunt u de migratie doorvoeren. Resources worden niet meer weergegeven in het klassieke implementatiemodel en zijn alleen beschikbaar in het implementatiemodel resourcebeheer. De gemigreerde resources kunnen alleen in de nieuwe portal worden beheerd.

> [!NOTE]
> Dit is een idempotente bewerking. Als het mislukt, probeert u de bewerking opnieuw. Als het mislukt, maakt u een ondersteuningsticket of maakt u een forum op [Microsoft Q&A](https://docs.microsoft.com/answers/index.html)
>
>

![Diagram van commit-stap](../articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Migratiestroomdiagram

Hier is een stroomdiagram dat laat zien hoe verder te gaan met migratie:

![Schermafbeelding van de migratiestappen](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Vertaling van het klassieke implementatiemodel naar resourcemanagerbronnen
In de volgende tabel vindt u het klassieke implementatiemodel en resourcemanager-weergaven van de resources. Andere functies en resources worden momenteel niet ondersteund.

| Klassieke weergave | Weergave van de Resource Manager | Opmerkingen |
| --- | --- | --- |
| Naam cloudservice |DNS-naam |Tijdens de migratie wordt een nieuwe resourcegroep gemaakt voor elke cloudservice met het naamgevingspatroon `<cloudservicename>-migrated`. Deze resourcegroep bevat al uw resources. De naam van de cloudservice wordt een DNS-naam die is gekoppeld aan het openbare IP-adres. |
| Virtuele machine |Virtuele machine |VM-specifieke eigenschappen worden ongewijzigd gemigreerd. Bepaalde osProfile-informatie, zoals de naam van de computer, wordt niet opgeslagen in het klassieke implementatiemodel en blijft leeg na migratie. |
| Schijfresources die zijn gekoppeld aan de VM |Impliciete schijven die zijn gekoppeld aan de VM |Schijven worden niet gemodelleerd als resources op het hoogste niveau in het Resource Manager-implementatiemodel. Ze worden gemigreerd als impliciete schijven onder de virtuele machine. Alleen de schijven die zijn gekoppeld aan een virtuele machine worden momenteel ondersteund. Vm's voor ResourceBeheer kunnen nu opslagaccounts gebruiken in het klassieke implementatiemodel, waardoor de schijven eenvoudig kunnen worden gemigreerd zonder updates. |
| VM-extensies |VM-extensies |Alle de resource-extensies, met uitzondering van XML-extensies, worden gemigreerd vanuit het klassieke implementatiemodel. |
| Certificaten van virtuele machine |Certificaten in Azure Key Vault |Als een cloudservice servicecertificaten bevat, wordt met de migratie een nieuwe Azure-sleutelkluis per cloudservice gemaakt en worden de certificaten naar de sleutelkluis verplaatst. De virtuele machines worden bijgewerkt zodat ze verwijzen naar de certificaten van de sleutelkluis. <br><br> Verwijder de sleutelkluis niet. Hierdoor kan de VM in een mislukte status terechtkomen. |
| WinRM-configuratie |WinRM-configuratie in osProfile |De Windows Remote Management-configuratie wordt ongewijzigd verplaatst als onderdeel van de migratie. |
| Eigenschap van beschikbaarheidsset |Resource van beschikbaarheidsset | Beschikbaarheidssetspecificatie is een eigenschap op de VM in het klassieke implementatiemodel. Beschikbaarheidssets worden een resource op het hoogste niveau als onderdeel van de migratie. De volgende configuraties worden niet ondersteund: meerdere beschikbaarheidssets per cloudservice of één of meer beschikbaarheidssets samen met de virtuele machines die zich niet in een beschikbaarheidsset in een cloudservice bevinden. |
| Netwerkconfiguratie op een virtuele machine |Primaire netwerkinterface |De netwerkconfiguratie op een virtuele machine wordt weergegeven als de resource primaire netwerkinterface na de migratie. Het interne IP-adres van virtuele machines die zich niet in een virtueel netwerk bevinden, wordt gewijzigd tijdens de migratie. |
| Meerdere netwerkinterfaces in een VM |Netwerkinterfaces |Als aan een VM meerdere netwerkinterfaces zijn gekoppeld, wordt elke netwerkinterface een bron op het hoogste niveau als onderdeel van de migratie, samen met alle eigenschappen. |
| Eindpuntset met gelijke taakverdeling |Load balancer |In het klassieke implementatiemodel wees het platform een impliciete load balancer toe aan elke cloudservice. Tijdens de migratie wordt een nieuwe load-balancerresource gemaakt. De eindpuntset met gelijke taakverdeling wordt omgezet in load-balancerregels. |
| Inkomende NAT-regels |Inkomende NAT-regels |Invoereindpunten die zijn gedefinieerd op de virtuele machine worden tijdens de migratie geconverteerd naar binnenkomende vertaalregels voor netwerkadressen onder de load balancer. |
| VIP-adres |Openbaar IP-adres met DNS-naam |Het virtuele IP-adres wordt een openbaar IP-adres en is gekoppeld aan de load balancer. Een virtueel IP-adres kan alleen worden gemigreerd als er een invoereindpunt aan is toegewezen. |
| Virtueel netwerk |Virtueel netwerk |Het virtuele netwerk wordt met alle eigenschappen gemigreerd naar het Resource Manager-implementatiemodel. Er wordt een nieuwe resourcegroep gemaakt met de naam `-migrated`. |
| Gereserveerde IP-adressen |Openbaar IP-adres met een statische toewijzingsmethode |Gereserveerd IP-adressen die zijn gekoppeld aan de load balancer worden gemigreerd, samen met de migratie van de cloudservice of de virtuele machine. Niet-gekoppelde gereserveerde IP-migratie wordt momenteel niet ondersteund. |
| Openbaar IP-adres per VM |Openbaar IP-adres met een dynamische toewijzingsmethode |Het openbare IP-adres dat is gekoppeld aan de virtuele machine wordt geconverteerd naar een openbare IP-adresresource. De toewijzingsmethode wordt ingesteld op statisch. |
| NSG's |NSG's |Netwerkbeveiligingsgroepen die zijn gekoppeld aan een subnet worden gekloond als onderdeel van de migratie naar het Resource Manager-implementatiemodel. De NSG in het klassieke implementatiemodel wordt niet verwijderd tijdens de migratie. De bewerkingen op het beheervlak voor de NSG worden echter geblokkeerd wanneer de migratie wordt uitgevoerd. |
| DNS-servers |DNS-servers |DNS-servers die zijn gekoppeld aan een virtueel netwerk of de virtuele machine worden gemigreerd als onderdeel van de bijbehorende resourcemigratie, samen met alle eigenschappen. |
| UDR's |UDR's |Door de gebruiker gedefinieerde routes die zijn gekoppeld aan een subnet worden gekloond als onderdeel van de migratie naar het Resource Manager-implementatiemodel. De UDR in het klassieke implementatiemodel wordt niet verwijderd tijdens de migratie. De bewerkingen op het beheervlak voor de UDR worden geblokkeerd wanneer de migratie wordt uitgevoerd. |
| De eigenschap Doorsturen via IP op de netwerkconfiguratie van een virtuele machine |De eigenschap Doorsturen via IP op de NIC |De eigenschap Doorsturen via IP op een virtuele machine wordt tijdens de migratie geconverteerd naar een eigenschap op de netwerkinterface. |
| Load balancer met meerdere IP-adressen |Load balancer met meerdere openbare IP-resources |Elk openbaar IP-adres dat aan de load balancer is gekoppeld, wordt geconverteerd naar een openbare IP-bron en gekoppeld aan de load balancer na migratie. |
| Interne DNS-namen op de virtuele machine |Interne DNS-namen op de NIC |Tijdens de migratie worden de interne DNS-achtervoegsels voor de virtuele machines gemigreerd naar een alleen-lezeneigenschap met de naam 'InternalDomainNameSuffix' op de NIC. Het achtervoegsel blijft ongewijzigd na migratie en vm-resolutie moet blijven werken zoals voorheen. |
| Gateway van een virtueel netwerk |Gateway van een virtueel netwerk |Eigenschappen van virtuele netwerkgatewayworden ongewijzigd gemigreerd. Het VIP dat is gekoppeld aan de gateway wordt ook niet gewijzigd. |
| Lokale netwerksite |Lokale netwerkgateway |Lokale netwerksiteeigenschappen worden ongewijzigd gemigreerd naar een nieuwe bron, een lokale netwerkgateway. Dit vertegenwoordigt on-premises adresvoorvoegsels en het externe gateway-IP. |
| Verwijzingen naar verbindingen |Verbinding |Connectiviteitsverwijzingen tussen de gateway en de lokale netwerksite in netwerkconfiguratie worden weergegeven door een nieuwe bron genaamd Verbinding. Alle eigenschappen van verbindingsverwijzing in netwerkconfiguratiebestanden worden ongewijzigd gekopieerd naar de verbindingsbron. Connectiviteit tussen virtuele netwerken in het klassieke implementatiemodel wordt bereikt door twee IPsec-tunnels te maken naar lokale netwerksites die de virtuele netwerken vertegenwoordigen. Dit wordt omgezet naar het type verbinding tussen virtuele netwerken en virtuele netwerken in het Resource Manager-model, zonder dat er lokale netwerkgateways nodig zijn. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Wijzigingen aanbrengen in uw automatisering en hulpprogramma’s na de migratie
Als onderdeel van het migreren van uw resources van het klassieke implementatiemodel naar het implementatiemodel resourcebeheer, moet u uw bestaande automatisering of tooling bijwerken om ervoor te zorgen dat deze na de migratie blijft werken.
