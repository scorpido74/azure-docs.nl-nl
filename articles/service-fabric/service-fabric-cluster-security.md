---
title: Een Azure Service Fabric-cluster beveiligen
description: Meer informatie over beveiligingsscenario's voor een Azure Service Fabric-cluster en de verschillende technologieën die u gebruiken om ze te implementeren.
ms.topic: conceptual
ms.date: 08/14/2018
ms.custom: sfrev
ms.openlocfilehash: c43cfbd4468a64867d50482d9c8055622602f159
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461579"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Beveiligingsscenario's voor servicefabric-cluster

Een Azure Service Fabric-cluster is een bron waarvan u eigenaar bent. Het is uw verantwoordelijkheid om uw clusters te beveiligen om te voorkomen dat onbevoegde gebruikers er verbinding mee maken. Een beveiligd cluster is vooral belangrijk wanneer u productieworkloads op het cluster uitvoert. Het is mogelijk om een onbeveiligd cluster te maken, maar als het cluster beheereindpunten blootstelt aan het openbare internet, kunnen anonieme gebruikers er verbinding mee maken. Onbeveiligde clusters worden niet ondersteund voor productieworkloads. 

Dit artikel is een overzicht van beveiligingsscenario's voor Azure-clusters en zelfstandige clusters en de verschillende technologieën die u gebruiken om ze te implementeren:

* Beveiliging van knooppunt tot knooppunt
* Client-to-node beveiliging
* RBAC (op rollen gebaseerd toegangsbeheer)

## <a name="node-to-node-security"></a>Beveiliging van knooppunt tot knooppunt

Beveiliging van knooppunten helpt de communicatie tussen de VM's of computers in een cluster te beveiligen. Dit beveiligingsscenario zorgt ervoor dat alleen computers die gemachtigd zijn om deel te nemen aan het cluster, kunnen deelnemen aan hostingtoepassingen en -services in het cluster.

![Diagram van knooppuntcommunicatie][Node-to-Node]

