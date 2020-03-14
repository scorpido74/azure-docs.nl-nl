---
title: Zelf studie-terraform-modules in azure testen met behulp van Terratest
description: Informatie over het gebruik van Terratest voor het testen van uw Terraform-modules.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370794"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Zelf studie: terraform-modules testen in azure met behulp van Terratest

> [!NOTE]
> De voorbeeld code in dit artikel werkt niet met versie 0,12 (en hoger).

U kunt Azure Terraform-modules gebruiken om herbruikbare, samenstelbare en testbare onderdelen te maken. Terraform-modules maken gebruik van inkapseling, wat handig is bij het implementeren van infrastructuur terwijl de code wordt uitgevoerd.

Het implementeren van kwaliteitscontrole is belangrijk bij het maken van Terraform-modules. Er is helaas weinig documentatie beschikbaar waarin wordt uitgelegd hoe moduletests en integratietests in Terraform-modules kunnen worden gemaakt. In deze zelfstudie maakt u kennis met een infrastructuur voor testen en best practices die we hebben gebruikt tijdens het ontwikkelen van onze [Azure Terraform-modules](https://registry.terraform.io/browse?provider=azurerm).

We hebben gekeken naar de populairste infrastructuren voor testen en gekozen voor [Terratest](https://github.com/gruntwork-io/terratest) voor het testen van de Terraform-modules. Terratest wordt geïmplementeerd als een Go-bibliotheek. Terratest biedt een verzameling ondersteunende functies en patronen voor algemene testtaken, zoals het maken van HTTP-aanvragen en het gebruik van SSH voor toegang tot een bepaalde virtuele machine. In de volgende lijst worden enkele belangrijke voordelen van het gebruik van Terratest beschreven:

- **Terratest biedt handige helpers om de infrastructuur te controleren.** Deze functie is handig om uw bestaande infrastructuur in een echte omgeving te controleren.
- **De mapstructuur is duidelijk ingedeeld**. Uw testcases worden duidelijk geordend en volgen de [standaard mapstructuur voor Terraform-modules](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Alle testcases worden geschreven in Go.** De meeste ontwikkelaars die gebruikmaken van Terraform zijn Go-ontwikkelaars. Als u een Go-ontwikkelaar bent, hoeft u geen andere programmeertaal te leren om Terratest te gebruiken. Bovendien zijn Go en Terraform de enige afhankelijkheden die u nodig hebt om testcases in Terratest uit te voeren.
- **De infrastructuur kan in hoge mate worden uitgebreid**. Naast Terratest kunt u extra functies te gebruiken, zoals Azure-specifieke functies.

## <a name="prerequisites"></a>Vereisten

Dit praktische artikel is platform-onafhankelijk. U kunt de codevoorbeelden die in dit artikel worden gebruikt, uitvoeren voor Windows, Linux of MacOS. 

Voordat u begint, dient u de volgende software te installeren:

- **Go-programmeertaal**: Terraform-testcases worden geschreven in [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) is een tool voor afhankelijkheidsbeheer voor Go.
- **Azure CLI**: de [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) is een opdrachtregelprogramma dat u kunt gebruiken voor het beheren van Azure-resources. (Terraform biedt ondersteuning voor verificatie bij Azure via een service-principal of [via de Azure CLI](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: het [uitvoerbare bestand mage](https://github.com/magefile/mage/releases) wordt gebruikt om te laten zien hoe het uitvoeren van Terratest-cases kan worden vereenvoudigd. 

## <a name="create-a-static-webpage-module"></a>Een statische webpaginamodule maken

In deze zelfstudie maakt u een Terraform-module waarmee een statische webpagina wordt ingericht door één HTML-bestand te uploaden naar een Azure Storage-blob. Dankzij deze module hebben gebruikers van over de hele wereld toegang tot de webpagina via een URL die door de module wordt geretourneerd.

> [!NOTE]
> Maak onder de locatie [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH) alle bestanden die in deze sectie worden beschreven.

Maak eerst een nieuwe map met de naam `staticwebpage` in uw map `src` van GoPath. De algehele mappenstructuur van deze zelfstudie wordt weergegeven in het volgende voorbeeld. Het gaat in deze sectie met name over de bestanden die zijn gemarkeerd met een sterretje `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

De module voor de statische webpagina accepteert drie items als invoer. Deze worden gedeclareerd in `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Zoals hierboven al vermeld, wordt door deze module een URL geretourneerd die in `./outputs.tf` wordt gedeclareerd:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

De belangrijkste logica van de module bestaat uit het inrichten van vier resources:
- **resourcegroep**: de naam van de resourcegroep is de `website_name`-invoer, gevolgd door `-staging-rg`.
- **opslagaccount**: de naam van het opslagaccount is de `website_name`-invoer, gevolgd door `data001`. Om te voldoen aan de beperkingen die gelden voor de naam van het opslagaccount, worden alle speciale tekens verwijderd en worden alleen kleine letters gebruikt in de naam van het opslagaccount.
- **container met vaste naam**: de container heeft de naam `wwwroot` en wordt in het opslagaccount gemaakt.
- **één HTML-bestand**: het HTML-bestand wordt gelezen vanuit de `html_path`-invoer en naar `wwwroot/index.html` geüpload.

De modulelogica van de statische webpagina wordt geïmplementeerd in `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Eenheidstest

Terratest is ontworpen voor integratietests. Om die reden worden door Terratest echte resources ingericht in een echte omgeving. Soms kunnen integratietesttaken uitzonderlijk groot worden, vooral wanneer u grote hoeveelheden resources hebt die moeten worden ingericht. Een goed voorbeeld is de logica waarmee namen van opslagaccounts worden omgezet, zoals in de vorige sectie beschreven. 

Maar eigenlijk willen we geen resources inrichten. We willen er alleen maar zeker van zijn dat de logica voor de naamomzetting juist is. Dankzij de flexibiliteit van Terratest kunnen we moduletests gebruiken. Moduletests zijn testcases die lokaal worden uitgevoerd (hoewel er wel verbinding met internet nodig is). Moduletestcases voeren `terraform init`- en `terraform plan`-opdrachten uit om de uitvoer van `terraform plan` te parseren en naar de te vergelijken waarden te zoeken.

In de rest van deze sectie wordt beschreven hoe we Terratest gebruiken voor het implementeren van een moduletest om ervoor te zorgen dat de logica voor de naamomzetting van opslagaccounts juist is. We zijn alleen geïnteresseerd in de bestanden die zijn gemarkeerd met een sterretje `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Eerst gebruiken we een leeg HTML-bestand met de naam `./test/fixtures/storage-account-name/empty.html` als tijdelijke aanduiding.

Bestand `./test/fixtures/storage-account-name/main.tf` vormt het kader van de testcase. Het bestand accepteert één `website_name` als invoer en deze is tevens de invoer van de moduletests. De logica is als volgt:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

Het belangrijkste onderdeel is de implementatie van de moduletests in `./test/storage_account_name_unit_test.go`.

Go-ontwikkelaars zullen waarschijnlijk herkennen dat de moduletest overeenkomt met de handtekening van een klassieke Go-testfunctie waarbij een argument van het type `*testing.T` wordt geaccepteerd.

In de hoofdtekst van de moduletest zijn er in totaal vijf cases gedefinieerd in variabele `testCases` (`key` als invoer, en `value` als verwachte uitvoer). Voor elke moduletest voeren we eerst `terraform init` uit met de map test/fixtures (`./test/fixtures/storage-account-name/`) als doel. 

Vervolgens wordt met een `terraform plan`-opdracht met specifieke invoer voor een testcase (bekijk de definitie van `website_name` in `tfOptions`) het resultaat opgeslagen in `./test/fixtures/storage-account-name/terraform.tfplan` (niet vermeld in de algehele mappenstructuur).

Dit resultatenbestand wordt geparseerd naar een voor code leesbare structuur met behulp van de officiële Terraform-planparser.

We kijken nu naar de kenmerken waarin we geïnteresseerd zijn (in dit geval de `name` van het `azurerm_storage_account`) en vergelijken de resultaten met de verwachte uitvoer:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Voor het uitvoeren van de moduletests dient u de volgende stappen in de opdrachtregel uit te voeren:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

Het traditionele Go-testresultaat wordt na circa een minuut geretourneerd.

### <a name="integration-test"></a>Integratietest

In tegenstelling tot moduletests moeten integratietests worden gebruikt voor het inrichten van resources in een echte omgeving voor een end-to-end perspectief. Voor dit soort taken is Terratest erg geschikt. 

Aanbevolen procedures voor Terraform-modules zijn onder meer het installeren van de map `examples`. De map `examples` bevat enkele end-to-end voorbeelden. In plaats van echte gegevens kunt u deze voorbeelden gebruiken als integratietests. In deze sectie richten we ons op de drie bestanden die in de volgende mapstructuur met een sterretje `(*)` zijn gemarkeerd:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Laten we beginnen met de voorbeelden. Er wordt nieuwe map met voorbeelden met de naam `hello-world/` gemaakt in de map `./examples/`. We geven hier een eenvoudige HTML-pagina die moet worden geüpload: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

Terraform-voorbeeld `./examples/hello-world/main.tf` is soortgelijk aan dat uit de moduletest. Er is één belangrijk verschil: in het voorbeeld wordt ook de URL van het geüploade HTML-bestand afgedrukt als een webpagina. Deze heeft de naam `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

We gebruiken Terratest- en klassieke Go-testfuncties opnieuw in het bestand van de integratietest `./test/hello_world_example_test.go`.

In tegenstelling tot moduletests worden met integratietests werkelijke resources in Azure gemaakt. Daarom dient u goed op te letten dat er geen conflicten tussen namen optreden. (Let vooral op enkele wereld wijd unieke namen als namen van opslag accounts.) Daarom is de eerste stap van de test logica het genereren van een wille keurig `websiteName` met behulp van de `UniqueId()` functie van Terratest. Deze functie genereert een willekeurige naam met kleine letters, hoofdletters en cijfers. `tfOptions` maakt alle Terraform-opdrachten die de map `./examples/hello-world/` als doel hebben. Het zorgt er ook voor dat `website_name` wordt ingesteld op de willekeurig gegenereerde `websiteName`.

Vervolgens worden `terraform init`, `terraform apply` en `terraform output` een voor een uitgevoerd. We gebruiken een andere helperfunctie, `HttpGetWithCustomValidation()`, die door Terratest wordt geleverd. De helperfunctie wordt gebruikt om ervoor te zorgen dat HTML wordt geüpload naar de `homepage`-URL van de uitvoer die door `terraform output` wordt geretourneerd. We vergelijken de HTTP GET-statuscode met `200` en zoeken in de HTML-inhoud naar bepaalde trefwoorden. Ten slotte wordt "toegezegd" dat `terraform destroy` wordt uitgevoerd door gebruik te maken van de `defer`-functie van Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Voor het uitvoeren van de integratietests dient u de volgende stappen in de opdrachtregel uit te voeren:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

Het traditionele Go-testresultaat wordt na circa twee minuten geretourneerd. U kunt ook zowel moduletests als integratietests uitvoeren door de volgende opdrachten uit te voeren:

```shell
go fmt
go test
```

Integratietests nemen veel meer tijd in beslag dan moduletests (twee minuten voor één integratiecase tegenover één minuut voor vijf modulecases). U beslist echter zelf of u voor een scenario moduletests of integratietests wilt gebruiken. Meestal gebruiken we liever moduletests voor complexe logica door Terraform HCL-functies te gebruiken. Gewoonlijk gebruiken we integratietests voor het end-to-end perspectief van een gebruiker.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Gebruik mage om het uitvoeren van Terratest-cases te vereenvoudigen 
Voor het uitvoeren van test cases in Azure Cloud Shell moet u verschillende opdrachten uitvoeren in verschillende directory's. Om dit proces efficiënter te maken, introduceren we het build-systeem in het project. In deze sectie gebruiken we voor die taak een Go-buildsysteem, mage.

Het enige wat mage nodig heeft, is een `magefile.go` in de hoofdmap van uw project (gemarkeerd met `(+)` in het volgende voorbeeld):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Hier is een voorbeeld van `./magefile.go`. In dit buildscript, geschreven in Go, hebben we vijf buildstappen geïmplementeerd:
- `Clean`: hierin worden alle gegenereerde en tijdelijke bestanden verwijderd die tijdens testuitvoeringen zijn gegenereerd.
- `Format`: hierin worden `terraform fmt` en `go fmt` uitgevoerd om uw codebasis op te maken.
- `Unit`: hierin worden alle moduletests uitgevoerd (met behulp van de naamgevingsconventie `TestUT_*` voor de functie) onder de map `./test/`.
- `Integration`: deze stap is vergelijkbaar met `Unit`, maar in plaats van moduletests worden integratietests (`TestIT_*`) uitgevoerd.
- `Full`: hierin worden achtereenvolgens `Clean`, `Format`, `Unit` en `Integration` uitgevoerd.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

U kunt de volgende opdrachten gebruiken om een volledig testpakket uit te voeren. De code is soortgelijk aan die in de uitvoerstappen die eerder in deze sectie zijn gebruikt. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

U kunt de laatste opdrachtregel vervangen door aanvullende mage-stappen. U kunt bijvoorbeeld `mage unit` of `mage clean` gebruiken. Het is een goed idee `dep`-opdrachten en `az login` in het mage-bestand in te sluiten. De code wordt hier niet getoond. 

Met mage kunt u ook de stappen delen door het Go-pakketsysteem te gebruiken. In dat geval kunt u mage-bestanden in al uw modules vereenvoudigen door alleen maar te verwijzen naar een gezamenlijke implementatie en door afhankelijkheden te declareren (`mg.Deps()`).

**Optioneel: omgevingsvariabelen van de service-principal instellen op het uitvoeren van acceptatietests**
 
In plaats van `az login` uit te voeren voordat u tests uitvoert, kunt u Azure-verificatie voltooien door omgevingsvariabelen van de service-principal in te stellen. Terraform publiceert [een lijst met namen van omgevingsvariabelen](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Alleen de eerste vier van deze omgevings variabelen zijn vereist.) Terraform publiceert ook gedetailleerde instructies waarin wordt uitgelegd hoe u [de waarde van deze omgevings variabelen kunt verkrijgen](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"] 
> [Terratest github-pagina](https://github.com/gruntwork-io/terratest).