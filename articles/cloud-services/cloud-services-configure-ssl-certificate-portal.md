---
title: SSL configureren voor een cloudservice | Microsoft Documenten
description: Meer informatie over het opgeven van een HTTPS-eindpunt voor een webrol en hoe u een SSL-certificaat uploadt om uw toepassing te beveiligen. In deze voorbeelden wordt de Azure-portal gebruikt.
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 05/26/2017
ms.author: tagore
ms.openlocfilehash: 6ddb7001f770a9d8aea38d1a4698e15c167aeaa4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273135"
---
# <a name="configuring-ssl-for-an-application-in-azure"></a>SSL configureren voor een toepassing in Azure

Ssl-versleuteling (Secure Socket Layer) is de meest gebruikte methode voor het beveiligen van gegevens die via internet worden verzonden. In deze veelgebruikte taak wordt besproken hoe u een HTTPS-eindpunt opgeeft voor een webrol en hoe u een SSL-certificaat uploadt om uw toepassing te beveiligen.

> [!NOTE]
> De procedures in deze taak zijn van toepassing op Azure Cloud Services; voor App Services, zie [dit](../app-service/configure-ssl-bindings.md).
>

Deze taak maakt gebruik van een productie-implementatie. Aan het einde van dit onderwerp vindt u informatie over het gebruik van een faseringsimplementatie.

Lees [dit](cloud-services-how-to-create-deploy-portal.md) eerst als u nog geen cloudservice hebt gemaakt.

## <a name="step-1-get-an-ssl-certificate"></a>Stap 1: Een SSL-certificaat aanvragen
Als u SSL voor een toepassing wilt configureren, moet u eerst een SSL-certificaat ontvangen dat is ondertekend door een Certificate Authority (CA), een vertrouwde derde partij die hiervoor certificaten uitgeeft. Als u er nog geen hebt, moet u er een verkrijgen bij een bedrijf dat SSL-certificaten verkoopt.

Het certificaat moet voldoen aan de volgende vereisten voor SSL-certificaten in Azure:

* Het certificaat moet een privésleutel bevatten.
* Het certificaat moet worden gemaakt voor sleuteluitwisseling, exporteerbaar naar een bestand voor persoonlijke informatie-uitwisseling (.pfx).
* De onderwerpnaam van het certificaat moet overeenkomen met het domein dat wordt gebruikt om toegang te krijgen tot de cloudservice. U geen SSL-certificaat verkrijgen van een certificeringsinstantie (CA) voor het cloudapp.net domein. U moet een aangepaste domeinnaam aanschaffen om te gebruiken wanneer u toegang krijgt tot uw service. Wanneer u een certificaat aanvraagt bij een CA, moet de onderwerpnaam van het certificaat overeenkomen met de aangepaste domeinnaam die wordt gebruikt om toegang te krijgen tot uw toepassing. Als uw aangepaste domeinnaam bijvoorbeeld **contoso.com,** zou u een certificaat aanvragen bij uw CA voor ***.contoso.com** of **\.www contoso.com**.
* Het certificaat moet minimaal 2048-bits versleuteling gebruiken.

Voor testdoeleinden u een zelfondertekend certificaat [maken](cloud-services-certs-create.md) en gebruiken. Een zelfondertekend certificaat wordt niet geverifieerd via een CA en kan het cloudapp.net-domein als URL van de website gebruiken. In de volgende taak wordt bijvoorbeeld een zelfondertekend certificaat gebruikt waarin de algemene naam (GN) die in het certificaat wordt **gebruikt, sslexample.cloudapp.net**is.

Vervolgens moet u informatie over het certificaat opnemen in uw servicedefinitie- en serviceconfiguratiebestanden.

<a name="modify"> </a>

## <a name="step-2-modify-the-service-definition-and-configuration-files"></a>Stap 2: De servicedefinitie- en configuratiebestanden wijzigen
Uw toepassing moet zijn geconfigureerd om het certificaat te gebruiken en er moet een HTTPS-eindpunt worden toegevoegd. Als gevolg hiervan moeten de servicedefinitie- en serviceconfiguratiebestanden worden bijgewerkt.

1. Open in uw ontwikkelomgeving het servicedefinitiebestand (CSDEF), voeg een sectie **Certificaten** toe in de sectie **WebRole** en voeg de volgende informatie over het certificaat (en tussenliggende certificaten) op:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="My"
                        permissionLevel="limitedOrElevated" />
            <!-- IMPORTANT! Unless your certificate is either
            self-signed or signed directly by the CA root, you
            must include all the intermediate certificates
            here. You must list them here, even if they are
            not bound to any endpoints. Failing to list any of
            the intermediate certificates may cause hard-to-reproduce
            interoperability problems on some clients.-->
            <Certificate name="CAForSampleCertificate"
                        storeLocation="LocalMachine"
                        storeName="CA"
                        permissionLevel="limitedOrElevated" />
        </Certificates>
    ...
    </WebRole>
    ```

   De sectie **Certificaten** definieert de naam van ons certificaat, de locatie en de naam van de winkel waar het zich bevindt.

   Machtigingen (kenmerk)`permissionLevel` kunnen worden ingesteld op een van de volgende waarden:

   | Machtigingswaarde | Beschrijving |
   | --- | --- |
   | limitedOrElevated |**(Standaard)** Alle rolprocessen hebben toegang tot de privésleutel. |
   | Verhoogde |Alleen verhoogde processen hebben toegang tot de privésleutel. |

2. Voeg in het servicedefinitiebestand een **Element InputEndpoint** toe in de sectie **Eindpunten** om HTTPS in te schakelen:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Endpoints>
            <InputEndpoint name="HttpsIn" protocol="https" port="443"
                certificate="SampleCertificate" />
        </Endpoints>
    ...
    </WebRole>
    ```

