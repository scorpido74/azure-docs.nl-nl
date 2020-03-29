---
title: Verificatie in Azure Data Lake Storage Gen1 met Azure Active Directory | Microsoft Documenten
description: Meer informatie over het verifiëren met Azure Data Lake Storage Gen1 met Azure Active Directory
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f83cf183bee930dd07c707b0eb49125cecd70b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60193591"
---
# <a name="authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Verificatie met Azure Data Lake Storage Gen1 met Azure Active Directory

Azure Data Lake Storage Gen1 gebruikt Azure Active Directory voor verificatie. Voordat u een toepassing maakt die werkt met Data Lake Storage Gen1, moet u bepalen hoe u uw toepassing verifieert met Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Verificatieopties

* Verificatie van eindgebruikers - De **Azure-referenties** van een eindgebruiker worden gebruikt om te verifiëren met Data Lake Storage Gen1. De toepassing die u maakt om te werken met Data Lake Storage Gen1 vraagt om deze gebruikersreferenties. Als gevolg hiervan is dit verificatiemechanisme *interactief* en wordt de toepassing uitgevoerd in de context van de aangemelde gebruiker. Zie [Verificatie van eindgebruikers voor Gegevensmeeropslag Gen1 voor](data-lake-store-end-user-authenticate-using-active-directory.md)meer informatie en instructies.

* **Service-to-service-verificatie** - Gebruik deze optie als u wilt dat een toepassing zichzelf verifieert met Data Lake Storage Gen1. In dergelijke gevallen maakt u een AD-toepassing (Azure Active Directory) en gebruikt u de sleutel van de Azure AD-toepassing om te verifiëren met Data Lake Storage Gen1. Als gevolg hiervan is dit verificatiemechanisme *niet-interactief.* Zie [Service-to-service-verificatie voor Data Lake Storage Gen1 voor](data-lake-store-service-to-service-authenticate-using-active-directory.md)meer informatie en instructies.

In de volgende tabel wordt uitgelegd hoe verificatiemechanismen voor eindgebruikers en service-to-service worden ondersteund voor Data Lake Storage Gen1. Zo lees je de tafel.

* Het ✔* symbool geeft aan dat verificatieoptie wordt ondersteund en wordt koppelingen naar een artikel dat laat zien hoe u de verificatieoptie gebruikt. 
* Het ✔ symbool geeft aan dat de verificatieoptie wordt ondersteund. 
* De lege cellen geven aan dat de verificatieoptie niet wordt ondersteund.


|Gebruik deze verificatieoptie met...                   |.NET         |Java     |PowerShell |Azure-CLI | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Eindgebruiker (zonder MFA**)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(afgeschaft)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Eindgebruiker (met MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Service-to-service (met behulp van clientsleutel)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Service-to-service (met clientcertificaat) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Klik op het <b>✔\* </b> symbool. Het is een link.</i><br>
<i>** MFA staat voor multi-factor authenticatie</i>

Zie [Verificatiescenario's voor Azure Active Directory](../active-directory/develop/authentication-scenarios.md) voor meer informatie over het gebruik van Azure Active Directory voor verificatie.

## <a name="next-steps"></a>Volgende stappen

* [Verificatie van de eindgebruiker](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Verificatie van service-tot-service](data-lake-store-service-to-service-authenticate-using-active-directory.md)


