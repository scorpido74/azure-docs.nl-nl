---
title: Avere vFXT implementeren voor Azure
description: Stappen om het Avere vFXT-cluster in Azure te implementeren
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: e70d1dfebcf25ee8f4e90a062cee6dd72a663e02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252595"
---
# <a name="deploy-the-vfxt-cluster"></a>Het vFXT-cluster implementeren

Met deze procedure u de wizard Implementatie gebruiken die beschikbaar is in de Azure Marketplace. De wizard implementeert het cluster automatisch met behulp van een Azure Resource Manager-sjabloon. Nadat u de parameters in het formulier hebt ingevoerd en op **Maken**hebt geklikt, voltooit Azure deze taken automatisch:

* Hiermee maakt u de clustercontroller, een basis-VM die de software bevat die nodig is om het cluster te implementeren en te beheren.
* Hiermee stelt u resourcegroep en virtuele netwerkinfrastructuur in, inclusief het maken van nieuwe elementen.
* Hiermee maakt u de VM's van het clusterknooppunt en configureert u deze als het Avere-cluster.
* Maakt op verzoek een nieuwe Azure Blob-container en configureert deze als clustercore-filer.

Nadat u de instructies in dit document hebt gevolgd, beschikt u over een virtueel netwerk, een subnet, een clustercontroller en een vFXT-cluster, zoals in het volgende diagram wordt weergegeven. In dit diagram wordt de optionele Azure Blob-kernfiler weergegeven, die een nieuwe Blob-opslagcontainer bevat (in een nieuw opslagaccount, niet weergegeven) en een serviceeindpunt voor Microsoft-opslag in het subnet.

