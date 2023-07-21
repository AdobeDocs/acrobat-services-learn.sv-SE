---
title: Skapa och redigera rapporter
description: Lär dig hur du genererar kundrapporter för PDF på din webbplats
role: Developer
level: Intermediate
type: Tutorial
thumbnail: KT-8093.jpg
jira: KT-8093
exl-id: 2f2bf1c2-1b33-4eee-9fd2-5d0b77e6b0a9
source-git-commit: 2d1151c17dfcfa67aca05411976f4ef17adf421b
workflow-type: tm+mt
source-wordcount: '1346'
ht-degree: 1%

---

# Skapa och redigera rapporter

![BANDEROLL MED ANVÄNDNINGSFALL](assets/UseCaseReportHero.jpg)

Finans-, utbildnings-, marknadsförings- och andra branscher använder PDF för att dela data med kunder och intressenter. PDF gör det enkelt att dela dokument med tabeller, grafik och interaktivt material i ett format som alla kan visa. [!DNL Adobe Acrobat Services] Med API:er kan dessa företag generera delbara PDF-rapporter från Microsoft Word, Microsoft Excel, grafik och andra olika dokumentformat.

Säg att du [driva ett företag för spårning av sociala medier](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html). Dina kunder loggar in på en lösenordsskyddad del av webbplatsen för att se sina kampanjanalyser. Ofta vill de dela denna statistik med sina chefer, aktieägare, givare eller andra intressenter. Hämtningsbara PDF-dokument är ett bra sätt för dina kunder att dela siffror, diagram med mera.

Genom att införliva [PDF Services API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-tools.html) till din webbplats kan du skapa PDF-rapporter för varje kund när du är på språng. Du kan skapa PDF och sedan kombinera dem till en enda praktisk rapport som dina kunder kan ladda ned och skicka vidare till sina intressenter.

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du använder PDF Services SDK i miljöerna Node.js och Express.js (med bara JavaScript, HTML och CSS) för att snabbt och enkelt lägga till PDF-orienterade funktioner på en webbplats. På den här webbplatsen finns en sida där administratörer överför rapporter, där man kan se en lista över tillgängliga rapporter och välja dokument som ska konverteras till PDF samt praktiska slutpunkter för att hämta PDF som genererats av systemet.

## Relevanta API:er och resurser

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

## Instrumentpanel för kampanjrapporter för kunder

>[!NOTE]
>
>Den här självstudiekursen handlar inte om de bästa metoderna för Node.js eller hur du skyddar dina webbprogram. Vissa delar av webbplatsen visas för allmänheten, och namngivning av dokument kan vara oproduktionsvänligt. För att diskutera bästa möjliga tillvägagångssätt för att designa ett system som detta, konsultera dina arkitekter och ingenjörer.

Här har du ett grundläggande Express.js-webbprogram med ett kundrapporteringsområde och ett administratörsavsnitt. Det här programmet kan visa rapporter för kampanjer i sociala medier. Den kan till exempel visa hur många gånger en annons klickas.

![Skärmdump av hur man får personaliserade rapporter](assets/report_1.png)

