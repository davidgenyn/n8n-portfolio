# n8n-portfolio

AI-automatiseringsworkflows, gebouwd in self-hosted n8n (Docker op Ubuntu Server). 
Elke workflow draait in productie op een eigen server en is hier als JSON-export beschikbaar.

**Technieken:** n8n · Docker Compose · Ubuntu Server · OAuth 2.0 (Google Cloud Console) · 
Gmail / Drive / Sheets API · Anthropic API (Claude) · JSON · Linux/SSH

---

## Project 1 — Google Drive backup
Dagelijkse geplande backup: kopieert alle bestanden uit een bronmap naar een backupmap.

- Schedule Trigger (dagelijks) → Drive Search → Drive Copy
- OAuth 2.0-credential via eigen Google Cloud-project
  ![Workflow](project1.png)

## Project 2 — Gmail AI-classificatie
Classificeert elke inkomende mail automatisch in vijf categorieën (Factuur, Sollicitatie, 
Trading, Nieuwsbrief, Overig) en zet het bijhorende Gmail-label.

- Gmail Trigger (polling elke 5 min) → Text Classifier (Claude Haiku) → Add Label per categorie
- Categoriebeschrijvingen fungeren als prompt; bijgestuurd op basis van echte mailstroom
  ![Workflow](project2.png)

## Project 3 — Factuur-extractor
Leest PDF-facturen uit inkomende mail, extraheert gestructureerde velden met AI en 
schrijft ze als rij naar een Google Sheet.

- Gmail Trigger (filter: PDF-bijlagen) → Extract from File (PDF→tekst) → 
  Information Extractor (8 attributen, getypeerd: Boolean/Number/String) → 
  IF (is_factuur) → Sheets Append/Update Row
- Herkent datumformaten en valuta (EUR/USD) correct

**Known limitations (bewust genoteerd):**
- Eén PDF-bijlage per mail wordt verwerkt (attachment_0)
-
![Workflow](project3.png)


## Project 4 — Valutamonitor
Haalt dagelijks de eurokoersen op bij de Europese Centrale Bank (XML),
vraagt per valuta via GraphQL op welke landen die munt gebruiken,
en schrijft het resultaat als historiek naar een Google Sheet.

- Schedule Trigger (dagelijks 17:00) → HTTP Request (ECB XML) →
  XML→JSON → Edit Fields → Split Out → GraphQL (Countries API) →
  Sheets Append/Update Row
- Dubbel-detectie via samengestelde sleutel (datum + valuta)
- Twee externe dataformaten (XML en GraphQL) samengevoegd in één rapport

![Workflow](project4.png)

## Project 5 — Outlook AI-classificatie
Sorteert inkomende Outlook-mail automatisch in mappen op basis van
AI-classificatie. Microsoft 365 is de standaard bij Belgische KMO's,
waardoor dit de zakelijk meest bruikbare variant van project 2 is.

- Microsoft Outlook Trigger (elke 5 min) → Text Classifier (Claude Haiku,
  5 categorieën) → HTTP Request per categorie (Microsoft Graph API)
- Eigen app-registratie in Microsoft Entra ID met OAuth2 en
  gedelegeerde Graph-rechten (Mail.ReadWrite, offline_access)
- De ingebouwde Outlook-node bleek het bericht-ID uit een expressie niet
  correct door te geven (bekend probleem, 400/ErrorInvalidIdMalformed).
  Na systematisch isoleren van de oorzaak vervangen door directe
  Graph-aanroepen via HTTP Request — daarmee werkt de volledige keten.

![Workflow](project5.png)


## Achtergrond


Automatiseringservaring uit een eerder traject: 100+ NinjaTrader-strategieën (NinjaScript/C#) 
ontwikkeld met AI-ondersteunde workflow. 

Contact: lotusflow.contact@gmail.com
