---
title: Geo-replicatie toepassen voor een register
description: Aan de slag met het maken en beheren van een geo-gerepliceerd Azure-containerregister, waarmee het register meerdere regio's kan bedienen met multimaster regionale replica's.
author: stevelas
ms.topic: article
ms.date: 08/16/2019
ms.author: stevelas
ms.openlocfilehash: d238de30e458261a11c941c03ac127c732ca8d3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456439"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replicatie in Azure Container Registry

Bedrijven die lokale aanwezigheid willen, of een back-up zonder opnieuw opstarten, kunnen services uitvoeren vanuit meerdere Azure-regio's. Het plaatsen van een containerregister in elke regio waarin installatiekopieën worden uitgevoerd, is een best practice die bewerkingen dicht bij het netwerk mogelijk maakt, wat zorgt voor snelle, betrouwbare overdrachten van installatiekopielagen. Met geo-replicatie kan een Azure-containerregister als één register functioneren en meerdere regio's bedienen met regionale registers voor meerdere masters. 

Een geo-gerepliceerd register biedt de volgende voordelen:

* Dezelfde register-/installatiekopie-/tagnamen kunnen worden gebruikt in meerdere regio's
* Toegang tot het register dicht bij het netwerk via regionale implementaties
* Geen extra kosten voor uitgaand verkeer, aangezien de installatiekopieën worden opgehaald uit een lokaal, gerepliceerd register in dezelfde regio als uw containerhost
* Beheer van een enkel register voor meerdere regio's

> [!NOTE]
> Als u kopieën van containerinstallaties in meer dan één Azure-containerregister wilt behouden, kunt u met Azure Container Registry ook [installatiekopieën importeren](container-registry-import-images.md). U kunt bijvoorbeeld binnen een DevOps-werkstroom een installatiekopie uit een ontwikkelingsregister naar een productieregister importeren zonder Docker-opdrachten te gebruiken.
>

## <a name="example-use-case"></a>Voorbeeld van een toepassing
Contoso voert een website voor openbare aanwezigheid uit voor de VS, Canada en Europa. Contoso voert AKS-clusters ([Azure Kubernetes Service](/azure/aks/)) uit in VS - west, VS - oost, Canada - centraal en Europa - west om deze markten met lokale inhoud van dicht bij het netwerk te bedienen. De webtoepassing, geïmplementeerd als een Docker-installatiekopie, gebruikt dezelfde code en installatiekopie in alle regio's. Inhoud, die lokaal is voor een bepaalde regio, wordt opgehaald uit een database, die uniek is ingericht in elke regio. Elke regionale implementatie heeft een unieke configuratie voor resources zoals de lokale database.

Het ontwikkelteam bevindt zich in Seattle, WA en gebruikt het datacentrum VS - west.

