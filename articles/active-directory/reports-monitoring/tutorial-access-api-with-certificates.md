---
title: Zelfstudie voor AD-rapportage-API met certificaten | Microsoft Documenten
description: In deze zelfstudie wordt uitgelegd hoe u de Azure AD Reporting API met certificaatreferenties gebruiken om gegevens van mappen te verkrijgen zonder tussenkomst van de gebruiker.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2808c8431a6b98b162920fb58a6e2ac0498d2055
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081707"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Zelfstudie: Gegevens verzamelen met de Azure Active Directory-rapportage-API met certificaten

De [Azure Active Directory (Azure AD) rapportage-API's](concept-reporting-api.md) bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U kunt deze API's vanuit een groot aantal computertalen en hulpprogramma's aanroepen. Als u zonder tussenkomst van de gebruiker toegang wilt krijgen tot de Azure AD Reporting API, moet u uw toegang tot het gebruik van certificaten configureren.

In deze zelfstudie leert u hoe u een testcertificaat gebruikt om toegang te krijgen tot de MS Graph API voor rapportage. We raden het gebruik van testcertificaten niet aan in een productieomgeving. 

## <a name="prerequisites"></a>Vereisten

1. Als u aanmeldingsgegevens wilt openen, controleert u of u een Azure Active Directory-tenant hebt met een Premium-licentie (P1/P2). Zie [Aan de slag met Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) om uw versie van Azure Active Directory te upgraden. Als er vóór de upgrade nog geen activiteitgegevens waren, duurt het na het upgraden naar een premium-licentie enkele dagen voordat er gegevens worden weergegeven in de rapporten. 

2. Maak of schakel over naar een gebruikersaccount in de **globale beheerder,** **beveiligingsbeheerder,** **beveiligingslezer** of **rapportlezerrol** voor de tenant. 

3. Voer de [vereisten in om toegang te krijgen tot de Azure Active Directory-rapportage-API](howto-configure-prerequisites-for-reporting-api.md). 

4. Azure [AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md)downloaden en installeren .

5. [MsCloudIdUtils installeren](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Deze module biedt verschillende cmdlets, waaronder:
    - De ADAL-bibliotheken die nodig zijn voor authenticatie
    - Toegangstokens van gebruiker, toepassingssleutels en certificaten met behulp van ADAL
    - Afhandeling van pagina's met zoekresultaten door Graph API

6. Als het de eerste keer is dat u de module **Install-MSCloudIdUtilsModule**gebruikt, importeert u deze anders met de opdracht **PowerShell importeren.** Uw sessie moet er hetzelfde uitzien als dit scherm: ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Gebruik de **PowerShell-opdrachtopdracht nieuw-zelfondertekendcertificaat** om een testcertificaat te maken.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Gebruik de opdracht **opdracht Export-certificaat** om het te exporteren naar een certificaatbestand.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten

1. Navigeer naar de [Azure-portal,](https://portal.azure.com)selecteer **Azure Active Directory,** selecteer **vervolgens App-registraties** en kies uw toepassing in de lijst. 

2. Selecteer **Certificaten & geheimen** onder Sectie **Beheren** op het blad voor toepassingsregistratie en selecteer **Certificaat uploaden**.

3. Selecteer het certificaatbestand uit de vorige stap en selecteer **Toevoegen**. 

4. Let op de applicatie-id en de duimafdruk van het certificaat dat u zojuist bij uw aanvraag hebt geregistreerd. Ga naar **Certificaten & geheimen** onder Sectie **Beheren** om de duimafdruk te vinden op uw toepassingspagina in de portal. De duimafdruk bevindt zich onder de lijst **Certificaten.**

5. Open het toepassingsmanifest in de inline manifesteditor en controleer of de eigenschap *keyCredentials* wordt bijgewerkt met uw nieuwe certificaatgegevens zoals hieronder wordt weergegeven - 

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
6. Nu u een toegangstoken voor de MS Graph API krijgen met dit certificaat. Gebruik de **CMdlet Get-MSCloudIdMSGraphAccessTokenFromCert** van de MSCloudIdUtils PowerShell-module, waarbij u de toepassings-id en de duimafdruk indie u bij de vorige stap hebt verkregen. 

   ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Gebruik het toegangstoken in uw PowerShell-script om de Graph-API op te vragen. Gebruik de cmdlet **Invoke-MSCloudIdMSGraphQuery** uit de MSCloudIDUtils om de aanmeldingen en directoryAudits-eindpunt op te sommen. Deze cmdlet verwerkt resultaten met meerdere pagina's en stuurt deze resultaten naar de PowerShell-pijplijn.

8. Query the directoryAudits eindpunt om de controlelogboeken op te halen. 
   ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Vraag het eindpunt van de aanmeldingen op om de aanmeldingslogboeken op te halen.
    ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

10. U er nu voor kiezen om deze gegevens naar een CSV te exporteren en op te slaan in een SIEM-systeem. U kunt uw script ook verpakken in een geplande taak om periodiek gegevens van Azure AD op te halen uit uw tenant zonder dat u toepassingssleutels hoeft op te slaan in de broncode. 

## <a name="next-steps"></a>Volgende stappen

* [Een eerste indruk van de rapportage-API 's krijgen](concept-reporting-api.md)
* [Api-verwijzing voor controle](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-verwijzing naar voor aanmeldingsactiviteitsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
