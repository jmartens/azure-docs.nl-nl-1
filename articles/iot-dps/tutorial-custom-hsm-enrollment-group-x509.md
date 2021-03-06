---
title: 'Zelfstudie: X.509-apparaten inrichten voor Azure IoT Hub met behulp van een aangepaste HSM (Hardware Security Module)'
description: In deze zelfstudie worden inschrijvingsgroepen gebruikt. In deze zelfstudie leert u hoe u X.509-apparaten kunt inrichten met behulp van een aangepaste HSM (Hardware Security Module) en de SDK voor C-apparaten voor Azure IoT Hub Device Provisioning Service (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 11/18/2020
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6845923d65b5fbe5a9f010474330ce2bbed948e1
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780090"
---
# <a name="tutorial-provision-multiple-x509-devices-using-enrollment-groups"></a>Zelfstudie: X.509-apparaten inrichten met behulp van inschrijvingsgroepen

In deze zelfstudie leert u hoe u groepen IoT-apparaten inricht die gebruikmaken van X.509-certificaten voor verificatie. Voorbeeldcode van de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) wordt gebruikt om uw ontwikkelcomputer in te richten als een IoT-apparaat. 

Azure IoT Device Provisioning Service ondersteunt twee typen inschrijvingen:

* [Inschrijvingsgroepen](concepts-service.md#enrollment-group): Wordt gebruikt om meerdere gerelateerde apparaten in te schrijven.
* [Afzonderlijke inschrijvingen](concepts-service.md#individual-enrollment): Wordt gebruikt om één apparaat in te schrijven.

Deze zelfstudie is vergelijkbaar met de vorige zelfstudies waarin wordt uitgelegd hoe u inschrijvingsgroepen kunt gebruiken om apparaten in te richten. In deze zelfstudie worden echter X.509-certificaten gebruikt in plaats van symmetrische sleutels. Bekijk de vorige zelfstudies in deze sectie voor een eenvoudige benadering met behulp van [symmetrische sleutels](./concepts-symmetric-key-attestation.md).

In deze zelfstudie wordt het [aangepaste HSM-voorbeeld](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client/samples/custom_hsm_example) gedemonstreerd dat een stub-implementatie biedt voor communicatie met beveiligde opslag op basis van hardware. Een [HSM (Hardware Security Module)](./concepts-service.md#hardware-security-module) wordt gebruikt voor beveiligde, op hardware gebaseerde opslag van apparaatgeheimen. Een HSM kan worden gebruikt met symmetrische sleutels, X.509-certificaten of TPM-attestation om beveiligde opslag voor geheimen te bieden. Hardwarematige opslag van geheimen is niet vereist, maar wordt nadrukkelijk aanbevolen om gevoelige informatie te beschermen, zoals de persoonlijke sleutel van uw apparaat.

Raadpleeg het overzicht [Inrichten](about-iot-dps.md#provisioning-process) als u niet bekend bent met het proces van automatisch inrichten. Controleer ook of u de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](quick-setup-auto-provision.md) hebt voltooid voordat u verdergaat met deze zelfstudie. 


In deze zelfstudie voltooit u de volgende doelstellingen:

> [!div class="checklist"]
> * Een certificaatketen van vertrouwen om een groep apparaten in te delen met X.509-certificaten.
> * Een volledige bewijs van bezit met een handtekeningcertificaat dat wordt gebruikt in combinatie met de certificaatketen.
> * Een nieuwe groepsinschrijving die gebruikmaakt van de certificaatketen
> * Instellen van de ontwikkelomgeving voor het inrichten van een apparaat met behulp van code van de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c)
> * Inrichten van een apparaat met behulp van de certificaatketen met de aangepaste voorbeeld-HSM (Hardware Security Module) in de SDK.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

De volgende vereisten gelden voor een ontwikkelomgeving in Windows. Voor Linux of macOS raadpleegt u het desbetreffende gedeelte in [Uw ontwikkelomgeving voorbereiden](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) in de SDK-documentatie.

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2019 met de workload [Desktopontwikkeling met C++](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development) ingeschakeld. Visual Studio 2015 en Visual Studio 2017 worden ook ondersteund.

* Meest recente versie van [Git](https://git-scm.com/download/) geïnstalleerd.

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>De ontwikkelomgeving voorbereiden voor de Azure IoT C-SDK

In deze sectie bereidt u een ontwikkelomgeving voor die wordt gebruikt om de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te bouwen. De SDK bevat voorbeeldcode en hulpprogramma's die worden gebruikt door X.509-apparaten die met DPS worden ingericht.

1. Download het [CMake-buildsysteem](https://cmake.org/download/).

    Het is belangrijk dat de vereisten voor Visual Studio met ([Visual Studio](https://visualstudio.microsoft.com/vs/) en de workload [Desktopontwikkeling met C++](https://docs.microsoft.com/cpp/ide/using-the-visual-studio-ide-for-cpp-desktop-development)) op uw computer zijn geïnstalleerd **voordat** de `CMake`-installatie wordt gestart. Zodra aan de vereisten is voldaan en de download is geverifieerd, installeert u het CMake-bouwsysteem.

2. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de Azure IoT C-SDK.

3. Open een opdrachtprompt of Git Bash-shell. Voer de volgende opdrachten uit om de nieuwste release van de GitHub-opslagplaats van de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te klonen. Gebruik de tag die u in de vorige stap hebt gevonden als waarde voor de parameter `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

4. Maak de submap `cmake` in de hoofdmap van de Git-opslagplaats en navigeer naar die map. 

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. De `cmake`-map die u hebt gemaakt, bevat het aangepaste HSM-voorbeeld en de voorbeeldcode voor het inrichten van het apparaat waarin gebruik wordt gemaakt van de aangepaste HSM om X.509-verificatie uit te kunnen voeren. 

    Voer de volgende opdracht in de `cmake`-map uit om een versie van de SDK te bouwen die specifiek is voor uw ontwikkelplatform. De build bevat een verwijzing naar het aangepaste HSM-voorbeeld. 

    Wanneer u het pad opgeeft dat met `-Dhsm_custom_lib` hieronder wordt gebruikt, gebruikt u het pad dat relatief is ten opzichte van de `cmake`-map die u eerder hebt gemaakt. Het relatieve pad dat hieronder wordt weergegeven, is slechts een voorbeeld.

    ```cmd
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    ```

    Als `cmake` uw C++-compiler niet kan vinden, kunnen er fouten in de build optreden tijdens het uitvoeren van de bovenstaande opdracht. Als dit gebeurt, voert u deze opdracht uit bij de [Visual Studio-opdrachtprompt](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

    Zodra de build werkt, wordt er een Visual Studio-oplossing gegenereerd in uw `cmake`-map. De laatste paar uitvoerregels zijn vergelijkbaar met de volgende uitvoer:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=/d/azure-iot-sdk-c/cmake/provisioning_client/samples/custom_hsm_example/Debug/custom_hsm_example.lib ..
    -- Building for: Visual Studio 16 2019
    -- The C compiler identification is MSVC 19.23.28107.0
    -- The CXX compiler identification is MSVC 19.23.28107.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: D:/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-x509-certificate-chain"></a>Een X.509-certificaatketen maken

In deze sectie genereert u een X.509-certificaatketen van drie certificaten voor het testen in deze zelfstudie. De certificaten hebben de volgende hiërarchie.

![Certificaatketen van apparaat in zelfstudie](./media/tutorial-custom-hsm-enrollment-group-x509/example-device-cert-chain.png#lightbox)

[Basiscertificaat:](concepts-x509-attestation.md#root-certificate): U voltooit het [bewijs van bezit](how-to-verify-certificates.md) om het basiscertificaat te verifiëren. Met deze verificatie kan DPS het certificaat vertrouwen en certificaten verifiëren die ermee zijn ondertekend.

[Tussenliggend certificaat](concepts-x509-attestation.md#intermediate-certificate): Het is gebruikelijk dat tussenliggende certificaten worden gebruikt om apparaten logisch te groeperen op basis van productlijnen, bedrijfsafdelingen of andere criteria. In deze zelfstudie wordt een certificaatketen gebruikt die uit één tussenliggend certificaat bestaat. Het tussenliggende certificaat wordt ondertekend door het basiscertificaat. Dit certificaat wordt ook gebruikt voor de inschrijvingsgroep die in DPS is gemaakt om een groep apparaten logisch te groeperen. Met deze configuratie kunt u een hele groep apparaten beheren waarvan de certificaten zijn ondertekend door hetzelfde tussenliggende certificaat. U kunt inschrijvingsgroepen maken om een groep apparaten in of uit te schakelen. Zie [Een tussenliggend of basis-X.509-CA-certificaat weigeren met behulp van een inschrijvingsgroep](how-to-revoke-device-access-portal.md#disallow-an-x509-intermediate-or-root-ca-certificate-by-using-an-enrollment-group) voor meer informatie over het uitschakelen van een groep apparaten

[Apparaatcertificaat](concepts-x509-attestation.md#end-entity-leaf-certificate): Het apparaat(leaf)certificaat wordt ondertekend door het tussenliggende certificaat en samen met de persoonlijke sleutel opgeslagen op het apparaat. Bij het inrichten presenteert het apparaat dit certificaat en de persoonlijke sleutel samen met de certificaatketen. 

Het certificaat maken:

1. Open een Git Bash-opdrachtprompt. Voer stap 1 en 2 uit met behulp van de Bash-shellinstructies die zich bevinden in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md#managing-test-ca-certificates-for-samples-and-tutorials) (Test-CA-certificaten beheren voor voorbeelden en zelfstudies)

    Met deze stap maakt u een werkmap voor de certificaatscripts en wordt met behulp van openssl het basis- en tussenliggende voorbeeldcertificaat voor de certificaatketen gegenereerd. 

    In de uitvoer ziet u de locatie van het zelfondertekende basiscertificaat. Dit certificaat doorloopt [bewijs van het bezit](how-to-verify-certificates.md) om het eigendom later te verifiëren.

    ```output
    Creating the Root CA Certificate
    CA Root Certificate Generated At:
    ---------------------------------
        ./certs/azure-iot-test-only.root.ca.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number:
                fc:cc:6b:ab:3b:9a:3e:fe
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:30 2020 GMT
                Not After : Nov 22 21:30:30 2020 GMT
            Subject: CN=Azure IoT Hub CA Cert Test Only
    ```        

    In de uitvoer ziet u de locatie van het tussenliggende certificaat dat wordt ondertekend/uitgegeven door het basiscertificaat. Dit certificaat wordt gebruikt in combinatie met de inschrijvingsgroep die u later gaat maken.

    ```output
    Intermediate CA Certificate Generated At:
    -----------------------------------------
        ./certs/azure-iot-test-only.intermediate.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 1 (0x1)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub CA Cert Test Only
            Validity
                Not Before: Oct 23 21:30:33 2020 GMT
                Not After : Nov 22 21:30:33 2020 GMT
            Subject: CN=Azure IoT Hub Intermediate Cert Test Only
    ```    

2. Voer vervolgens de volgende opdracht uit om een nieuw apparaat-/leafcertificaat te maken met een onderwerpnaam die u als parameter opgeeft. Gebruik de voorbeeldnaam van het onderwerp dat aan deze zelfstudie is gegeven: `custom-hsm-device-01`. Deze onderwerpnaam is de apparaat-id voor uw IoT-apparaat. 

    > [!WARNING]
    > Gebruik geen spaties in de onderwerpnaam. Deze onderwerpnaam is de apparaat-id voor het IoT-apparaat dat wordt ingericht. Het moet de regels voor een apparaat-id volgen. Zie [Eigenschappen van apparaat-id](../iot-hub/iot-hub-devguide-identity-registry.md#device-identity-properties) voor meer informatie.

    ```cmd
    ./certGen.sh create_device_certificate_from_intermediate "custom-hsm-device-01"
    ```

    De volgende uitvoer toont de locatie van het nieuwe apparaatcertificaat. Het apparaatcertificaat wordt ondertekend (uitgegeven) door het tussenliggende certificaat.

    ```output
    -----------------------------------
    ./certs/new-device.cert.pem: OK
    Leaf Device Certificate Generated At:
    ----------------------------------------
        ./certs/new-device.cert.pem
    
    Certificate:
        Data:
            Version: 3 (0x2)
            Serial Number: 9 (0x9)
        Signature Algorithm: sha256WithRSAEncryption
            Issuer: CN=Azure IoT Hub Intermediate Cert Test Only
            Validity
                Not Before: Nov 10 09:20:33 2020 GMT
                Not After : Dec 10 09:20:33 2020 GMT
            Subject: CN=custom-hsm-device-01
    ```    
    
3. Voer de volgende opdracht uit om een PEM-bestand met een volledige certificaatketen te maken dat het nieuwe apparaatcertificaat bevat.

    ```Bash
    cd ./certs && cat new-device.cert.pem azure-iot-test-only.intermediate.cert.pem azure-iot-test-only.root.ca.cert.pem > new-device-full-chain.cert.pem
    ```

    Gebruik een teksteditor en open het certificaatketenbestand, *./certs/new-device-full-chain.cert.pem*. De tekst van de certificaatketen bevat de volledige keten van alle drie de certificaten. Verderop in deze zelfstudie gebruikt u deze tekst als de certificaatketen met de aangepaste HSM-code.

    De tekst van de volledige keten heeft de volgende indeling:
 
    ```output 
    -----BEGIN CERTIFICATE-----
        <Text for the device certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the intermediate certificate includes public key>
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
        <Text for the root certificate includes public key>
    -----END CERTIFICATE-----
    ```

5. De persoonlijke sleutel voor het nieuwe apparaatcertificaat wordt geschreven naar *./private/new-device.key.pem*. Het apparaat heeft de tekst voor deze sleutel tijdens het inrichten nodig. De tekst wordt later toegevoegd aan het aangepaste HSM-voorbeeld.

    > [!WARNING]
    > De tekst voor de certificaten bevat alleen informatie over openbare sleutels. 
    >
    > Het apparaat moet echter ook toegang hebben tot de persoonlijke sleutel voor het apparaatcertificaat. Dit is nodig omdat het apparaat tijdens het inrichten verificatie moet uitvoeren met behulp van deze sleutel tijdens runtime. De gevoeligheid van deze sleutel is een van de belangrijkste redenen om op hardware gebaseerde opslag te gebruiken in een echte HSM om persoonlijke sleutels te kunnen beveiligen.



## <a name="configure-the-custom-hsm-stub-code"></a>De stub-code voor de aangepaste HSM configureren

De bijzonderheden van de interactie met de daadwerkelijke beveiligde opslag op basis van hardware is afhankelijk van de hardware. Als gevolg hiervan wordt de certificaatketen die door het apparaat in deze zelfstudie wordt gebruikt, in de stub-code voor de aangepaste HSM vastgelegd. In de praktijk zou de certificaatketen worden opgeslagen in de daadwerkelijke HSM-hardware om een betere beveiliging te kunnen bieden voor gevoelige informatie. Methoden die vergelijkbaar zijn met de stub-methoden uit dit voorbeeld worden vervolgens geïmplementeerd om de geheimen van die op hardware gebaseerde opslag te lezen. 

Hoewel HSM-hardware niet is vereist, is het niet raadzaam om gevoelige informatie, zoals de persoonlijke sleutel van het certificaat, in de broncode te hebben. Hiermee wordt de sleutel voor iedereen beschikbaar die de code kan zien. Dat wordt in dit artikel alleen gedaan om te helpen bij het leren.

De stub-code voor de aangepaste HSM in deze zelfstudie bijwerken:

1. Start Visual Studio en open het nieuwe oplossingsbestand dat is gemaakt in de map `cmake` die u hebt gemaakt in de hoofdmap van de Git-opslagplaats azure-iot-sdk-c. Het oplossingsbestand heeft de naam `azure_iot_sdks.sln`.

2. Ga in Solution Explorer voor Visual Studio naar **Provisioning_Samples > custom_hsm_example > Bronbestanden** en open *custom_hsm_example.c*.

3. Werk de tekenreekswaarde van de tekenreeksconstante `COMMON_NAME` bij met de algemene naam die u hebt gebruikt bij het genereren van het apparaatcertificaat.

    ```c
    static const char* const COMMON_NAME = "custom-hsm-device-01";
    ```

4. Nadat u uw certificaten hebt gegenereerd, werkt u in hetzelfde bestand de tekenreekswaarde van de tekenreeksconstante `CERTIFICATE` bij met behlp van de tekst van de certificaatketen die u hebt opgeslagen in *./certs/new-device-full-chain.cert.pem*.

    > [!IMPORTANT]
    > Wanneer u de tekst naar Visual Studio kopieert, kunt u zien dat de tekst is geparseerd en bijgewerkt met spatiëring en dergelijke. Als dat zo is, moet u deze spatiëring en parsering verwijderen door eenmaal op **CTRL+Z** te drukken.

    Werk de certificaattekst bij zonder de extra spaties of parsering die door Visual Studio wordt uitgevoerd, zodat de tekst het onderstaande patroon volgt:

    ```c
    // <Device/leaf cert>
    // <intermediates>
    // <root>
    static const char* const CERTIFICATE = "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB"
    "\n-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFPDCCAySgAwIBAgIBATANBgkqhkiG9w0BAQsFADAqMSgwJgYDVQQDDB9BenVy"
        ...
    "MTEyMjIxMzAzM1owNDEyMDAGA1UEAwwpQXp1cmUgSW9UIEh1YiBJbnRlcm1lZGlh"
    "\n-----END CERTIFICATE-----\n"
    "-----BEGIN CERTIFICATE-----\n"
    "MIIFOjCCAyKgAwIBAgIJAPzMa6s7mj7+MA0GCSqGSIb3DQEBCwUAMCoxKDAmBgNV"
        ...
    "MDMwWhcNMjAxMTIyMjEzMDMwWjAqMSgwJgYDVQQDDB9BenVyZSBJb1QgSHViIENB"
    "\n-----END CERTIFICATE-----";        
    ```

5. Werk in hetzelfde bestand de tekenreekswaarde van de tekenreeksconstante `PRIVATE_KEY` bij met behulp van de persoonlijke sleutel voor apparaatcertificaat.

    > [!IMPORTANT]
    > Wanneer u de tekst naar Visual Studio kopieert, kunt u zien dat de tekst is geparseerd en bijgewerkt met spatiëring en dergelijke. Als dat zo is, moet u deze spatiëring en parsering verwijderen door eenmaal op **CTRL+Z** te drukken.

    Werk de tekst van de persoonlijke sleutel bij zonder de extra spaties of parsering die door Visual Studio wordt uitgevoerd, zodat de tekst het onderstaande patroon volgt:

    ```c
    static const char* const PRIVATE_KEY = "-----BEGIN RSA PRIVATE KEY-----\n"
    "MIIJJwIBAAKCAgEAtjvKQjIhp0EE1PoADL1rfF/W6v4vlAzOSifKSQsaPeebqg8U"
        ...
    "X7fi9OZ26QpnkS5QjjPTYI/wwn0J9YAwNfKSlNeXTJDfJ+KpjXBcvaLxeBQbQhij"
    "\n-----END RSA PRIVATE KEY-----";
    ```

6. Sla *custom_hsm_example.c* op.


## <a name="verify-ownership-of-the-root-certificate"></a>Eigendom van het basiscertificaat verifiëren

1. Gebruik de instructies in [Het openbare deel van een X.509-certificaat registreren en een verificatiecode ophalen](how-to-verify-certificates.md#register-the-public-part-of-an-x509-certificate-and-get-a-verification-code), upload het basiscertificaat (`./certs/azure-iot-test-only.root.ca.cert.pem`) en haal een verificatiecode op basis van DPS op.

2. Zodra u een verificatiecode op basis van DPS voor het basiscertificaat hebt, voert u de volgende opdracht uit vanuit de werkmap met het certificaatscript om een verificatiecertificaat te genereren.
 
    De hier getoonde verificatiecode is slechts een voorbeeld. Gebruik de code die u hebt gegenereerd op basis van DPS.    

    ```Bash
    ./certGen.sh create_verification_certificate 1B1F84DE79B9BD5F16D71E92709917C2A1CA19D5A156CB9F    
    ```    

    Met dit script wordt een certificaat gemaakt dat is ondertekend door het basiscertificaat waarvan de onderwerpnaam is ingesteld op de verificatiecode. Met dit certificaat kan DPS controleren of u toegang hebt tot de persoonlijke sleutel van het basiscertificaat. De locatie van het verificatiecertificaat bevindt zich in de uitvoer van het script. Dit certificaat wordt gegenereerd in `.pfx`-indeling.

    ```output
    Leaf Device PFX Certificate Generated At:
    --------------------------------------------
        ./certs/verification-code.cert.pfx
    ```

3. Zoals vermeld in [Het ondertekende verificatiecertificaat uploaden](how-to-verify-certificates.md#upload-the-signed-verification-certificate), uploadt u het verificatiecertificaat en klikt u in DPS op **Verifiëren** om het bewijs van bezit voor het basiscertificaat te voltooien.


## <a name="update-the-certificate-store-on-windows-based-devices"></a>Het certificaatarchief op Windows-apparaten bijwerken

Op niet-Windows-apparaten kunt u de certificaatketen vanuit de code als het certificaatarchief doorgeven.

Op Windows-apparaten moet u de handtekeningcertificaten (basis en tussenliggend) toevoegen aan een [Windows-certificaatarchief](https://docs.microsoft.com/windows/win32/secauthn/certificate-stores). Anders worden de handtekeningcertificaten niet naar DPS getransporteerd via een beveiligd kanaal met TLS (Transport Layer Security).

Ga als volgt te werk om de handtekeningcertificaten toe te voegen aan het certificaatarchief op Windows-apparaten:

1. Ga in een Git Bash-prompt naar de submap `certs` die uw handtekeningcertificaten bevat en converteer deze naar als volgt naar `.pfx`.

    basiscertificaat:

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.root.ca.key.pem -in ./azure-iot-test-only.root.ca.cert.pem -export -out ./root.pfx
    ```
    
    tussenliggend certificaat:   

    ```bash
    winpty openssl pkcs12 -inkey ../private/azure-iot-test-only.intermediate.key.pem -in ./azure-iot-test-only.intermediate.cert.pem -export -out ./intermediate.pfx
    ```

2. Klik met de rechtermuisknop op de Windows-knop **Start**. Klik vervolgens op **Uitvoeren**. Voer *certmgr.mcs* in en klik op **OK** om de MMC-module van de certificaatbeheerder te starten.

3. Klik in de certificaatbeheerder, onder **Certificaten - Huidige gebruiker**, op **Vertrouwde basiscertificeringsinstanties**. Klik vervolgens in het menu op **Actie** > **Alle taken** > **Importeren** om `root.pfx` te importeren.

    * Zoek op **Personal Information Exchange (.pfx)**
    * Gebruik `1234` als wachtwoord.
    * Plaats het certificaat in het certificaatarchief **Vertrouwde basiscertificeringsinstanties**.

4. Klik in de certificaatbeheerder, onder **Certificaten - Huidige gebruiker**, op **Tussenliggende certificeringsinstanties**. Klik vervolgens in het menu op **Actie** > **Alle taken** > **Importeren** om `intermediate.pfx` te importeren.

    * Zoek op **Personal Information Exchange (.pfx)**
    * Gebruik `1234` als wachtwoord.
    * Plaats het certificaat in het certificaatarchief **Tussenliggende certificeringsinstanties**.

Uw handtekeningcertificaten worden nu op het Windows-apparaat vertrouwd en de volledige keten kan naar DPS worden getransporteerd.



## <a name="create-an-enrollment-group"></a>Een inschrijvingsgroep maken

1. Meld u aan bij Azure Portal, selecteer in het linkermenu de knop **Alle resources** en open Device Provisioning Service.

2. Selecteer het tabblad **Inschrijvingen beheren** en selecteer vervolgens bovenaan de knop **Inschrijvingsgroep toevoegen**.

3. Voer in het deelvenster **Inschrijvingsgroep toevoegen** de volgende informatie in en druk op de knop **Opslaan**.

      ![Inschrijvingsgroep voor X.509-attestation toevoegen in de portal](./media/tutorial-custom-hsm-enrollment-group-x509/custom-hsm-enrollment-group-x509.png#lightbox)

    | Veld        | Waarde           |
    | :----------- | :-------------- |
    | **Groepsnaam** | Voer voor deze zelfstudie **custom-hsm-x509-devices** in |
    | **Type verklaring** | Selecteer **Certificaat** |
    | **IoT Edge-apparaat** | Selecteer **Onwaar** |
    | **Certificaattype** | Selecteer **Tussenliggend certificaat** |
    | **PEM- of CER-bestand voor primair certificaat** | Ga naar het tussenliggende certificaat dat u eerder hebt gemaakt ( *./certs/azure-iot-test-only.intermediate.cert.pem*) |


## <a name="configure-the-provisioning-device-code"></a>De code voor de apparaatinrichting configureren

In deze sectie werkt u de voorbeeldcode voor het inrichten van het apparaat bij naar uw Device Provisioning Service-exemplaar. Als het apparaat wordt geverifieerd, wordt het toegewezen aan een IoT-hub die is gekoppeld aan het Device Provisioning Service-exemplaar.

1. Selecteer in Azure Portal het tabblad **Overzicht** voor uw Device Provisioning-service en noteer de waarde van het **_Id-bereik_**.

    ![Device Provisioning Service-eindpuntgegevens uit de portalblade extraheren](./media/quick-create-simulated-device-x509/extract-dps-endpoints.png) 

2. Ga in Solution Explorer voor Visual Studio naar **Provisioning_Samples > prov_dev_client_sample > Bronbestanden** en open *prov_dev_client_sample.c*.

3. Zoek de constante `id_scope` op en vervang de waarde door uw **Id-bereik**-waarde die u eerder hebt gekopieerd. 

    ```c
    static const char* id_scope = "0ne00000A0A";
    ```

4. Zoek de definitie voor de functie `main()` op in hetzelfde bestand. Zorg ervoor dat variabele `hsm_type` is ingesteld op `SECURE_DEVICE_TYPE_X509`, zoals hieronder wordt weergegeven.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    hsm_type = SECURE_DEVICE_TYPE_X509;
    //hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Klik met de rechtermuisknop op het **prov\_dev\_client\_sample**-project en selecteer **Set as Startup Project**.

6. Selecteer in het menu van Visual Studio de optie **Debug** > **Start without debugging** om de oplossing uit te voeren. Wanneer wordt gevraagd het project opnieuw te bouwen, selecteert u **Ja** om het project opnieuw te bouwen voordat het wordt uitgevoerd.

    De volgende uitvoer is een voorbeeld van de voorbeeldcode van de Provisioning Device-client die wordt opgestart en verbinding maakt met de Provisioning Service. Het apparaat is toegewezen aan een IoT-hub en is geregistreerd:

    ```cmd
    Provisioning API Version: 1.3.9
    
    Registering Device
    
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    
    Registration Information received from service: test-docs-hub.azure-devices.net, deviceId: custom-hsm-device-01
    Press enter key to exit:
    ```

7. Navigeer in de portal naar de IoT-hub die is gekoppeld aan uw Provisioning-service en selecteer het tabblad **IoT-apparaten**. Wanneer het inrichten van het X.509-apparaat voor de hub is voltooid, wordt de apparaat-id weergegeven op de blade **IoT-apparaten** met *STATUS* **ingeschakeld**. Mogelijk moet u bovenaan op de knop **Vernieuwen** drukken. 

    ![Aangepast HSM-apparaat wordt geregistreerd bij de IoT-hub](./media/tutorial-custom-hsm-enrollment-group-x509/hub-provisioned-custom-hsm-x509-device.png) 

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met het testen en verkennen van dit voorbeeld van een apparaatclient, gebruikt u de volgende stappen om alle resources te verwijderen die in deze zelfstudie zijn gemaakt.

1. Sluit het uitvoervenster van het voorbeeld van de apparaatclient op de computer.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer uw Device Provisioning Service. Open het tabblad **Inschrijvingen beheren** voor uw service en selecteer vervolgens het tabblad **Inschrijvingsgroepen**. Schakel het selectievakje naast *Groepsnaam* in van de apparaatgroep die in deze zelfstudie hebt gemaakt. Druk vervolgens bovenaan het deelvenster op de knop **Verwijderen**. 
1. Klik in DPS op **Certificaten**. Voor elk certificaat dat u in deze zelfstudie hebt geüpload en geverifieerd, klikt u op het certificaat en klikt u op de knop **Verwijderen** om het te verwijderen.
1. Selecteer in het linkermenu in Azure Portal **Alle resources** en selecteer vervolgens uw IoT-hub. Open **IoT-apparaten** voor uw hub. Schakel het selectievakje in naast de *APPARAAT-ID* van het apparaat dat u in deze zelfstudie hebt geregistreerd. Klik bovenaan het deelvenster op de knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een X.509-apparaat ingericht met een aangepaste HSM voor uw IoT-hub. Ga verder met de volgende zelfstudie voor meer informatie over het inrichten van IoT-apparaten voor meerdere hubs. 

> [!div class="nextstepaction"]
> [Zelfstudie: Apparaten inrichten in IoT-hubs met gelijke taakverdeling](tutorial-provision-multiple-hubs.md)
