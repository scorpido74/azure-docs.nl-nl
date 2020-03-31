---
title: Versleuteling in Azure Data Lake Storage Gen1 | Microsoft Documenten
description: Versleuteling in Azure Data Lake Storage Gen1 helpt u uw gegevens te beschermen, bedrijfsbeveiligingsbeleid te implementeren en te voldoen aan de nalevingsvereisten van de regelgeving. In dit artikel vindt u een overzicht van het ontwerp en worden een aantal technische aspecten van de implementatie besproken.
services: data-lake-store
documentationcenter: ''
author: esung22
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: yagupta
ms.openlocfilehash: a009f212bd8baaa353d602dc6090aeeccddd4936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878393"
---
# <a name="encryption-of-data-in-azure-data-lake-storage-gen1"></a>Versleuteling van gegevens in Azure Data Lake Storage Gen1

Versleuteling in Azure Data Lake Storage Gen1 helpt u uw gegevens te beschermen, bedrijfsbeveiligingsbeleid te implementeren en te voldoen aan de nalevingsvereisten van de regelgeving. In dit artikel vindt u een overzicht van het ontwerp en worden een aantal technische aspecten van de implementatie besproken.

Data Lake Storage Gen1 ondersteunt versleuteling van gegevens, zowel in rust als onderweg. Voor gegevens in rust ondersteunt Data Lake Storage Gen1 'standaard' transparante versleuteling. Hier wordt iets gedetailleerder uitgelegd wat deze termen betekenen:

* **Standaard ingeschakeld**: Wanneer u een nieuw Data Lake Storage Gen1-account maakt, maakt de standaardinstelling versleuteling mogelijk. Daarna worden gegevens die worden opgeslagen in Data Lake Storage Gen1 altijd versleuteld voordat ze worden opgeslagen op persistente media. Dit is het gedrag voor alle gegevens en kan niet worden gewijzigd nadat u een account hebt gemaakt.
* **Transparant**: Data Lake Storage Gen1 versleutelt automatisch gegevens voordat ze blijven bestaan en decodeert gegevens voordat ze worden opgehaald. De versleuteling wordt geconfigureerd en beheerd op het accountniveau Van Data Lake Storage Gen1 door een beheerder. Er worden geen wijzigingen aangebracht aan de API's voor gegevenstoegang. Er zijn dus geen wijzigingen vereist in toepassingen en services die communiceren met Data Lake Storage Gen1 vanwege versleuteling.

Data in transit (ook bekend als data in motion) wordt ook altijd versleuteld in Data Lake Storage Gen1. Naast het versleutelen van gegevens voorafgaand aan het opslaan op permanente media, worden de gegevens tijdens overdracht ook altijd beveiligd met behulp van HTTPS. HTTPS is het enige protocol dat wordt ondersteund voor de Data Lake Storage Gen1 REST-interfaces. In het volgende diagram ziet u hoe gegevens worden versleuteld in Data Lake Storage Gen1:

