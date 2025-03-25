---
title: Hantera försäljningsförslag och avtal
description: Lär dig skapa ett effektivt arbetsflöde för att automatisera och förenkla försäljningsförslag
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8099
thumbnail: KT-8099.jpg
exl-id: 219c70de-fec1-4946-b10e-8ab5812562ef
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '1306'
ht-degree: 0%

---

# Hantera försäljningsförslag och avtal

![Banderoll för användningsfall](assets/UseCaseSalesHero.jpg)

Säljförslag är det första steget i ett företags resa mot kundförvärv. Som med allt, första intrycket varar. Så, din första interaktion med kunder sätta sina förväntningar för ditt företag. Ert förslag måste vara koncist, exakt och bekvämt.

Kontrakt och förslag innehåller olika typer av data i dokumentstrukturen. De innehåller både dynamiska data (klientnamn, offertbelopp och så vidare) och statiska data (allmän text som fasta funktioner, teamprofiler och standardvillkor för verksamhetsåtagande). Att skapa malldokument, till exempel försäljningsförslag, innebär ofta enformiga uppgifter som att manuellt ersätta projektdetaljer i en mall. I den här självstudiekursen använder du dynamiska data och arbetsflöden för att skapa en effektiv process för att [skapa säljförslag](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/sales-proposals-and-contracts).

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du implementerar dynamiska data och arbetsflöden med flera verktyg, varav de viktigaste är [!DNL Adobe Acrobat Services] API:er. Dessa API:er används för att göra säljförslag och avtal mer praktiska för dig och ditt företag. I den här självstudiekursen demonstreras praktiska tekniker som visar hur du skapar, sammanfogar och visar PDF-dokument automatiskt. Att utföra dessa uppgifter manuellt är tidskrävande och omständligt. Genom att utnyttja [!DNL Acrobat Services] API:er kan du förkorta tiden som läggs på dessa aktiviteter.

## Relevanta API:er och resurser

