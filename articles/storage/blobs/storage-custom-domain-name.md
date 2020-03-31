---
title: Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt
titleSuffix: Azure Storage
description: Een aangepast domein toewijzen aan een Blob Storage of webeindpunt in een Azure-opslagaccount.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: normesta
ms.reviewer: dineshm
ms.subservice: blobs
ms.openlocfilehash: 9d05677ec47851557594ef47499da653accad141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370471"
---
# <a name="map-a-custom-domain-to-an-azure-blob-storage-endpoint"></a>Een aangepast domein toewijzen aan een Azure Blob Storage-eindpunt

U een aangepast domein toewijzen aan een eindpunt van een blobservice of een [statisch eindpunt van de website.](storage-blob-static-website.md) 

[!INCLUDE [updated-for-az](../../../includes/storage-data-lake-gen2-support.md)]

> [!NOTE] 
> Deze toewijzing werkt alleen voor subdomeinen (bijvoorbeeld: `www.contoso.com`). Als u wilt dat uw webeindpunt beschikbaar is op `contoso.com`het hoofddomein (bijvoorbeeld: ), moet u Azure CDN gebruiken. Zie voor begeleiding het gedeelte [Een aangepast domein toewijzen met HTTPS ingeschakeld](#enable-https) in dit artikel. Omdat je naar dat gedeelte van dit artikel gaat om het hoofddomein van je aangepaste domein in te schakelen, is de stap binnen die sectie voor het inschakelen van HTTPS optioneel. 

<a id="enable-http" />

## <a name="map-a-custom-domain-with-only-http-enabled"></a>Een aangepast domein toewijzen met alleen HTTP ingeschakeld

Deze aanpak is eenvoudiger, maar maakt alleen HTTP-toegang mogelijk. Als het opslagaccount is geconfigureerd om veilige overdracht via HTTPS [te vereisen,](../common/storage-require-secure-transfer.md) moet u HTTPS-toegang voor uw aangepaste domein inschakelen. 

Zie het gedeelte [Een aangepast domein toewijzen met HTTPS ingeschakeld](#enable-https) in dit artikel als u HTTPS-toegang wilt inschakelen. 

<a id="map-a-domain" />

### <a name="map-a-custom-domain"></a>Een aangepast domein toewijzen

> [!IMPORTANT]
> Uw aangepaste domein is kort niet beschikbaar voor gebruikers terwijl u de configuratie voltooit. Als uw domein momenteel een toepassing ondersteunt met een SLA (service-level agreement) die nul downtime vereist, volgt u de stappen in het [gedeelte Een aangepast domein toewijzen met nul downtime](#zero-down-time) van dit artikel om ervoor te zorgen dat gebruikers toegang hebben tot uw domein terwijl de DNS-toewijzing plaatsvindt.

Als u zich geen zorgen maakt dat het domein kort niet beschikbaar is voor uw gebruikers, voert u deze stappen uit.

:heavy_check_mark: Stap 1: Download de hostnaam van uw opslageindpunt.

:heavy_check_mark: Stap 2: Een Canonieke naam (CNAME)-record maken bij uw domeinprovider.

:heavy_check_mark: Stap 3: Registreer het aangepaste domein met Azure. 

:heavy_check_mark: Stap 4: Test uw aangepaste domein.

<a id="endpoint" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Stap 1: De hostnaam van uw opslageindpunt opmaken 

De hostnaam is de URL van het opslageindpunt zonder de protocol-id en de trailing slash. 

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer **Eigenschappen**in het menuvenster onder **Instellingen**.  

3. Kopieer de waarde van het eindpunt van de **primaire blobservice** of het eindpunt van de **primaire statische website** naar een tekstbestand. 

4. Verwijder de protocol-id *(bijvoorbeeld*HTTPS) en de slepende slash uit die tekenreeks. De volgende tabel bevat voorbeelden.

   | Type eindpunt |  endpoint | hostnaam |
   |------------|-----------------|-------------------|
   |blobservice  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statische website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Zet deze waarde opzij voor later.

<a id="create-cname-record" />

#### <a name="step-2-create-a-canonical-name-cname-record-with-your-domain-provider"></a>Stap 2: Een CNAME-record (canonieke naam) maken bij uw domeinprovider

Maak een CNAME-record om naar uw hostnaam te wijzen. Een CNAME-record is een soort DNS-record dat een brondomeinnaam toewijst aan een doeldomeinnaam.

1. Meld u aan bij de website van uw domeinregistrar en ga vervolgens naar de pagina voor het beheren van DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **Domain Name**, **DNS**of Name **Server Management**.

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME,** **Alias**of **Subdomeinen.**

3. Maak een CNAME-record. Geef als onderdeel van die record de volgende items op: 

   - De subdomeinalias `www` zoals `photos`of . Het subdomein is vereist, hoofddomeinen worden niet ondersteund. 
      
   - De hostnaam die u eerder in dit artikel hebt verkregen in [de hostnaam van uw opslageindpunt.](#endpoint) 

<a id="register" />

#### <a name="step-3-register-your-custom-domain-with-azure"></a>Stap 3: Uw aangepaste domein registreren met Azure

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Blob Service**de optie **Aangepast domein**.  

   ![aangepaste domeinoptie](./media/storage-custom-domain-name/custom-domain-button.png "aangepast domein")

   Het **deelvenster Aangepast domein** wordt geopend.

3. Voer in het tekstvak **Domeinnaam** de naam van uw aangepaste domein in, inclusief het subdomein  
   
   Als uw domein bijvoorbeeld *contoso.com* is en uw subdomeinalias *www*is, voert u . `www.contoso.com` Als uw subdomein *foto's*is, voert u . `photos.contoso.com`

4. Als u het aangepaste domein wilt registreren, kiest u de knop **Opslaan.**

   Nadat de CNAME-record is gepropageerd via de DNS (Domain Name Servers) en als uw gebruikers over de juiste machtigingen beschikken, kunnen ze blobgegevens bekijken met behulp van het aangepaste domein.

#### <a name="step-4-test-your-custom-domain"></a>Stap 4: Uw aangepaste domein testen

Als u wilt bevestigen dat uw aangepaste domein is toegewezen aan het eindpunt van uw blobservice, maakt u een blob in een openbare container binnen uw opslagaccount. Vervolgens opent u in een webbrowser de blob met behulp van een URI in de volgende indeling:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Als u bijvoorbeeld toegang wilt krijgen tot een webformulier in de container *myforms* in het aangepaste subdomein *photos.contoso.com* aangepaste domein, u de volgende URI gebruiken:`http://photos.contoso.com/myforms/applicationform.htm`

<a id="zero-down-time" />

### <a name="map-a-custom-domain-with-zero-downtime"></a>Een aangepast domein toewijzen met nul downtime

> [!NOTE]
> Als u zich geen zorgen maakt dat het domein kort niet beschikbaar is voor uw gebruikers, u overwegen de stappen in het [gedeelte Een aangepast domein](#map-a-domain) van dit artikel te volgen. Het is een eenvoudigere aanpak met minder stappen.  

Als uw domein momenteel een toepassing ondersteunt met een SLA (service-level agreement) die nul downtime vereist, volgt u deze stappen om ervoor te zorgen dat gebruikers toegang hebben tot uw domein terwijl de DNS-toewijzing plaatsvindt. 

:heavy_check_mark: Stap 1: Download de hostnaam van uw opslageindpunt.

:heavy_check_mark: Stap 2: Maak een CNAME-record (intermediary canonieke naam) bij uw domeinprovider.

:heavy_check_mark: Stap 3: Registreer het aangepaste domein vooraf met Azure.

:heavy_check_mark: Stap 4: Een CNAME-record maken met uw domeinprovider.

:heavy_check_mark: Stap 5: Test uw aangepaste domein.

<a id="endpoint-2" />

#### <a name="step-1-get-the-host-name-of-your-storage-endpoint"></a>Stap 1: De hostnaam van uw opslageindpunt opmaken 

De hostnaam is de URL van het opslageindpunt zonder de protocol-id en de trailing slash. 

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer **Eigenschappen**in het menuvenster onder **Instellingen**.  

3. Kopieer de waarde van het eindpunt van de **primaire blobservice** of het eindpunt van de **primaire statische website** naar een tekstbestand. 

4. Verwijder de protocol-id *(bijvoorbeeld*HTTPS) en de slepende slash uit die tekenreeks. De volgende tabel bevat voorbeelden.

   | Type eindpunt |  endpoint | hostnaam |
   |------------|-----------------|-------------------|
   |blobservice  | `https://mystorageaccount.blob.core.windows.net/` | `mystorageaccount.blob.core.windows.net` |
   |statische website  | `https://mystorageaccount.z5.web.core.windows.net/` | `mystorageaccount.z5.web.core.windows.net` |
  
   Zet deze waarde opzij voor later.

#### <a name="step-2-create-a-intermediary-canonical-name-cname-record-with-your-domain-provider"></a>Stap 2: Een CNAME-record (intermediary canoniekname) maken bij uw domeinprovider

Maak een tijdelijke CNAME-record om naar uw hostnaam te wijzen. Een CNAME-record is een soort DNS-record dat een brondomeinnaam toewijst aan een doeldomeinnaam.

1. Meld u aan bij de website van uw domeinregistrar en ga vervolgens naar de pagina voor het beheren van DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **Domain Name**, **DNS**of Name **Server Management**.

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME,** **Alias**of **Subdomeinen.**

3. Maak een CNAME-record. Geef als onderdeel van die record de volgende items op: 

   - De subdomeinalias `www` zoals `photos`of . Het subdomein is vereist, hoofddomeinen worden niet ondersteund.

     Voeg `asverify` het subdomein toe aan de alias. Bijvoorbeeld: `asverify.www` of `asverify.photos`.
       
   - De hostnaam die u eerder in dit artikel hebt verkregen in [de hostnaam van uw opslageindpunt.](#endpoint) 

     Voeg het `asverify` subdomein toe aan de hostnaam. Bijvoorbeeld: `asverify.mystorageaccount.blob.core.windows.net`.

4. Als u het aangepaste domein wilt registreren, kiest u de knop **Opslaan.**

   Als de registratie is geslaagd, waarschuwt de portal u dat uw opslagaccount is bijgewerkt. Uw aangepaste domein is geverifieerd door Azure, maar verkeer naar uw domein wordt nog niet doorgestuurd naar uw opslagaccount.

#### <a name="step-3-pre-register-your-custom-domain-with-azure"></a>Stap 3: Uw aangepaste domein vooraf registreren met Azure

Wanneer u uw aangepaste domein vooraf registreert met Azure, staat u Azure toe uw aangepaste domein te herkennen zonder dat u de DNS-record voor het domein hoeft te wijzigen. Op die manier, wanneer u de DNS-record voor het domein wijzigt, wordt deze toegewezen aan het blob-eindpunt zonder downtime.

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Blob Service**de optie **Aangepast domein**.  

   ![aangepaste domeinoptie](./media/storage-custom-domain-name/custom-domain-button.png "aangepast domein")

   Het **deelvenster Aangepast domein** wordt geopend.

3. Voer in het tekstvak **Domeinnaam** de naam van uw aangepaste domein in, inclusief het subdomein  
   
   Als uw domein bijvoorbeeld *contoso.com* is en uw subdomeinalias *www*is, voert u . `www.contoso.com` Als uw subdomein *foto's*is, voert u . `photos.contoso.com`

4. Schakel het selectievakje **Indirecte CNAME-validatie gebruiken** in.

5. Als u het aangepaste domein wilt registreren, kiest u de knop **Opslaan.**
  
   Nadat de CNAME-record is gepropageerd via de DNS (Domain Name Servers) en als uw gebruikers over de juiste machtigingen beschikken, kunnen ze blobgegevens bekijken met behulp van het aangepaste domein.

#### <a name="step-4-create-a-cname-record-with-your-domain-provider"></a>Stap 4: Een CNAME-record maken met uw domeinprovider

Maak een tijdelijke CNAME-record om naar uw hostnaam te wijzen.

1. Meld u aan bij de website van uw domeinregistrar en ga vervolgens naar de pagina voor het beheren van DNS-instelling.

   Mogelijk vindt u de pagina in een sectie met de naam **Domain Name**, **DNS**of Name **Server Management**.

2. Zoek de sectie voor het beheren van CNAME-records. 

   Mogelijk moet u naar een pagina met geavanceerde instellingen gaan en zoeken naar **CNAME,** **Alias**of **Subdomeinen.**

3. Maak een CNAME-record. Geef als onderdeel van die record de volgende items op: 

   - De subdomeinalias `www` zoals `photos`of . Het subdomein is vereist, hoofddomeinen worden niet ondersteund.
      
   - De hostnaam die u eerder in dit artikel hebt verkregen in [de hostnaam van uw opslageindpunt.](#endpoint-2) 

#### <a name="step-5-test-your-custom-domain"></a>Stap 5: Test uw aangepaste domein

Als u wilt bevestigen dat uw aangepaste domein is toegewezen aan het eindpunt van uw blobservice, maakt u een blob in een openbare container binnen uw opslagaccount. Vervolgens opent u in een webbrowser de blob met behulp van een URI in de volgende indeling:`http://<subdomain.customdomain>/<mycontainer>/<myblob>`

Als u bijvoorbeeld toegang wilt krijgen tot een webformulier in de container *myforms* in het aangepaste subdomein *photos.contoso.com* aangepaste domein, u de volgende URI gebruiken:`http://photos.contoso.com/myforms/applicationform.htm`

### <a name="remove-a-custom-domain-mapping"></a>Een aangepaste domeintoewijzing verwijderen

Als u een aangepaste domeintoewijzing wilt verwijderen, u het aangepaste domein uitschrijven. Gebruik een van de volgende procedures.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Ga als volgt te werk om de aangepaste domeininstelling te verwijderen:

1. Ga in de [Azure-portal](https://portal.azure.com)naar uw opslagaccount.

2. Selecteer in het menuvenster onder **Blob Service**de optie **Aangepast domein**.  
   Het **deelvenster Aangepast domein** wordt geopend.

3. De inhoud van het tekstvak met uw aangepaste domeinnaam wissen.

4. Selecteer de knop **Opslaan**.

Nadat het aangepaste domein is verwijderd, ziet u een portalmelding dat uw opslagaccount is bijgewerkt

#### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Als u een aangepaste domeinregistratie wilt verwijderen, gebruikt u de cli-opdracht `--custom-domain` VOOR DE WIJZIGING VAN [AZ-opslagaccountupdate](https://docs.microsoft.com/cli/azure/storage/account) en geeft u vervolgens een lege tekenreeks (`""`) op voor de argumentwaarde.

* Opdrachtindeling:

  ```azurecli
  az storage account update \
      --name <storage-account-name> \
      --resource-group <resource-group-name> \
      --custom-domain ""
  ```

* Opdrachtvoorbeeld:

  ```azurecli
  az storage account update \
      --name mystorageaccount \
      --resource-group myresourcegroup \
      --custom-domain ""
  ```

#### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als u een aangepaste domeinregistratie wilt verwijderen, gebruikt u de PowerShell-cmdlet [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) en geeft u vervolgens een lege tekenreeks (`""`) op voor de `-CustomDomainName` argumentwaarde.

* Opdrachtindeling:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "<resource-group-name>" `
      -AccountName "<storage-account-name>" `
      -CustomDomainName ""
  ```

* Opdrachtvoorbeeld:

  ```powershell
  Set-AzStorageAccount `
      -ResourceGroupName "myresourcegroup" `
      -AccountName "mystorageaccount" `
      -CustomDomainName ""
  ```
---

<a id="enable-https" />

## <a name="map-a-custom-domain-with-https-enabled"></a>Een aangepast domein toewijzen met HTTPS ingeschakeld

Deze aanpak omvat meer stappen, maar het maakt HTTPS-toegang mogelijk. 

Als u geen gebruikers nodig hebt om toegang te krijgen tot uw blob of webinhoud met HTTPS, raadpleegt u het [aangepaste domein toewijzen met alleen het](#enable-http) gedeelte HTTP dat is ingeschakeld in dit artikel. 

Ga als volgt te werk om een aangepast domein in kaart te brengen en HTTPS-toegang in te schakelen:

1. [Azure CDN](../../cdn/cdn-overview.md) inschakelen op uw blob of webeindpunt. 

   Zie [Een Azure-opslagaccount integreren met Azure CDN](../../cdn/cdn-create-a-storage-account-with-cdn.md)voor een eindpunt voor blobopslag. 

   Zie Een statische website [integreren met Azure CDN](static-website-content-delivery-network.md)voor een statisch eindpunt van de website.

2. [Azure CDN-inhoud toewijzen aan een aangepast domein.](../../cdn/cdn-map-content-to-custom-domain.md)

3. [HTTPS inschakelen op een aangepast Azure CDN-domein](../../cdn/cdn-custom-ssl.md).

   > [!NOTE] 
   > Wanneer u uw statische website bijwerkt, moet u inhoud in de cache wissen op de CDN-randservers door het CDN-eindpunt te zuiveren. Zie voor meer informatie [Een Azure CDN-eindpunt leegmaken](../../cdn/cdn-purge-endpoint.md).

4. (Optioneel) Bekijk de volgende richtlijnen:

   * [SAS-tokens (Shared Access Signature) met Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#shared-access-signatures).

   * [HTTP-naar-HTTPS-omleiding met Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

   * [Prijzen en facturering bij het gebruik van Blob Storage met Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-storage-custom-domain-https#http-to-https-redirection).

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over statische websitehosting in Azure Blob-opslag](storage-blob-static-website.md)