![Diagram van gegevensversleuteling in Data Lake Storage Gen1](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-storage-gen1"></a>Versleuteling instellen met Data Lake Storage Gen1

Versleuteling voor Data Lake Storage Gen1 wordt ingesteld tijdens het maken van een account en is altijd standaard ingeschakeld. U de sleutels zelf beheren of Data Lake Storage Gen1 toestaan ze voor u te beheren (dit is de standaard).

Zie de [Introductie](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal) voor meer informatie.

## <a name="how-encryption-works-in-data-lake-storage-gen1"></a>Hoe versleuteling werkt in Data Lake Storage Gen1

De volgende informatie heeft betrekking op het beheren van hoofdversleutelingssleutels en het verklaart de drie verschillende soorten sleutels die u gebruiken in gegevensversleuteling voor Data Lake Storage Gen1.

### <a name="master-encryption-keys"></a>Hoofdversleutelingssleutels

Data Lake Storage Gen1 biedt twee modi voor het beheer van master encryption keys (MEKs). Op dit moment wordt ervan uitgegaan dat de hoofdversleutelingssleutel de sleutel op het hoogste niveau is. Toegang tot de hoofdversleutelingssleutel is vereist om alle gegevens die zijn opgeslagen in Data Lake Storage Gen1 te decoderen.

De twee modi voor het beheer van de hoofdversleutelingssleutel zijn als volgt:

*   Door service beheerde sleutels
*   Door klant beheerde sleutels

In beide modi wordt de hoofdversleutelingssleutel beveiligd door opslag in Azure Key Vault. Key Vault is een volledig beheerde, zeer veilige service in Azure die kan worden gebruikt ter bescherming van de cryptografische sleutels. Zie voor meer informatie [Key Vault](https://azure.microsoft.com/services/key-vault).

Hier volgt een korte vergelijking van de mogelijkheden van de twee modi voor het beheren van de MEK's.

|  | Door service beheerde sleutels | Door klant beheerde sleutels |
| --- | --- | --- |
|Hoe worden gegevens opgeslagen?|Altijd versleuteld voordat deze worden opgeslagen.|Altijd versleuteld voordat deze worden opgeslagen.|
|Waar wordt de hoofdversleutelingssleutel opgeslagen?|Key Vault|Key Vault|
|Worden versleutelingssleutels opgeslagen buiten Key Vault? |Nee|Nee|
|Kan de MEK worden opgehaald uit Key Vault?|Nee. Eenmaal opgeslagen in de Key Vault kan de MEK alleen worden gebruikt voor versleuteling en ontsleuteling.|Nee. Eenmaal opgeslagen in de Key Vault kan de MEK alleen worden gebruikt voor versleuteling en ontsleuteling.|
|Wie is eigenaar van de Key Vault-instantie en de MEK?|De Data Lake Storage Gen1-service|U bent eigenaar van de Key Vault-instantie in uw eigen Azure-abonnement. De MEK in de Key Vault kan worden beheerd door software of hardware.|
|u de toegang tot de MEK intrekken voor de Data Lake Storage Gen1-service?|Nee|Ja. U toegangscontrolelijsten beheren in Key Vault en toegangscontrolegegevens verwijderen voor de service-identiteit voor de Data Lake Storage Gen1-service.|
|Kunt u de MEK permanent verwijderen?|Nee|Ja. Als u de MEK uit Key Vault verwijdert, kunnen de gegevens in het Data Lake Storage Gen1-account door niemand worden gedecodeerd, inclusief de Data Lake Storage Gen1-service. <br><br> Als u een expliciete back-up van de MEK maakt voordat u deze uit de Key Vault verwijdert, kan deze worden hersteld en kunnen de gegevens vervolgens worden hersteld. Als u echter geen back-up van de MEK hebt gemaakt voordat u het uit Key Vault verwijderde, kunnen de gegevens in het Data Lake Storage Gen1-account daarna nooit meer worden gedecodeerd.|


Afgezien van het verschil wat betreft wie de MEK en de Key Vault-instantie waarin deze zich bevindt beheert, is de rest van het ontwerp hetzelfde voor beide modi.

Het is belangrijk het volgende te onthouden wanneer u de modus voor de hoofdversleutelingssleutels kiest:

*   U kiezen of u door de klant beheerde sleutels of servicebeheersleutels wilt gebruiken wanneer u een Data Lake Storage Gen1-account indient.
*   Nadat een Data Lake Storage Gen1-account is ingericht, kan de modus niet worden gewijzigd.

### <a name="encryption-and-decryption-of-data"></a>Versleuteling en ontsleuteling van gegevens

Er zijn drie soorten sleutels die worden gebruikt in het ontwerp van gegevensversleuteling. De volgende tabel geeft een samenvatting:

| Sleutel                   | Afkorting | Gekoppeld aan | Opslaglocatie                             | Type       | Opmerkingen                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| Hoofdversleutelingssleutel | MEK          | Een Data Lake Storage Gen1-account | Key Vault                              | Asymmetrisch | Het kan worden beheerd door Data Lake Storage Gen1 of u.                                                              |
| Gegevensversleutelingssleutel   | DEK          | Een Data Lake Storage Gen1-account | Permanente opslag, beheerd door de Data Lake Storage Gen1-service | Symmetrisch  | De DEK is versleuteld door de MEK. De versleutelde DEK is hetgeen wat wordt opgeslagen op persistente media. |
| Blokversleutelingssleutel  | BEK          | Een gegevensblok | Geen                                         | Symmetrisch  | De BEK wordt afgeleid van de DEK en het gegevensblok.                                                      |

Het volgende diagram illustreert deze concepten:

![Sleutels bij gegevensversleuteling](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>Pseudo-algoritme wanneer een bestand moet worden ontsleuteld:
1.  Controleer of de DEK voor het Data Lake Storage Gen1-account in de cache is opgeslagen en klaar is voor gebruik.
    - Als dat niet het geval is, leest u de versleutelde DEK uit de permanente opslag en stuurt u deze naar de Key Vault voor ontsleuteling. Sla de ontsleutelde DEK op in het cachegeheugen. Deze is nu gereed voor gebruik.
2.  Voor elk gegevensblok in het bestand:
    - Lees het versleutelde gegevensblok uit de permanente opslag.
    - Genereer de BEK uit de DEK en het versleutelde gegevensblok.
    - Gebruik de BEK om gegevens te ontsleutelen.


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>Pseudo-algoritme wanneer een gegevensblok moeten worden versleuteld:
1.  Controleer of de DEK voor het Data Lake Storage Gen1-account in de cache is opgeslagen en klaar is voor gebruik.
    - Als dat niet het geval is, leest u de versleutelde DEK uit de permanente opslag en stuurt u deze naar de Key Vault voor ontsleuteling. Sla de ontsleutelde DEK op in het cachegeheugen. Deze is nu gereed voor gebruik.
2.  Genereer een unieke BEK voor het gegevensblok uit de DEK.
3.  Versleutel het gegevensblok met de BEK met behulp van AES-256-codering.
4.  Sla het versleutelde gegevensblok op in permanente opslag.

> [!NOTE] 
> De DEK wordt altijd versleuteld door de MEK opgeslagen, op permanente media of in het cachegeheugen.

## <a name="key-rotation"></a>Sleutelroulatie

Wanneer u door de klant beheerde sleutels gebruikt, kunt u de MEK rouleren. Zie [Aan de slag voor](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)meer informatie over het instellen van een Data Lake Storage Gen1-account met door de klant beheerde sleutels.

### <a name="prerequisites"></a>Vereisten

Wanneer u het Data Lake Storage Gen1-account instelt, hebt u ervoor gekozen om uw eigen sleutels te gebruiken. Deze optie kan niet worden gewijzigd nadat het account is gemaakt. In de volgende stappen wordt ervan uitgegaan dat u door de klant beheerde sleutels gebruikt (u hebt uw eigen sleutels uit Key Vault gekozen).

Houd er rekening mee dat als u de standaardopties voor versleuteling gebruikt, uw gegevens altijd worden versleuteld met sleutels die worden beheerd door Data Lake Storage Gen1. In deze optie hebt u niet de mogelijkheid om toetsen te roteren, omdat ze worden beheerd door Data Lake Storage Gen1.

### <a name="how-to-rotate-the-mek-in-data-lake-storage-gen1"></a>De MEK roteren in Data Lake Storage Gen1

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Blader naar het exemplaar Key Vault waarmee uw sleutels worden opgeslagen die zijn gekoppeld aan uw Data Lake Storage Gen1-account. Selecteer **Sleutels**.

    ![Schermafdruk van Key Vault](./media/data-lake-store-encryption/keyvault.png)

3. Selecteer de sleutel die is gekoppeld aan uw Data Lake Storage Gen1-account en maak een nieuwe versie van deze sleutel. Data Lake Storage Gen1 ondersteunt momenteel alleen sleutelrotatie naar een nieuwe versie van een sleutel. Roulatie naar een andere sleutel wordt niet ondersteund.

   ![Schermafdruk van het venster Sleutels met de nieuwe versie gemarkeerd](./media/data-lake-store-encryption/keynewversion.png)

4. Blader naar het Data Lake Storage Gen1-account en selecteer **Versleuteling**.

   ![Schermafbeelding van het accountvenster Data Lake Storage Gen1, met versleuteling gemarkeerd](./media/data-lake-store-encryption/select-encryption.png)

5. Er wordt een bericht weergegeven dat een nieuwe sleutelversie van de sleutel beschikbaar is. Klik op **Sleutel rouleren** om de sleutel naar de nieuwe versie bij te werken.

   ![Schermafbeelding van het Venster Gegevensmeeropslag Gen1 met bericht en Sleutel roteren gemarkeerd](./media/data-lake-store-encryption/rotatekey.png)

Deze bewerking duurt minder dan twee minuten en er is geen verwachte uitvaltijd vanwege het rouleren van de sleutel. Nadat de bewerking is voltooid, wordt de nieuwe versie van de sleutel gebruikt.

> [!IMPORTANT]
> Nadat de sleutelroulatiebewerking voltooid is, wordt de oude versie van de sleutel niet meer actief gebruikt voor het versleutelen van uw gegevens.  In zeldzame gevallen van onverwachte fouten waardoor zelfs redundante exemplaren van uw gegevens zijn getroffen, kunnen gegevens echter mogelijk worden hersteld vanuit een back-up waarvoor nog de oude sleutel wordt gebruikt. Bewaar een kopie van de vorige versie van de versleutelingssleutel, om ervoor te zorgen dat uw gegevens in deze zeldzame omstandigheden toegankelijk zijn. Zie [richtlijnen voor noodherstel voor gegevens in Data Lake Storage Gen1](data-lake-store-disaster-recovery-guidance.md) voor aanbevolen procedures voor uw planning voor noodherstel. 