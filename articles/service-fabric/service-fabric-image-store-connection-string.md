---
title: Verbindingstekenreeks azure Service Fabric-afbeeldingsarchief
description: Meer informatie over de verbindingstekenreeks voor afbeeldingenopteplaatsen, inclusief het gebruik en de toepassingen ervan in een cluster van Servicefabric.
author: alexwun
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: alexwun
ms.openlocfilehash: c3395248188c2a16736cfc8cea262fe163a6944b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645664"
---
# <a name="understand-the-imagestoreconnectionstring-setting"></a>Meer informatie over de instelling ImageStoreConnectionString

In sommige van onze documentatie vermelden we kort het bestaan van een parameter "ImageStoreConnectionString" zonder te beschrijven wat het werkelijk betekent. En na het doorlopen van een artikel als [Implementeren en verwijderen van toepassingen met PowerShell,][10]het lijkt erop dat alles wat je doet is kopiëren / plakken van de waarde zoals weergegeven in het cluster manifest van het doelcluster. De instelling moet dus configureerbaar zijn per cluster, maar wanneer u een cluster maakt via de [Azure-portal,][11]is er geen optie om deze instelling te configureren en is het altijd "fabric:ImageStore". Wat is dan het doel van deze instelling?

![Clustermanifest][img_cm]

Service Fabric begon als een platform voor interne Microsoft-consumptie door vele verschillende teams, dus sommige aspecten ervan zijn zeer aanpasbaar - de "Image Store" is een dergelijk aspect. In wezen is de Image Store een pluggable repository voor het opslaan van toepassingspakketten. Wanneer uw toepassing wordt geïmplementeerd op een knooppunt in het cluster, downloadt dat knooppunt de inhoud van uw toepassingspakket uit de Image Store. De ImageStoreConnectionString is een instelling die alle benodigde informatie bevat voor zowel clients als knooppunten om de juiste Image Store voor een bepaald cluster te vinden.

Er zijn momenteel drie mogelijke soorten Image Store-providers en de bijbehorende verbindingstekenreeksen zijn als volgt:

1. Image Store-service: "fabric:ImageStore"

2. Bestandssysteem: "bestand:[bestandssysteempad]"

3. Azure Storage: "xstore:DefaultEndpointsProtocol=https; AccountName=[...]; AccountKey=[...]; Container=[...]"

Het type provider dat wordt gebruikt in de productie is de Image Store-service, een stateful persisted-systeemservice die u zien in Service Fabric Explorer. 

![Image Store-service][img_is]

Als u de Image Store host in een systeemservice binnen het cluster zelf, worden externe afhankelijkheden voor de pakketopslagplaats geëlimineerd en hebben we meer controle over de plaats van opslag. Toekomstige verbeteringen rond de Image Store zijn waarschijnlijk eerst gericht op de Image Store-provider, zo niet uitsluitend. De verbindingstekenreeks voor de Image Store-serviceprovider heeft geen unieke informatie, omdat de client al is verbonden met het doelcluster. De client hoeft alleen te weten dat protocollen die zich richten op de systeemservice moeten worden gebruikt.

De bestandssysteemprovider wordt gebruikt in plaats van de Image Store-service voor lokale clusters met één doos tijdens de ontwikkeling om het cluster iets sneller op te starten. Het verschil is meestal klein, maar het is een nuttige optimalisatie voor de meeste mensen tijdens de ontwikkeling. Het is ook mogelijk om een lokaal cluster met één doos te implementeren met de andere typen opslagleveranciers, maar er is meestal geen reden om dit te doen omdat de ontwikkel-/testworkflow hetzelfde blijft, ongeacht de provider. De Azure Storage-provider bestaat alleen voor oudere ondersteuning van oude clusters die zijn geïmplementeerd voordat de Image Store-serviceprovider werd geïntroduceerd.

Bovendien moet niet de bestandssysteemprovider of de Azure Storage-provider worden gebruikt als een methode om een Image Store tussen meerdere clusters te delen - dit zal resulteren in beschadiging van clusterconfiguratiegegevens, omdat elk cluster conflicterende gegevens naar de afbeelding kan schrijven Winkel. Als u ingerichte toepassingspakketten wilt delen tussen meerdere clusters, gebruikt u in plaats daarvan [sfpkg-bestanden,][12] die kunnen worden geüpload naar elke externe winkel met een download-URI.

Dus terwijl de ImageStoreConnectionString configureerbaar is, gebruikt u gewoon de standaardinstelling. Wanneer u via Visual Studio naar Azure publiceert, wordt de parameter automatisch voor u ingesteld. Voor programmatische implementatie naar clusters die in Azure worden gehost, is de verbindingstekenreeks altijd 'fabric:ImageStore'. Hoewel bij twijfel, de waarde ervan kan altijd worden geverifieerd door het ophalen van het clustermanifest door [PowerShell](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricclustermanifest), [.NET](https://msdn.microsoft.com/library/azure/mt161375.aspx)of [REST](https://docs.microsoft.com/rest/api/servicefabric/get-a-cluster-manifest). Zowel on-premises test- als productieclusters moeten altijd worden geconfigureerd om ook de Image Store-serviceprovider te gebruiken.

### <a name="next-steps"></a>Volgende stappen
[Toepassingen implementeren en verwijderen met PowerShell][10]

<!--Image references-->
[img_is]: ./media/service-fabric-image-store-connection-string/image_store_service.png
[img_cm]: ./media/service-fabric-image-store-connection-string/cluster_manifest.png

[10]: service-fabric-deploy-remove-applications.md
[11]: service-fabric-cluster-creation-via-portal.md
[12]: service-fabric-package-apps.md#create-an-sfpkg
