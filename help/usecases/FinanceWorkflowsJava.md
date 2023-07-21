---
title: Hantera arbetsflöden för finansiella dokument i Java
description: "[!DNL Adobe Acrobat Services] tillhandahåller alla verktyg, tjänster och funktioner som krävs för att bearbeta och extrahera data från finansdokument från PDF."
type: Tutorial
role: Developer
level: Intermediate
thumbnail: KT-7482.jpg
jria: KT-7482
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1315'
ht-degree: 0%

---

# Hantera arbetsflöden för finansiella dokument i Java

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseFinancialHero.jpg)

Finansbranschen använder PDF-filer i stor utsträckning för att utbyta data eftersom det bidrar till att upprätthålla dokumentformat, design och struktur. Detta robusta format gör det möjligt för finansanalytiker och rådgivare att hjälpa sina kunder att fatta välgrundade beslut.

PDF-formatet kan dock vara svårt att bearbeta och automatisera, särskilt när man kombinerar flera datakällor - ett vanligt användningsfall inom finansbranschen. Att skapa en anpassad lösning för att behandla PDF-dokument är ett alternativ, men det finns ingen anledning att investera för mycket tid och pengar i program och infrastruktur. [!DNL Adobe Acrobat Services] innehåller alla verktyg, tjänster och funktioner som behövs för att bearbeta och extrahera data från PDF-dokument.

## Vad du kan lära dig

