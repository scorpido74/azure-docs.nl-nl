---
title: AVERE vFXT-opslag configureren-Azure
description: Een back-end-opslag systeem toevoegen aan uw avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: 86b63e6d9799387347093e469015fbd3019069d1
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255041"
---
# <a name="configure-storage"></a>Opslag configureren

Met deze stap stelt u een back-end-opslag systeem in voor uw vFXT-cluster.

> [!TIP]
> Als u een nieuwe Azure Blob-container hebt gemaakt samen met het avere vFXT-cluster, is die container al ingesteld voor gebruik en hoeft u geen opslag ruimte toe te voegen.

Volg deze instructies als u geen nieuwe BLOB-container hebt gemaakt met uw cluster of als u een extra hardware-of cloud-gebaseerd opslag systeem wilt toevoegen.

Er zijn twee hoofd taken:

1. [Maak een kern bestand](#create-a-core-filer), waarmee uw vFXT-cluster wordt verbonden met een bestaand opslag systeem of een Azure Storage-account.

1. [Maak een naam ruimte koppeling](#create-a-junction)die het pad definieert dat clients zullen koppelen.

In deze stappen wordt gebruikgemaakt van het configuratie scherm van AVERE. Lees [toegang tot het vFXT-cluster](avere-vfxt-cluster-gui.md) voor meer informatie over het gebruik ervan.

## <a name="create-a-core-filer"></a>Een kern bestand maken

' Kern bestand ' is een vFXT-term voor een back-end-opslag systeem. De opslag kan een NAS-apparaat zijn zoals NetApp of Isilon, of het kan een object archief in de Cloud zijn. Meer informatie over kern bestanden vindt u [in de hand leiding avere cluster Settings (Engelstalig](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/settings_overview.html#managing-core-filers)).

Als u een kern bestand wilt toevoegen, kiest u een van de twee belangrijkste typen kern bestanden:

  * [NAS core-bestand](#nas-core-filer) -beschrijft hoe een NAS-kern bestand moet worden toegevoegd 
  * [Azure Storage Cloud core-bestand](#azure-storage-cloud-core-filer) : beschrijft hoe u een Azure Storage account toevoegt als een Cloud kern bestand

### <a name="nas-core-filer"></a>NAS-kern bestand

Een NAS core-bestand kan een on-premises NetApp of Isilon of een NAS-eind punt in de Cloud zijn. Het opslag systeem moet een betrouw bare snelle verbinding hebben met het avere vFXT-cluster, bijvoorbeeld een 1 Gbps ExpressRoute-verbinding (geen VPN), en moet het cluster hoofd toegang geven tot de NAS-exports die worden gebruikt.

Met de volgende stappen wordt een NAS-kern bestand toegevoegd:

1. Klik in het configuratie scherm van AVERE op het tabblad **instellingen** bovenaan.

1. Klik op **kern bestand** > **kern bestanden** aan de linkerkant beheren.

1. Klik op **Maken**.

   ![Scherm afbeelding van de pagina nieuwe kern bestand toevoegen met een cursor boven de knop maken](media/avere-vfxt-add-core-filer-start.png)

1. Vul de vereiste gegevens in de wizard in: 

   * Geef uw kern bestand een naam.
   * Geef een Fully Qualified Domain Name (FQDN) op, indien beschikbaar. Geef anders een IP-adres of een hostnaam op die wordt omgezet in uw kern bestand.
   * Kies uw bestands groep in de lijst. Als u dit niet zeker weet, kiest u **Overige**.

     ![Scherm afbeelding van de pagina nieuwe kern bestand toevoegen met de naam van de kern bestand en de Fully Qualified Domain Name](media/avere-vfxt-add-core-filer.png)
  
   * Klik op **volgende** en kies een cache beleid. 
   * Klik op **bestand toevoegen**.
   * Zie [een nieuwe NAS-kern bestand toevoegen](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_nas.html) in de avere-hand leiding voor cluster instellingen voor meer gedetailleerde informatie.

Ga vervolgens verder met het [maken van een verbinding](#create-a-junction).  

### <a name="azure-storage-cloud-core-filer"></a>Azure Storage Cloud-kern bestand

Als u Azure Blob Storage wilt gebruiken als back-upopslag van uw vFXT-cluster, hebt u een lege container nodig om toe te voegen als een kern bestand.

> [!TIP] 
> Als u ervoor kiest om een BLOB-container te maken op het moment dat u het avere vFXT-cluster maakt, maakt de implementatie sjabloon of het script een opslag container, definieert deze als een kern bestand en wordt de naam ruimte verbinding gemaakt als onderdeel van het maken van het vFXT-cluster. Met de sjabloon wordt ook een opslag service-eind punt in het virtuele netwerk van het cluster gemaakt. 

Voor het toevoegen van Blob-opslag aan uw cluster zijn de volgende taken vereist:

* Een opslag account maken (stap 1, hieronder)
* Een lege BLOB-container maken (stap 2-3)
* Voeg de toegangs sleutel voor opslag toe als een Cloud referentie voor het vFXT-cluster (stap 4-6)
* Voeg de BLOB-container toe als een kern bestand voor het vFXT-cluster (stap 7-9)
* Maak een naam ruimte koppeling die clients gebruiken om de kern bestand te koppelen ([een verbinding maken](#create-a-junction), hetzelfde voor hardware-en Cloud opslag)

Voer de volgende stappen uit om Blob-opslag toe te voegen na het maken van het cluster. 

1. Maak een opslag account voor algemeen gebruik v2 met de volgende instellingen:

   * **Abonnement** : hetzelfde als het vFXT-cluster
   * **Resource groep** : hetzelfde als de vFXT-cluster groep (optioneel)
   * **Locatie** : hetzelfde als het vFXT-cluster
   * **Prestaties** -standaard (Premium-opslag wordt niet ondersteund)
   * **Type account** -algemeen gebruik v2 (StorageV2)
   * **Replicatie** -lokaal redundante opslag (LRS)
   * **Access-laag** -Hot
   * **Beveiligde overdracht vereist** : Schakel deze optie uit (niet-standaard waarde)
   * **Virtuele netwerken** -niet vereist

   U kunt de Azure Portal gebruiken of klikken op de knop implementeren naar Azure hieronder.

   [![button voor het maken van een opslag account](media/deploytoazure.png)](https://ms.portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAvere%2Fmaster%2Fsrc%2Fvfxt%2Fstorageaccount%2Fazuredeploy.json)

1. Nadat het account is gemaakt, bladert u naar de pagina voor het opslag account.

   ![Nieuw opslag account in Azure Portal](media/avere-vfxt-new-storage-acct.png)

1. Maak een BLOB-container door te klikken op **blobs** op de pagina overzicht en klik vervolgens op **+ container**. Gebruik een wille keurige container naam en zorg ervoor dat de toegang is ingesteld op **privé**.

   ![Pagina met opslag-blobs zonder bestaande containers](media/avere-vfxt-blob-no-container.png)

1. Haal de sleutel van het Azure Storage-account op door te klikken op **toegangs sleutels** onder **instellingen**:

   ![Azure Portal GUI voor het kopiëren van de sleutel](media/avere-vfxt-copy-storage-key.png) 

1. Open het configuratie scherm van AVERE voor uw cluster. Klik op **instellingen**en open vervolgens **cluster**@no__t 2-**referenties voor Cloud** in het navigatie deel venster aan de linkerkant. Klik op de pagina Cloud referenties op **referentie toevoegen**.

   ![Klik op de knop referentie toevoegen op de pagina configuratie van Cloud referenties](media/avere-vfxt-new-credential-button.png)

1. Vul de volgende gegevens in om een referentie te maken voor de Cloud core Filer: 

   | Veld | Waarde |
   | --- | --- |
   | Referentienaam | een beschrijvende naam |
   | Servicetype | (Selecteer Azure Storage toegangs sleutel) |
   | Tenant | naam van opslagaccount |
   | Abonnement | subscription ID |
   | Toegangs sleutel voor opslag | Sleutel van het Azure-opslag account (gekopieerd in de vorige stap) | 

   Klik op **Submit**

   ![Het formulier voor de Cloud referentie in het configuratie scherm van AVERE is voltooid](media/avere-vfxt-new-credential-submit.png)

1. Maak vervolgens de kern bestand. Klik aan de linkerkant van het configuratie scherm van AVERE op **core filer** >  **kern bestanden beheren**. 

1. Klik op de knop **maken** op de pagina instellingen voor **kern bestanden beheren** .

1. Vul de wizard in:

   * Selecteer de **Cloud**van het type filer. 
   * Geef de nieuwe kern bestand een naam en klik op **volgende**.
   * Accepteer het standaard cache beleid en ga door naar de derde pagina.
   * Kies in **Service type**de optie **Azure Storage**. 
   * Kies de referentie die u eerder hebt gemaakt.
   * **Bucket inhoud** instellen op **leeg**
   * **Certificaat verificatie** wijzigen in **uitgeschakeld**
   * **Compressie modus** wijzigen in **geen**  
   * Klik op **Volgende**.
   * Voer op de vierde pagina de naam van de container in **Bucket naam** in als *storage_account_name*/*container_name*.
   * U kunt ook het **versleutelings type** instellen op **geen**.  Azure Storage is standaard versleuteld.
   * Klik op **bestand toevoegen**.

   Lees [een nieuwe Cloud core filer toevoegen](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/new_core_filer_cloud.html>) in de avere-cluster configuratie handleiding voor meer gedetailleerde informatie. 

De pagina wordt vernieuwd of u kunt de pagina vernieuwen om uw nieuwe kern bestand weer te geven.

Vervolgens moet u [een verbinding maken](#create-a-junction).

## <a name="create-a-junction"></a>Een verbinding maken

Een verbinding is een pad dat u voor clients maakt. Clients koppelen het pad en ontvangen op de bestemming die u kiest.

U kunt bijvoorbeeld `/avere/files` maken om toe te wijzen aan uw NetApp core-bestand `/vol0/data`-export en de submap `/project/resources`.

Meer informatie over koppelingen vindt u in de [sectie naam ruimte van de avere-cluster configuratie handleiding](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_namespace.html).

Volg deze stappen in de instellingen interface van het configuratie scherm van AVERE:

* Klik in de linkerbovenhoek op **VServer** > **naam ruimte** .
* Geef een naam ruimte-pad op dat begint met/(slash), bijvoorbeeld ``/avere/data``.
* Kies uw kern bestand.
* Kies de kern bestands export.
* Klik op **Volgende**.

  ![Scherm opname van de pagina ' nieuwe verbinding toevoegen ' met de velden die zijn voltooid voor Junction, kern bestand en exporteren](media/avere-vfxt-add-junction.png)

De verbinding wordt na een paar seconden weer gegeven. Maak indien nodig extra-koppelingen.

Nadat de verbinding is gemaakt, kunnen clients [het avere vFXT-cluster koppelen](avere-vfxt-mount-clients.md) voor toegang tot het bestands systeem.
