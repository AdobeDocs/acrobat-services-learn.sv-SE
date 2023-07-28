---
title: Skapa och redigera rapporter
description: Lär dig hur du genererar kundrapporter på din webbplats för PDF
role: Developer
level: Intermediate
type: Tutorial
feature: Use Cases
thumbnail: KT-8093.jpg
jira: KT-8093
exl-id: 2f2bf1c2-1b33-4eee-9fd2-5d0b77e6b0a9
source-git-commit: b65ffa3efa3978587564eb0be0c0e7381c8c83ab
workflow-type: tm+mt
source-wordcount: '1346'
ht-degree: 1%

---

# Skapa och redigera rapporter

![Banderoll för användningsfall](assets/UseCaseReportHero.jpg)

Ekonomi, utbildning, marknadsföring och andra branscher använder PDF för att dela data med sina kunder och intressenter. PDF gör det enkelt att dela interaktiva dokument med tabeller, grafik och interaktivt innehåll i ett format som alla kan visa. [!DNL Adobe Acrobat Services] Med API:er kan dessa företag generera delbara PDF-rapporter från Microsoft Word, Microsoft Excel, grafik och andra olika dokumentformat.

Säg dig [driva ett företag för spårning i sociala medier](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html). Dina kunder loggar in på en lösenordsskyddad del av webbplatsen för att se sina kampanjanalyser. Ofta vill de dela denna statistik med sina chefer, aktieägare, givare eller andra intressenter. Hämtningsbara PDF-dokument är ett bra sätt för dina kunder att dela siffror, diagram med mera.

Genom att [PDF Services API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) till din webbplats, kan du generera PDF rapporter på språng för varje kund. Du kan skapa PDF och sedan kombinera dem till en enda, praktisk rapport som dina kunder kan hämta och skicka vidare till sina intressenter.

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du använder PDF Services SDK i en miljö med Node.js och Express.js (med bara vissa JavaScript, HTML och CSS) för att snabbt och enkelt lägga till PDF-orienterad funktionalitet på en befintlig webbplats. På den här webbplatsen finns en sida där administratörer överför rapporter, ett område där kunder visar en lista med tillgängliga rapporter och väljer dokument som ska konverteras till PDF samt användbara slutpunkter för att hämta PDF som genererats av systemet.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

## Instrumentpanel för kampanjrapporter för kunder

>[!NOTE]
>
>Den här självstudiekursen handlar inte om de bästa metoderna för Node.js eller hur du skyddar dina webbprogram. Vissa delar av webbplatsen visas för allmänheten, och namngivning av dokument kan vara oproduktionsvänligt. För att diskutera bästa möjliga tillvägagångssätt för att utforma ett system som detta, rådgör med dina arkitekter och ingenjörer.

Här har du ett grundläggande Express.js-webbprogram med ett kundrapporteringsområde och ett administratörsavsnitt. Den här appen kan visa upp rapporter för kampanjer i sociala medier. Den kan t.ex. visa hur många gånger en annons klickas.

![Skärmbild av hur du får personliga rapporter](assets/report_1.png)

Du kan hämta det här projektet från [GitHub-databas](https://github.com/afzaal-ahmad-zeeshan/express-adobe-pdf-tools).

Låt oss nu utforska hur vi publicerar rapporterna.

## Överför rapporter

För att göra det enkelt bör du bara använda den filsystemsbaserade överföringen och bearbetningen här. I Express.js kan du använda modulen fs för att lista alla tillgängliga filer under en katalog.

Aktivera administratören att överföra rapportfiler till servern så att kunderna kan se dem på samma sida. De här filerna kan ha många olika format, t.ex. Microsoft Word, Microsoft Excel, HTML och [andra dataformat]https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) inklusive grafikfiler. Administratörssidan ser ut så här:

![Skärmbild av administratörsfunktionen](assets/report_2.png)

>[!NOTE]
>
>Lösenordsskydda dina URL:er eller använd passpaketet från npm för att skydda ditt program bakom autentiserings- och auktoriseringslagret.

När administratören väljer och överför en fil flyttas den till en offentlig databas där andra har åtkomst till den. Du använder samma databas för att publicera dokument från administratörssidan och visa tillgängliga marknadsföringsrapporter för kunder. Denna kod är:

```
router.get('/', (req, res) => {
try {
let files = fs.readdirSync('./public/documents/raw') // read the files
res.status(200).render("reports", { page: 'reports', files: files });
} catch (error) {
res.status(500).render("crash", { error: error });
}
});
```

Den här koden listar alla filer och visar fillistan.

## Välja rapporter

På användarsidan har du ett formulär där kunderna kan välja de dokument som de vill inkludera i sin rapport om kampanjer i sociala medier. För enkelhetens skull kan du på exempelsidan bara visa dokumentnamnet och en kryssruta för att markera dokumentet. Man kan välja en enstaka rapport eller flera rapporter som ska kombineras i ett enda PDF-dokument.

Om du vill ha ett mer avancerat användargränssnitt kan du även visa en förhandsgranskning av rapporten här.

![Skärmbild av kundkapacitet](assets/report_3.png)

## Generera en PDF-rapport

Använd PDF Services SDK för att skapa PDF-rapporter från dina dataindata. Data (som visas i skärmbilderna ovan) kan komma från olika dataformat som Microsoft Word, Microsoft Excel, HTML, grafik, med mera. Börja med att installera npm-paketet för PDF Services SDK.

```
$ npm install --save @adobe/documentservices-pdftools-node-sdk
```

