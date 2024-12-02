---
title: Hantera arbetsflöden för finansiella dokument i Java
description: '[!DNL Adobe Acrobat Services] tillhandahåller alla verktyg, tjänster och funktioner som behövs för att bearbeta och extrahera data från finansdokument från PDF'
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-7482
thumbnail: KT-7482.jpg
exl-id: 3bdc2610-d497-4a54-afc0-8b8baa234960
source-git-commit: ad13c28a0c218fc0027afc02445e5ed532c2340d
workflow-type: tm+mt
source-wordcount: '1204'
ht-degree: 0%

---

# Hantera arbetsflöden för finansiella dokument i Java

![Banderoll för användningsfall](assets/UseCaseFinancialHero.jpg)

Finansbranschen använder PDF-filer i stor utsträckning för att utbyta data eftersom det hjälper till att upprätthålla dokumentformat, design och struktur. Detta robusta format gör det möjligt för finansanalytiker och rådgivare att hjälpa sina kunder att fatta välinformerade beslut.

PDF-formatet kan dock vara svårt att bearbeta och automatisera, särskilt när man kombinerar flera datakällor - ett vanligt användningsfall inom finansbranschen. Att skapa en anpassad lösning för att behandla PDF-dokument är ett alternativ, men det finns ingen anledning att investera för mycket tid och pengar i mjukvara och infrastruktur. [!DNL Adobe Acrobat Services] tillhandahåller alla verktyg, tjänster och funktioner som behövs för att bearbeta och extrahera data från PDF-dokument.

## Vad du kan lära dig