I den här praktiska självstudiekursen lär du dig hur du använder [!DNL Adobe Acrobat Services] API:er för [!DNL Java Spring Boot] program. Du kan skapa en MVC-app (model-view-controller) som extraherar innehåll från PDF-dokument, konverterar det till andra dataformat som Excel, kombinerar flera PDF och lösenordsskyddar materialet. I den här självstudiekursen beskrivs hur du bearbetar PDF-dokument och visar dem på webbplatser med Adobe [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html).

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Projektexempel](https://github.com/adobe/pdftools-java-sdk-samples)

## Installation

[!DNL Adobe Acrobat Services] använder ett autentiseringssystem för att styra resursåtkomsten. För att få tillgång till tjänsterna måste du begära en API-nyckel från Adobe för din organisation eller ditt program. Om du har en API-nyckel fortsätter du till nästa avsnitt. Om du vill skapa en ny API-nyckel går du till [Komma igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) i [!DNL Acrobat Services] webbplats. Du kan skapa en nyckel med deras kostnadsfria testversion som innehåller 1 000 dokumenttransaktioner som kan användas i upp till sex månader.

Om du vill följa med i den här självstudiekursen behöver du två uppsättningar API-nycklar:

* Adobe PDF Services - används för att bearbeta PDF-dokumentet

* Adobe PDF Embed API

När du har skapat inloggningsuppgifterna kopierar du API-uppgifterna för PDF Services och den privata nyckeln till [!DNL Spring Boot] program i avsnittet resurser. Läs mer om [Bibliotek och beroenden för Maven och Gradle](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) om [!DNL Adobe Acrobat Services] webbplats. Se till att du konfigurerar alla nödvändiga paket och bibliotek innan du fortsätter.

![Skärmbild av katalogplatsen för API-uppgifter för PDF Services](assets/FAWJ_1.png)

Konfigurera loggningstjänsterna på [Adobe-dokumentation](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) och bläddra till avsnittet Loggning .

>[!NOTE]
>
> Spara inte de privata nycklarna i versionskontrollen i produktionsmiljön. Använd alltid ett hemligt valv eller en nyckelinjektionstjänst för att förhindra obehörig användning av autentiseringsuppgifter.

Nu när din [!DNL Spring Boot] om programmet är konfigurerat kan du fortsätta att bearbeta PDF och generera kundrapporter.

## Inlämning av rapportdata

Om du vill använda Adobe PDF Services API konfigurerar du först ett `ExecutionContext` som förbrukar de autentiseringsuppgifter du anger. Eftersom du har inloggningsuppgifterna i programmet kan du läsa dem från filen och skapa sammanhanget på följande sätt:

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

Hämta sedan kontexten för att bearbeta PDF-dokumenten. Här är de åtgärder du kan utföra:

* Konvertera PDF-dokument (till Excel-, Word- eller bildtyp)

* Skapa PDF-dokument (från HTML, Excel, Word med flera)

* Kombinera flera PDF-dokument

* Protect och ta bort skyddet för PDF-dokumenten (du måste ha lösenordet)

* Optimera PDF-dokument för nätverksdistribution

Alla dessa prover finns i [GitHub-exempel](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples) databas.

Nästa, i [!DNL Spring Boot], du kan hämta en fil med strängsökvägen eller strömmen där filen överförs. Varje åtgärd du utför måste initieras och en sökväg för indatafilen måste anges. I den här självstudiekursen använder du de allmänt tillgängliga PDF-rapporterna från [Blackrock](https://www.blackrock.com/us/individual/products/investment-funds). Du kan använda vilken källa som helst, inklusive dina egna rapporter.

Börja med att hämta [FileRef](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/io/FileRef.html) -objekt från filen. För enkelhetens skull bör du fokusera på filerna via sökvägen String. Nedan skapar du en åtgärd för att konvertera en fil i sökvägen från PDF till Excel:

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

Efter det här steget är programmet redo att köra den första åtgärden på PDF. Sedan utför du åtgärden och får resultatet i Excel-bladet:

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

I det här scenariot hanteras endast en PDF-fil. Du kan också börja med flera PDF-filer och kombinera dem till en enda fil. Det är vanligt att använda flera filer vid rapportering av finansiella data eftersom du måste bearbeta medel från flera källor för att få en omfattande rapport.

## Genererar rapporten

[!DNL Adobe Acrobat Services] stöder inte bearbetning av färdiga Excel-dokument, men du kan fortfarande använda ramverk och bibliotek för communityn för att bearbeta innehållet.

Du kan till exempel använda kommandot [Apache POI](https://poi.apache.org/) för att bearbeta Excel (eller andra Microsoft-dokument) i [!DNL Java Spring Boot] eller utföra andra manuella eller automatiska uppgifter på Excel-filen.

I det här exemplet extraherar du nettotillgångsvärdet för dina tre PDF-dokument och visar dem i en tabell. Du kan även hämta annan information, till exempel diagram och tabeller, baserat på dina behov och tillgängliga data. Du kan även hämta in data från andra källor.

När rapporten har skapats - i det här exemplet i Excel-format - kan du använda Adobe PDF Services-åtgärder för att konvertera tillbaka rapporten till ett PDF-dokument och skydda den.

Gör så här om du vill konvertera en rapport från Excel-format till ett PDF-dokument:

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
CreatePDFOperation exportOperation = CreatePDFOperation.createNew();

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_EXCEL);
exportOperation.setInput(inputPdf);

try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_PDF);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

>[!TIP]
>
> Om du vill undvika att behöva återskapa objektet varje gång en begäran kommer använder du Vårens beroendeinjektion för att injicera `ExecutionContext` objekt.

Med den här koden genereras ett PDF-dokument från rapporten i Excel-format.

Innan du skickar PDF till dina kunder kan du skydda den med ett lösenord. Skapa en annan åtgärd som hanterar detta skydd åt dig, [ProtectPDFOperation](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/pdfops/ProtectPDFOperation.html)och sedan använda [ProtectPDFOoptions](https://opensource.adobe.com/pdfservices-java-sdk-samples/apidocs/latest/com/adobe/pdfservices/operation/pdfops/options/protectpdf/package-summary.html) för att lägga till lösenordet i dokumentet.

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

Ange sedan indata och kör åtgärden. Den resulterande filen ska ha ett lösenord för att förhindra obehörig åtkomst.

## Visa rapporten

Nu när din PDF-rapport har skapats kan du visa den på webbplatsen med Adobe PDF Embed API. Med det här JavaScript-API:t kan webbutvecklare läsa in och återge PDF-dokument direkt i webbläsaren.

>[!NOTE]
>
> Nu behöver du den andra autentiseringstoken, klient-ID:t.

I din [!DNL Spring Boot] lägger du till följande HTML-kodfragment där du vill återge PDF-rapporten:

```
<div id="pdf-viewer"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function()
    {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-client-id-here>", divId: "pdf-viewer" });
        adobeDCView.previewFile(
        {
            content: {
                location: {
                    url: "<your-document.pdf>"
                }
            },
            metaData: {
                fileName: "<document-name.pdf>"
            }
        });
    });
</script>
```

Det här skriptet läser in PDF-dokumentet och gör att användarna kan kommentera och anteckna i dokumenten. Här är vyn av detta inbäddade API som visas i Firefox:

![Skärmdump av ett PDF-dokument i Firefox](assets/FAWJ_2.png)

PDF Embed API innehåller alla verktyg som behövs för att förhandsgranska PDF och för att kommentera rapporten.

## Nästa steg

Den här praktiska självstudiekursen utforskade [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) API:er och diskuterade hur dessa tjänster kan användas för att bearbeta PDF-data och generera rapporter för ekonomiska beslut. Det visade hur du kan integrera API:erna i dina system med [!DNL Java Spring Boot] som en exempelram för att visa hur enkelt det är att snabbt behandla PDF-dokument.

Utforska [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) och se vad Adobe PDF Services kan göra för företaget. Mer information om fler funktioner i SDK finns i [GitHub-databas](https://github.com/adobe/pdftools-java-sdk-samples) och utforska hur [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) kan hjälpa dig att snabbt visa PDF i dina program.

För att enkelt kombinera och hantera dokument, skapa användbara PDF-rapporter för dina finanskunder, börja med att registrera dig gratis [Utvecklarkonto för Adobe](https://www.adobe.io/apis/documentcloud/dcsdk/) idag.