![Pushen naar meerdere registers](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Pushen naar meerdere registers*

Voordat Contoso de functies voor geo-replicatie ging gebruiken, had het bedrijf een in de VS gebaseerd register in VS - west, met een extra register in Europa - west. Het ontwikkelteam pushte installatiekopieën naar twee verschillende registers om deze verschillende regio's te bedienen.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Ophalen vanuit meerdere registers](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Ophalen vanuit meerdere registers*

Typische uitdagingen van meerdere registers zijn onder meer:

* De clusters VS - oost, VS - west en Canada - centraal halen allemaal gegevens op uit het register VS - west, waarbij extra kosten voor uitgaand verkeer worden gegenereerd omdat al deze externe containerhosts installatiekopieën ophalen vanuit datacenters in VS - west.
* Het ontwikkelteam moet installatiekopieën pushen naar registers in VS - west en Europa - west.
* Het ontwikkelteam moet elke regionale implementatie configureren en onderhouden met installatiekopienamen die verwijzen naar het lokale register.
* Toegang tot het register moet voor elke regio worden geconfigureerd.

## <a name="benefits-of-geo-replication"></a>Voordelen van geo-replicatie

![Ophalen vanuit een geo-gerepliceerd register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

De functie voor geo-replicatie van Azure Container Registry biedt de volgende voordelen:

* Eén register beheren voor alle regio's: `contoso.azurecr.io`
* Eén configuratie van installatiekopie-implementaties beheren, omdat voor alle regio's dezelfde installatiekopie-URL wordt gebruikt: `contoso.azurecr.io/public/products/web:1.2`
* Duw naar één register, terwijl ACR de geo-replicatie beheert. U regionale [webhooks](container-registry-webhook.md) configureren om u op de hoogte te stellen van gebeurtenissen in specifieke replica's.

## <a name="configure-geo-replication"></a>Geo-replicatie configureren

Het configureren van geo-replicatie is net zo gemakkelijk als regio's aanklikken op een kaart. U geo-replicatie ook beheren met behulp van hulpprogramma's, waaronder de [az acr-replicatieopdrachten](/cli/azure/acr/replication) in de Azure CLI, of een register implementeren dat is ingeschakeld voor georeplicatie met een [Azure Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

De functie voor geo-replicatie is alleen voor [Premium-registers](container-registry-skus.md) beschikbaar. Als uw register nog niet Premium is, kunt u overstappen van Basic en Standard naar Premium in de [Azure-portal](https://portal.azure.com):

![Van SKU wisselen in de Azure-portal](media/container-registry-skus/update-registry-sku.png)

Als u geo-replicatie voor een Premium-register wilt configureren, moet u zich aanmelden bij de Azure-portal via https://portal.azure.com.

Ga naar Azure Container Registry en selecteer **Replicaties**:

![Replicaties in de containerregister-UI van Azure Portal](media/container-registry-geo-replication/registry-services.png)

Er wordt een kaart weergegeven met alle huidige Azure-regio's:

 ![Kaart met regio's in Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blauwe zeshoeken staan voor huidige replica's
* Groene zeshoeken staan voor mogelijk replicatieregio's
* Grijze zeshoeken staan voor Azure-regio's die nog niet beschikbaar zijn voor replicatie

Als u een replica wilt configureren, selecteert u een groene zeshoek en selecteert u vervolgens **Maken**:

 ![Gebruikersinterface voor het maken van een replicatie in Azure Portal](media/container-registry-geo-replication/create-replication.png)

Als u extra replica's wilt configureren, selecteert u de groene zeshoeken voor andere regio's en klikt u op **Maken**.

ACR begint installatiekopieën te synchroniseren voor de geconfigureerde replica's. Zodra dit is voltooid, geeft de portal *Gereed* weer. De status van de replica in de portal wordt niet automatisch bijgewerkt. Gebruik de vernieuwknop om de bijgewerkte status te bekijken.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Overwegingen voor het gebruik van een geo-gerepliceerd register

* Elke regio in een geo-gerepliceerd register is onafhankelijk zodra deze is ingesteld. Azure Container Registry SLA's zijn van toepassing op elke geo-gerepliceerde regio.
* Wanneer u afbeeldingen uit een geo-gerepliceerd register duwt of ophaalt, stuurt Azure Traffic Manager op de achtergrond het verzoek naar het register in de regio die het dichtst bij u staat.
* Nadat u een afbeelding of tagupdate naar het dichtstbijzijnde gebied hebt gepusht, duurt het enige tijd voordat Azure Container Registry de manifesten en lagen repliceert naar de resterende regio's waaru zich voor hebt aangemeld. Grotere afbeeldingen duren langer om te repliceren dan kleinere. Afbeeldingen en tags worden gesynchroniseerd in de replicatiegebieden met een uiteindelijk consistentiemodel.
* Als u werkstromen wilt beheren die afhankelijk zijn van push-updates voor een geo-gerepliceerde, raden we u aan [webhooks](container-registry-webhook.md) te configureren om te reageren op de pushgebeurtenissen. U regionale webhooks instellen in een geo-gerepliceerd register om pushgebeurtenissen bij te houden die worden voltooid in de geo-gerepliceerde regio's.

## <a name="delete-a-replica"></a>Een replica verwijderen

Nadat u een replica voor uw register hebt geconfigureerd, u deze op elk gewenst moment verwijderen als deze niet meer nodig is. Een replica verwijderen met behulp van de Azure-portal of andere hulpprogramma's, zoals de opdracht voor [het verwijderen van az acr-replicatie](/cli/azure/acr/replication#az-acr-replication-delete) in de Azure CLI.

Ga als lid van het nieuwe bedrijf over een replica in de Azure-portal:

1. Navigeer naar uw Azure Container Registry en selecteer **Replicaties**.
1. Selecteer de naam van een replica en selecteer **Verwijderen**. Controleer of u de replica wilt verwijderen.

> [!NOTE]
> U de registerreplica niet verwijderen in het *thuisgebied* van het register, dat wil zeggen de locatie waar u het register hebt gemaakt. U de huisreplica alleen verwijderen door het register zelf te verwijderen.

## <a name="geo-replication-pricing"></a>Prijzen van geo-replicatie

Geo-replicatie is een functie van de [Premium SKU](container-registry-skus.md) van Azure Container Registry. Wanneer u een register naar de gewenste regio's repliceert, worden er kosten voor het Premium-register voor elke regio gemaakt.

In het voorgaande voorbeeld ging Contoso van twee registers naar één en voegde het bedrijf replica's toe aan VS - oost, Canada - centraal en Europa - west. Contoso zou vier keer per maand Premium betalen, zonder extra configuratie of beheer. Elke regio haalt nu installatiekopieën lokaal op, wat zorgt voor betere prestaties en een hogere betrouwbaarheid zonder kosten voor uitgaand netwerkverkeer voor VS - west naar Canada en VS - oost.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Push-bewerkingen oplossen met geo-gerepliceerde registers
 
Een Docker-client die een afbeelding naar een geo-gerepliceerd register duwt, mag niet alle afbeeldingslagen en het manifest naar één gerepliceerd gebied pushen. Dit kan gebeuren omdat Azure Traffic Manager registeraanvragen doorstuurt naar het gerepliceerde register dat het dichtst bij het netwerk ligt. Als het register twee *replicatiegebieden in* de buurt heeft, kunnen afbeeldingslagen en het manifest worden gedistribueerd naar de twee sites en mislukt de pushbewerking wanneer het manifest wordt gevalideerd. Dit probleem treedt op vanwege de manier waarop de DNS-naam van het register is opgelost op sommige Linux-hosts. Dit probleem doet zich niet voor op Windows, dat een DNS-cache aan clientzijde biedt.
 
Als dit probleem zich voordoet, is een oplossing `dnsmasq` om een DNS-cache aan de clientzijde toe te passen, zoals op de Linux-host. Dit helpt ervoor te zorgen dat de naam van het register consistent wordt opgelost. Als u een Linux-VM in Azure gebruikt om naar een register te pushen, raadpleegt u opties in [dns-naamomzettingsopties voor virtuele Linux-machines in Azure.](../virtual-machines/linux/azure-dns.md)

Als u de DNS-resolutie wilt optimaliseren voor de dichtstbijzijnde replica wanneer u afbeeldingen pusht, configureert u een geo-gerepliceerd register in dezelfde Azure-regio's als de bron van de pushbewerkingen of de dichtstbijzijnde regio wanneer u buiten Azure werkt.

## <a name="next-steps"></a>Volgende stappen

Bekijk de driedelige zelfstudiereeks [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md). Ontdek hoe u een geo-gerepliceerd register maakt, een container bouwt en het vervolgens met één `docker push`-opdracht implementeert naar meerdere regionale Web App for Containers-instanties.

> [!div class="nextstepaction"]
> [Geo-replicatie in Azure Container Registry](container-registry-tutorial-prepare-registry.md)
