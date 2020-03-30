---
title: Avere vFXT-opslag configureren - Azure
description: Een back-endopslagsysteem toevoegen aan uw Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: dfffef90201ba4bbb5a912df6101e8338012df44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252608"
---
# <a name="configure-storage"></a>Opslag configureren

Met deze stap wordt een back-end opslagsysteem voor uw vFXT-cluster ingesteld.

> [!TIP]
> Als u samen met het Avere vFXT-cluster een nieuwe Azure Blob-container hebt gemaakt, is die container al geconfigureerd en klaar voor gebruik.

Volg deze instructies als u geen nieuwe Blob-container met uw cluster hebt gemaakt of als u een extra hardware- of cloudopslagsysteem wilt toevoegen.

Er zijn twee hoofdtaken:

1. [Maak een core filer,](#create-a-core-filer)die uw vFXT-cluster verbindt met een bestaand opslagsysteem of een Azure Storage-accountcontainer.

1. [Maak een naamruimteknooppunt](#create-a-junction), dat het pad definieert dat clients zullen monteren.

Deze stappen maken gebruik van het Configuratiescherm van Avere. Lees [Access het vFXT-cluster](avere-vfxt-cluster-gui.md) voor meer informatie over het gebruik ervan.

## <a name="create-a-core-filer"></a>Een core filer maken

"Core filer" is een vFXT term voor een back-end opslagsysteem. De opslag kan een hardware NAS-toestel zijn, zoals NetApp of Isilon, of het kan een cloudobjectwinkel zijn. Meer informatie over core filers is te vinden in de [avere cluster instellingen gids](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers).

Als u een core filer wilt toevoegen, kiest u een van de twee hoofdtypen kernbestanden:

* [NAS core filer](#nas-core-filer) - beschrijft hoe u een NAS-core filer toevoegt
* [Azure Storage cloud core filer](#azure-blob-storage-cloud-core-filer) - beschrijft hoe u een Azure Blob-opslagcontainer toevoegt als een cloudcore-filer

### <a name="nas-core-filer"></a>NAS-kernfiler

Een NAS-core filer kan een on-premises NetApp- of Isilon-toestel zijn, of een NAS-eindpunt in de cloud. Het opslagsysteem moet een betrouwbare snelle verbinding hebben met het Avere vFXT-cluster - bijvoorbeeld een 1GBps ExpressRoute-verbinding (geen VPN) - en het moet de clusterroottoegang geven tot de NAS-export die wordt gebruikt.

Volg de volgende stappen om een NAS-kernfiler toe te voegen:

1. Klik in het Configuratiescherm van Avere op het tabblad **Instellingen** bovenaan.

1. Klik op **Core Filer** > **Core-bestanden beheren aan** de linkerkant.

1. Klik **op Maken**.

   ![Schermafbeelding van de pagina Nieuwe kernfiler toevoegen met een cursor over de knop Maken](media/avere-vfxt-add-core-filer-start.png)

1. Vul de vereiste informatie in de wizard in:

   * Geef je core filer een naam.
   * Geef een volledig gekwalificeerde domeinnaam (FQDN) indien beschikbaar. Geef anders een IP-adres of hostnaam op dat wordt opgelost in uw kernfiler.
   * Kies uw filerklasse in de lijst. Kies Andere **,** als u het niet zeker weet.

     ![Schermafbeelding van de pagina Nieuwe kernfiler toevoegen met de naam van de core filer en de volledig gekwalificeerde domeinnaam](media/avere-vfxt-add-core-filer.png)
  
   * Klik **op Volgende** en kies een cachebeleid.
   * Klik **op Filer toevoegen**.
   * Zie Voor meer gedetailleerde informatie het [toevoegen van een nieuwe NAS-kernfiler](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) in de richtlijnen voor avere-clusterinstellingen.

Ga vervolgens verder met [Een knooppunt maken](#create-a-junction).  

### <a name="azure-blob-storage-cloud-core-filer"></a>Azure Blob Storage cloud core filer

Als u Azure Blob-opslag wilt gebruiken als de back-endopslag van uw vFXT-cluster, hebt u een lege container nodig om als kernfiler toe te voegen.

Voor het toevoegen van Blob-opslag aan uw cluster zijn de volgende taken vereist:

* Een opslagaccount maken (stap 1, hieronder)
* Een lege Blob-container maken (stap 2-3)
* De opslagtoegangssleutel toevoegen als cloudreferentie voor het vFXT-cluster (stap 4-6)
* De Blob-container toevoegen als een core filer voor het vFXT-cluster (stap 7-9)
* Een naamruimteverbinding maken die clients gebruiken om de core filer te monteren[(Een knooppunt maken](#create-a-junction), hetzelfde voor zowel hardware- als cloudopslag)

> [!TIP]
> Als u een nieuwe Blob-container maakt wanneer u een Avere vFXT voor Azure-cluster maakt, configureert de implementatiesjabloon de container automatisch als een core filer. (Dit geldt ook als u het aanmaakscript gebruikt, dat op aanvraag beschikbaar is.) U hoeft de core filer daarna niet te configureren.
>
> Het hulpprogramma voor het maken van het cluster uitvoert deze configuratietaken voor u:
>
> * Een nieuwe Blob-container maken in het opgegeven opslagaccount
> * Definieert de container als een kernfiler
> * Hiermee maakt u een naamruimteverbinding met de container
> * Hiermee maakt u een eindpunt van de opslagservice in het virtuele netwerk van het cluster

Voer deze stappen uit om Blob-opslag toe te voegen nadat u het cluster hebt gemaakt.

1. Maak een V2-opslagaccount voor algemene doeleinden met de volgende instellingen:

   * **Abonnement** - hetzelfde als het vFXT-cluster
   * **Resourcegroep** - hetzelfde als de vFXT-clustergroep (optioneel)
   * **Locatie** - hetzelfde als het vFXT-cluster
   * **Prestaties** - Standaard (Premium-opslag wordt niet ondersteund)
   * **Accountsoort** - V2 voor algemene doeleinden (StorageV2)
   * **Replicatie** - Lokaal redundante opslag (LRS)
   * **Toegangslaag** - Hot
   * **Beveiligde overdracht vereist** - schakel deze optie uit (niet-standaardwaarde)
   * **Virtuele netwerken** - niet vereist

   U de Azure-portal gebruiken of hieronder op de knop 'Implementeren naar Azure' klikken.

   [![knop om een opslagaccount te maken](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Nadat het account is gemaakt, bladert u naar de pagina met het opslagaccount.

   ![Nieuw opslagaccount in Azure-portal](media/avere-vfxt-new-storage-acct.png)

1. Een nieuwe Blob-container maken: klik op **Containers** op de overzichtspagina en klik vervolgens op **+Container**. Gebruik een containernaam en zorg ervoor dat de toegang is ingesteld op **Privé.**

   ![Pagina Blobs opslaan met de knop +container omcirkeld en een nieuwe container die wordt gemaakt op een pop-uppagina](media/avere-vfxt-new-blob.png)

1. Download de Azure Storage-accountsleutel door te klikken op **Toegangssleutels** onder **Instellingen**. Kopieer een van de meegeleverde sleutels.

   ![Azure-portal-GUI voor het kopiëren van de sleutel](media/avere-vfxt-copy-storage-key.png)

1. Open het Configuratiescherm van Avere voor uw cluster. Klik **op Instellingen**en open vervolgens**clustercloudreferenties** **Cluster** > in het linkernavigatiedeelvenster. Klik op de pagina Cloudreferenties op **Referenties toevoegen**.

   ![Klik op de knop Referenties toevoegen op de configuratiepagina cloudreferenties](media/avere-vfxt-new-credential-button.png)

1. Vul de volgende gegevens in om een referentie voor de cloudcore-filer te maken:

   | Veld | Waarde |
   | --- | --- |
   | Referentienaam | elke beschrijvende naam |
   | Servicetype | (selecteer Azure Storage access key) |
   | Tenant | naam van opslagaccount |
   | Abonnement | subscription ID |
   | Opslagtoegangssleutel | Azure-opslagaccountsleutel (gekopieerd in de vorige stap) |

   Klik **op Verzenden**.

   ![Voltooid formulier voor cloudreferenties in het Configuratiescherm van Avere](media/avere-vfxt-new-credential-submit.png)

1. Maak vervolgens de core filer. Klik aan de linkerkant van het Configuratiescherm van Avere op **Core Filer** >  **Manage Core Filers**.

1. Klik **op** de knop Maken op de pagina **Instellingen van Core Filers beheren.**

1. Vul de wizard in:

   * Selecteer filertype **Cloud**.
   * Geef de nieuwe core filer een naam en klik op **Volgende**.
   * Accepteer het standaardcachebeleid en ga verder naar de derde pagina.
   * Kies in **Servicetype** **Azure-opslag**.
   * Kies de eerder gemaakte referentie.
   * **Bucket-inhoud** instellen op **Leeg**
   * **Certificaatverificatie** wijzigen in **uitgeschakeld**
   * **Compressiemodus** wijzigen in **Geen**
   * Klik op **Volgende**.
   * Voer op de vierde pagina de naam van de container in **de naam Bucket** *in, storage_account_name*/*container_name*.
   * Stel eventueel **versleutelingstype** in op **Geen**.  Azure Storage is standaard versleuteld.
   * Klik **op Filer toevoegen**.

   Lees Voor meer gedetailleerde informatie [een nieuwe cloudcorefiler toevoegen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) in de configuratiehandleiding voor het Avere-cluster.

De pagina wordt vernieuwd of u de pagina vernieuwen om uw nieuwe core filer weer te geven.

Vervolgens moet u [een knooppunt maken.](#create-a-junction)

## <a name="create-a-junction"></a>Een knooppunt maken

Een knooppunt is een pad dat u voor clients maakt. Klanten monteren het pad en komen aan op de bestemming die u kiest.

U bijvoorbeeld `/vfxt/files` maken om uw NetApp-core filer-export `/vol0/data` en de `/project/resources` submap toe te geven.

Meer informatie over knooppunten vindt u in het [gedeelte naamruimte van de configuratiegids van het Avere-cluster.](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html)

Volg de volgende stappen in de interface van het Configuratiescherm van Avere:

* Klik linksboven op > **VServer-naamruimte.** **VServer**
* Geef een naamruimtepad op dat begint ``/vfxt/data``met / (slash vooruit), zoals .
* Kies uw core filer.
* Kies de export van de core filer.
* Klik op **Volgende**.

  ![Schermafbeelding van de pagina 'Nieuwe knooppunt toevoegen' met de velden voltooid voor knooppunt, core filer en exporteren](media/avere-vfxt-add-junction.png)

De kruising verschijnt na enkele seconden. Maak indien nodig extra knooppunten.

Nadat de verbinding is gemaakt, gebruiken clients het naamruimtepad om toegang te krijgen tot de bestanden vanuit het opslagsysteem.

## <a name="next-steps"></a>Volgende stappen

* [Het Avere vFXT-cluster koppelen](avere-vfxt-mount-clients.md)
* Ontdek efficiënte manieren om [gegevens naar een nieuwe Blob-container te verplaatsen](avere-vfxt-data-ingest.md)