![diagram met drie concentrische rechthoeken met Avere-clustercomponenten. De buitenste rechthoek heeft het label 'Resourcegroep' en bevat een zeshoek met het label 'Blob storage (optioneel)'. De volgende rechthoek in is gelabeld 'Virtueel netwerk: 10.0.0.0/16' en bevat geen unieke componenten. De binnenste rechthoek heeft het label 'Subnet:10.0.0.0/24' en bevat een VM met het label 'Cluster controller', een stapel van drie VM's met het label 'vFXT-knooppunten (vFXT-cluster)' en een zeshoek met het label 'Service endpoint'. Er is een pijl die het serviceeindpunt (dat zich in het subnet bevindt) en de blobopslag (die zich buiten het subnet en vnet bevindt, in de resourcegroep verbindt). De pijl loopt door de subnet en virtuele netwerkgrenzen.](media/avere-vfxt-deployment.png)

Voordat u de sjabloon voor maken gebruikt, controleert u of u aan deze vereisten hebt gereageerd:  

* [Nieuw abonnement](avere-vfxt-prereqs.md#create-a-new-subscription)
* [Machtigingen voor abonnementseigenaren](avere-vfxt-prereqs.md#configure-subscription-owner-permissions)
* [Quotum voor het vFXT-cluster](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster)
* [Eindpunt van de opslagservice (indien nodig)](avere-vfxt-prereqs.md#create-a-storage-service-endpoint-in-your-virtual-network-if-needed) - Vereist voor implementaties die een bestaand virtueel netwerk gebruiken en blob-opslag maken

Lees Het overzicht [van uw Avere vFXT-systeem](avere-vfxt-deploy-plan.md) en [implementatie plannen](avere-vfxt-deploy-overview.md)voor meer informatie over clusterimplementatiestappen en -planning.

## <a name="create-the-avere-vfxt-for-azure"></a>De Avere vFXT voor Azure maken

Toegang tot de creatiesjabloon in de Azure-portal door te zoeken naar Avere en "Avere vFXT for Azure ARM Template" te selecteren.

![Browservenster met de Azure-portal met broodkruimels "New > Marketplace > Everything". Op de pagina Alles heeft het zoekveld de term "avere" en het tweede resultaat, "Avere vFXT for Azure ARM Template" wordt in het rood weergegeven om het te markeren.](media/avere-vfxt-template-choose.png)

Nadat u de details op de pagina Avere vFXT voor Azure ARM Template hebt gelezen, klikt u op de knop **Maken** om te beginnen.

![Azure-marktplaats met de eerste pagina van de implementatiesjabloon met](media/avere-vfxt-deploy-first.png)

De sjabloon is verdeeld in vier stappen - twee pagina's voor het verzamelen van informatie, plus validatie- en bevestigingsstappen.

* Pagina één verzamelt instellingen voor de vm van de clustercontroller.
* Pagina twee verzamelt parameters voor het maken van het cluster en extra bronnen zoals subnetten en opslag.
* Pagina drie vat uw keuzes samen en valideert de configuratie.
* Pagina vier legt de algemene voorwaarden van de software uit en stelt u in staat om het proces voor het maken van het clusterproces te starten.

## <a name="page-one-parameters---cluster-controller-information"></a>Pagina één parameters - clustercontrollerinformatie

De eerste pagina van de implementatiesjabloon richt zich op de clustercontroller.

![Eerste pagina van de implementatiesjabloon](media/avere-vfxt-deploy-1.png)

Vul de volgende gegevens in:

* **Naam clustercontroller** - Stel de naam in voor de vm van de clustercontroller.

* **Gebruikersnaam controller** - Stel de hoofdgebruikersnaam in voor de vm van de clustercontroller.

* **Verificatietype** - Kies verificatie van een wachtwoord of SSH-hoofdsleutel voor verbinding maken met de controller. De SSH public key methode wordt aanbevolen; lees [Hoe maak en gebruik je SSH-toetsen](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows) als je hulp nodig hebt.

* **Wachtwoord** of **SSH-openbare sleutel** - Afhankelijk van het verificatietype dat u hebt geselecteerd, moet u in de volgende velden een RSA-openbare sleutel of een wachtwoord opgeven. Deze referentie wordt gebruikt met de gebruikersnaam die eerder is opgegeven.

* **Abonnement** - Selecteer het abonnement voor de Avere vFXT.

* **Resourcegroep** : selecteer een bestaande lege resourcegroep voor het Avere vFXT-cluster of klik op 'Nieuw maken' en voer een nieuwe naam van de resourcegroep in.

* **Locatie** : selecteer de Azure-locatie voor uw cluster en resources.

Klik op **OK** als u klaar bent.

> [!NOTE]
> Als u wilt dat de clustercontroller een ip-adres heeft dat in het openbaar is gericht, maakt u een nieuw virtueel netwerk voor het cluster in plaats van een bestaand netwerk te selecteren. Deze instelling staat op pagina twee.

## <a name="page-two-parameters---vfxt-cluster-information"></a>Pagina twee parameters - vFXT-clusterinformatie

Op de tweede pagina van de implementatiesjabloon u onder andere de clustergrootte, het knooppunttype, de cachegrootte en de opslagparameters instellen.

![Tweede pagina van de implementatiesjabloon](media/avere-vfxt-deploy-2.png)

* **Avere vFXT clusterknooppunt telling** - Kies het aantal knooppunten in het cluster. Het minimum is drie knooppunten en het maximum is twaalf.

* **Wachtwoord voor clusterbeheer** - Het wachtwoord voor clusterbeheer maken. Dit wachtwoord wordt gebruikt ```admin``` met de gebruikersnaam om u aan te melden bij het clusterconfiguratiepaneel, waar u het cluster bewaken en clusterinstellingen configureren.

* **Avere vFXT clusternaam** - Geef het cluster een unieke naam.

* **Grootte** : deze sectie toont het VM-type dat wordt gebruikt voor de clusterknooppunten. Hoewel er slechts één aanbevolen optie is, opent de koppeling **Grootte wijzigen** een tabel met details over dit instantietype en een koppeling naar een prijscalculator.

* **Cachegrootte per knooppunt** - De clustercache is verspreid over de clusterknooppunten, dus de totale cachegrootte op uw Avere vFXT-cluster wordt vermenigvuldigd met het aantal knooppunten.

  Aanbevolen configuratie: gebruik 4 TB per knooppunt voor Standard_E32s_v3 knooppunten.

* **Virtueel netwerk** - Definieer een nieuw virtueel netwerk om het cluster te huisvesten of selecteer een bestaand netwerk dat voldoet aan de vereisten die zijn beschreven in [Uw Avere vFXT-systeem plannen.](avere-vfxt-deploy-plan.md#subscription-resource-group-and-network-infrastructure)

  > [!NOTE]
  > Als u een nieuw virtueel netwerk maakt, heeft de clustercontroller een openbaar IP-adres, zodat u toegang hebt tot het nieuwe privénetwerk. Als u een bestaand virtueel netwerk kiest, is de clustercontroller geconfigureerd zonder openbaar IP-adres.
  >
  > Een openbaar zichtbaar IP-adres op de clustercontroller biedt gemakkelijkere toegang tot het vFXT-cluster, maar creëert een klein beveiligingsrisico.
  >* Met een openbaar IP-adres op de clustercontroller u het gebruiken als een jump host om verbinding te maken met het Avere vFXT-cluster van buiten het privésubnet.
  >* Als u geen openbaar IP-adres op de controller hebt, hebt u een andere jump host, een VPN-verbinding of ExpressRoute nodig om toegang te krijgen tot het cluster. Gebruik bijvoorbeeld een bestaand virtueel netwerk dat al een VPN-verbinding heeft geconfigureerd.
  >* Als u een controller met een openbaar IP-adres maakt, moet u de vm van de controller beveiligen met een netwerkbeveiligingsgroep. Standaard maakt de Avere vFXT voor Azure-implementatie een netwerkbeveiligingsgroep die de inkomende toegang beperkt tot alleen poort 22 voor controllers met openbare IP-adressen. U het systeem verder beschermen door de toegang tot uw bereik van IP-bronadressen te vergrendelen- dat wil zeggen, alleen verbindingen toestaan van machines die u wilt gebruiken voor clustertoegang.

  Een nieuw virtueel netwerk is ook geconfigureerd met een eindpunt voor de opslagservice voor Azure Blob-opslag en met netwerktoegangscontrole die is vergrendeld om alleen IP's uit het clustersubnet toe te staan.

* **Subnet** - Kies een subnet of maak een nieuw subnet.

* **Blob-opslag maken en gebruiken** - Kies **echt** om een nieuwe Azure Blob-container te maken en configureer deze als back-endopslag voor het nieuwe Avere vFXT-cluster. Met deze optie wordt ook een nieuw opslagaccount gemaakt in de brongroep van het cluster en wordt een eindpunt van de Microsoft-opslagservice gemaakt in het clustersubnet.
  
  Als u een bestaand virtueel netwerk levert, moet het eindpunt van de opslagservice hebben voordat u het cluster maakt. (Lees Voor meer informatie [Uw Avere vFXT-systeem](avere-vfxt-deploy-plan.md)plannen .)

  Stel dit veld **in op false** als u geen nieuwe container wilt maken. In dit geval moet u opslag toevoegen en configureren nadat u het cluster hebt gemaakt. Lees [Opslag configureren](avere-vfxt-add-storage.md) voor instructies.

* **(Nieuw) opslagaccount** - Als u een nieuwe Azure Blob-container maakt, voert u een naam in voor het nieuwe opslagaccount.

## <a name="validation-and-purchase"></a>Validatie en aankoop

Pagina drie vat de configuratie samen en valideert de parameters. Nadat de validatie is geslaagd, controleert u het overzicht en klikt u op de knop **OK.**

> [!TIP]
> U de aanmaakinstellingen van dit cluster opslaan door op de koppeling **Sjabloon downloaden en parameters** naast de knop **OK** te klikken. Deze informatie kan handig zijn als u later een vergelijkbaar cluster moet maken, bijvoorbeeld om een vervangend cluster te maken in een scenario voor noodherstel. (Lees [richtlijnen voor herstel na noodgevallen](disaster-recovery.md) voor meer informatie.)

![Derde pagina van de implementatiesjabloon - validatie](media/avere-vfxt-deploy-3.png)

Pagina vier geeft de gebruiksvoorwaarden en links naar privacy- en prijsinformatie.

Voer ontbrekende contactgegevens in, klik op de knop **Maken** om de voorwaarden te accepteren en maak het Avere vFXT voor Azure-cluster.

![Vierde pagina van de implementatiesjabloon - algemene voorwaarden, knop maken](media/avere-vfxt-deploy-4.png)

De implementatie van het cluster duurt 15-20 minuten.

## <a name="gather-template-output"></a>Sjabloonuitvoer verzamelen

Wanneer de Avere vFXT-sjabloon klaar is met het maken van het cluster, worden belangrijke informatie over het nieuwe cluster weergegeven.

> [!TIP]
> Zorg ervoor dat u het **IP-adres** van het beheer kopieert uit de sjabloonuitvoer. U hebt dit adres nodig om het cluster te beheren.

Ga als het gaat om informatie:

1. Ga naar de brongroep voor uw clustercontroller.

1. Klik aan de linkerkant op **Implementaties**en vervolgens op **microsoft-avere.vfxt-template**.

   ![Portalpagina resourcegroep met implementaties links en microsoft-avere.vfxt-template in een tabel onder de naam Implementatie](media/avere-vfxt-outputs-deployments.png)

1. Klik aan de linkerkant op **Uitvoer**. Kopieer de waarden in elk van de velden.

   ![uitvoerpagina met SSHSTRING-, RESOURCE_GROUP-, LOCATIE-, NETWORK_RESOURCE_GROUP-, NETWERK-, SUBNET-, SUBNET_ID-, VSERVER_IPs- en MGMT_IP-waarden in velden rechts van de labels](media/avere-vfxt-outputs-values.png)

## <a name="next-steps"></a>Volgende stappen

Nu het cluster wordt uitgevoerd en u het IP-adres van het beheer kent, [maakt u verbinding met het clusterconfiguratiehulpprogramma.](avere-vfxt-cluster-gui.md)

Gebruik de configuratie-interface om uw cluster aan te passen, inclusief de volgende installatietaken:

* [Ondersteuning inschakelen](avere-vfxt-enable-support.md)
* [Opslag toevoegen](avere-vfxt-add-storage.md) (indien nodig)
