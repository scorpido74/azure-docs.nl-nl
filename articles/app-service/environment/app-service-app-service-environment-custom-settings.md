---
title: Aangepaste instellingen configureren
description: Instellingen configureren die van toepassing zijn op de hele Azure App Service omgeving. Meer informatie over hoe u dit doet met Azure Resource Manager sjablonen.
author: stefsch
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: stefsch
ms.custom: mvc, seodec18
ms.openlocfilehash: 25393007a3cc878737ea5927cb65bcf7ef945313
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80057568"
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Aangepaste configuratie-instellingen voor App Service Environment-omgevingen
## <a name="overview"></a>Overzicht
Omdat App Service Environment-omgevingen (ASE's) per klant geïsoleerd zijn, zijn er bepaalde configuratie-instellingen die uitsluitend op App Service Environment-omgevingen kunnen worden toegepast. In dit artikel vindt u een beschrijving van de specifieke aanpassingen die beschikbaar zijn voor App Service-omgevingen.

Raadpleeg [Een App Service Environment-omgeving maken](app-service-web-how-to-create-an-app-service-environment.md) als u nog geen App Service Environment-omgeving hebt.

U kunt App Service Environment-aanpassingen opslaan met behulp van een matrix in het nieuwe kenmerk **clusterSettings**. Dit kenmerk is te vinden in de woordenlijst 'Eigenschappen' van de Azure Resource Manager-entiteit *hostingEnvironments*.

Het volgende verkorte Resource Manager-sjablooncodefragment bevat het kenmerk **clusterSettings**:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

Het kenmerk **clusterSettings** kan worden opgenomen in een Resource Manager-sjabloon om de App Service Environment-omgeving bij te werken.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Een App Service Environment-omgeving bijwerken met behulp van Azure Resource Explorer
U kunt de App Service Environment-omgeving ook bijwerken met behulp van [Azure Resource Explorer](https://resources.azure.com).  

1. Ga in resource Explorer naar het knoop punt voor de app service Environment (**abonnementen** > **resourceGroups** > **-providers** > **micro soft. Web** > **hostingEnvironments**). Klik vervolgens op de specifieke App Service Environment-omgeving die u wilt bijwerken.
2. Klik in het rechterdeelvenster op **Read/Write** (Lezen/Schrijven) in de bovenste werkbalk om interactieve bewerkingen in Resource Explorer toe te staan.  
3. Klik op de blauwe knop **Edit** (Bewerken) zodat de Resource Manager-sjabloon kan worden bewerkt.
4. Schuif omlaag naar het einde van het rechterdeelvenster. Het kenmerk **clusterSettings** staat helemaal onderaan, waar u de waarde ervan kunt invoeren of bijwerken.
5. Typ (of kopieer en plak) de matrix van de gewenste configuratiewaarden in het kenmerk **clusterSettings**.  
6. Klik op de groene knop **PUT** bovenin het rechterdeelvenster om de wijziging door te voeren in de App Service Environment-omgeving.

Als u de wijziging hebt ingediend, duurt het ongeveer 30 minuten maal het aantal front-ends in de App Service Environment-omgeving om de wijziging door te voeren.
In een App Service Environment-omgeving met vier front-ends duurt het ongeveer twee uur voordat de configuratie is bijgewerkt. Tijdens het doorvoeren van de configuratiewijziging kunt u geen andere schaalbewerkingen of configuratiewijzigingen uitvoeren in de App Service-omgeving.

## <a name="enable-internal-encryption"></a>Interne versleuteling inschakelen

De App Service Environment fungeert als een zwart kader systeem waarin u de interne onderdelen of de communicatie binnen het systeem niet kunt zien. Voor een hogere door Voer is versleuteling niet standaard ingeschakeld tussen interne onderdelen. Het systeem is veilig omdat het verkeer volledig niet toegankelijk is om te worden bewaakt of geopend. Als u een nalevings vereiste hebt waarbij het gegevenspad volledig moet worden versleuteld van het end-to-end, is het mogelijk om dit in te scha kelen met een clusterSetting.  

        "clusterSettings": [
            {
                "name": "InternalEncryption",
                "value": "1"
            }
        ],
 
Nadat de InternalEncryption clusterSetting is ingeschakeld, kan dit gevolgen hebben voor de prestaties van uw systeem. Wanneer u de wijziging aanbrengt om InternalEncryption in te scha kelen, heeft uw ASE een instabiele status totdat de wijziging volledig is door gegeven. Het volt ooien van de wijziging kan enkele uren duren, afhankelijk van het aantal instanties dat u in uw ASE hebt. We raden u ten zeerste aan dit niet in te scha kelen op een ASE terwijl deze in gebruik is. Als u dit wilt inschakelen op een actief gebruikte ASE, wordt u ten zeerste aangeraden om verkeer door te sturen naar een back-upomgeving totdat de bewerking is voltooid. 

## <a name="disable-tls-10-and-tls-11"></a>TLS 1.0 en TLS 1.1 uitschakelen

Als u TLS-instellingen voor afzonderlijke apps wilt beheren, kunt u gebruikmaken van de richtlijnen in de documentatie voor [TLS-instellingen afdwingen](../configure-ssl-bindings.md#enforce-tls-versions). 

Als u alle inkomende TLS 1.0- en 1.1 TLS-verkeer wilt uitschakelen voor alle apps in een ASE, kunt u de volgende **clusterSettings**-vermelding instellen:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

De naam van de instelling geeft 1.0 aan, maar wanneer deze vermelding is geconfigureerd, wordt zowel TLS 1.0 als 1.1 TLS uitgeschakeld.

## <a name="change-tls-cipher-suite-order"></a>Volgorde van TLS-suite met coderingsmethoden wijzigen
Een andere vraag van klanten is of ze de lijst met door hun server onderhandelde codes kunnen wijzigen. Dat kan door **clusterSettings** als volgt te wijzigen. De lijst met beschikbare suites met coderingsmethoden kan worden opgehaald uit [dit MSDN-artikel](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Als er onjuiste waarden voor de suite met coderingsmethoden worden ingesteld en deze niet worden begrepen in SChannel, is er wellicht geen TLS-communicatie met de server meer mogelijk. In dat geval moet u de vermelding *FrontEndSSLCipherSuiteOrder* uit **clusterSettings** verwijderen en de bijgewerkte Resource Manager-sjabloon opnieuw indienen om de standaardinstellingen voor de suite met coderingsmethoden terug te zetten.  Gebruik deze functionaliteit voorzichtig.
> 
> 

## <a name="get-started"></a>Aan de slag
De website met Azure Quick Start Resource Manager-sjablonen bevat een sjabloon met de basisdefinitie voor het [maken van een App Service Environment-omgeving](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
