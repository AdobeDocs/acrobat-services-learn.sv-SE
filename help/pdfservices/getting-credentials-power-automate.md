---
title: Hämta autentiseringsuppgifter för Microsoft Power Automate
description: Lär dig få inloggningsuppgifter för att börja använda eller testa Adobe PDF-tjänster
feature: PDF Services API
role: Developer
level: Beginner
type: Tutorial
jira: KT-10382
thumbnail: KT-10382.jpg
exl-id: 68ec654f-74aa-41b7-9103-44df13402032
source-git-commit: c6272ee4ec33f89f5db27023d78d1f08005b04ef
workflow-type: tm+mt
source-wordcount: '872'
ht-degree: 1%

---

# Hämta autentiseringsuppgifter för Microsoft Power Automate

[Microsoft Power Automate](https://powerautomate.microsoft.com/sv-se/) är ett kraftfullt sätt för medborgarutvecklare och -utvecklare att skapa kraftfulla automatiserade processer för att förbättra sina verksamheter utan att behöva skriva kod. Med anslutningen [Adobe PDF Services](https://us.flow.microsoft.com/en-us/connectors/shared_adobepdftools/adobe-pdf-services/), som en del av [[!DNL Adobe Acrobat Services]](https://developer.adobe.com/document-services), kan användare utföra alla åtgärder som är tillgängliga i API:et för Adobe PDF-tjänster i Microsoft Power Automate.

I den här självstudiekursen får du lära dig hur du skaffar inloggningsuppgifter för att börja använda eller testa Adobe PDF-tjänster. Beroende på om du är testanvändare eller befintlig kund går den här självstudiekursen igenom rätt steg för att få inloggningsuppgifter.

## Hur kan Microsoft Power Automate-användare börja använda Adobe PDF Services-anslutningen?

Befintliga Microsoft Power Automate-användare kan [få autentiseringsuppgifter för provperioder](https://www.adobe.com/go/powerautomate_getstarted) för Adobe PDF-tjänster. Länken ovan är en särskild registreringslänk som hjälper dig i den här processen för Microsoft Power Automate-användare.

![Adobe Developer-användarinloggning](assets/credentials_1.png)


>[!IMPORTANT]
> Om du loggar in för en testversion måste du använda ett Adobe ID och inte ett Enterprise ID. Om du för närvarande inte prenumererar på Adobe PDF Services API och försöker logga in med ditt Enterprise ID kan du få ett behörighetsfel eftersom ditt företag inte har rätt att använda Adobe PDF Services API. Av detta skäl rekommenderar vi att du använder ett eget Adobe ID som är gratis.
>

1. När du har loggat in uppmanas du att välja ett namn för dina nya inloggningsuppgifter. Ange ditt *Autentiseringsnamn*.
1. Markera kryssrutan för att godkänna utvecklarvillkoren.
1. Välj **[!UICONTROL Skapa autentiseringsuppgifter]**.

   ![Namnge dina autentiseringsuppgifter](assets/credentials_2.png)

Dessa behörighetsuppgifter omfattar fem olika värden:

* Klient-ID (API-nyckel)
* Klienthemlighet
* Organisations-ID
* ID för tekniskt konto
* Base64 (kodad privat nyckel)

![Nya autentiseringsuppgifter](assets/credentials_3.png)

En JSON-fil som innehåller alla dessa värden hämtas också automatiskt till systemet. Filen heter `pdfservices-api-pa-credentials.json` och ser ut så här:

```json
{
 "client_id": "client id value",
 "client_secret": "client secret value",
 "organization_id": "organized id value",
 "account_id": "account id value",
 "base64_encoded_private_key": "base64 version of the private key"
}
```

Spara den här filen på en säker plats eftersom det inte går att hämta en kopia av den privata nyckeln igen.

### Lägg till anslutning i Microsoft Power Automate

Nu när du har dina inloggningsuppgifter kan du börja använda dem i Microsoft Power Automate-flöden.

1. Öppna menyn **[!UICONTROL Data]** på menyn i sidopanelen och välj **Anslutningar**:

   ![Anslutningsmenyn på Microsoft Power Automate-webbplatsen](assets/credentials_4.png)

1. Välj **+ [!UICONTROL Ny anslutning]**.

1. Nästa skärm visar en lista med möjliga anslutningstyper. I det övre högra hörnet anger du adobe för att filtrera alternativen:

   ![Lista över Adobe-anslutningar](assets/credentials_5.png)

1. Markera **[!UICONTROL Adobe PDF-tjänster (förhandsversion)]**.
1. Ange alla fem värden som du genererade tidigare i det modala fönstret. Välj **[!UICONTROL Skapa]** när det är klart.

   ![Formulärfält för att ange inloggningsinformation](assets/credentials_6.png)

Nu kan du använda Adobe PDF-tjänsterna i Microsoft Power Automate.

### Åtkomst till autentiseringsuppgifter efter att de har skapats

Om du redan har skapat autentiseringsuppgifter och förlorat de hämtade autentiseringsuppgifterna kan du hämta dem igen i [Adobe Developer Console](https://developer.adobe.com/console).

1. När du har loggat in på [Adobe Developer Console](https://developer.adobe.com/console) måste du först leta reda på projektet och välja det.
1. I menyn till vänster under *Autentiseringsuppgifter* väljer du **Tjänstkonto (JWT)**:

   ![Befintliga autentiseringsuppgifter](assets/credentials_7.png)

1. Observera de fem värdena som visas här: *Klient-ID*, *Klienthemlighet*, *ID för tekniskt konto*, *E-postadress till tekniskt konto* och *Organisations-ID*.

Tyvärr kan du inte ladda ner den tidigare privata nyckeln, men du kan använda knappen &quot;Generera ett offentligt/privat nyckelpar&quot; för att skapa en ny.

## Använda befintliga autentiseringsuppgifter för Adobe PDF Services

Om du har befintliga API-uppgifter för Adobe PDF Services som har genererats från webbplatsen [!DNL Adobe Acrobat Services] kan du använda dem med Microsoft Power Automate. Om du hämtade ett SDK när du registrerade dig kom dina befintliga inloggningsuppgifter i form av en JSON-fil, med det troliga namnet `pdfservices-api-credentials.json`. JSON-filen innehåller de fem nycklar som behövs för att skapa anslutningsreferenserna. Kopiera varje värde från JSON-filen till motsvarande anslutningsfält.

Värdet på din privata nyckel kommer från en annan fil med namnet `private.key`.

Du kan också få värdena från Adobe Developer Console enligt beskrivningen ovan.

## Hur kan användare av [!DNL Adobe Acrobat Services] börja arbeta med Microsoft Power Automate?

För att komma igång med Power Automate, gå först till <https://powerautomate.microsoft.com> och använd knappen &quot;Starta gratis&quot;. Om du inte har något Microsoft-konto måste du skapa ett. När du har loggat in visas Power Automate-kontrollpanelen.

![PA-instrumentpanel, inledande vy](assets/credentials_8.png)

Enligt beskrivningen i början av den här självstudiekursen skapar du ett nytt flöde, lägger till ett steg och hittar Adobe PDF-tjänsterna. Välj en åtgärd så kan ett varningsmeddelande visas om att ett premiumkonto krävs.

![Premium-kontovarning](assets/credentials_9.png)

Som skärmbilden ovan visar kan du antingen växla till ett arbetskonto eller konfigurera ett nytt organisationskonto. När du har gjort det kan du lägga till åtgärden Adobe PDF-tjänster.

Du kan läsa mer om hur du skapar ditt första Microsoft Power Automate-flöde med [!DNL Adobe Acrobat Services] i [Skapa ditt första arbetsflöde i Microsoft Power Automate](https://experienceleague.adobe.com/sv/docs/acrobat-services-learn/tutorials/pdfservices/create-workflow-power-automate).

## Ytterligare resurser

Här är en lista med ytterligare resurser som kan hjälpa dig mer:

* Först ut är Adobe PDF Services Power Automate-dokumenten: <https://docs.microsoft.com/en-us/connectors/adobepdftools/>. Dessa resurser kompletterar det du har lärt dig här.
* Behöver du exempel? Du hittar många [Power Automate-mallar](https://powerautomate.microsoft.com/en-us/connectors/details/shared_adobepdftools/adobe-pdf-services/) som visar PDF.
* Vårt livevideoinnehåll, [Pappersklipp](https://www.youtube.com/playlist?list=PLcVEYUqU7VRe4sT-Bf8flvRz1XXUyGmtF), innehåller också videor som visar hur Power Automate används.
* [Adobe Tech-bloggen](https://medium.com/adobetech/tagged/microsoft-power-automate) innehåller många artiklar om hur du arbetar med Power Automate.
* Se till sist även dokumentationen om [PDF](https://developer.adobe.com/document-services/docs/overview/).
