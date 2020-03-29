---
title: 'Azure Databricks: veelgestelde vragen en hulp'
description: Antwoorden op veelgestelde vragen en informatie over probleemoplossing over Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671567"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Veelgestelde vragen over Azure Databricks

In dit artikel worden de belangrijkste vragen weergegeven die u mogelijk hebt met betrekking tot Azure Databricks. Het bevat ook een aantal veelvoorkomende problemen die u zou kunnen hebben tijdens het gebruik van Databricks. Zie [Wat is Azure Databricks voor](what-is-azure-databricks.md)meer informatie. 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Kan ik Azure Key Vault gebruiken om sleutels/geheimen op te slaan die in Azure Databricks kunnen worden gebruikt?
Ja. U Azure Key Vault gebruiken om sleutels/geheimen op te slaan voor gebruik met Azure Databricks. Zie [Azure Key Vault-scopes](/azure/databricks/security/secrets/secret-scopes)voor meer informatie.


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Kan ik Azure Virtual Networks gebruiken met Databricks?
Ja. U een Azure Virtual Network (VNET) gebruiken met Azure Databricks. Zie [Azure Databricks implementeren in uw Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)voor meer informatie.

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Hoe krijg ik toegang tot Azure Data Lake Storage vanuit een notitieblok? 

Volg deze stappen:
1. In Azure Active Directory (Azure AD) een serviceprincipal inrichten en de sleutel registreren.
1. Wijs de benodigde machtigingen toe aan de serviceprincipal in Data Lake Storage.
1. Als u toegang wilt krijgen tot een bestand in Data Lake Storage, gebruikt u de hoofdreferenties van de service in Notitieblok.

Zie [Azure Data Lake Storage gebruiken met Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake)voor meer informatie.

## <a name="fix-common-problems"></a>Veelvoorkomende problemen oplossen

Hier zijn een paar problemen die u zou kunnen tegenkomen met Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Probleem: Dit abonnement is niet geregistreerd om de naamruimte 'Microsoft.Databricks' te gebruiken

#### <a name="error-message"></a>Foutbericht

"Dit abonnement is niet geregistreerd om de naamruimte 'Microsoft.Databricks' te gebruiken. Zie https://aka.ms/rps-not-found voor het registreren van abonnementen. (Code: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Oplossing

1. Ga naar de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Abonnementen,** het abonnement dat u gebruikt en vervolgens **Resourceproviders**. 
1. Selecteer **Register**in de lijst met resourceproviders tegen **Microsoft.Databricks**. U moet de rol van inzender of eigenaar bij het abonnement hebben om de resourceprovider te registreren.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Probleem: uw account {e-mail} heeft niet de eigenaar of bijdrager rol op de Databricks workspace resource in de Azure portal

#### <a name="error-message"></a>Foutbericht

"Uw account {e-mail} heeft geen rol van eigenaar of inzender op de Databricks-werkruimtebron in de Azure-portal. Deze fout kan ook optreden als u een gastgebruiker bent in de tenant. Vraag uw beheerder om u toegang te verlenen of u als gebruiker rechtstreeks toe te voegen aan de Databricks-werkruimte." 

#### <a name="solution"></a>Oplossing

De volgende zijn een paar oplossingen voor dit probleem:

* Om de tenant te initialiseren, moet u zijn aangemeld als een regelmatige gebruiker van de tenant, niet als gastgebruiker. U moet ook een bijdragerol hebben in de resource van de Databricks-werkruimte. U een gebruiker toegang verlenen via het tabblad **Toegangsbeheer (IAM)** in uw Databricks-werkruimte in de Azure-portal.

* Deze fout kan ook optreden als uw e-maildomeinnaam is toegewezen aan meerdere mappen in Azure AD. Als u dit probleem wilt oplossen, maakt u een nieuwe gebruiker in de map die het abonnement met uw Databricks-werkruimte bevat.

    a. Ga in de Azure-portal naar Azure AD. Selecteer **Gebruikers en groepen** > **Voeg een gebruiker toe.**

    b. Voeg een gebruiker `@<tenant_name>.onmicrosoft.com` toe `@<your_domain>` met een e-mail in plaats van e-mail. U deze optie vinden in **Aangepaste domeinen**onder Azure AD in de Azure-portal.
    
    c. Geef deze nieuwe gebruiker de **rol inzender op** de databricks-werkruimtebron.
    
    d. Meld u aan bij de Azure-portal met de nieuwe gebruiker en zoek de werkruimte Databricks.
    
    e. Start de werkruimte Databricks als deze gebruiker.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Probleem: Uw account {e-mail} is niet geregistreerd in Databricks 

#### <a name="solution"></a>Oplossing