Clusters die op Azure en zelfstandige clusters op Windows worden uitgevoerd, kunnen certificaatbeveiliging of [Windows-beveiliging](https://msdn.microsoft.com/library/ff649801.aspx) voor Windows Server-computers gebruiken. [Windows security](https://msdn.microsoft.com/library/ff649396.aspx)

### <a name="node-to-node-certificate-security"></a>Beveiliging van node-to-nodecertificaat

Service Fabric gebruikt X.509-servercertificaten die u opgeeft als onderdeel van de node-type configuratie wanneer u een cluster maakt. Aan het einde van dit artikel ziet u een kort overzicht van wat deze certificaten zijn en hoe u ze verkrijgen of maken.

Stel certificaatbeveiliging in wanneer u het cluster maakt, in de Azure-portal, met behulp van een Azure Resource Manager-sjabloon of met behulp van een zelfstandige JSON-sjabloon. Het standaardgedrag van Service Fabric SDK is het implementeren en installeren van het certificaat met het verst in het toekomstige verlopende certificaat; het klassieke gedrag stond het definiëren van primaire en secundaire certificaten toe, om handmatig gestarte rollovers toe te staan en wordt niet aanbevolen voor gebruik via de nieuwe functionaliteit. De primaire certificaten die worden gebruikt, hebben het verst in de toekomstige vervaldatum, moeten afwijken van de beheerclient en alleen-lezen clientcertificaten die u instelt voor beveiliging van [client-naar-node.](#client-to-node-security)

Zie [Een cluster instellen met behulp van een Azure Resource Manager-sjabloon](service-fabric-cluster-creation-via-arm.md)voor meer informatie over het instellen van certificaatbeveiliging in een cluster voor Azure.

Zie Een zelfstandig cluster op Windows beveiligen [met X.509-certificaten](service-fabric-windows-cluster-x509-security.md)voor meer informatie over het instellen van certificaatbeveiliging in een cluster voor een zelfstandig Windows Server-cluster.

### <a name="node-to-node-windows-security"></a>Windows-beveiliging van knooppunt tot knooppunt

Zie Een zelfstandig cluster op Windows beveiligen met [Windows-beveiliging](service-fabric-windows-cluster-windows-security.md)voor meer informatie over het instellen van Windows-beveiliging voor een zelfstandig Windows Server-cluster.

## <a name="client-to-node-security"></a>Client-to-node beveiliging

Client-to-node beveiliging verifieert clients en helpt de communicatie tussen een client en afzonderlijke knooppunten in het cluster te beveiligen. Dit type beveiliging zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de toepassingen die op het cluster worden geïmplementeerd. Clients worden uniek geïdentificeerd via hun Windows-beveiligingsreferenties of hun certificaatbeveiligingsreferenties.

![Diagram van communicatie tussen client-naar-node][Client-to-Node]

Clusters die draaien op Azure en zelfstandige clusters die op Windows worden uitgevoerd, kunnen [zowel certificaatbeveiliging](https://msdn.microsoft.com/library/ff649801.aspx) als [Windows-beveiliging](https://msdn.microsoft.com/library/ff649396.aspx)gebruiken.

### <a name="client-to-node-certificate-security"></a>Client-to-node certificaatbeveiliging

Stel certificaatbeveiliging van client-naar-node in wanneer u het cluster maakt, hetzij in de Azure-portal, met behulp van een Resource Manager-sjabloon of met behulp van een zelfstandige JSON-sjabloon. Als u het certificaat wilt maken, geeft u een beheerdersclientcertificaat of een gebruikersclientcertificaat op. Als best practice moeten de beheerdersclient- en gebruikersclientcertificaten die u opgeeft, afwijken van de primaire en secundaire certificaten die u opgeeft voor beveiliging van [knooppunt tot knooppunt.](#node-to-node-security) Clustercertificaten hebben dezelfde rechten als clientbeheerderscertificaten. Ze mogen echter alleen worden gebruikt per cluster en niet door administratieve gebruikers als beste beveiligingspraktijk.

Clients die verbinding maken met het cluster met behulp van het beheerderscertificaat hebben volledige toegang tot beheermogelijkheden. Clients die verbinding maken met het cluster met behulp van het alleen-lezen gebruikersclientcertificaat, hebben alleen leestoegang tot beheermogelijkheden. Deze certificaten worden gebruikt voor de RBAC die later in dit artikel wordt beschreven.

Zie [Een cluster instellen met behulp van een Azure Resource Manager-sjabloon](service-fabric-cluster-creation-via-arm.md)voor meer informatie over het instellen van certificaatbeveiliging in een cluster voor Azure.

Zie Een zelfstandig cluster op Windows beveiligen [met X.509-certificaten](service-fabric-windows-cluster-x509-security.md)voor meer informatie over het instellen van certificaatbeveiliging in een cluster voor een zelfstandig Windows Server-cluster.

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Azure Active Directory-beveiliging van client tot knooppunt op Azure

Azure Active Directory maakt het beheren van toegang tot toepassingen door organisaties (bekend als tenants) mogelijk. Toepassingen zijn onderverdeeld in toepassingen met een web-based sign-in UI en die met een native client ervaring. Als u nog geen tenant hebt gemaakt, leest u [nu hoe u een Azure Active Directory-tenant krijgen.][active-directory-howto-tenant]

Een Service Fabric-cluster biedt verschillende toegangspunten bij de management-functionaliteit, met inbegrip van de webconsoles [Service Fabric Explorer][service-fabric-visualizing-your-cluster] en [Visual Studio][service-fabric-manage-application-in-visual-studio]. Als gevolg hiervan maakt u twee Azure AD-toepassingen om de toegang tot het cluster, één webtoepassing en één native toepassing te beheren.

Voor clusters die op Azure worden uitgevoerd, u ook de toegang tot beheereindpunten beveiligen met Azure AD (Azure AD). Zie [Azure AD instellen om](service-fabric-cluster-creation-setup-aad.md)clients te verifiëren voor meer informatie over het maken van de vereiste Azure AD-artefacten en hoe u deze invullen wanneer u het cluster maakt.

## <a name="security-recommendations"></a>Aanbevelingen voor beveiliging

Voor Service Fabric-clusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor wederzijdse verificatie van client-naar-knooppunt:

* Azure Active Directory gebruiken voor de identiteit van de client
* Een certificaat voor serveridentiteit en TLS-versleuteling van http-communicatie

Voor servicefabricclusters die zijn geïmplementeerd in een openbaar netwerk dat wordt gehost op Azure, is de aanbeveling voor node-to-nodebeveiliging het gebruik van een clustercertificaat om knooppunten te verifiëren.

Voor zelfstandige Windows Server-clusters raden we u aan windows-beveiliging te gebruiken met groepsbeheerserviceaccounts als u Windows Server 2012 R2 en Windows Active Directory hebt. Gebruik anders Windows-beveiliging met Windows-accounts.

## <a name="role-based-access-control-rbac"></a>RBAC (op rollen gebaseerd toegangsbeheer)

U toegangsbeheer gebruiken om de toegang tot bepaalde clusterbewerkingen voor verschillende groepen gebruikers te beperken. Dit helpt het cluster veiliger te maken. Er worden twee typen toegangsbeheer ondersteund voor clients die verbinding maken met een cluster: de rol Administrator en de rol Gebruiker.

Gebruikers aan wie de administratorrol is toegewezen, hebben volledige toegang tot beheermogelijkheden, waaronder lees- en schrijfmogelijkheden. Gebruikers die de gebruikersrol toegewezen hebben, hebben standaard alleen leestoegang tot beheermogelijkheden (bijvoorbeeld querymogelijkheden). Ze kunnen ook toepassingen en services oplossen.

Stel de clientrollen administrator en gebruiker in wanneer u het cluster maakt. Rolrollen toewijzen door afzonderlijke identiteiten op te geven (bijvoorbeeld door certificaten of Azure AD te gebruiken) voor elk roltype. Zie [Role-Based Access Control voor Service Fabric-clients voor](service-fabric-cluster-security-roles.md)meer informatie over standaardinstellingen voor toegangsbeheer en hoe u de standaardinstellingen wijzigen.

## <a name="x509-certificates-and-service-fabric"></a>X.509-certificaten en servicestof

X.509 digitale certificaten worden vaak gebruikt om clients en servers te verifiëren. Ze worden ook gebruikt om berichten te versleutelen en digitaal te ondertekenen. Service Fabric gebruikt X.509-certificaten om een cluster te beveiligen en beveiligingsfuncties voor toepassingen te bieden. Zie [Werken met certificaten](https://msdn.microsoft.com/library/ms731899.aspx)voor meer informatie over digitale X.509-certificaten. U gebruikt [Key Vault](../key-vault/general/overview.md) om certificaten te beheren voor Service Fabric-clusters in Azure.

Enkele belangrijke dingen om te overwegen:

* Als u certificaten wilt maken voor clusters waarop productieworkloads worden uitgevoerd, gebruikt u een correct geconfigureerde Windows Server-certificaatservice of een van een goedgekeurde [certificaatinstantie (CA).](https://en.wikipedia.org/wiki/Certificate_authority)
* Gebruik nooit tijdelijke certificaten die u maakt met behulp van tools zoals MakeCert.exe in een productieomgeving.
* U een zelfondertekend certificaat gebruiken, maar alleen in een testcluster. Gebruik geen zelfondertekend certificaat in productie.
* Wanneer u de duimafdruk van het certificaat genereert, moet u een SHA1-duimafdruk genereren. SHA1 is wat wordt gebruikt bij het configureren van de duimafdrukken van het client- en clustercertificaat.

### <a name="cluster-and-server-certificate-required"></a>Cluster- en servercertificaat (vereist)

Deze certificaten (een primaire en eventueel een secundaire) zijn vereist om een cluster te beveiligen en onbevoegde toegang tot het te voorkomen. Deze certificaten bieden cluster- en serververificatie.

Clusterverificatie verifieert knooppuntcommunicatie voor clusterfederatie. Alleen knooppunten die hun identiteit met dit certificaat kunnen bewijzen, kunnen lid worden van het cluster. Serververificatie verifieert de eindpunten voor clusterbeheer naar een beheerclient, zodat de beheerclient weet dat hij met het echte cluster praat en niet met een 'man in the middle'. Dit certificaat biedt ook een TLS voor de HTTPS-beheer-API en voor Service Fabric Explorer via HTTPS. Wanneer een client of knooppunt een knooppunt verifieert, is een van de eerste controles de waarde van de algemene naam in het veld **Onderwerp.** Deze algemene naam of een van de alternatieve namen van het certificaat (SAN's) moeten aanwezig zijn in de lijst met toegestane algemene namen.

Het certificaat moet aan de volgende eisen voldoen:

* Het certificaat moet een privésleutel bevatten. Deze certificaten hebben meestal extensies .pfx of .pem  
* Het certificaat moet worden gemaakt voor sleuteluitwisseling, dat kan worden geëxporteerd naar een bestand voor persoonlijke informatie-uitwisseling (.pfx).
* De **onderwerpnaam van het certificaat moet overeenkomen met het domein dat u gebruikt om toegang te krijgen tot het cluster Servicefabric.** Deze matching is vereist om een TLS te bieden voor het HTTPS-beheereindpunt en De Fabric Explorer van het cluster. U geen TLS/SSL-certificaat verkrijgen van een certificeringsinstantie (CA) voor het domein *.cloudapp.azure.com. U hebt voor uw cluster een aangepaste domeinnaam nodig. Wanneer u een certificaat van een CA aanvraagt, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die u voor uw cluster gebruikt.

Enkele andere dingen om te overwegen:

* Het veld **Onderwerp** kan meerdere waarden hebben. Elke waarde wordt vooraf vastgesteld met een initialisatie om het waardetype aan te geven. Meestal is de initialisatie **CN** (voor *gemeenschappelijke naam);* **CN = www\.contoso.com**.
* Het veld **Onderwerp** kan leeg zijn.
* Als het optionele veld **Alternatieve naam onderwerp** is ingevuld, moet het zowel de algemene naam van het certificaat als één vermelding per SAN hebben. Deze worden ingevoerd als **DNS-naamwaarden.** Zie [Een alternatieve naam voor onderwerp toevoegen aan een beveiligd LDAP-certificaat](https://support.microsoft.com/kb/931351)voor meer informatie over het genereren van certificaten met SAN's.
* De waarde van het veld **Beoogde doeleinden** van het certificaat moet een passende waarde bevatten, zoals **serververificatie** of **clientverificatie.**

### <a name="application-certificates-optional"></a>Toepassingscertificaten (optioneel)

Een willekeurig aantal extra certificaten kan op een cluster worden geïnstalleerd voor toepassingsbeveiligingsdoeleinden. Voordat u uw cluster maakt, moet u rekening houden met de toepassingsbeveiligingsscenario's waarvoor een certificaat moet worden geïnstalleerd op de knooppunten, zoals:

* Versleuteling en decryptie van toepassingsconfiguratiewaarden.
* Versleuteling van gegevens tussen knooppunten tijdens replicatie.

Het concept van het creëren van veilige clusters is hetzelfde, of ze nu Linux of Windows clusters.

### <a name="client-authentication-certificates-optional"></a>Clientverificatiecertificaten (optioneel)

Een willekeurig aantal extra certificaten kan worden opgegeven voor beheer- of gebruikersclientbewerkingen. De client kan dit certificaat gebruiken wanneer wederzijdse verificatie vereist is. Clientcertificaten worden doorgaans niet uitgegeven door een CA van derden. In plaats daarvan bevat de persoonlijke opslag van de huidige gebruikerslocatie meestal clientcertificaten die daar door een hoofdautoriteit zijn geplaatst. Het certificaat moet een **beoogde doelwaarde van** **clientverificatie**hebben.  

Standaard heeft het clustercertificaat beheerdersclientrechten. Deze aanvullende clientcertificaten mogen niet in het cluster worden geïnstalleerd, maar zijn opgegeven als zijnde toegestaan in de clusterconfiguratie.  De clientcertificaten moeten echter op de clientmachines worden geïnstalleerd om verbinding te maken met het cluster en eventuele bewerkingen uit te voeren.

> [!NOTE]
> Voor alle beheerbewerkingen op een Service Fabric-cluster zijn servercertificaten vereist. Clientcertificaten kunnen niet worden gebruikt voor beheer.

## <a name="next-steps"></a>Volgende stappen

* [Een cluster maken in Azure met behulp van een resourcemanagersjabloon](service-fabric-cluster-creation-via-arm.md)
* [Een cluster maken met Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
