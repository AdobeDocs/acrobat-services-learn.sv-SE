---
title: Samverkan mellan elever och lärare
description: Lär dig skapa en onlineutbildningsplattform som gör det möjligt för lärare och elever att enkelt dela resurser i PDF
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8091
thumbnail: KT-8091.jpg
exl-id: 570a635c-e539-4afc-a475-ecf576415217
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1385'
ht-degree: 0%

---

# Samverkan mellan studerande och lärare

![Banderoll för användningsfall](assets/UseCaseStudentHero.jpg)

Utbildningsinstitutioner använder PDF-dokument för att dela utbildningsmaterial med elever. PDF är ett utbytbart dokumentformat för lärare.

Om du integrerar [Adobe PDF Services API](https://developer.adobe.com/document-services/apis/pdf-services) och [Adobe PDF Embed API](https://developer.adobe.com/document-services/apis/pdf-embed) i ett program får lärare och elever en enda plattform att undervisa och lära sig på. Din app kan t.ex. göra det möjligt för elever att ställa frågor om sina uppgifter och rapportkort och samarbeta om grupptilldelningar.

Det finns en officiell SDK för Node.js-program för att komma åt PDF Services API. Detta gör att du kan konvertera dokument som Microsoft Word och Microsoft Excel till
PDF. Du kan också utföra mer avancerade åtgärder som att kombinera flera rapporter, ordna om sidor och skydda PDF. Mer information finns i [produktdokumentationen](https://developer.adobe.com/document-services/homepage/).

## Vad du kan lära dig

I den här praktiska självstudiekursen kan du lära dig skapa en onlineutbildningsplattform som [gör det möjligt för lärare och elever att enkelt dela resurser](https://developer.adobe.com/document-services/use-cases/collaboration/student-teacher-collaboration) i PDF. I den här självstudiekursen används en [utbildningsportal](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers) som har skapats med JavaScript-körningen Node.js (Node.js) och PDF.

Utbildningsportalen har följande funktioner:

* Gör det möjligt för lärare att överföra resurser

* Gör det möjligt för elever att välja flera dokument som ska konverteras till PDF

* Möjliggör konvertering av dokument till PDF

* Tillhandahåller en PDF-förhandsgranskning för elever i en webbläsare och gör det möjligt för dem att kommentera dokumenten utan ytterligare program

* Gör det möjligt för elever att lämna kommentarer och hämta dem till sina datorer

Se hur [!DNL Adobe Acrobat Services] kan ge dina elever en spännande upplevelse med PDF. [!DNL Acrobat Services] API:er integreras sömlöst i dina befintliga program så att eleverna kan överföra, konvertera och visa filer och sedan göra och spara kommentarer - allt i din nuvarande konfiguration.

## Relevanta API:er och resurser

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Projektkod](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers)

## Överför resurser till utbildningsportalen

I lärarnas avsnitt i utbildningsportalen kan lärarna ladda upp dokument som tilldelningar och tester. Dokumenten kan ha vilket format som helst, t.ex. Microsoft Word, Microsoft Excel, HTML och olika bildformat.

![Skärmbild av lärarsektionen i utbildningsportalen](assets/edu_1.png)

Överförda dokument lagras och visas för eleverna när de öppnar sin webbsida.

Information om hur programmet överför filerna finns i [projektkoden](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers).

## Konvertera dokument till PDF

Eleverna kan konvertera enstaka eller flera dokument av olika typ till PDF, t.ex. Microsoft Word, Excel och PowerPoint, samt andra vanliga text- och bildfiltyper. Utbildningsportalen använder PDF-tjänster för att konvertera filer till PDF.

Om du vill skapa en egen utbildningsportal måste du först skapa dina egna inloggningsuppgifter. [Registrera dig](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) för att
använd PDF Services API kostnadsfritt i sex månader och upp till 1 000 dokumenttransaktioner. Därefter [betalar du per användning](https://developer.adobe.com/document-services/pricing/main) med bara \$0,05 per dokumenttransaktion när klassen ökar sina tilldelningar.

När en elev väljer ett dokument från instrumentpanelen ser hen följande:

![Skärmbild av elevsektionen i utbildningsportalen](assets/edu_2.png)

Eleven väljer helt enkelt dokumenten för konvertering och klickar på **Hämta rapport**.

Utbildningsportalen konverterar dokumenten till PDF och visar en rapportsida tillsammans med en förhandsgranskning av PDF-filen.

Här är exempelkoden för det här steget:

```
async function createPdf(rawFile, outputPdf) {
    try {
            // configurations
            const credentials =  adobe.Credentials
            .serviceAccountCredentialsBuilder()
            .fromFile("./src/pdftools-api-credentials.json")
            .build();
 
            // Capture the credential from app and show create the context
            const executionContext = adobe.ExecutionContext.create(credentials),
            operation = adobe.CreatePDF.Operation.createNew();
 
            // Pass the content as input (stream)
            const input = adobe.FileRef.createFromLocalFile(rawFile);
            operation.setInput(input);
 
            // Async create the PDF
            let result = await operation.execute(executionContext);
            await result.saveAsFile(outputPdf);
    } catch (err) {
            console.log('Exception encountered while executing operation', err);
    }
}
```

Exempelkoden anropar metoden `createPdf` i Express-flödeshanteraren för att generera PDF.

Information om hur metoden anropas finns i [projektkoden](https://github.com/afzaal-ahmad-zeeshan/adobe-pdf-tools-for-teachers/blob/master/src/helpers/pdf.js).

## Förhandsgranska utbildningsresurserna

Användargränssnittet använder PDF Embed API för att återge PDF i en webbläsare. Detta API är tillgängligt för användning utan kostnad.

PDF Embed API använder en annan autentiseringsuppgift än PDF Services API, så du måste [skapa en autentiseringsuppgift](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html)
innan du kan använda den. Sedan kan du använda PDF Embed helt gratis.

Se till att du anger rätt webbplats-URL i token. Annars kanske du inte kan återge PDF med token.

Användargränssnittet använder mallspråket [Handlebars](https://handlebarsjs.com/). Då visas PDF i en webbläsare.

Här är koden för det här steget:

```
<div id="adobe-dc-view" style="height: 750px; width: 700px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
    document.addEventListener("adobe_dc_view_sdk.ready", function () {
        var adobeDCView = new AdobeDC.View({ clientId: "<your-credentials-here>", divId: "adobe-dc-view" });
        adobeDCView.previewFile(
            {
                content: {
                    location: { url: "<file-url>" }
                },
                    metaData: { fileName: "<file-name>" }
            },
           );
    });
</script>
 
<p>Material has been generated, <a href="/students/download/{{filename}}" target="_blank">click here</a> to download it.
</p>
```

I koden visas utdata från PDF och länken för att hämta PDF-rapporten, som visas i skärmbilden nedan:

![Skärmbild av elevens förhandsgranskning av PDF](assets/edu_3.png)

Eleverna ska kunna ladda ner rapporten eller arbeta med materialet här.

## Anteckna PDF-dokument

En utbildningsplattform bör ha stöd för grundläggande anteckningar, kommentarer och diskussioner i PDF. PDF Embed API har alla dessa funktioner. Detta aktiverar anteckningsstöd med `showAnnotationTools`, vilket gör det möjligt för lärare och elever att kommentera dokumenten och arkivera kommentarer som en del av PDF.

Om du vill aktivera anteckningar i PDF-dokument behöver du bara skicka argumentet `showAnnotationTools` : true till metoden `previewFile`. Då visas anteckningsverktyget i förhandsvisningen i PDF. Det här verktyget kommer du åt från menyn med tre punkter i förhandsvisningens övre högra hörn.

![Skärmbild av kommentarverktyg i PDF](assets/edu_4.png)

I de dokument som lärarna har överfört kan eleverna markera text, lägga till kommentarer och så vidare.

![Skärmbild av hur kommentarer läggs till i PDF](assets/edu_5.png)

I skärmbilden ovan är användaren märkt Gäst, men du kan konfigurera profiler för användare som elever och lärare.

När en elev använder en kommentar visar PDF Embed API en **Spara**-knapp längs den övre banderollen. När du sparar läggs anteckningar till i filen. Klicka på **Spara** om du vill se hur filen sparas med anteckningen inbäddad i rapporten.

Elever kan använda anteckningar för att ställa frågor eller dela med sig av sina kommentarer om utbildningsmaterialet.

## Spåra dokumentanvändning

Det är viktigt för lärare och skolor att se hur eleverna använder onlineplattformar. Det hjälper lärare att hjälpa sina elever med resurser som hjälper dem att utföra sina uppgifter bättre. PDF Embed API kan integreras med analysfunktioner som du kan använda för att mäta alla händelser som äger rum, till exempel när användare öppnar, läser och stänger dokument. Med PDF Services API kan lärarna även inaktivera utskrift, hämtning och filändringar för att bibehålla den akademiska integriteten.

Om du har en [Adobe Analytics](https://developer.adobe.com/analytics-apis/docs/2.0/)-licens kan du använda dess [färdiga integrering](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/controlpdfexperience#adobe-analytics). Annars kan du använda återanrop för att integrera PDF-tjänsterna med andra analysleverantörer, till exempel [Google](https://experienceleague.adobe.com/en/docs/acrobat-services-learn/tutorials/pdfembed/controlpdfexperience#google-analytics).

Om du vill aktivera mätning av dokumenthändelser kopplar du händelsehanterarna med `registerCallback`-metoden till Adobe DC-vyinstansen. Du kan visa grundläggande måttangivelser i konsolen, t.ex. om du öppnar ett dokument eller läser en sida. Du kan också spara mätvärdena i en logg eller publicera dem i andra analysarkiv.

Här är exempelkoden för att bifoga händelsehanterare:

```
adobeDCView.registerCallback(
    AdobeDC.View.Enum.CallbackType.EVENT_LISTENER,
    function(event) {
           console.log(event);
    },
    {
           enablePDFAnalytics: true
    }
);
```

Lärare kan se hur många elever som har sett uppgiften, hur många som har gått igenom alla sidor i anteckningarna och andra värdefulla detaljer.

Här är en skärmdump av webbläsarkonsolen:

![Skärmbild av webbläsarkonsolen](assets/edu_6.png)

Den här skärmbilden visar att eleven öppnade uppdragsfilen och läste den första sidan. Eleven bläddrade antingen inte vidare till ytterligare sidor eller så hade dokumentet bara en sida och laddade sedan ned filen. Du kan samla in dessa mätvärden för att utföra analyser och studera elevernas beteende.

Dessutom är [Adobe Analytics](https://business.adobe.com/products/adobe-analytics.html) integrerat med PDF Embed API, så om du har ett abonnemang på Adobe Analytics kan du publicera mätvärdena i abonnemanget. Om du vill publicera mätvärdena i Adobe Analytics behöver du bara skicka ditt svit-ID till PDF Embed API-konstruktorn. (Observera att du måste använda dina inloggningsuppgifter för PDF Embed API, inte dina API-inloggningsuppgifter för PDF Services).

Här är exempelkod som visar hur du skickar svit-ID till PDF Embed API-konstruktorn:

```
var adobeDCView = new AdobeDC.View({
    clientId: "<your-adobe-dc-credential>",
    divId: "<#element>"
    reportSuiteId: <your-id-here>,
}); 
```

## Nästa steg

I den här praktiska självstudiekursen har vi granskat hur du använder API:et PDF Services och PDF Embed API för att skapa en utbildningsportal, vilket underlättar ett effektivt [samarbete mellan elever och lärare](https://developer.adobe.com/document-services/use-cases/collaboration/student-teacher-collaboration). Med hjälp av den här portalen kan lärare ladda upp utbildningsmaterial i valfritt format och konvertera det till PDF med hjälp av PDF Services API. Eleverna kan sedan förhandsgranska dessa PDF med hjälp av PDF Embed API.

Nu när du vet hur du lägger till anteckningar i PDF, arkiverar anteckningarna och spårar användningen av PDF-rapporter kan du börja implementera dessa i dina egna projekt.

Du kan använda [!DNL Adobe Acrobat Services] API:er för att skapa användarvänliga, interaktiva PDF-upplevelser på din webbplats. Använd Adobe PDF Services API kostnadsfritt i sex månader och sedan bara [betala per användning](https://developer.adobe.com/document-services/pricing/main) (via AWS eller ett direktavtal) för endast \$0,05 per dokumenttransaktion. Använd Adobe PDF Embed gratis utan tidsbegränsning. Skapa ett kostnadsfritt konto för att [komma igång](https://www.adobe.com/go/dcsdks_credentials) i dag.
