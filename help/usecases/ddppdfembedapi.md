---
title: Digital dokumentpublicering
description: Lär dig hur du visar inbäddade PDF-dokument på webbsidor med Adobe PDF Embed API
feature: Use Cases
role: Developer
level: Intermediate
type: Tutorial
jira: KT-8090
thumbnail: KT-8090.jpg
exl-id: 3aa9aa40-a23c-409c-bc0b-31645fa01b40
source-git-commit: 5222e1626f4e79c02298e81d621216469753ca72
workflow-type: tm+mt
source-wordcount: '1722'
ht-degree: 0%

---

# Publicering av digitala dokument

![Banderoll för användningsfall](assets/UseCaseDigitalHero.jpg)

Elektroniska dokument finns överallt - faktum är att det förmodligen finns [ biljoner PDF](https://itextpdf.com/en/blog/technical-notes/do-you-know-how-many-pdf-documents-exist-world) globalt, och det antalet stiger varje dag. Genom att bädda in ett PDF-visningsprogram på dina webbsidor kan användarna visa dokument utan att designa om HTML och CSS-kod eller hindra åtkomsten till webbplatsen.

Låt oss utforska ett populärt scenario. Ett företag publicerar [informationsdokument på sin webbplats](https://www.adobe.io/apis/documentcloud/dcsdk/digital-content-publishing.html)
för att tillhandahålla kontext för deras program och tjänster. Webbplatsens marknadsförare vill bättre förstå hur användare interagerar med sitt PDF-baserade innehåll och införliva det med sin webbsida och varumärke. De har bestämt sig för att publicera informationsdokumenten som [grupperat innehåll](https://whatis.techtarget.com/definition/gated-content-ungated-content#:~:text=Gated%20content%20is%20online%20materials,about%20their%20jobs%20and%20organizations.), vilket styr vem som kan hämta dem.

## Vad du kan lära dig

I den här praktiska självstudiekursen får du lära dig hur du visar inbäddade PDF-dokument på webbsidor med [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html), som är kostnadsfritt och enkelt att använda. I dessa exempel används JavaScript, Node.js, Express.js, HTML och CSS. Du kan se den fullständiga projektkoden på [GitHub](https://www.google.com/url?q=https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app&amp;sa=D&amp;source=editors&amp;ust=1617129543031000&amp;usg=AOvVaw2rzSwYuJ_JI7biVIgbNMw1).

## Relevanta API:er och resurser

* [PDF Embed API](https://www.adobe.com/devnet-docs/dcsdk_io/viewSDK/index.html)

* [PDF Services API](https://opensource.adobe.com/pdftools-sdk-docs/release/latest/index.html)

* [Projektkod](https://www.google.com/url?q=https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app&amp;sa=D&amp;source=editors&amp;ust=1617129543031000&amp;usg=AOvVaw2rzSwYuJ_JI7biVIgbNMw1)

## Skapa en Node Web App

Vi börjar med att skapa en webbplats med Node.js och Express som använder en snygg mall och erbjuder flera PDF för hämtning.

Först [hämtar och installerar du Node.js](https://nodejs.org/en/download/).

Om du enkelt vill skapa ett Node.js-projekt med en minimal webbprogramstruktur ska du installera programgenereringsverktyget `` `express-generator` ``.

```
npm install express-generator -g
```

Skapa sedan den nya Express-appen som heter pdf-app och välj som visningsmotor.

```
express pdf-app --view=ejs
```

Gå nu till katalogen \\pdf-app och installera alla projektberoenden.

```
cd pdf-app
npm install
```

Starta sedan den lokala webbservern och kör programmet.

```
npm start
```

Öppna slutligen webbplatsen på <http://localhost:3000>.

![Skärmbild av grundläggande webbplats](assets/ddp_1.png)

Du har nu en grundläggande webbplats.

## Renderar vitboksdata

För att lägga upp informationsdokument på webbplatsen definieras och förbereds informationsdokumenten på webbplatsen. Skapa först en ny \\data -mapp i projektets rot. Informationen om tillgängliga informationsdokument kommer från en ny fil med namnet [data.json](https://github.com/marcelooliveira/EmbedPDF/blob/main/pdf-app/data/data.json), som finns i datamappen.

Om du vill ge webbappen ett snyggt och snyggt utseende installerar du [Bootstrap](https://getbootstrap.com/)- och [Font Awesome](https://fontawesome.com/)-klientbiblioteken.

```
npm install bootstrap
npm install font-awesome
```

Öppna filen app.js och inkludera katalogerna som källor för statiska filer. Placera dem efter den befintliga `` `express.static` ``-raden.

```
app.use(express.static(path.join(__dirname, '/node_modules/bootstrap/dist')));
app.use(express.static(path.join(__dirname, '/node_modules/font-awesome')));
```

Om du vill inkludera PDF-dokumenten skapar du en mapp med namnet \\pdfs under projektets \\gemensamma mapp. Istället för att skapa PDF och miniatyrbilderna själv kan du kopiera dem från [GitHub-databasmappen](https://github.com/marcelooliveira/EmbedPDF/tree/main/pdf-app/public) till \\pdfs- och \\bildmapparna.

Mappen \\public\\pdfs innehåller nu PDF-dokument:

![Skärmbild av PDF-filikon](assets/ddp_2.png)

Mappen \\public\\images ska innehålla miniatyrbilderna för vart och ett av PDF-dokumenten:

![Skärmbild av PDF-miniatyrbilder](assets/ddp_3.png)

Öppna nu filen \\vägar\\index.js, som innehåller logiken för dirigering av startsidan. Om du vill använda whitepaper-data från filen data.json måste du läsa in modulen Node.js som ansvarar för åtkomst till och interaktion med filsystemet. Deklarera sedan konstanten `fs` på den första raden i filen \\route\\index.js, enligt följande:

```
const fs = require('fs');
```

Läs och analysera sedan filen data.json och spara dem i pappersvariabeln:

```
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
```

Nu kan du ändra raden så att renderingsmetoden för indexvyn används och skicka papperssamlingen som modell för indexvyn.

```
res.render('index', { title: 'Embedding PDF', papers: papers });
```

Om du vill återge samlingen med informationsdokument på startsidan öppnar du filen \\views\\index.ejs och ersätter den befintliga koden med koden från projektets [indexfil](https://github.com/marcelooliveira/EmbedPDF/blob/main/pdf-app/views/index.ejs).

Kör nu om npm start och öppna <http://localhost:3000> för att visa samlingen med tillgängliga informationsdokument.

![Skärmbild av miniatyrbilder för informationsdokument](assets/ddp_4.png)

I de följande avsnitten förbättrar du webbplatsen och använder [PDF Embed API](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-embed.html) för att visa PDF-dokumenten på webbsidan. PDF Embed API är gratis att använda - du behöver bara få en API-uppgift.

## Hämta en PDF Embed API-autentiseringsuppgift

Om du vill hämta en kostnadsfri API-inloggningsuppgift för PDF Embed går du till sidan [Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) när du har registrerat dig för ett nytt konto eller loggat in till ditt gamla konto.

Klicka på **Skapa nya autentiseringsuppgifter** och sedan **Kom igång:**

![Skärmbild av hur du skapar nya autentiseringsuppgifter](assets/ddp_5.png)

Nu ombeds du att registrera dig för ett kostnadsfritt konto om du inte har ett.

Markera **PDF Embed API** och ange sedan ditt inloggningsnamn och din programdomän. Använd domänen **localhost** på grund av att webbprogrammet testas lokalt.

![Skärmbild av att skapa nya autentiseringsuppgifter för PDF Embed API](assets/ddp_6.png)

Klicka på knappen **Skapa autentiseringsuppgifter** för att komma åt dina autentiseringsuppgifter för PDF och hämta klient-ID (API-NYCKEL).

![Skärmbild av hur nya autentiseringsuppgifter kopieras](assets/ddp_7.png)

Skapa en fil med namnet .ENV i programmets rotmapp i Node.js-projektet och deklarera miljövariabeln för ditt PDF Embed Client ID med API KEY-autentiseringsuppgiften från föregående steg.

```
PDF_EMBED_CLIENT_ID=**********************************************
```

Du använder sedan detta klient-ID för att komma åt PDF Embed API. Installera dotenv-paketet för att få åtkomst till miljövariabeln med hjälp av koden Node.js.

```
npm install dotenv
```

Öppna nu filen app.js och lägg till följande rad högst upp i filen så att Node.js kan läsa in dotenv-modulen:

```
require('dotenv').config();
```

## Visa PDF i webbappen

Använd nu PDF Embed API för att visa PDF på webbplatsen. Öppna live-demon [PDF Embed API](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf).

![Skärmbild av live PDF Embed API-demo](assets/ddp_8.png)

På den vänstra panelen kan du välja det inbäddningsläge som bäst passar dina webbplatsbehov:

* **Fullständigt fönster**: PDF täcker hela webbsidans utrymme

* **Behållare med storlek**: PDF visas inuti webbsidan, en sida i taget, i en div med begränsad storlek

* **Inline**: Hela PDF visas i en div på webbsidan

* **Ljuslåda**: PDF visas som ett lager ovanpå webbsidan

Vi rekommenderar att du använder inbäddningsläget In-line för informationsdokument och kodgeneratorn för att sedan bädda in en PDF i programmet.

## Skapa en inbäddningssida i det inbyggda läget

Om du vill bädda in ett PDF-visningsprogram på din webbsida och visa alla sidorna samtidigt, skapar du en ny-sida med hjälp av det inbyggda inbäddningsläget.

Skapa en ny vy i filen \\views\\in-line.ejs med EJS-vymotorn.

```
<! html DOCTYPE >
<html>
<head>
<title>
<%= title %>
</title>
<link rel='stylesheet' href='/stylesheets/style.css' />
<link rel='stylesheet' href='/css/bootstrap.min.css'/>
<link rel='stylesheet' href='/css/font-awesome.min.css' />
<style type="text/css">
p {
font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
}
</style>
</head>
<body class="m-0">
<div>
<main>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<h3>
<p class="text-center">Grow your business, establish your brand,<br
/>
```

Och låt kunderna komma i första hand.

```
</p>
</h3>
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
</div>
</main>
<footer>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<p class="text-center">Bodea Inc. Your trusted partner since 2008</p>
</div>
</div>
</footer>
</div>
</div>
</body>
</html>
```

Du ändrar sedan \\views\\index.ejs för att skapa en knapp för att öppna den textbundna vyn.

```
<div class="card-body">
<h5 class="card-title">
<span>
<%= paper.title %>
</span>
</h5>
<p>
<a class="btn btn-sm btn btn-danger" href="/in-line/<%=
paper.id %>">
<span type="button"></span>
<span class="fa fa-file-pdf-o"></span>&nbsp;View Document</button>
</a>
</p>
</div>
```

Öppna filen app.js och deklarera en ny router efter indexRouter-deklarationen:

```
var indexRouter = require('./routes/index');
var inLineRouter = require('./routes/in-line');
```

Lägg sedan till koden efter app.use(&#39;/&#39;, indexRouter); för att associera den infogade inbäddningslägesvyn med dess router:

```
app.use('/', indexRouter);
app.use('/in-line', inLineRouter);
```

Skapa nu en ny in-line.js -fil under \\vägar för att skapa ny routerlogik. Inkludera Express, en nodmodul som möjliggör en backend-webbapplikation.

```
var express = require('express');
const fs = require('fs');
var router = express.Router();
```

Skapa sedan en slutpunkt som hanterar GET-förfrågningar för ett specifikt whitepaper-ID och återger vyn in-line.ejs.

```
router.all('/:id', function(req, res, next) {
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
let paper = papers.filter(p => p.id == parseInt(req.params.id))[0];
res.render('in-line', { title: paper.title, paper: paper });
});
module.exports = router;
```

Titta på [live-demon](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf) igen om du vill generera PDF Embed API-kod automatiskt. Klicka på **Inline** i den vänstra panelen:

![Skärmbild av live PDF Embed API-demo](assets/ddp_8.png)

Klicka på **Generera kod** för att se vilken HTML-kod som krävs för att visa PDF-visningsprogrammet för behållarstorlek.

![Skärmbild av kodförhandsgranskning](assets/ddp_9.png)

Klicka på **Kopiera kod** och klistra in koden i filen in-line.ejs.

```
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
<div class="row align-items-center border border-primary">
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function(){
var adobeDCView = new AdobeDC.View({clientId: "<YOUR_CLIENT_ID>", divId: "adobe-dc-view"});
adobeDCView.previewFile({
content:{location: {url: "https://documentcloud.adobe.com/view-sdk-demo/PDFs/Bodea Brochure.pdf"}},
metaData:{fileName: "Bodea Brochure.pdf"}
}, {embedMode: "IN_LINE"});
});
</script>
</div>
```

Dokumentparametrarna är dock fortfarande hårdkodade. Vi ersätter dem med EJS-gafflingens syntax (\&lt;%= someValue %\>) för att återge sidan enligt modelldata i informationsdokumentet.

```
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function () {
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url: "<%= paper.pdf %>" } },
metaData: { fileName: "<%= paper.fileName %>" }
}, {
embedMode: "IN_LINE"
});
});
</script>
```

Kör nu programmet med kommandot npm start och öppna webbplatsen på <http://localhost:3000>.

![Skärmbild av miniatyrbilder av PDF-vitt papper](assets/ddp_10.png)

Välj till sist ett faktablad och klicka på **Visa dokument** för att öppna en ny sida med den textbundna inbäddade PDF:

![Skärmbild av PDF-rapporten ](assets/ddp_11.png)

Lägg märke till hur alternativen Hämta PDF och Skriv ut PDF nu finns.

![Skärmbild av nedladdnings- och utskriftsalternativ](assets/ddp_12.png)

Du vill kontrollera flaggorna på servern. Senare kan du implementera auktoriseringskontroller baserat på användaridentitet och begränsa åtkomsten enligt dina affärsregler. Komplexiteten behövs inte här, så vi ändrar \\vägar\\in-line.js för att inkludera autentiserade egenskaper och behörighetsegenskaper i modellobjektet.

```
let authenticated = false;
res.render('in-line', {
title: paper.title,
paper: paper,
authenticated: authenticated,
permissions: {
showDownloadPDF: true,
showPrintPDF: true,
showFullScreen: true
}
});
```

Ändra sedan \\views\\in-line.ejs så att din webbsida kan återge flaggvärden som kommer från backend.

```
embedMode: "IN_LINE",
showDownloadPDF: <%= permissions.showDownloadPDF %>,
showPrintPDF: <%= permissions.showPrintPDF %>,
showFullScreen: <%= permissions.showFullScreen %>
Now, open the in-line.js route file and modify it to disallow the printing, downloading, and full-screen controls.
permissions: {
showDownloadPDF: false,
showPrintPDF: false,
showFullScreen: false
}
```

Kör sedan programmet igen för att se hur den här ändringen visas i PDF.

![Skärmbild av PDF-fil](assets/ddp_13.png)

## Skapa spärrat innehåll

Enligt slutanvändarscenariot vill marknadsföraren för bolagets webbplats bättre förstå hur användare interagerar med sitt innehåll som är baserat i PDF och införliva det med det övriga innehållet på sin webbsida och sitt varumärke.

Vi fokuserar på PDF-inbäddning, så du ska inte skapa en användarautentiseringsfunktion. Istället, bara implementera en enkel, falsk betalvägg med hjälp av ett webbformulär som accepterar viss användarinformation sedan visas PDF dokumentet när användaren skickar formuläret.

Ersätt filen \\route\\in-line.js med innehållet nedan och förse vymodellen med användarinformation:

```
var express = require('express');
const fs = require('fs');
var router = express.Router();
router.all('/:id', function(req, res, next) {
let rawdata = fs.readFileSync('data/data.json');
let papers = JSON.parse(rawdata);
let paper = papers.filter(p => p.id == parseInt(req.params.id))[0];
let authenticated = false;
let user = {};
if (req.body.firstName) {
user = {
firstName: req.body.firstName,
lastName: req.body.lastName,
jobTitle: req.body.jobTitle,
email: req.body.email,
};
authenticated = true;
}
res.render('in-line', {
title: paper.title,
paper: paper,
user: user,
authenticated: authenticated,
permissions: {
showDownloadPDF: false,
showPrintPDF: false,
showFullScreen: false
}
});
});
module.exports = router;
```

Ersätt sedan \\views\\in-line.ejs-innehållet med koden nedan. Där visas användardataformuläret eller PDF-visningsprogrammet, beroende på om det är en autentiserad användare.

```
<!DOCTYPE html>
<html>
<head>
<title>
<%= title %>
</title>
<link rel='stylesheet' href='/css/bootstrap.min.css'/>
<link rel='stylesheet' href='/css/font-awesome.min.css' />
<style type="text/css">
p {
font-family: 'Gill Sans', 'Gill Sans MT', Calibri, 'Trebuchet MS', sans-serif
}
</style>
</head>
<body class="m-0">
<% if (authenticated) { %>
<header class="bg-dark text-white">
<div class="text-right mr-4">Hello, <%= user.firstName %> <%= user.lastName%></div>
</header>
<% } %>
<div>
<main>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<h3>
<p class="text-center">Grow your business, establish your brand,<br
/>
```

Och låt kunderna komma i första hand.

```
</p>
</h3>
<div>
<p class="text-center">Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do<br />
eiusmod tempor incididunt ut labore et dolore</p>
</div>
<% if (!authenticated) { %>
<div class="row">
<form method="POST" class="center-panel text offset-md-3 col-md-6 border">
<fieldset class="offset-md-1">
<legend>Submit your info to<br/>access the whitepaper</legend>
<p><input name="firstName" placeholder="first name"/></p>
<p><input name="lastName" placeholder="last name"/></p>
<p><input name="jobTitle" placeholder="job title"/></p>
<p><input name="email" placeholder="email"/></p>
<p><button type="submit" class="btn btn-sm btn btn-primary">Submit</button></p>
</fieldset>
</form>
</div>
<% } %>
<% if (authenticated) { %>
<div class="row align-items-center border border-primary">
<div id="adobe-dc-view" style="width: 800px;"></div>
<script src="https://documentcloud.adobe.com/view-sdk/main.js"></script>
<script type="text/javascript">
document.addEventListener("adobe_dc_view_sdk.ready", function () {
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.previewFile({
content: { location: { url: "<%= paper.pdf %>" } },
metaData: { fileName: "<%= paper.fileName %>" }
}, {
embedMode: "IN_LINE",
showDownloadPDF: <%= permissions.showDownloadPDF %>,
showPrintPDF: <%= permissions.showPrintPDF %>,
showFullScreen: <%= permissions.showFullScreen %>
});
});
</script>
<% } %>
</div>
</div>
</main>
<footer>
<div class="row">
<div class="col-sm-3"></div>
<div class="col-sm-6">
<p class="text-center">Bodea Inc. Your trusted partner since 2008</p>
</div>
</div>
</footer>
</div>
</div>
</body>
</html>
```

![Skärmbild av portningsinnehåll](assets/ddp_14.png)

Webbplatsbesökare kan nu endast komma åt PDF efter att ha skickat in sina uppgifter:

![Skärmbild av PDF-innehåll i inbäddad visningsprogram](assets/ddp_15.png)

## Aktivera händelser

Nu ska vi se hur du enkelt kan integrera PDF-visningshändelser med ditt program för att samla in analysdata för marknadsföraren. Om du vill utöka visningsprogrammet med PDF EmbedAPI lägger du till följande kodrader efter att variabeln adobeDCView har deklarerats och innan du anropar metoden previewFile:

```
var adobeDCView = new AdobeDC.View({ clientId: "<%=process.env.PDF_EMBED_CLIENT_ID %>", divId: "adobe-dc-view" });
adobeDCView.registerCallback(
AdobeDC.View.Enum.CallbackType.EVENT_LISTENER,
function(event) {
console.log(event);
},
{ enablePDFAnalytics: true }
);
```

Kör nu programmet igen och öppna webbläsarens utvecklarverktyg för att se händelsedata.

![Skärmbild av kod](assets/ddp_16.png)

Du kan skicka dessa data till [Adobe Analytics](https://www.adobe.io/apis/documentcloud/dcsdk/docs.html?view=view) eller andra analysverktyg.

## Nästa steg

[!DNL Acrobat Services] API:er hjälper utvecklare att enkelt lösa problem med digital publicering genom ett arbetsflöde som är centrerat kring PDF. Du har sett hur du skapar en webbapp med exempelnoder för att visa en samling informationsdokument. Sedan skaffar du en [kostnadsfri API-uppgift](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) och skapar begränsad åtkomst till informationsdokumenten, som kan visas i ett av fyra [inbäddningslägen](https://documentcloud.adobe.com/view-sdk-demo/index.html#/view/FULL_WINDOW/Bodea%20Brochure.pdf).

Om du samlar ihop det här arbetsflödet kan [den hypotetiska marknadsföraren](https://www.adobe.io/apis/documentcloud/dcsdk/digital-content-publishing.html) samla in kontaktinformation om kundämnen i utbyte mot nedladdningar av faktablad och visa statistik om vem som interagerar med PDF. Du kan infoga dessa funktioner på din webbplats för att driva och övervaka användarengagemang.

Om du är Angular- eller React-utvecklare kan du prova [ytterligare exempel](https://github.com/adobe/pdf-embed-api-samples) om du vill integrera PDF Embed API med React- och Angular-projekt.

Med Adobe kan du bygga upp en komplett kundupplevelse med nyskapande lösningar. Ta en titt på [Adobe PDF Embed API](https://www.adobe.io/apis/documentcloud/viesdk) kostnadsfritt. Om du vill utforska vad mer du kan göra kan du prova Adobe PDF Services API med [pay-as-you-gopr](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html)[isning](https://www.adobe.io/apis/documentcloud/dcsdk/pdf-pricing.html).

[Kom igång](https://www.adobe.io/apis/documentcloud/dcsdk/gettingstarted.html) med [!DNL Adobe Acrobat Services] API:er i dag.