Innan du börjar måste du ha API-inloggningsuppgifter, [fri från Adobe](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred). Använd din [!DNL Acrobat Services] redogörelse [i sex månader och sedan betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för bara \$0,05 per dokumenttransaktion.

Hämta arkivfilen och extrahera JSON-filen för inloggningsuppgifter och den privata nyckeln. I exempelprojektet placerar du filen i src-katalogen.

![Skärmbild av src-katalogen](assets/report_4.png)

Nu när du har ställt in autentiseringsuppgifterna kan du skriva konverteringsaktiviteten PDF. För den här demonstrationen har du två åtgärder som du måste utföra i programmet:

* Konvertera raw-dokument till PDF-filer

* Kombinera flera PDF-filer i en enda rapport

Den övergripande proceduren är densamma för att köra alla operationer. Den enda skillnaden är den tjänst du använder. I följande exempel konverterar du raw-dokumentet till en PDF-fil:

```
async function createPdf(rawFile, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
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

I koden ovan läser du autentiseringsuppgifterna och skapar körningskontexten. SDK för PDF-tjänster kräver körningskontexten för att autentisera dina förfrågningar.

Sedan kör du åtgärden Skapa PDF som konverterar raw-dokumenten till PDF-format. Slutligen använder du `outputPdf` -parametern för att kopiera PDF-rapporten. I kodexemplet hittar du den här koden under filen src/helpers/pdf.js. Senare i den här självstudiekursen importerar du PDF-modulen och anropar den här metoden.

Som visas i föregående avsnitt kan dina kunder gå till följande sida för att välja de rapporter som de vill konvertera till PDF:

![Skärmbild av kundkapacitet](assets/report_3.png)

När en kund väljer en eller flera av dessa rapporter skapas PDF-filen.

Först ska vi se hur en enda PDF-fil fungerar. När en användare markerar en enskild rapport, behöver du bara konvertera den till PDF och ange hämtningslänken.

```
try {
console.log(`[INFO] generating the report...`);
await pdf.createPdf(`./public/documents/raw/${reports}`, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the report...`);
res.status(200).render("download", { page: 'reports', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

Den här koden skapar en rapport och delar hämtnings-URL:en med kunden. Här är webbsidan för utdata:

![Skärmbild av kundens nedladdningsskärm](assets/report_5.png)

Och här är resultatet PDF:

![Skärmbild av allmän rapport](assets/report_6.png)

Kunder kan välja flera filer för att generera en kombinerad rapport. När kunden markerar mer än ett dokument utför du två åtgärder: i det första skapas en partiell PDF för varje dokument och i det andra kombineras de i en PDF-rapport.

```
async function combinePdf(pdfs, outputPdf) {
try {
// configurations
const credentials = adobe.Credentials
.serviceAccountCredentialsBuilder()
.fromFile("./src/pdftools-api-credentials.json")
.build();
// Capture the credential from app and show create the context
const executionContext = adobe.ExecutionContext.create(credentials),
operation = adobe.CombineFiles.Operation.createNew();
// Pass the PDF content as input (stream)
for (let pdf of pdfs) {
const source = adobe.FileRef.createFromLocalFile(pdf);
operation.addInput(source);
}
// Async create the PDF
let result = await operation.execute(executionContext);
await result.saveAsFile(outputPdf);
} catch (err) {
console.log('Exception encountered while executing operation', err);
}
}
```

Den här metoden finns under filen src/helpers/pdf.js och visas som en del av modulexporten.

```
try {
console.log(`[INFO] creating a batch report...`);
// Create a batch report and send it back
let partials = [];
for (let index in reports) {
const name = `partial-${index}-${reports[index]}`;
await pdf.createPdf(`./public/documents/raw/${reports[index]}`, `./public/documents/processed/${name}`);
partials.push(`./public/documents/processed/${name.replace('docx', 'pdf').replace('xlsx', 'pdf')}`);
}
await pdf.combinePdf(partials, `./public/documents/processed/output.pdf`);
console.log(`[INFO] sending the combined report...`);
res.status(200).render("download", { page: 'reports', filename: 'output.pdf' });
} catch(error) {
console.log(`[ERROR] ${JSON.stringify(error)}`);
res.status(500).render("crash", { error: error });
}
```

Den här koden genererar en kompilerad rapport för flera inmatningsdokument. Den enda tillagda funktionen är `combinePdf` metod som tar en lista med sökvägsnamn för PDF och returnerar ett enda PDF.

Nu kan dina kontrollpanelskunder för sociala medier välja aktuella rapporter från sitt konto och hämta dem som en behändig PDF. På den här instrumentpanelen kan de visa hur ledningen och andra intressenter lyckas med sina kampanjer med data, tabeller och diagram i ett allmänt lättöppnat format.

## Nästa steg

Denna praktiska självstudiekurs gick igenom hur man använder PDF Services API för att hjälpa kunder att ladda ner rapporter som är enkla att dela PDF. Du har skapat ett Node.js-program för att visa upp kraften i PDF Services API för rapporterings- och lästjänster för PDF. Programmet visar hur dina kunder kan hämta ett enda rapportdokument eller kombinera och sammanfoga flera dokument till en enda PDF-rapport.

Detta program med Adobe-teknik hjälper dina [kunder med instrumentpaneler för sociala medier](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html) få och dela de rapporter de behöver, utan att oroa sig för om alla mottagare har Microsoft Office eller annan programvara installerad på sin enhet. Du kan använda samma tekniker i ditt eget program till att hjälpa användarna att visa, kombinera och hämta dokument. Eller kolla in Adobe många andra API:er för att lägga till och spåra signaturer och mycket mer.

Kom igång genom att göra anspråk på din kostnadsfria [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) -konto och sedan skapa engagerande rapporteringsupplevelser för dina anställda och kunder. Njut av ditt konto gratis i sex månader sedan [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) när dina marknadsföringsinsatser utökas, bara \$0,05 per dokumenttransaktion.