* [Microsoft Word](https://www.office.com/)

* [Node.js](https://nodejs.org/en/)

* [npm](https://www.npmjs.com/get-npm)

* [[!DNL Acrobat Services] API:er](https://developer.adobe.com/document-services/homepage/)

* [Adobe-dokumentgenererings-API](https://developer.adobe.com/document-services/apis/doc-generation)

* [Adobe Sign API](https://developer.adobe.com/adobesign-api/)

* [Genereringstagg för Adobe-dokument](https://opensource.adobe.com/pdftools-sdk-docs/docgen/latest/wordaddin.html#add-in-demo)

## Lösning av problemet

Nu när du har verktygen installerade kan du börja lösa problemet. Förslagen har både statiskt innehåll och dynamiskt innehåll som är unikt för varje kund. Det finns flaskhalsar eftersom båda typerna av uppgifter behövs varje gång ett förslag läggs fram. Det är tidskrävande att ange den statiska texten, så du kommer att automatisera den och endast hantera de dynamiska data från varje klient manuellt.

Skapa först ett datainsamlingsformulär i [Microsoft Forms](https://www.office.com/launch/forms?auth=1) (eller det formulärverktyg du föredrar). Det här formuläret används för dynamiska data från kunder som läggs till i ett försäljningsförslag. Fyll i formuläret med frågor för att få de uppgifter du behöver från kunderna - till exempel företagsnamn, datum, adress, projektomfattning, prissättning och ytterligare kommentarer. Om du vill skapa ett eget formulär använder du det här [formuläret]&#x200B;(https://forms.office.com/Pages/ShareFormPage.aspx id=DQSIkWdsW0yxEjajBLZtrQAAAAAAAAAN__rtiGj5UNElTR0pCQ09ZNkJRUlowSjVQWDNYUEg2RC4u&amp;sharetoken=1AJzzee Målet är att potentiella klienter ska fylla i formuläret och sedan exportera sina svar som JSON-filer, som skickas till nästa del av arbetsflödet.

Vissa formulärverktyg låter dig bara exportera data som CSV-filer. Du kan använda [konvertera](http://csvjson.com/csv2json) den genererade CSV-filen till en JSON-fil.

De statiska uppgifterna återanvänds i varje försäljningsförslag. Du kan alltså använda en försäljningsförslagsmall i Microsoft Word för att ange den statiska texten. Du kan använda den här [mallen](https://1drv.ms/w/s!AiqaN2pp7giKkmhVu2_2pId9MiPa?e=oeqoQ2), men du kan skapa en egen eller använda en [Adobe-mall](https://developer.adobe.com/document-services/apis/doc-generation).

Nu behöver du något som tar både dynamiska data från kunder i JSON-format och statisk text i Microsoft Word-mallen för att göra ett unikt försäljningsförslag för en kund. API:erna [!DNL Acrobat Services] används för att slå samman de två och generera ett PDF som kan signeras.

Du använder taggar för att få det att fungera. Taggar är lättanvända strängar som kan representera siffror, ord, matriser eller till och med komplexa objekt. Taggar fungerar som platshållare för dynamiska data, vilket i det här fallet är klientdata som anges i formuläret. När du har infogat taggar i mallen kan du mappa formulärfält från JSON-filen till Word-mallen.

## Använda taggar

Öppna mallen för försäljningsförslag och välj fliken **Infoga**. Välj **Hämta tillägg** i gruppen **Tillägg**. Sedan väljer du **Tillägg för dokumentgenerering i Adobe** för att lägga till det. När du har lagt till en tagg för dokumentgenerering visas den på fliken **Start** i gruppen **Adobe**.

Markera **Dokumentgenerering** på fliken **Hem** i gruppen **Adobe** för att komma igång med att tagga dokumentet. En användbar demonstrationsvideo visas på en panel till höger i fönstret.

![Skärmbild av Document Tagger-tillägget i Word](assets/sales_1.png)

Välj **Kom igång**. Du ombeds sedan att tillhandahålla exempeldata. Klistra in eller överför JSON-filen för formulärsvaret enligt nedan.

![Skärmbild av inklistring av exempelkod](assets/sales_2.png)

Välj **Generera taggar** för att få en lista med fält från JSON-filen som du klistrade in eller överförde. Taggarna visas nedan i det högra sidofältet.

![Skärmbild av tillgängliga taggar](assets/sales_3.png)

När du har genererat taggarna kan du infoga dem i dokumentet. Taggar läggs till i dokumentet vid markörens plats. Som visas ovan bör du lägga till taggen **Project Scope** direkt under underrubriken **Projekt**. När en kund anger projektets omfång i formuläret hamnar deras svar under underrubriken **Projektomfång** och ersätter taggen du nyss lade till. När du är klar med att lägga till taggar bör en del av dokumentet se ut som skärmbilden nedan.

![Skärmbild av att lägga till taggar i Word-dokument](assets/sales_4.png)

## Använda API:erna

Gå till [!DNL Acrobat Services] API:er [startsidan](https://developer.adobe.com/document-services/apis/doc-generation). Om du vill börja använda [!DNL Acrobat Services] API:er behöver du autentiseringsuppgifter för ditt program. Bläddra hela vägen ned och välj **Starta kostnadsfri provperiod** för att skapa autentiseringsuppgifter. Du kan använda de här tjänsterna [kostnadsfritt i sex månader och sedan betala per användning](https://developer.adobe.com/document-services/pricing/main) för bara $0,05 per dokumenttransaktion, så du betalar bara för det du behöver.

Markera **PDF Services API** som önskad tjänst och fyll i övriga uppgifter som visas nedan.

![Skärmbild av att hämta autentiseringsuppgifter](assets/sales_5.png)

När du har skapat dina inloggningsuppgifter får du några kodexempel. Välj önskat språk (i den här självstudiekursen används Node.js). Dina API-uppgifter finns i en zip-fil. Extrahera filerna till PDFToolsSDK-Node.jsSamples.

Starta genom att skapa en tom mapp med namnet auto-doc\*\*.\*\* Kör följande kommando i mappen för att initiera ett Node.js-projekt: `npm init`. Ge projektet namnet &quot;auto-doc&quot;*.*

I mappen ./PDFToolsSDK-Node.jsSamples/adobe-dc-pdf-tools-sdk-node-samples finns det en fil som heter pdftools-api-credentials.json. Flytta den och private.key till mappen auto-doc. Det innehåller dina API-inloggningsuppgifter. I mappen med automatiska dokument skapar du även en undermapp med namnet &quot;resources&quot;. Den innehåller JSON-formaterade data som tas emot från klienter när du genererar ett försäljningsförslag. Spara mallen för försäljningsförslag från Microsoft Word i samma mapp.

Nu är du redo att göra lite magi! Eftersom du använder Node.js i den här självstudiekursen måste du installera Node.js [!DNL Acrobat Services] SDK. För att göra detta kör du garn lägg till @adobe/documentservices-pdftools-node-sdk i mappen auto-doc.

Skapa nu en fil som heter merge.js och klistra in följande kod i den.

```
javascript
const PDFToolsSdk = require('@adobe/documentservices-pdftools-node-sdk'),
fs = require('fs');
try {
// Initial setup, create credentials instance.
const credentials = PDFToolsSdk.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("pdftools-api-credentials.json")
.build();
// Setup input data for the document merge process
const jsonString = fs.readFileSync('resources/Proposal.json'),
jsonDataForMerge = JSON.parse(jsonString);
// Create an ExecutionContext using credentials
const executionContext = PDFToolsSdk.ExecutionContext.create(credentials);
// Create a new DocumentMerge options instance
const documentMerge = PDFToolsSdk.DocumentMerge,
documentMergeOptions = documentMerge.options,
options = new documentMergeOptions.DocumentMergeOptions(jsonDataForMerge, documentMergeOptions.OutputFormat.PDF);
// Create a new operation instance using the options instance
const documentMergeOperation = documentMerge.Operation.createNew(options)
// Set operation input document template from a source file.
const input = PDFToolsSdk.FileRef.createFromLocalFile('resources/Proposal.docx');
documentMergeOperation.setInput(input);
// Execute the operation and Save the result to the specified location.
documentMergeOperation.execute(executionContext)
.then(result => result.saveAsFile('output/Proposal.pdf'))
.catch(err => {
if (err instanceof PDFToolsSdk.Error.ServiceApiError
|| err instanceof PDFToolsSdk.Error.ServiceUsageError) {
console.log('Exception encountered while executing operation', err);
} else {
console.log('Exception encountered while executing operation', err);
}
});
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
```

Den här koden hämtar JSON-filen från Microsoft-formuläret med hjälp av taggarna som du skapade med [!DNL Acrobat Services]. Sedan sammanfogas uppgifterna med den försäljningsförslagsmall du skapade i Microsoft Word för att skapa ett helt nytt PDF. PDF sparas i det nya dokumentet./utdatamapp.

Dessutom använder koden [Adobe Sign API](https://developer.adobe.com/adobesign-api/) för att båda företagen ska signera det genererade försäljningsförslaget. Titta i det här blogginlägget om du vill ha en detaljerad förklaring av API:et.

## Nästa steg

Du började med en ineffektiv, långtråkig process som krävde automatisering. Du gick från att skapa dokument manuellt för varje kund till att skapa ett smidigt arbetsflöde för att automatisera och förenkla [processen för försäljningsförslag](https://developer.adobe.com/document-services/use-cases/agreements-and-contracts/sales-proposals-and-contracts).

Genom att använda Microsoft Forms fick du viktiga data från dina kunder som skulle ingå i deras unika förslag. Du har skapat en mall för försäljningsförslag i Microsoft Word för att tillhandahålla den statiska text som du inte vill återskapa varje gång. Sedan använde du [!DNL Acrobat Services] API:er för att sammanfoga data från formuläret och mallen för att skapa ett försäljningsförslag PDF för dina kunder på ett mer effektivt sätt.

Den här praktiska självstudiekursen är bara en glimt av vad som är möjligt med dessa API:er. Gå till sidan [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) API:er om du vill upptäcka fler lösningar. Använd alla dessa verktyg är gratis i sex månader. Betala sedan bara $0,05 per dokumenttransaktion på [fördelningsplanen](https://developer.adobe.com/document-services/pricing/main), så att du bara betalar när teamet lägger till fler potentiella kunder i säljprojektet.