Als u de werkruimte niet hebt gemaakt en u als gebruiker wordt toegevoegd, neemt u contact op met de persoon die de werkruimte heeft gemaakt. Laat die persoon u toevoegen met behulp van de Azure Databricks Admin Console. Zie Gebruikers [toevoegen en beheren](/azure/databricks/administration-guide/users-groups/users)voor instructies. Als u de werkruimte hebt gemaakt en u deze fout nog steeds krijgt, probeert u **Initialize Workspace** opnieuw te selecteren in de Azure-portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Probleem: startfout voor de lancering van cloudprovider tijdens het instellen van het cluster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Foutbericht

"Cloud Provider Launch Failure: Er is een fout van de cloudprovider opgetreden tijdens het instellen van het cluster. Zie voor meer informatie de Databricks-gids. Azure-foutcode: PublicIPCountLimitReached. Azure-foutbericht: kan niet meer dan 10 openbare IP-adressen maken voor dit abonnement in deze regio."

#### <a name="background"></a>Achtergrond

Databricks-clusters gebruiken één openbaar IP-adres per knooppunt (inclusief het stuurprogrammaknooppunt). Azure-abonnementen hebben [openbare IP-adreslimieten](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) per regio. Het maken en opschalen van clusteren kan dus mislukken als het aantal openbare IP-adressen dat aan dat abonnement in dat gebied is toegewezen, de limiet overschrijdt. Deze limiet omvat ook openbare IP-adressen die zijn toegewezen voor niet-Databricks-gebruik, zoals aangepaste door de gebruiker gedefinieerde VM's.

Over het algemeen verbruiken clusters alleen openbare IP-adressen terwijl ze actief zijn. Fouten `PublicIPCountLimitReached` kunnen echter nog steeds optreden voor een korte periode, zelfs nadat andere clusters zijn beëindigd. Dit komt omdat Databricks tijdelijk Azure-bronnen in de cache opslaat wanneer een cluster wordt beëindigd. Resourcecache is door het ontwerp, omdat het de latentie van clusteropstart en automatisch schalen in veel voorkomende scenario's aanzienlijk vermindert.

#### <a name="solution"></a>Oplossing

Als uw abonnement al de limiet voor het openbare IP-adres voor een bepaalde regio heeft bereikt, moet u het ene of het andere van het volgende doen.

- Maak nieuwe clusters in een andere Databricks-werkruimte. De andere werkruimte moet zich bevinden in een regio waar u de openbare IP-adreslimiet van uw abonnement niet hebt bereikt.
- [Verzoek om uw ip-adreslimiet voor het publiek te verhogen.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) Kies **Quota** als **probleemtype**en **Netwerken: ARM** als **quotumtype**. Vraag in **Details**een quotumverhoging voor openbaar IP-adres aan. Als uw limiet momenteel 60 is en u een cluster met 100 node wilt maken, vraagt u bijvoorbeeld een limietverhoging aan tot 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Probleem: een tweede type lanceringsfout van een cloudprovider tijdens het instellen van het cluster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Foutbericht

"Cloud Provider Launch Failure: Er is een fout van de cloudprovider opgetreden tijdens het instellen van het cluster. Zie voor meer informatie de Databricks-gids.
Azure-foutcode: Ontbrekende SubscriptionRegistration Azure-foutbericht: het abonnement is niet geregistreerd om naamruimte 'Microsoft.Compute' te gebruiken. Zie https://aka.ms/rps-not-found hoe u abonnementen registreren.

#### <a name="solution"></a>Oplossing

1. Ga naar de [Azure-portal.](https://portal.azure.com)
1. Selecteer **Abonnementen,** het abonnement dat u gebruikt en vervolgens **Resourceproviders**. 
1. Selecteer **Register**in de lijst met resourceproviders tegen **Microsoft.Compute**. U moet de rol van inzender of eigenaar bij het abonnement hebben om de resourceprovider te registreren.

Zie [Resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer gedetailleerde instructies.

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Probleem: Azure Databricks heeft machtigingen nodig om toegang te krijgen tot bronnen in uw organisatie die alleen een beheerder kan verlenen.

#### <a name="background"></a>Achtergrond

Azure Databricks is geïntegreerd met Azure Active Directory. U machtigingen instellen binnen Azure Databricks (bijvoorbeeld op notitieblokken of clusters) door gebruikers op te geven vanuit Azure AD. Om de namen van de gebruikers van uw Azure AD te kunnen weergeven, moet u leestoestemming hebben voor die informatie en toestemming. Als de toestemming nog niet beschikbaar is, ziet u de fout.

#### <a name="solution"></a>Oplossing

Meld u aan als globale beheerder bij de Azure-portal. Ga voor Azure Active Directory naar het tabblad **Gebruikersinstellingen** en controleer of **gebruikers toestemming kunnen geven voor apps die namens hen toegang hebben tot bedrijfsgegevens** is ingesteld op **Ja.**

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: aan de slag met Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Wat is Azure Databricks?](what-is-azure-databricks.md)