Du kan hämta projektet från [GitHub-databas](https://github.com/afzaal-ahmad-zeeshan/express-adobe-pdf-tools).

Låt oss nu utforska hur vi kan publicera rapporterna.

## Överför rapporter

För enkelhetens skull bör du bara använda filsystemsbaserad uppladdning och bearbetning här. I Express.js kan du använda modulen fs för att lista alla tillgängliga filer under en katalog.

På samma sida kan administratören överföra rapportfiler till servern så att kunderna kan se dem. Dessa filer kan ha många olika format, t.ex. Microsoft Word, Microsoft Excel, HTML och [andra dataformat]https://opensource.adobe.com/pdftools-sdk-docs/release/latest/howtos.html#create-a-pdf) inklusive grafikfiler. Administratörssidan ser ut så här:

![Skärmdump av administratörsfunktioner](assets/report_2.png)

>[!NOTE]
>
>Lösenordsskydda dina URL:er eller använd passpaketet från npm för att skydda programmet bakom autentiserings- och auktoriseringsskiktet.

När administratören väljer och överför en fil, flyttas den till en offentlig databas dit andra kan komma åt den. Du använder samma databas för att publicera dokument från administratörssidan och visa tillgängliga marknadsföringsrapporter för kunder. Denna kod är:

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

Den här koden listar alla filer och återger en vy av fillistan.

## Välja rapporter

På användarsidan har du ett formulär där kunderna kan välja de dokument som de vill inkludera i sin rapport över sociala medier-kampanjer. För enkelhetens skull visas bara dokumentnamnet och en kryssruta på exempelsidan för att markera dokumentet. Man kan välja en eller flera rapporter som ska sammanställas i ett och samma PDF-dokument.

För ett mer avancerat användargränssnitt kan du även visa en förhandsgranskning av rapporten här.

![Skärmdump av kundens kapacitet](assets/report_3.png)

## Generera en PDF-rapport

Använd PDF Services SDK för att skapa PDF-rapporter från dina indata. Informationen (som visas på skärmbilderna ovan) kan komma från olika dataformat som Microsoft Word, Microsoft Excel, HTML, grafik med mera. Börja med att installera npm-paketet för PDF Services SDK.

```
$ npm install --save @adobe/documentservices-pdftools-node-sdk
```

Innan du börjar måste du ha API-uppgifter, [fri från Adobe](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html#getcred). Använd din [!DNL Acrobat Services] konto [gratis i sex månader och sedan betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) för endast 0,05 USD per dokumenttransaktion.

Hämta arkivfilen och extrahera JSON-filen för inloggningsuppgifter och den privata nyckeln. I exempelprojektet placerar du filen i src-katalogen.

![Skärmdump av src-katalog](assets/report_4.png)

Nu när du har konfigurerat inloggningsuppgifterna kan du skriva konverteringsaktiviteten för PDF. I den här demonstrationen finns det två åtgärder som du måste utföra i programmet:

* Konvertera raw-dokument till PDF-filer

* Kombinera flera PDF-filer i en och samma rapport

Den övergripande proceduren är densamma för alla operationer. Den enda skillnaden är den tjänst du använder. I följande exempel konverterar du raw-dokumentet till en PDF-fil:

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

I koden ovan läser du autentiseringsuppgifterna och skapar körningskontexten. PDF Services SDK behöver körningskontexten för att autentisera dina begäranden.

Sedan kör du åtgärden Skapa PDF som konverterar raw-dokument till PDF-format. Slutligen använder du `outputPdf` -parametern för att kopiera PDF-rapporten. I kodexemplet hittar du den här koden under filen src/helpers/pdf.js . Senare i den här självstudiekursen importerar du modulen PDF och anropar den här metoden.

Som visas i föregående avsnitt kan dina kunder gå till följande sida för att välja de rapporter som de vill konvertera till PDF:

![Skärmdump av kundens kapacitet](assets/report_3.png)

När en kund väljer en eller flera av dessa rapporter skapas PDF-filen.

Låt oss först se en enda PDF-fil i aktion. När en användare markerar en enskild rapport behöver du bara konvertera den till PDF och ange hämtningslänken.

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

![Skärmdump av kundnedladdningsskärmen](assets/report_5.png)

Och här är resultatet PDF:

![Skärmdump av generisk rapport](assets/report_6.png)

Kunder kan välja flera filer för att generera en kombinerad rapport. När kunden väljer mer än ett dokument utför du två åtgärder: I det första dokumentet skapas en del av PDF för varje dokument och i det andra sammanförs de till ett enda PDF-betänkande.

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

Den här metoden är tillgänglig under filen src/helpers/pdf.js och visas som en del av modulexporten.

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

Den här koden genererar en kompilerad rapport för flera inmatningsdokument. Den enda tillagda funktionen är `combinePdf` metod som tar en lista med PDF-filsökvägsnamn och returnerar ett PDF.

Nu kan era kontrollpanelskunder för sociala medier välja aktuella rapporter från sitt konto och hämta dem som en behändig PDF. På den här instrumentpanelen kan de visa hur väl ledningen och andra intressenter lyckas med sina kampanjer med data, tabeller och diagram i ett universellt lättöppnat format.

## Nästa steg

Den här praktiska självstudiekursen gick igenom hur man använder API:et för PDF-tjänster för att hjälpa kunderna att ladda ned rapporter som lättanvända PDF. Du har skapat ett Node.js-program för att visa upp kraften i PDF Services API för PDF-rapporterings- och lästjänster. Applikationen visade hur kunderna kunde ladda ned ett enda rapportdokument eller kombinera och slå samman flera dokument till en enda PDF-rapport.

Med det här programmet från Adobe kan du [kunder med instrumentpanel för sociala medier](https://www.adobe.io/apis/documentcloud/dcsdk/on-demand-report-creation.html) få och dela de rapporter de behöver, utan att behöva oroa sig för om alla mottagare har Microsoft Office eller andra program installerade på sin enhet. Du kan använda samma tekniker i ditt eget program för att hjälpa användarna att visa, kombinera och hämta dokument. Eller kolla in Adobe många andra API:er för att lägga till och spåra signaturer och mycket mer.

Kom igång genom att göra anspråk på din kostnadsfria [[!DNL Adobe Acrobat Services]](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) och skapa sedan engagerande rapporteringsupplevelser för era medarbetare och kunder. Njut av ditt konto gratis i sex månader sedan [betala per användning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html) i takt med att marknadsföringen ökar: endast 0,05 USD per dokumenttransaktion.