I den här praktiska självstudien lär du dig hur du använder [!DNL Adobe Acrobat Services] API:er för [!DNL Java Spring Boot]-program. Du skapar en MVC-app (Model-View-Controller) där innehåll extraheras från PDF-dokument, konverteras till andra dataformat, till exempel Excel, flera PDF kombineras och resurserna lösenordsskyddas. I den här självstudiekursen beskrivs hur du bearbetar PDF-dokument och visar dem på dina webbplatser med Adobe [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html).

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [Projektexempel](https://github.com/adobe/pdftools-java-sdk-samples)

## Installation

[!DNL Adobe Acrobat Services] använder ett autentiseringssystem för att kontrollera resursåtkomsten. Du måste begära en API-nyckel från Adobe för din organisation eller ditt program för att få åtkomst till tjänsterna. Fortsätt till nästa avsnitt om du har en API-nyckel. Om du vill skapa en ny API-nyckel går du till [Komma igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) på webbplatsen [!DNL Acrobat Services]. Du kan skapa en nyckel med den kostnadsfria testversionen som innehåller 1 000 dokumenttransaktioner som kan användas i upp till sex månader.

Om du vill följa den här självstudiekursen behöver du två uppsättningar API-nycklar:

* Adobe PDF Services - används för att bearbeta PDF-dokumentet

* Adobe PDF Embed API

När du har skapat autentiseringsuppgifterna kopierar du API-autentiseringsuppgifterna för PDF Services och den privata nyckeln till programmet [!DNL Spring Boot] i resursavsnittet. Läs mer om [Bibliotek och beroenden för Maven och Gradle](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) på webbplatsen [!DNL Adobe Acrobat Services]. Se till att du konfigurerar alla nödvändiga paket och bibliotek innan du fortsätter.

![Skärmbild av katalogplatsen för API-uppgifter för PDF Services](assets/FAWJ_1.png)

Om du vill konfigurera loggningstjänsterna går du till [Adobe-dokumentationen](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=services) och bläddrar till avsnittet Loggning.

>[!NOTE]
>
> Spara inte de privata nycklarna i versionskontrollen i produktionsmiljön. Använd alltid ett hemligt valv eller en nyckelinjektionstjänst för att förhindra obehörig användning av autentiseringsuppgifter.

Nu när ditt [!DNL Spring Boot]-program är konfigurerat kan du fortsätta med att bearbeta PDF och generera kundrapporter.

## Inlämning av rapportdata

Om du vill använda Adobe PDF Services API måste du först konfigurera en `ExecutionContext` som använder de autentiseringsuppgifter du anger. Eftersom du har inloggningsuppgifterna i programmet kan du läsa dem från filen och skapa sammanhanget på följande sätt:

```
Credentials credentials = Credentials.serviceAccountCredentialsBuilder()
    .fromFile(AUTH_FILE_PATH)
    .build();

ExecutionContext executionContext = ExecutionContext.create(credentials);
```

Hämta sedan kontexten för att bearbeta PDF-dokumenten. Här är de åtgärder du kan utföra:

* Konvertera PDF-dokument (till Excel, Word eller bildtyp)

* Skapa PDF-dokument (från HTML, Excel, Word med mera)

* Kombinera flera PDF-dokument

* Protect och ta bort skyddet från PDF-dokumenten (du måste ha lösenordet)

* Optimera PDF-dokument för visning i nätverk

Alla dessa exempel är tillgängliga i [GitHub-exempeldatabasen](https://github.com/adobe/pdfservices-java-sdk-samples/tree/master/src/main/java/com/adobe/pdfservices/operation/samples).

I [!DNL Spring Boot] kan du sedan hämta en fil med strängsökvägen eller strömmen där filen laddas upp. Varje åtgärd du utför måste initieras och en indatafilsökväg måste anges. I den här självstudiekursen använder du de offentligt tillgängliga PDF-rapporterna från [Blackrock](https://www.blackrock.com/us/individual/products/investment-funds). Du kan använda vilken källa som helst, inklusive dina egna rapporter.

Börja med att hämta FileRef-objektet från filen. För enkelhetens skull bör du fokusera på filerna via strängsökvägen. Nedan visas en åtgärd för att konvertera en fil i sökvägen från PDF till Excel:

```
ExecutionContext executionContext = ExecutionContext.create(credentials);
ExportPDFOperation exportOperation = ExportPDFOperation.createNew(ExportPDFTargetFormat.XLSX);

// Create the input source
FileRef inputPdf = FileRef.createFromLocalFile(INPUT_PDF);
exportOperation.setInput(inputPdf);
```

Efter detta steg, ditt program är redo att köra den första operationen på PDF. Därefter utför du åtgärden och får resultatet i Excel-bladet:

```
try {
    FileRef output = exportOperation.execute(executionContext);
    output.saveAs(OUTPUT_EXCEL);
} catch (ServiceApiException e) {
    e.printStackTrace();
}
```

I det här scenariot hanteras endast en PDF-fil. Du kan också börja med flera PDF-filer och kombinera dem till en enda fil. Det är vanligt att använda flera filer vid rapportering av finansiella data eftersom du måste bearbeta medel från flera källor för att kunna tillhandahålla en omfattande rapport.

## Genererar rapporten

[!DNL Adobe Acrobat Services] stöder inte bearbetning av Excel-dokument direkt, men du kan fortfarande använda ramverk och bibliotek för communityn för att bearbeta innehållet.

Du kan till exempel använda [Apache POI](https://poi.apache.org/) för att bearbeta Excel (eller andra Microsoft-dokument) i programmet [!DNL Java Spring Boot], eller utföra andra manuella eller automatiska uppgifter i Excel-filen.

I det här exemplet börjar du med dina PDF-dokument och extraherar nettotillgångsvärdet för dina tre fonder och visar dem i en tabell. Du kan även hämta annan information, t.ex. diagram och tabeller, utifrån dina behov och tillgängliga data. Du kan till och med hämta data från andra källor.

När rapporten har skapats - i det här exemplet i ett Excel-format - kan du använda Adobe PDF Services-åtgärder för att konvertera tillbaka rapporten till ett PDF-dokument och skydda det.

Om du vill konvertera rapporten från Excel-format till ett PDF-dokument använder du följande åtgärd:

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
> Om du vill förhindra att objektet måste återskapas varje gång en begäran kommer använder du Spring beroendeinjektion för att mata in `ExecutionContext`-objektet.

Med den här koden genereras ett PDF-dokument av rapporten i Excel-format.

Innan du levererar PDF till dina kunder kan du skydda den med ett lösenord. Skapa en annan åtgärd som hanterar detta skydd för dig, ProtectPDFOperation, och använd sedan ProtectPDFOoptions för att lägga till lösenordet i dokumentet.

```
ProtectPDFOptions options = ProtectPDFOptions.passwordProtectOptionsBuilder()
                    .setUserPassword("p@55w0rd")
                    .setEncryptionAlgorithm(EncryptionAlgorithm.AES_256)
                    .build();
ProtectPDFOperation operation = ProtectPDFOperation.createNew(options);
```

Ange sedan indata och kör åtgärden. Den resulterande filen bör ha ett lösenord för att förhindra obehörig åtkomst.

## Visa rapporten

Nu när din PDF-rapport har skapats kan du visa den på webbplatsen med Adobe PDF Embed API. Med detta JavaScript API kan webbutvecklare läsa in och återge PDF-dokument direkt i webbläsaren.

>[!NOTE]
>
> Nu behöver du en andra autentiseringstoken, klient-ID.

Lägg till följande HTML-kodfragment i programmet [!DNL Spring Boot] där du vill återge PDF-rapporten:

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

Det här skriptet läser in PDF-dokumentet och gör det möjligt för användare att kommentera och anteckna i dokumenten. Här är vyn av detta Embed API som visas i Firefox:

![Skärmbild av ett PDF-dokument i Firefox](assets/FAWJ_2.png)

PDF Embed API innehåller alla verktyg som behövs för att förhandsgranska PDF samt för att kommentera rapporten.

## Nästa steg

I den här praktiska självstudiekursen har vi utforskat API:erna för [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) och diskuterat hur de här tjänsterna kan användas för att bearbeta PDF-data och generera rapporter för ekonomiska beslut. Det visar hur du kan integrera API:erna i dina system med hjälp av [!DNL Java Spring Boot] som exempelramverk och hur enkelt det är att snabbt bearbeta PDF-dokument.

Utforska [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/) och se vad Adobe PDF-tjänster kan göra för ditt företag. Mer information om SDK finns i [GitHub-katalogen](https://github.com/adobe/pdftools-java-sdk-samples). Ta en titt på hur [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) kan hjälpa dig att snabbt visa PDF i dina program.

Om du vill kunna kombinera och ändra dokument och skapa användbara PDF-rapporter åt dina finanskunder kan du börja med att registrera dig för ett kostnadsfritt [Adobe-utvecklarkonto](https://www.adobe.io/apis/documentcloud/dcsdk/) i dag.
