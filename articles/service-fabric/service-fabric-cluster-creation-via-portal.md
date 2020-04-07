---
title: Een Service Fabric-cluster maken in Azure Portal
description: Meer informatie over het instellen van een beveiligd Cluster van ServiceFabric in Azure met behulp van de Azure-portal en Azure Key Vault.
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: e2de920ce9517e156934a636559a6fd6f5a71eb5
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754104"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Een cluster van Servicefabric maken in Azure met behulp van de Azure-portal
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

Dit is een stapsgewijze handleiding die u door de stappen van het instellen van een Service Fabric-cluster (Linux of Windows) in Azure leidt met behulp van de Azure-portal. Deze gids leidt u door de volgende stappen:

* Maak een cluster in Azure via de Azure-portal.
* Beheerders verifiëren met behulp van certificaten.

> [!NOTE]
> Maak [uw cluster met Azure Resource Manager][create-cluster-arm]voor meer geavanceerde beveiligingsopties, zoals gebruikersverificatie met Azure Active Directory en het instellen van certificaten voor toepassingsbeveiliging.
> 
> 

## <a name="cluster-security"></a>Clusterbeveiliging 
Er worden in Service Fabric certificaten gebruikt voor verificatie en versleuteling om verschillende aspecten van een cluster en de bijbehorende toepassingen te beveiligen. Zie [Service Fabric-clusterbeveiligingsscenario's][service-fabric-cluster-security] voor meer informatie over hoe certificaten worden gebruikt in Service Fabric.

Als dit de eerste keer is dat u een cluster voor servicefabricmaakt of een cluster implementeert voor testworkloads, u naar de volgende sectie **(Cluster maken in de Azure Portal)** en de systeemgenererende certificaten hebben die nodig zijn voor uw clusters die testworkloads uitvoeren. Als u een cluster instelt voor productieworkloads, gaat u verder met lezen.

#### <a name="cluster-and-server-certificate-required"></a>Cluster- en servercertificaat (vereist)
Dit certificaat is vereist om een cluster te beveiligen en ongeautoriseerde toegang tot het cluster te voorkomen. Het biedt clusterbeveiliging op een paar manieren:

* **Clusterverificatie:** Verifieert knooppuntcommunicatie voor clusterfederatie. Alleen knooppunten die hun identiteit met dit certificaat kunnen bewijzen, kunnen lid worden van het cluster.
* **Serververificatie:** Verifieert de eindpunten voor clusterbeheer naar een beheerclient, zodat de beheerclient weet dat hij met het echte cluster praat. Dit certificaat biedt ook TLS voor de HTTPS-beheer-API en voor Service Fabric Explorer via HTTPS.

Om deze doeleinden te bereiken, moet het certificaat aan de volgende eisen voldoen:

* Het certificaat moet een privésleutel bevatten.
* Het certificaat moet worden gemaakt voor sleuteluitwisseling, exporteerbaar naar een bestand voor persoonlijke informatie-uitwisseling (.pfx).
* De **onderwerpnaam** van het certificaat moet overeenkomen met het domein dat wordt gebruikt om toegang te krijgen tot het cluster ServiceFabric. Dit is vereist om TLS te verstrekken voor de HTTPS-beheereindpunten en Service Fabric Explorer van het cluster. U geen TLS/SSL-certificaat verkrijgen van een `.cloudapp.azure.com` certificeringsinstantie (CA) voor het domein. Een aangepaste domeinnaam voor uw cluster aanschaffen. Wanneer u een certificaat aanvraagt bij een CA, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die voor uw cluster wordt gebruikt.

#### <a name="client-authentication-certificates"></a>Clientverificatiecertificaten
Aanvullende clientcertificaten verifiëren beheerders voor clusterbeheertaken. Service Fabric heeft twee toegangsniveaus: **admin** en **alleen-lezen gebruiker**. Ten minste één enkel certificaat voor administratieve toegang moet worden gebruikt. Voor extra toegang op gebruikersniveau moet een apart certificaat worden verstrekt. Zie voor meer informatie over toegangsrollen [op rollen het op rollen gebaseerde toegangscontrole voor Service Fabric-clients.][service-fabric-cluster-security-roles]

U hoeft geen clientverificatiecertificaten te uploaden naar Key Vault om met Service Fabric te werken. Deze certificaten hoeven alleen te worden verstrekt aan gebruikers die zijn geautoriseerd voor clusterbeheer. 

> [!NOTE]
> Azure Active Directory is de aanbevolen manier om clients te verifiëren voor clusterbeheerbewerkingen. Als u Azure Active Directory wilt gebruiken, moet u [een cluster maken met Azure Resource Manager][create-cluster-arm].
> 
> 

#### <a name="application-certificates-optional"></a>Toepassingscertificaten (optioneel)
Een willekeurig aantal extra certificaten kan op een cluster worden geïnstalleerd voor toepassingsbeveiligingsdoeleinden. Voordat u uw cluster maakt, moet u rekening houden met de toepassingsbeveiligingsscenario's waarvoor een certificaat moet worden geïnstalleerd op de knooppunten, zoals:

* Versleuteling en decryptie van toepassingsconfiguratiewaarden
* Versleuteling van gegevens tussen knooppunten tijdens replicatie 

Toepassingscertificaten kunnen niet worden geconfigureerd bij [het maken van een cluster via de Azure-portal.](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md) Als u toepassingscertificaten wilt configureren op het moment van clusterinstelling, moet u [een cluster maken met Azure Resource Manager][create-cluster-arm]. U ook toepassingscertificaten aan het cluster toevoegen nadat het is gemaakt.

## <a name="create-cluster-in-the-azure-portal"></a>Cluster maken in de Azure-portal

Het maken van een productiecluster om aan uw toepassingsbehoeften te voldoen, omvat enige planning, om u daarbij te helpen, wordt het ten zeerste aanbevolen dat u het document [voor het plannen van servicestructuurcluster-overwegingen][service-fabric-cluster-capacity] leest en begrijpt. 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Zoeken naar de clusterbron ServiceFabric

Meld u aan bij [Azure Portal][azure-portal].
Klik **op Een resource maken** om een nieuwe resourcesjabloon toe te voegen. Zoek naar de sjabloon servicestructuurcluster in de **Marketplace** onder **Alles**.
Selecteer **Cluster servicestructuur** in de lijst.

![zoeken naar clustersjabloon ServiceFabric op de Azure-portal.][SearchforServiceFabricClusterTemplate]

Navigeer naar het **clusterblad ServiceFabric** en klik op **Maken**.

Het **clusterblad Servicefabric maken** heeft de volgende vier stappen:

### <a name="1-basics"></a>1. Basisbeginselen
![Schermafbeelding van het maken van een nieuwe brongroep.][CreateRG]

In het basisblad moet u de basisgegevens voor uw cluster opgeven.

1. Voer de naam van uw cluster in.
2. Voer een **gebruikersnaam** en **wachtwoord** in voor Extern bureaublad voor de VM's.
3. Zorg ervoor dat u het **abonnement** selecteert waarop uw cluster moet worden geïmplementeerd, vooral als u meerdere abonnementen hebt.
4. Een nieuwe **resourcegroep maken**. Het is het beste om het dezelfde naam als het cluster te geven, omdat het helpt bij het vinden van ze later, vooral wanneer u probeert om wijzigingen aan te brengen in uw implementatie of uw cluster te verwijderen.
   
   > [!NOTE]
   > Hoewel u besluiten een bestaande resourcegroep te gebruiken, is het een goede gewoonte om een nieuwe resourcegroep te maken. Dit maakt het gemakkelijk om clusters en alle resources die het gebruikt te verwijderen.
   > 
   > 
5. Selecteer de **locatie** waarin u het cluster wilt maken. Als u van plan bent een bestaand certificaat te gebruiken dat u al hebt geüpload naar een sleutelkluis, moet u dezelfde regio gebruiken waarin uw sleutelkluis zich bevindt. 

### <a name="2-cluster-configuration"></a>2. Clusterconfiguratie
![Een knooppunttype maken][CreateNodeType]

Configureer uw clusterknooppunten. Knooppunttypen definiëren de VM-formaten, het aantal VM's en hun eigenschappen. Uw cluster kan meer dan één knooppunttype hebben, maar het primaire knooppunttype (het eerste knooppunt dat u op de portal definieert) moet ten minste vijf VM's hebben, omdat dit het knooppunttype is waar servicefabric-systeemservices worden geplaatst. Configureer **plaatsingseigenschappen** niet omdat automatisch een standaardplaatsingseigenschap van 'NodeTypeName' wordt toegevoegd.

> [!NOTE]
> Een veelvoorkomend scenario voor meerdere knooppunttypen is een toepassing die een front-endservice en een back-endservice bevat. U wilt de front-end service op kleinere VM's (VM-formaten zoals D2_V2) met poorten open voor het internet, en zet de back-end service op grotere VM's (met VM-formaten zoals D3_V2, D6_V2, D15_V2, enzovoort) zonder internet-facing poorten open.
> 

1. Kies een naam voor uw knooppunttype (1 tot 12 tekens die alleen letters en cijfers bevatten).
2. De minimale **grootte** van VM's voor het primaire knooppunttype wordt aangestuurd door de **duurzaamheidslaag die** u voor het cluster kiest. De standaardwaarde voor de duurzaamheidslaag is brons. Zie voor meer informatie over duurzaamheid hoe u [de duurzaamheid van het servicestofcluster][service-fabric-cluster-durability]kiezen.
3. Selecteer de grootte van de **virtuele machine**. D-serie VM's hebben SSD-schijven en worden ten zeerste aanbevolen voor stateful toepassingen. Gebruik geen VM SKU met gedeeltelijke cores of minder dan 10 GB beschikbare schijfcapaciteit. Raadpleeg [het clusterplanningsdocument][service-fabric-cluster-capacity] voor servicefabric voor hulp bij het selecteren van de VM-grootte.
4.  **Cluster met één knooppunt en drie knooppuntclusters** zijn alleen bedoeld voor testgebruik. Ze worden niet ondersteund voor het uitvoeren van productieworkloads.
5. Kies de **initiële vm-schaalsetcapaciteit** voor het knooppunttype. U het aantal VM's in een knooppunttype later opschalen of omlaag, maar op het primaire knooppunttype is het minimum vijf voor productieworkloads. Andere knooppunttypen kunnen minimaal één VM hebben. Het **minimumaantal** VM's voor het primaire knooppunttype bepaalt de **betrouwbaarheid** van uw cluster.  
6. **Aangepaste eindpunten**configureren . Met dit veld u een door komma's gescheiden lijst met poorten invoeren die u via de Azure Load Balancer wilt blootstellen aan het openbare internet voor uw toepassingen. Als u bijvoorbeeld van plan bent een webtoepassing in uw cluster te implementeren, voert u hier '80' in om verkeer op poort 80 toe te staan in uw cluster. Zie [Communiceren met toepassingen][service-fabric-connect-and-communicate-with-services] voor meer informatie over eindpunten
7. **Omgekeerde proxy inschakelen**.  De [reverse proxy van Service Fabric](service-fabric-reverseproxy.md) helpt microservices die worden uitgevoerd in een Cluster ServiceFabric te ontdekken en te communiceren met andere services die http-eindpunten hebben.
8. Terug in het **clusterconfiguratieblad,** onder **+Optionele instellingen weergeven**, **clusterdiagnose**configureren . Standaard is diagnostische gegevens ingeschakeld op uw cluster om problemen met het oplossen van problemen op te lossen. Als u diagnostische gegevens wilt uitschakelen, wijzigt u de **statusschakelaar** in **Uitschakelen**. Het uitschakelen van diagnostiek wordt **afgeraden.** Als u het Application Insights-project al hebt gemaakt, geeft u de sleutel, zodat de toepassingssporen naar het project worden doorgestuurd.
9. **DNS-service opnemen**.  De [DNS-service](service-fabric-dnsservice.md) is een optionele service waarmee u andere services vinden met behulp van het DNS-protocol.
10. Selecteer de **fabric-upgrademodus** waarop u het cluster wilt instellen. Selecteer **Automatisch**, als u wilt dat het systeem automatisch de laatst beschikbare versie ophaalt en probeert uw cluster ernaar te upgraden. Stel de modus in op **Handmatig,** als u een ondersteunde versie wilt kiezen. Zie het [clusterupgradedocument servicestructuur][service-fabric-cluster-upgrade] voor meer informatie over de fabric-upgrademodus.

> [!NOTE]
> We ondersteunen alleen clusters waarop ondersteunde versies van Service Fabric worden uitgevoerd. Door de **handmatige** modus te selecteren, neemt u de verantwoordelijkheid op zich om uw cluster te upgraden naar een ondersteunde versie.
> 

### <a name="3-security"></a>3. Beveiliging
![Schermafbeelding van beveiligingsconfiguraties op azure-portal.][BasicSecurityConfigs]

Om het opzetten van een veilig testcluster voor u eenvoudig te maken, hebben we de **Basic-optie** geleverd. Als u al een certificaat hebt en deze hebt geüpload naar uw [sleutelkluis](/azure/key-vault/) (en de sleutelkluis voor implementatie hebt ingeschakeld), gebruikt u de optie **Aangepast**

#### <a name="basic-option"></a>Basisoptie
Volg de schermen om een bestaande sleutelkluis toe te voegen of opnieuw te gebruiken en voeg een certificaat toe. De toevoeging van het certificaat is een synchroon proces en dus moet u wachten tot het certificaat wordt gemaakt.

Weersta de verleiding om weg te navigeren van het scherm totdat het voorgaande proces is voltooid.

![CreateKeyVault]

Nu de sleutelkluis is gemaakt, bewerkt u het toegangsbeleid voor uw sleutelkluis. 

![CreateKeyVault2]

Klik op het **toegangsbeleid bewerken**en geef **vervolgens geavanceerd toegangsbeleid weer** en schakel toegang tot Azure Virtual Machines in voor implementatie. Het wordt aanbevolen om de implementatie van de sjabloon ook in te schakelen. Zodra u uw selecties hebt gemaakt, vergeet dan niet op de knop **Opslaan** te klikken en uit het deelvenster **Toegangsbeleid** te sluiten.

![CreateKeyVault3]

Voer de naam van het certificaat in en klik op **OK**.

![CreateKeyVault4]

#### <a name="custom-option"></a>Aangepaste optie
Sla deze sectie over als u de stappen in de **basisoptie** al hebt uitgevoerd.

![Optie BeveiligingAangepast]

U hebt de gegevens bronsleutelkluis, certificaat-URL en certificaatafdrukgegevens nodig om de beveiligingspagina in te vullen. Als u het niet bij de hand hebt, opent u een ander browservenster en doet u in de Azure-portal het volgende

1. Navigeer naar uw key vault service.
2. Selecteer het tabblad Eigenschappen en kopieer de 'RESOURCE-id' naar 'Bronsleutelkluis' in het andere browservenster 

    ![CertInfo0 CertInfo0]

3. Selecteer nu het tabblad Certificaten.
4. Klik op certificaat duimafdruk, die u naar de pagina Versies brengt.
5. Klik op de GUIDs die u onder de huidige versie ziet.

    ![CertInfo1 CertInfo1 CertInfo1]

6. Je moet nu op het scherm zoals hieronder. De hexadecimale SHA-1 Thumbprint kopiëren naar "Certificaat duimafdruk" in het andere browservenster
7. Kopieer de 'Secret Identifier' naar de 'Certificaat-URL' in een ander browservenster.

    ![CertInfo2 CertInfo2 CertInfo2]

Schakel het selectievakje **Geavanceerde instellingen configureren in** om clientcertificaten in te voeren voor **beheerdersclient** en **alleen-lezen client.** Voer in deze velden de duimafdruk van uw beheerdersclientcertificaat en de duimafdruk van uw alleen-lezen gebruikersclientcertificaat in, indien van toepassing. Wanneer beheerders verbinding proberen te maken met het cluster, krijgen ze alleen toegang als ze een certificaat hebben met een duimafdruk die overeenkomt met de hier ingevoerde duimafdrukwaarden.  

### <a name="4-summary"></a>4. Samenvatting

Nu bent u klaar om het cluster te implementeren. Voordat u dat doet, download het certificaat, kijk in de grote blauwe informatievak voor de link. Zorg ervoor dat het cert op een veilige plaats blijft. je hebt het nodig om verbinding te maken met je cluster. Aangezien het certificaat dat u hebt gedownload geen wachtwoord heeft, wordt geadviseerd er een toe te voegen.

Als u het maken van het cluster wilt voltooien, klikt u op **Maken**. U de sjabloon optioneel downloaden.

![Samenvatting]

U kunt de voortgang van het maken in de meldingen bekijken. (Klik op het pictogram 'Bel' in de buurt van de statusbalk rechtsboven in het scherm.) Als u tijdens het maken van het cluster **op Vastmaken aan startbord** hebt geklikt, ziet u **Het cluster Implementatie van servicestructuur** vastgemaakt aan het **startbord.** Dit proces zal enige tijd duren. 

Als u beheerbewerkingen op uw cluster wilt uitvoeren met Powershell of CLI, moet u verbinding maken met uw cluster, meer lezen over hoe u [verbinding maken met uw cluster.](service-fabric-connect-to-secure-cluster.md)

## <a name="view-your-cluster-status"></a>Uw clusterstatus weergeven
![Schermafbeelding van clusterdetails in het dashboard.][ClusterDashboard]

Zodra uw cluster is gemaakt, u uw cluster in de portal inspecteren:

1. Ga naar **Bladeren** en klik op **Clusters van servicefabric .**
2. Zoek uw cluster en klik erop.
3. U kunt de gegevens van uw cluster nu bekijken op het dashboard, waaronder het openbare eindpunt van het cluster en een koppeling naar Service Fabric Explorer.

De sectie **Node Monitor** op het dashboardblad van het cluster geeft het aantal VM's aan dat gezond en niet in orde is. Meer informatie over de status van het cluster vindt u bij [de introductie van het servicefabric-statusmodel.][service-fabric-health-introduction]

> [!NOTE]
> Servicefabricclusters vereisen dat een bepaald aantal knooppunten altijd beschikbaar is om de beschikbaarheid te behouden en de status te behouden - aangeduid als 'quorum behouden'. Daarom is het meestal niet veilig om alle machines in het cluster uit te schakelen, tenzij u eerst een [volledige back-up van uw status][service-fabric-reliable-services-backup-restore]hebt uitgevoerd.
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>Verbinding maken met een instantie voor virtuele machineschaal of een clusterknooppunt
Elk van de NodeTypes die u in uw cluster opgeeft, resulteert in een virtuele machineschaalset die wordt ingesteld. <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>Volgende stappen
Op dit moment hebt u een beveiligd cluster met certificaten voor beheerverificatie. Maak vervolgens [verbinding met uw cluster](service-fabric-connect-to-secure-cluster.md) en leer hoe u [toepassingsgeheimen beheert.](service-fabric-application-secret-management.md)  Lees ook over [ondersteuningsopties voor Service Fabric.](service-fabric-support.md)

<!-- Links -->
[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/key-vault-overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0 CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1 CertInfo1 CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2 CertInfo2 CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[Optie BeveiligingAangepast]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Samenvatting]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
