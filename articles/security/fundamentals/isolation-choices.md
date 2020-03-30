---
title: Isolatie in de Azure Public Cloud | Microsoft Documenten
description: Ontdek hoe Azure isolatie biedt tegen zowel kwaadwillende als niet-kwaadwillende gebruikers en verschillende isolatiekeuzes biedt aan architecten.
services: security
documentationcenter: na
author: UnifyCloud
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: TomSh
ms.openlocfilehash: c6e74e7992326d2a4b8fe24510742422b005c2e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280311"
---
# <a name="isolation-in-the-azure-public-cloud"></a>Isolatie in de Azure Public Cloud
Met Azure u toepassingen en virtuele machines (VM's) uitvoeren op gedeelde fysieke infrastructuur. Een van de belangrijkste economische motivaties voor het uitvoeren van toepassingen in een cloudomgeving is de mogelijkheid om de kosten van gedeelde resources onder meerdere klanten te verdelen. Deze praktijk van multi-tenancy verbetert de efficiëntie door het multiplexen van middelen onder uiteenlopende klanten tegen lage kosten. Helaas, het introduceert ook het risico van het delen van fysieke servers en andere infrastructuur bronnen om uw gevoelige toepassingen en VM's die kunnen behoren tot een willekeurige en potentieel kwaadwillende gebruiker uit te voeren.

In dit artikel wordt beschreven hoe Azure isolatie biedt tegen zowel kwaadwillende als niet-kwaadwillende gebruikers en dient het als een leidraad voor het ontwerpen van cloudoplossingen door architecten verschillende isolatiekeuzes aan te bieden.

## <a name="tenant-level-isolation"></a>Isolatie van tenantniveau
Een van de belangrijkste voordelen van cloud computing is het concept van een gedeelde, gemeenschappelijke infrastructuur voor tal van klanten tegelijk, wat leidt tot schaalvoordelen. Dit concept heet multi-tenancy. Microsoft werkt er voortdurend aan om ervoor te zorgen dat de multi-tenantarchitectuur van Microsoft Cloud Azure beveiligings-, vertrouwelijkheids-, privacy-, integriteits- en beschikbaarheidsstandaarden ondersteunt.

Met betrekking tot werklocaties in de cloud kan een tenant worden gedefinieerd als een client of organisatie die een bepaald exemplaar van de gebruikte cloudservice in eigendom heeft en beheert. Met het identiteitsplatform van Microsoft Azure is een tenant gewoon een speciaal exemplaar van Azure Active Directory (Azure AD) dat uw organisatie ontvangt en bezit wanneer deze zich aanmeldt voor een Microsoft-cloudservice.

Elke Azure AD-directory is uniek en werkt afzonderlijk van andere Azure AD-directory’s. Net zoals een kantoorgebouw een beveiligd bedrijfsmiddel is van uw organisatie, is ook een Azure AD-directory ontworpen als een beveiligd bedrijfsmiddel dat alleen door uw organisatie kan worden gebruikt. De Azure AD-architectuur isoleert klant- en identiteitsgegevens, zodat deze niet door elkaar worden gehaald. Dit betekent dat gebruikers en beheerders van een Azure AD-directory niet per ongeluk of opzettelijk gegevens in een andere directory kunnen openen.

### <a name="azure-tenancy"></a>Azure-tenancy
Azure-huur (Azure-abonnement) verwijst naar een relatie tussen klant en facturering en een unieke [tenant](../../active-directory/develop/quickstart-create-new-tenant.md) in [Azure Active Directory.](../../active-directory/fundamentals/active-directory-whatis.md) Isolatie op tenantniveau in Microsoft Azure wordt bereikt met Azure Active Directory en [op rollen gebaseerde besturingselementen](../../role-based-access-control/overview.md) die door microsoft Azure worden aangeboden. Elk Azure-abonnement is gekoppeld aan één AD-map (Azure Active Directory).

Gebruikers, groepen en toepassingen uit die map kunnen resources beheren in het Azure-abonnement. U deze toegangsrechten toewijzen met behulp van de Azure-portal, Azure-opdrachtregelhulpprogramma's en Azure Management-API's. Een Azure AD-tenant is logisch geïsoleerd met behulp van beveiligingsgrenzen, zodat geen enkele klant medetenants kan openen of compromitteren, kwaadwillig of per ongeluk. Azure AD draait op 'bare metal'-servers geïsoleerd in een gescheiden netwerksegment, waar pakketfiltering op hostniveau en Windows Firewall ongewenste verbindingen en verkeer blokkeren.

- Toegang tot gegevens in Azure AD vereist gebruikersverificatie via een security token service (STS). Informatie over het bestaan, de ingeschakelde status en de rol van de gebruiker wordt door het autorisatiesysteem gebruikt om te bepalen of de gevraagde toegang tot de doeltenant in deze sessie is geautoriseerd voor deze gebruiker.

![Azure-tenancy](./media/isolation-choices/azure-isolation-fig1.png)


- Huurders zijn discrete containers en er is geen relatie tussen deze.

- Geen toegang tussen tenants, tenzij tenant beheerder verleent het via federatie of het inrichten van gebruikersaccounts van andere tenants.

- Fysieke toegang tot servers die de Azure AD-service omvatten en directe toegang tot de back-endsystemen van Azure AD is beperkt.

- Azure AD-gebruikers hebben geen toegang tot fysieke assets of locaties en daarom is het niet mogelijk voor hen om de logische RBAC-beleidscontroles te omzeilen die als volgt worden vermeld.

Voor diagnose- en onderhoudsbehoeften is een operationeel model vereist dat gebruik maakt van een just-in-time privilege elevation systeem en wordt gebruikt. Azure AD Privileged Identity Management (PIM) introduceert het concept van een in aanmerking komende beheerder. [In aanmerking komende beheerders](../../active-directory/privileged-identity-management/pim-configure.md) moeten gebruikers zijn die af en toe bevoorrechte toegang nodig hebben, maar niet elke dag. De rol is inactief totdat gebruikers toegang nodig hebben. Op dat moment voeren ze een activeringsproces uit en zijn ze gedurende een vooraf bepaalde hoeveelheid tijd een actieve beheerder.

![Azure AD Privileged Identity Management](./media/isolation-choices/azure-isolation-fig2.png)

Azure Active Directory host elke tenant in een eigen beveiligde container, met beleidsregels en machtigingen voor en binnen de container die uitsluitend eigendom zijn van en worden beheerd door de tenant.

Het concept van tenantcontainers is diep geworteld in de directoryservice op alle lagen, van portals tot permanente opslag.

Zelfs wanneer metagegevens van meerdere Azure Active Directory-tenants op dezelfde fysieke schijf worden opgeslagen, is er geen relatie tussen de andere containers dan wat wordt gedefinieerd door de directoryservice, die op zijn beurt wordt gedicteerd door de tenantbeheerder.

### <a name="azure-role-based-access-control-rbac"></a>Op Azure-rolegebaseerdtoegangsbeheer (RBAC)
[Met RBAC (Azure Role-Based Access Control)](../../role-based-access-control/overview.md) u verschillende onderdelen delen die beschikbaar zijn binnen een Azure-abonnement door fijnkorrelig toegangsbeheer voor Azure te bieden. Azure RBAC stelt u in staat om taken binnen uw organisatie te scheiden en toegang te verlenen op basis van wat gebruikers nodig hebben om hun taken uit te voeren. In plaats van iedereen onbeperkte machtigingen te geven in Azure-abonnementen of -bronnen, u alleen bepaalde acties toestaan.

Azure RBAC heeft drie basisrollen die van toepassing zijn op alle resourcetypen:

- **De eigenaar** heeft volledige toegang tot alle bronnen, inclusief het recht om de toegang tot anderen te delegeren.

- **Contribuant** kan alle typen Azure-resources maken en beheren, maar kan geen toegang verlenen aan anderen.

- **Reader** kan bestaande Azure-bronnen weergeven.

![Op rollen gebaseerd toegangsbeheer van Azure](./media/isolation-choices/azure-isolation-fig3.png)

De rest van de RBAC-rollen in Azure maken beheer van specifieke Azure-resources mogelijk. Met de rol Inzender voor virtuele machines kan een gebruiker bijvoorbeeld virtuele machines maken en beheren. Het geeft hen geen toegang tot het Azure Virtual Network of het subnet waarmee de virtuele machine verbinding maakt.

[RBAC ingebouwde rollen](../../role-based-access-control/built-in-roles.md) geven een overzicht van de rollen die beschikbaar zijn in Azure. Het geeft de bewerkingen en het bereik aan die elke ingebouwde rol aan gebruikers verleent. Als u uw eigen rollen wilt definiëren voor nog meer controle, raadpleegt u hoe u aangepaste rollen bouwen [in Azure RBAC.](../../role-based-access-control/custom-roles.md)

Enkele andere mogelijkheden voor Azure Active Directory zijn:
- Azure AD maakt eenmalige aanmelding (SSO) bij SaaS-toepassingen mogelijk, ongeacht waar ze worden gehost. Voor sommige toepassingen kan federatieve aanmelding worden gebruikt via Azure AD en voor andere toepassingen kan eenmalige aanmelding (SSO) met een wachtwoord worden gebruikt. Federatieve toepassingen kunnen ook ondersteuning bieden voor het inrichten van gebruikers en [het kluisspringen van wachtwoorden.](https://www.techopedia.com/definition/31415/password-vault)

- De toegang tot gegevens in [Azure Storage](https://azure.microsoft.com/services/storage/) wordt geregeld via verificatie. Elk opslagaccount heeft een primaire sleutel[(opslagaccountsleutel](../../storage/common/storage-create-storage-account.md)of SAK) en een secundaire geheime sleutel (de handtekening van gedeelde toegang of SAS).

- Azure AD biedt Identity as a Service via federation door gebruik te maken van [Active Directory Federation Services,](../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md)synchronisatie en replicatie met on-premises mappen.

- [Azure Multi-Factor Authentication](../../active-directory/authentication/multi-factor-authentication.md) is de multi-factor authenticatieservice die gebruikers verplicht om aanmeldingen te verifiëren met behulp van een mobiele app, telefoongesprek of sms. Het kan worden gebruikt met Azure AD om on-premises resources te beveiligen met de Azure Multi-Factor Authentication-server, en ook met aangepaste toepassingen en mappen met behulp van de SDK.

- [Met Azure AD Domain Services](https://azure.microsoft.com/services/active-directory-ds/) u virtuele Azure-machines aansluiten bij een Active Directory-domein zonder domeincontrollers te implementeren. U zich aanmelden bij deze virtuele machines met uw bedrijfsActive Directory-referenties en virtuele machines met domeinverbonden beheren met behulp van groepsbeleid om beveiligingsbasislijnen op al uw virtuele Azure-machines af te dwingen.

- [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) biedt een zeer beschikbare service voor beheer over wereldwijde identiteit voor toepassingen die gericht zijn op consumenten die worden geschaald naar honderden miljoenen identiteiten. De service kan worden geïntegreerd in zowel mobiele als webplatforms. Uw consumenten kunnen zich aanmelden bij al uw toepassingen door middel van aanpasbare ervaringen door gebruik te maken van hun bestaande sociale accounts of door referenties te maken.

### <a name="isolation-from-microsoft-administrators--data-deletion"></a>Isolatie van Microsoft-beheerders & het verwijderen van gegevens
Microsoft neemt krachtige maatregelen om uw gegevens te beschermen tegen ongepaste toegang of gebruik door onbevoegde personen. Deze operationele processen en controles worden ondersteund door de [Voorwaarden voor onlineservices,](https://aka.ms/Online-Services-Terms)die contractuele verplichtingen bieden die de toegang tot uw gegevens regelen.

-   Microsoft-technici hebben geen standaardtoegang tot uw gegevens in de cloud. In plaats daarvan krijgen ze alleen toegang onder toezicht van het management als dat nodig is. Die toegang wordt zorgvuldig gecontroleerd en geregistreerd, en ingetrokken wanneer deze niet meer nodig is.

-   Microsoft kan andere bedrijven inhuren om namens Microsoft beperkte services te leveren. Onderaannemers hebben alleen toegang tot klantgegevens om de diensten te leveren waarvoor we ze hebben ingehuurd om te leveren, en het is hen verboden deze voor andere doeleinden te gebruiken. Verder zijn ze contractueel verplicht om de vertrouwelijkheid van de informatie van onze klanten te bewaren.

Zakelijke diensten met gecontroleerde certificeringen zoals ISO/IEC 27001 worden regelmatig geverifieerd door Microsoft en geaccrediteerde accountantskantoren, die steekproefaudits uitvoeren om die toegang te bevestigen, alleen voor legitieme zakelijke doeleinden. U heeft altijd en om welke reden dan ook toegang tot uw eigen klantgegevens.

Als u gegevens verwijdert, verwijdert Microsoft Azure de gegevens, inclusief eventuele cache- of back-upkopieën. Voor service in het bereik vindt die verwijdering plaats binnen 90 dagen na het einde van de bewaartermijn. (In-scope services worden gedefinieerd in de sectie Gegevensverwerkingsvoorwaarden van onze [Online Services-voorwaarden](https://aka.ms/Online-Services-Terms).)

Als een schijf die wordt gebruikt voor opslag een hardwarefout ondervindt, wordt deze veilig [gewist of vernietigd](https://microsoft.com/trustcenter/privacy/you-own-your-data) voordat Microsoft deze terugstuurt naar de fabrikant voor vervanging of reparatie. De gegevens op het station worden overschreven om ervoor te zorgen dat de gegevens op geen enkele manier kunnen worden hersteld.

## <a name="compute-isolation"></a>Compute-isolatie
Microsoft Azure biedt verschillende cloudgebaseerde computerservices die een brede selectie van compute instances bevatten & services die automatisch kunnen worden opgeschaald en omlaag kunnen om aan de behoeften van uw toepassing of onderneming te voldoen. Deze compute instance en service bieden isolatie op meerdere niveaus om gegevens te beveiligen zonder in te boeten aan de flexibiliteit in de configuratie die klanten vragen.

### <a name="isolated-virtual-machine-sizes"></a>Geïsoleerde virtuele machineformaten

[!INCLUDE [virtual-machines-common-isolation](../../../includes/virtual-machines-common-isolation.md)]

### <a name="dedicated-hosts"></a>Toegewezen hosts
Naast de geïsoleerde hosts die in de vorige sectie worden beschreven, biedt Azure ook speciale hosts. Dedicated hosts in Azure is een service die fysieke servers biedt die een of meer virtuele machines kunnen hosten en die zijn toegewezen aan één Azure-abonnement. Speciale hosts bieden hardwareisolatie op fysiek serverniveau. Er worden geen andere VM's op uw hosts geplaatst. Speciale hosts worden geïmplementeerd in dezelfde datacenters en delen dezelfde netwerk- en onderliggende opslaginfrastructuur als andere niet-geïsoleerde hosts. Zie het gedetailleerde overzicht van [azure dedicated hosts](https://docs.microsoft.com/azure/virtual-machines/windows/dedicated-hosts)voor meer informatie.

### <a name="hyper-v--root-os-isolation-between-root-vm--guest-vms"></a>Hyper-V-& Root OS-isolatie tussen rootVM-& gastvm's
Het rekenplatform van Azure is gebaseerd op machinevirtualisatie, wat betekent dat alle klantcode wordt uitgevoerd in een virtuele hyper-v-machine. Op elk Azure-knooppunt (of netwerkeindpunt) is er een Hypervisor die rechtstreeks over de hardware draait en een knooppunt verdeelt in een variabel aantal Virtuele Gastmachines (VM's).


![Hyper-V-& Root OS-isolatie tussen rootVM-& gastvm's](./media/isolation-choices/azure-isolation-fig4.jpg)


Elk knooppunt heeft ook een speciale Root VM, die het Host OS draait. Een kritieke grens is de isolatie van de root VM van de gast VM's en de gast VM's van elkaar, beheerd door de hypervisor en de root OS. De hypervisor/root OS-koppeling maakt gebruik van microsoft's decennialange beveiligingservaring voor besturingssystemen en recenter leren van Microsoft's Hyper-V, om een sterke isolatie van gastVM's te bieden.

Voor het Azure-platform wordt gebruikgemaakt van een gevirtualiseerde omgeving. Gebruikersexemplaren werken als zelfstandige virtuele machines die geen toegang hebben tot een fysieke hostserver.

De Azure-hypervisor werkt als een microkernel en geeft alle hardwaretoegangsaanvragen van virtuele gastmachines door aan de host voor verwerking met behulp van een interface met gedeeld geheugen genaamd VMBus. Dit voorkomt dat gebruikers onbeperkt toegang krijgen tot het systeem om gegevens te lezen en te schrijven of programma’s uit te voeren en vermindert de risico’s die kleven aan het delen van systeemresources.

### <a name="advanced-vm-placement-algorithm--protection-from-side-channel-attacks"></a>Geavanceerd VM-plaatsingsalgoritme & bescherming tegen aanvallen via zijkanalen
Elke cross-VM-aanval omvat twee stappen: het plaatsen van een tegenstander gecontroleerde VM op dezelfde host als een van de slachtoffer VM's, en vervolgens het doorbreken van de isolatie grens om ofwel gevoelige slachtoffer informatie te stelen of de prestaties ervan beïnvloeden voor hebzucht of vandalisme. Microsoft Azure biedt bescherming bij beide stappen door gebruik te maken van een geavanceerd VM-plaatsingsalgoritme en bescherming tegen alle bekende side channel-aanvallen, waaronder luidruchtige neighbor-VM's.

### <a name="the-azure-fabric-controller"></a>De Azure Fabric Controller
De Azure Fabric Controller is verantwoordelijk voor het toewijzen van infrastructuurresources aan tenantworkloads en beheert unidirectionele communicatie van de host naar virtuele machines. Het VM-plaatsingsalgoritme van de Azure-fabriccontroller is zeer geavanceerd en bijna onmogelijk te voorspellen als fysiek hostniveau.

![De Azure Fabric Controller](./media/isolation-choices/azure-isolation-fig5.png)

De Azure-hypervisor dwingt geheugen en processcheiding tussen virtuele machines af en leidt het netwerkverkeer veilig door naar huurders van gast-besturingssysteem. Dit elimineert de mogelijkheid van en side channel aanval op VM-niveau.

In Azure is de root-VM speciaal: het draait een gehard besturingssysteem genaamd het root-besturingssysteem dat een fabricagent (FA) host. FA's worden op hun beurt gebruikt om gastagenten (GA) te beheren binnen gast-OEM's op vm's van klanten. FA's beheren ook opslagknooppunten.

De verzameling Azure-hypervisor, root OS/FA en vm's/CA's van de klant bestaat uit een compute node. FE's worden beheerd door een fabric controller (FC), die buiten compute en storage nodes bestaat (compute en storage clusters worden beheerd door afzonderlijke FCs). Als een klant het configuratiebestand van zijn of haar toepassing bijwerkt terwijl deze wordt uitgevoerd, communiceert de FC met de FA, die vervolgens contact opneemt met DE's, die de toepassing van de configuratiewijziging melden. In het geval van een hardwarestoring zal de FC automatisch beschikbare hardware vinden en de VM daar opnieuw starten.

![Azure-structuurcontroller](./media/isolation-choices/azure-isolation-fig6.jpg)

Communicatie van een Fabric Controller naar een agent is unidirectioneel. De agent implementeert een SSL-beveiligde service die alleen reageert op verzoeken van de controller. Het kan geen verbindingen met de controller of andere bevoorrechte interne knooppunten starten. De FC behandelt alle reacties alsof ze niet vertrouwd zijn.


![Stofcontroller](./media/isolation-choices/azure-isolation-fig7.png)

Isolatie strekt zich uit van de Root VM van Gast VM's, en de Gast VM's van elkaar. Compute-knooppunten zijn ook geïsoleerd van opslagknooppunten voor meer bescherming.


De hypervisor en het hostbesturingssysteem bieden netwerkpakket - filters om ervoor te zorgen dat niet-vertrouwde virtuele machines geen vervalst verkeer kunnen genereren of verkeer kunnen ontvangen dat niet aan hen is geadresseerd, verkeer naar beveiligde infrastructuureindpunten kunnen leiden of ongepast uitzendverkeer verzenden/ontvangen.


### <a name="additional-rules-configured-by-fabric-controller-agent-to-isolate-vm"></a>Aanvullende regels die door fabriccontrolleragent zijn geconfigureerd om VM te isoleren
Standaard wordt al het verkeer geblokkeerd wanneer een virtuele machine wordt gemaakt en configureert de fabriccontrolleragent het pakketfilter om regels en uitzonderingen toe te voegen om geautoriseerd verkeer toe te staan.

Er zijn twee categorieën regels die zijn geprogrammeerd:

-   **Machineconfiguratie- of infrastructuurregels:** Standaard wordt alle communicatie geblokkeerd. Er zijn uitzonderingen om een virtuele machine dhcp- en DNS-verkeer te laten verzenden en ontvangen. Virtuele machines kunnen ook verkeer naar het "openbare" internet verzenden en verkeer verzenden naar andere virtuele machines binnen hetzelfde Azure Virtual Network en de OS-activeringsserver. De lijst met toegestane uitgaande bestemmingen van de virtuele machines bevat geen subnetten van Azure-routers, Azure-beheer en andere Microsoft-eigenschappen.

-   **Rolconfiguratiebestand:** Hiermee worden de binnenkomende Toegangscontrolelijsten (ACL's) gedefinieerd op basis van het servicemodel van de tenant.

### <a name="vlan-isolation"></a>VLAN-isolatie
Er zijn drie VLAN's in elk cluster:

![VLAN-isolatie](./media/isolation-choices/azure-isolation-fig8.jpg)


-   De belangrijkste VLAN – verbindt niet-vertrouwde klantknooppunten

-   De FC VLAN – bevat vertrouwde FCs en ondersteunende systemen

-   Het apparaat VLAN – bevat vertrouwde netwerk- en andere infrastructuurapparaten

Communicatie is toegestaan van de FC VLAN naar de belangrijkste VLAN, maar kan niet worden gestart van de belangrijkste VLAN naar de FC VLAN. Communicatie is ook geblokkeerd van de belangrijkste VLAN naar het apparaat VLAN. Dit zorgt ervoor dat zelfs als een knooppunt met klantcode wordt gecompromitteerd, het geen knooppunten kan aanvallen op de FC of het apparaat VLAN's.

## <a name="storage-isolation"></a>Opslagisolatie
### <a name="logical-isolation-between-compute-and-storage"></a>Logische isolatie tussen rekenkracht en opslag
Als onderdeel van het fundamentele ontwerp scheidt Microsoft Azure vm-gebaseerde berekeningen van opslag. Deze scheiding maakt het mogelijk om berekeningen en opslag onafhankelijk te schalen, waardoor het gemakkelijker wordt om multi-tenancy en isolatie te bieden.

Azure Storage draait daarom op afzonderlijke hardware zonder netwerkverbinding met Azure Compute, behalve logisch. Dit betekent dat wanneer een virtuele schijf wordt gemaakt, schijfruimte niet wordt toegewezen voor de volledige capaciteit. In plaats daarvan wordt een tabel gemaakt die adressen op de virtuele schijf toebrengt aan gebieden op de fysieke schijf en die tabel in eerste instantie leeg is. **De eerste keer dat een klant gegevens schrijft op de virtuele schijf, wordt ruimte op de fysieke schijf toegewezen en wordt er een aanwijzer naar geplaatst in de tabel.**
### <a name="isolation-using-storage-access-control"></a>Isolatie met opslagtoegangsbesturingselement
**Access Control in Azure Storage** heeft een eenvoudig toegangscontrolemodel. Elk Azure-abonnement kan een of meer opslagaccounts maken. Elk opslagaccount heeft één geheime sleutel die wordt gebruikt om de toegang tot alle gegevens in dat opslagaccount te beheren.

![Isolatie met opslagtoegangsbesturingselement](./media/isolation-choices/azure-isolation-fig9.png)

**Toegang tot Azure Storage-gegevens (inclusief tabellen)** kan worden beheerd via een [SAS-token (Shared Access Signature),](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) waarmee scoped-toegang wordt toegekend. De SAS wordt gemaakt via een querysjabloon (URL), ondertekend met de [SAK -toets (Storage Account Key).](https://msdn.microsoft.com/library/azure/ee460785.aspx) Die [ondertekende URL](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) kan worden gegeven aan een ander proces (dat wil zeggen, gedelegeerd), dat vervolgens de details van de query kan invullen en de aanvraag van de opslagservice kan indienen. Met een SAS u op tijd gebaseerde toegang verlenen aan clients zonder de geheime sleutel van het opslagaccount te onthullen.

De SAS betekent dat we een client beperkte machtigingen kunnen verlenen, aan objecten in ons opslagaccount voor een bepaalde periode en met een bepaalde set machtigingen. We kunnen deze beperkte machtigingen verlenen zonder dat u uw toegangssleutels voor uw account hoeft te delen.

### <a name="ip-level-storage-isolation"></a>Opslagisolatie ip-niveau
U firewalls instellen en een IP-adresbereik definiëren voor uw vertrouwde clients. Met een IP-adresbereik kunnen alleen clients met een IP-adres binnen het gedefinieerde bereik verbinding maken met [Azure Storage.](../../storage/blobs/security-recommendations.md)

IP-opslaggegevens kunnen worden beschermd tegen onbevoegde gebruikers via een netwerkmechanisme dat wordt gebruikt om een speciale of speciale tunnel van verkeer toe te wijzen aan IP-opslag.

### <a name="encryption"></a>Versleuteling
Azure biedt de volgende typen versleuteling om gegevens te beschermen:
-   Versleuteling 'in transit'

-   Versleuteling 'at rest'

#### <a name="encryption-in-transit"></a>Versleuteling 'in transit'
Versleuteling tijdens het transport is een mechanisme om gegevens te beschermen wanneer deze via netwerken worden verzonden. Met Azure Storage u gegevens beveiligen met:

-   [Versleuteling op transportniveau,](../../storage/blobs/security-recommendations.md)zoals HTTPS wanneer u gegevens naar of uit Azure Storage overbrengt.

-   [Draadversleuteling](../../storage/blobs/security-recommendations.md), zoals SMB 3.0-versleuteling voor Azure File-shares.

-   [Client-side encryptie,](../../storage/blobs/security-recommendations.md)om de gegevens te versleutelen voordat het wordt overgedragen in de opslag en om de gegevens te decoderen nadat het is overgedragen uit de opslag.

#### <a name="encryption-at-rest"></a>Versleuteling 'at rest'
Voor veel organisaties is [gegevensversleuteling in rust](isolation-choices.md) een verplichte stap in de richting van gegevensprivacy, naleving en gegevenssoevereiniteit. Er zijn drie Azure-functies die versleuteling bieden van gegevens die 'in rust' zijn:

-   [Met Storage Service Encryption](../../storage/blobs/security-recommendations.md) u vragen dat de opslagservice gegevens automatisch versleutelt wanneer u deze naar Azure Storage schrijft.

-   [Client-side Encryptie](../../storage/blobs/security-recommendations.md) biedt ook de functie van encryptie in rust.

-   [Met Azure Disk Encryption](../azure-security-disk-encryption-overview.md) u de besturingssysteemschijven en gegevensschijven versleutelen die door een virtuele IaaS-machine worden gebruikt.

#### <a name="azure-disk-encryption"></a>Azure Disk Encryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) for virtual machines (VM's) helpt u om de vereisten voor organisatiebeveiliging en naleving te verhelpen door uw VM-schijven (inclusief opstart- en gegevensschijven) te versleutelen met sleutels en beleidsregels die u beheert in [Azure Key Vault.](https://azure.microsoft.com/services/key-vault/)

De Disk Encryption-oplossing voor Windows is gebaseerd op [Microsoft BitLocker Drive Encryption](https://technet.microsoft.com/library/cc732774.aspx), en de Linux-oplossing is gebaseerd op [dm-crypt.](https://en.wikipedia.org/wiki/Dm-crypt)

De oplossing ondersteunt de volgende scenario's voor IaaS VM's wanneer ze zijn ingeschakeld in Microsoft Azure:
-   Integratie met Azure Key Vault

-   Vm's van standaardlagen: A, D, DS, G, GS, enzovoort, IAAS VM's uit serie

-   Versleuteling inschakelen op Windows- en Linux IaaS VM's

-   Versleuteling uitschakelen op besturingssysteem- en gegevensstations voor Windows IaaS-vm's

-   Versleuteling uitschakelen op gegevensstations voor Linux IaaS VM's

-   Versleuteling inschakelen op IaaS VM's waarop Windows-client-besturingssysteem wordt uitgevoerd

-   Versleuteling inschakelen op volumes met bevestigingspaden

-   Versleuteling inschakelen op Linux VM's die zijn geconfigureerd met disk striping (RAID) met behulp van [mdadm](https://en.wikipedia.org/wiki/Mdadm)

-   Versleuteling op Linux VM's inschakelen met BEHULP van [LVM(Logical Volume Manager)](https://msdn.microsoft.com/library/windows/desktop/bb540532) voor gegevensschijven

-   Versleuteling inschakelen op Windows VM's die zijn geconfigureerd met behulp van opslagruimten

-   Alle openbare Azure-regio's worden ondersteund

De oplossing ondersteunt niet de volgende scenario's, functies en technologie in de release:

-   IaaS-vm's voor basislagen

-   Versleuteling uitschakelen op een OS-station voor Linux IaaS VM's

-   IaaS VM's die zijn gemaakt met behulp van de klassieke VM-creatiemethode

-   Integratie met uw on-premises Key Management Service

-   Azure Files (gedeeld bestandssysteem), Network File System (NFS), dynamische volumes en Windows VM's die zijn geconfigureerd met softwaregebaseerde RAID-systemen

## <a name="sql-azure-database-isolation"></a>SQL Azure-databaseisolatie
SQL Database is een relationele database-service in de Microsoft Cloud op basis van de toonaangevende Microsoft SQL Server-engine en is in staat bedrijfskritieke workloads af te handelen. SQL Database biedt voorspelbare gegevensisolatie op accountniveau, geografie / regio gebaseerd en gebaseerd op netwerken- allemaal met bijna nul beheer.

### <a name="sql-azure-application-model"></a>SQL Azure-toepassingsmodel

[Microsoft SQL Azure](../../sql-database/sql-database-single-database-get-started.md) Database is een cloudgebaseerde relationele databaseservice die is gebaseerd op SQL Server-technologieën. Het biedt een zeer beschikbare, schaalbare databaseservice met meerdere tenant's die door Microsoft in de cloud worden gehost.

Vanuit toepassingsperspectief biedt SQL Azure de volgende hiërarchie: elk niveau heeft een-op-veel insluiting van niveaus hieronder.

![SQL Azure-toepassingsmodel](./media/isolation-choices/azure-isolation-fig10.png)

Het account en het abonnement zijn Microsoft Azure-platformconcepten om facturering en beheer te koppelen.

Logische servers en databases zijn SQL Azure-specifieke concepten en worden beheerd met SQL Azure, mits OData- en TSQL-interfaces of via SQL Azure-portal die zijn geïntegreerd in Azure-portal.

SQL Azure-servers zijn geen fysieke of VM-exemplaren, maar verzamelingen van databases, beheer en beveiligingsbeleid voor delen, die zijn opgeslagen in de zogenaamde 'logische master'-database.

![SQL Azure](./media/isolation-choices/azure-isolation-fig11.png)

Logische masterdatabases zijn:

-   SQL-aanmeldingen die worden gebruikt om verbinding te maken met de server

-   Firewall-regels

Facturerings- en gebruiksgerelateerde informatie voor SQL Azure-databases van dezelfde logische server is niet gegarandeerd op dezelfde fysieke instantie in SQL Azure-cluster, in plaats daarvan moeten toepassingen de naam van de doeldatabase opgeven bij het maken van verbinding.

Vanuit klantperspectief wordt een logische server gemaakt in een geografisch gebied, terwijl de daadwerkelijke creatie van de server plaatsvindt in een van de clusters in de regio.

### <a name="isolation-through-network-topology"></a>Isolatie via netwerktopologie

Wanneer een logische server wordt gemaakt en de DNS-naam wordt geregistreerd, verwijst de DNS-naam naar het zogenaamde "Gateway VIP"-adres in het specifieke datacenter waar de server is geplaatst.

Achter de VIP (virtueel IP-adres) hebben we een verzameling stateloze gatewayservices. In het algemeen worden gateways betrokken wanneer er coördinatie nodig is tussen meerdere gegevensbronnen (hoofddatabase, gebruikersdatabase, enz.). Gatewayservices implementeren het volgende:
-   **TDS-verbinding proxying.** Dit omvat het lokaliseren van gebruikersdatabase in het backendcluster, het implementeren van de inlogvolgorde en het doorsturen van de TDS-pakketten naar de backend en terug.

-   **Databasebeheer.** Dit omvat het implementeren van een verzameling werkstromen om CREATE/ALTER/DROP-databasebewerkingen uit te voeren. De databasebewerkingen kunnen worden aangeroepen door tds-pakketten of expliciete OData-API's te snuiven.

-   Aanmelding/aanmelding/gebruiker maken/ALTER/DROP-bewerkingen

-   Logische serverbeheerbewerkingen via OData API

![Isolatie via netwerktopologie](./media/isolation-choices/azure-isolation-fig12.png)

De laag achter de gateways wordt "back-end" genoemd. Dit is waar alle gegevens worden opgeslagen in een zeer beschikbare manier. Elk stuk van de gegevens wordt gezegd dat behoren tot een "partitie" of "failover eenheid", elk van hen met ten minste drie replica's. Replica's worden opgeslagen en gerepliceerd door SQL Server engine en beheerd door een failover systeem vaak aangeduid als "fabric".

Over het algemeen communiceert het back-endsysteem niet als veiligheidsmaatregel uitgaande naar andere systemen. Dit is voorbehouden aan de systemen in de front-end (gateway) laag. De gateway tier machines hebben beperkte privileges op de back-end machines om het aanvalsoppervlak te minimaliseren als een verdediging-in-diepte mechanisme.

### <a name="isolation-by-machine-function-and-access"></a>Isolatie door machinefunctie en toegang
SQL Azure (bestaat uit services die op verschillende machinefuncties worden uitgevoerd. SQL Azure is onderverdeeld in "backend" Cloud Database en "front-end" (Gateway/ Management) omgevingen, met het algemene principe van het verkeer alleen gaan in back-end en niet uit. De front-end omgeving kan communiceren met de buitenwereld van andere diensten en in het algemeen, heeft slechts beperkte machtigingen in de back-end (genoeg om de toegangspunten die zij moet aanroepen noemen).

## <a name="networking-isolation"></a>Netwerkisolatie
Azure-implementatie heeft meerdere lagen netwerkisolatie. In het volgende diagram ziet u verschillende lagen van netwerkisolatie die Azure aan klanten biedt. Deze lagen zijn zowel inheems in het Azure-platform zelf als door de klant gedefinieerde functies. Azure DDoS is inkomende vanaf het internet en biedt isolatie tegen grootschalige aanvallen op Azure. De volgende isolatielaag is door de klant gedefinieerde openbare IP-adressen (eindpunten), die worden gebruikt om te bepalen welk verkeer via de cloudservice naar het virtuele netwerk kan gaan. Native Azure virtual network isolation zorgt voor volledige isolatie van alle andere netwerken en dat verkeer alleen door door door de gebruiker geconfigureerde paden en methoden stroomt. Deze paden en methoden zijn de volgende laag, waar NSGs, UDR en virtuele netwerkapparaten kunnen worden gebruikt om isolatiegrenzen te creëren om de toepassingsimplementaties in het beveiligde netwerk te beschermen.

![Netwerkisolatie](./media/isolation-choices/azure-isolation-fig13.png)

**Verkeersisolatie:** Een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) is de grens voor verkeersisolatie op het Azure-platform. Virtuele machines (VM's) in één virtueel netwerk kunnen niet rechtstreeks communiceren met VM's in een ander virtueel netwerk, zelfs als beide virtuele netwerken door dezelfde klant worden gemaakt. Isolatie is een kritieke eigenschap die ervoor zorgt dat klant VM's en communicatie privé blijft binnen een virtueel netwerk.

[Subnet](../../virtual-network/virtual-networks-overview.md) biedt een extra isolatielaag met in virtueel netwerk op basis van IP-bereik. IP-adressen in het virtuele netwerk, u een virtueel netwerk te verdelen in meerdere subnetten voor organisatie en beveiliging. Tussen VM's en PaaS-rolexemplaren die in (dezelfde of verschillende) subnetten in een VNET zijn geïmplementeerd, is communicatie mogelijk zonder extra configuratie. U ook [netwerkbeveiligingsgroep (NSG's)](../../virtual-network/virtual-networks-overview.md) configureren om netwerkverkeer toe te staan of te weigeren aan een VM-instantie op basis van regels die zijn geconfigureerd in de lijst met toegangscontrole (ACL) van NSG. NSG's kunnen worden gekoppeld aan subnetten of afzonderlijke VM-exemplaren in dat subnet. Als een NSG is gekoppeld aan een subnet, zijn de ACL-regels van toepassing op alle VM-exemplaren in dat subnet.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [opties voor netwerkisolatie voor machines in Windows Azure Virtual Networks](https://azure.microsoft.com/blog/network-isolation-options-for-machines-in-windows-azure-virtual-networks/). Dit omvat het klassieke front-end- en back-endscenario waarbij machines in een bepaald back-endnetwerk of subnetwerk bepaalde clients of andere computers alleen verbinding kunnen maken met een bepaald eindpunt op basis van een lijst met IP-adressen toestaan.

- Meer informatie over [isolatie van virtuele machines in Azure](../../virtual-machines/windows/isolation.md). Azure Compute biedt virtuele machineformaten die zijn geïsoleerd voor een specifiek hardwaretype en zijn toegewezen aan één klant.