3. Voeg in uw servicedefinitiebestand een **element Binding** toe in de sectie **Sites.** Dit element voegt een HTTPS-binding toe om het eindpunt aan uw site toe te wijzen:

   ```xml
    <WebRole name="CertificateTesting" vmsize="Small">
    ...
        <Sites>
            <Site name="Web">
                <Bindings>
                    <Binding name="HttpsIn" endpointName="HttpsIn" />
                </Bindings>
            </Site>
        </Sites>
    ...
    </WebRole>
    ```

   Alle vereiste wijzigingen in het servicedefinitiebestand zijn voltooid; U moet echter nog steeds de certificaatgegevens toevoegen aan het serviceconfiguratiebestand.
4. Voeg in uw serviceconfiguratiebestand (CSCFG) ServiceConfiguration.Cloud.cscfg een **certificatenwaarde** toe met die van uw certificaat. In het volgende codevoorbeeld vindt u details van de sectie **Certificaten,** met uitzondering van de duimafdrukwaarde.

   ```xml
    <Role name="Deployment">
    ...
        <Certificates>
            <Certificate name="SampleCertificate"
                thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff"
                thumbprintAlgorithm="sha1" />
            <Certificate name="CAForSampleCertificate"
                thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc"
                thumbprintAlgorithm="sha1" />
        </Certificates>
    ...
    </Role>
    ```

(In dit voorbeeld wordt **sha1** gebruikt voor het algoritme voor duimafdruk. Geef de juiste waarde op voor het duimafdrukalgoritme van uw certificaat.)

Nu de servicedefinitie- en serviceconfiguratiebestanden zijn bijgewerkt, u uw implementatie verpakken voor het uploaden naar Azure. Als u **cspack**gebruikt, gebruikt u de vlag **/generateConfigurationFile** niet, omdat dat de certificaatgegevens die u zojuist hebt ingevoegd, overschrijft.

## <a name="step-3-upload-a-certificate"></a>Stap 3: Een certificaat uploaden
Maak verbinding met de Azure-portal en...

1. Selecteer uw cloudservice in het gedeelte **Alle resources** van de portal.

    ![Uw cloudservice publiceren](media/cloud-services-configure-ssl-certificate-portal/browse.png)

2. Klik **op Certificaten**.

    ![Klik op het pictogram certificaten](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

3. Klik boven aan het certificaatgebied op **Uploaden.**

    ![Klik op het menu-item Uploaden](media/cloud-services-configure-ssl-certificate-portal/Upload_menu.png)

4. Geef het **bestand**, **wachtwoord**op en klik onder aan het gegevensinvoergebied op **Uploaden.**

## <a name="step-4-connect-to-the-role-instance-by-using-https"></a>Stap 4: Verbinding maken met de rolinstantie met HTTPS
Nu uw implementatie in Azure actief is, u er verbinding mee maken via HTTPS.

1. Klik op de **url van** de site om de webbrowser te openen.

   ![Klik op de site-URL](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2. Wijzig in uw webbrowser de koppeling om **https** te gebruiken in plaats van **http**en ga vervolgens naar de pagina.

   > [!NOTE]
   > Als u een zelfondertekend certificaat gebruikt, ziet u bij het bladeren naar een HTTPS-eindpunt dat is gekoppeld aan het zelfondertekende certificaat mogelijk een certificaatfout in de browser. Als u een certificaat gebruikt dat is ondertekend door een vertrouwde certificeringsinstantie, wordt dit probleem opgelost. in de tussentijd u de fout negeren. (Een andere optie is om het zelfondertekende certificaat toe te voegen aan het certificaatarchief van de vertrouwensautoriteit van de gebruiker.)
   >
   >

   ![Voorvertoning van de site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

   > [!TIP]
   > Als u SSL wilt gebruiken voor een implementatie met tijdelijke bestanden in plaats van een productie-implementatie, moet u eerst de URL bepalen die wordt gebruikt voor de implementatie van de fasering. Zodra uw cloudservice is geïmplementeerd, wordt de URL naar de faseringsomgeving bepaald door de **GUID implementatie-id** in deze indeling:`https://deployment-id.cloudapp.net/`  
   >
   > Maak een certificaat met de algemene naam (CN) gelijk aan de URL op basis van GUID (bijvoorbeeld **328187776e774ceda8fc57609d404462.cloudapp.net).** Gebruik de portal om het certificaat toe te voegen aan uw gefaseerde cloudservice. Voeg vervolgens de certificaatgegevens toe aan uw CSDEF- en CSCFG-bestanden, verpak uw toepassing opnieuw en werk uw gefaseerde implementatie bij om het nieuwe pakket te gebruiken.
   >

## <a name="next-steps"></a>Volgende stappen
* [Algemene configuratie van uw cloudservice.](cloud-services-how-to-configure-portal.md)
* Meer informatie over het [implementeren van een cloudservice](cloud-services-how-to-create-deploy-portal.md).
* Een [aangepaste domeinnaam configureren](cloud-services-custom-domain-name-portal.md).
* [Beheer uw cloudservice.](cloud-services-how-to-manage-portal.md)



