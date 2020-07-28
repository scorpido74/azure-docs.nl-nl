---
title: Zelf studie voor AD Reporting API met certificaten | Microsoft Docs
description: In deze zelf studie wordt uitgelegd hoe u de rapportage-API van Azure AD gebruikt met certificaat referenties om gegevens op te halen uit directory's zonder tussen komst van de gebruiker.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 4f27385cc33c6c289718c3143d03e24f0454a9f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608005"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Zelf studie: gegevens ophalen met behulp van de API voor Azure Active Directory rapportage met certificaten

De [Azure Active Directory (Azure AD) rapportage-API's](concept-reporting-api.md) bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen. Als u toegang wilt krijgen tot de rapportage-API van Azure AD zonder tussen komst van de gebruiker, moet u uw toegang configureren voor het gebruik van certificaten.

In deze zelf studie leert u hoe u een test certificaat kunt gebruiken om toegang te krijgen tot de MS-Graph API voor rapportage. Het gebruik van test certificaten in een productie omgeving wordt niet aanbevolen. 

## <a name="prerequisites"></a>Vereisten

1. Zorg ervoor dat u over een Azure Active Directory Tenant met een Premium-licentie (P1/P2) beschikt om toegang tot aanmeldings gegevens te krijgen. Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. Als er vóór de upgrade nog geen activiteitgegevens waren, duurt het na het upgraden naar een premium-licentie enkele dagen voordat er gegevens worden weergegeven in de rapporten. 

2. Maak of schakel over naar een gebruikers account in de rol **globale beheerder**, **beveiligings beheerder**, **beveiligings lezer** of **rapport lezer** voor de Tenant. 

3. Voltooi de [vereisten voor toegang tot de API voor Azure Active Directory rapportage](howto-configure-prerequisites-for-reporting-api.md). 

4. Down load en Installeer [Azure AD Power shell v2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installeer [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Deze module biedt verschillende cmdlets, waaronder:
    - De ADAL-bibliotheken die nodig zijn voor authenticatie
    - Toegangstokens van gebruiker, toepassingssleutels en certificaten met behulp van ADAL
    - Afhandeling van pagina's met zoekresultaten door Graph API

6. Als dit de eerste keer is dat u de module **install-MSCloudIdUtilsModule**uitvoert, kunt u deze ook importeren met de Power shell **-opdracht import-module** . Uw sessie moet er ongeveer als volgt uitzien: ![ Windows Power shell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Gebruik de **New-SelfSignedCertificate** Power shell-commandlet om een test certificaat te maken.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Gebruik de cmdlet **export-Certificate** om deze te exporteren naar een certificaat bestand.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten

1. Ga naar de [Azure Portal](https://portal.azure.com), selecteer **Azure Active Directory**, selecteer **app-registraties** en kies uw toepassing in de lijst. 

2. Selecteer **certificaten & geheimen** onder sectie **beheren** op de Blade toepassings registratie en selecteer **certificaat uploaden**.

3. Selecteer het certificaat bestand in de vorige stap en selecteer **toevoegen**. 

4. Noteer de toepassings-ID en de vinger afdruk van het certificaat dat u zojuist hebt geregistreerd bij uw toepassing. Als u de vinger afdruk wilt vinden, gaat u op de pagina toepassing in de portal naar **certificaten & geheimen** onder sectie **beheren** . De vinger afdruk vindt u onder de lijst **certificaten** .

5. Open het manifest van de toepassing in de editor van inline manifesten en controleer of *de eigenschap voor de gegevens van* de computer is bijgewerkt met de nieuwe certificaat informatie, zoals hieronder wordt weer gegeven. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. U kunt nu een toegangs token verkrijgen voor de MS-Graph API met behulp van dit certificaat. Gebruik de cmdlet **Get-MSCloudIdMSGraphAccessTokenFromCert** van de MSCloudIdUtils Power shell-module, waarbij u de toepassings-id en de vinger afdruk doorgeeft die u hebt verkregen uit de vorige stap. 

   ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Gebruik het toegangs token in uw Power shell-script om de Graph API op te vragen. Gebruik de cmdlet **invoke-MSCloudIdMSGraphQuery** van de MSCloudIDUtils om het eind punt aanmeldingen en directoryAudits te inventariseren. Met deze cmdlet worden resultaten met meerdere pagina's verwerkt en worden deze resultaten verzonden naar de Power shell-pijp lijn.

8. Zoek het directoryAudits-eind punt op om de audit logboeken op te halen. 
   ![Azure-portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Zoek het aanmeldingen-eind punt op om de aanmeld logboeken op te halen.
    ![Azure-portal](./media/tutorial-access-api-with-certificates/query-signins.png)

10. U kunt er nu voor kiezen om deze gegevens te exporteren naar een CSV en op te slaan in een SIEM-systeem. U kunt uw script ook verpakken in een geplande taak om periodiek gegevens van Azure AD op te halen uit uw tenant zonder dat u toepassingssleutels hoeft op te slaan in de broncode. 

## <a name="next-steps"></a>Volgende stappen

* [Een eerste indruk van de rapportage-API 's krijgen](concept-reporting-api.md)
* [Controle-API-verwijzing](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-naslag informatie voor aanmeld activiteiten](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
