# AWS-workshop

### Lambda!
Her skal vi leke oss litt med lambda, et ypperlig Function As A Service (FaaS) miljø vi kan bruke til å kjøre koden vår.
Vi kommer også til å ta i bruk et rammeverk som heter serverless for å kjenne litt på deploy og håndtering av prosjektet vårt.

### Prereqs
Installer aws cli (MacOS: `brew install awscli`).
Kjør kommandoen `aws configure`. Du får beskjed om å legge inn følgende verdier én etter én:
- AWS Access Key ID [********************]:  `(fra epost)`
- AWS Secret Access Key [********************]: `(fra epost)`
- Default region name: `eu-west-1`
- Default output format: `json`

Hvis du ønsker å gjøre endringer på dette seinere så finner du filen under `~/.aws/credentials`.

Installer [serverless](https://www.serverless.com/framework/docs/getting-started/).

## Oppgave 1
- I terminalen din, naviger til repoet hvor denne READMEen kjører, eller en annen mappe du vil bruke for denne workshoppen. Kjør kommandoen `serverless` der. Dette burde initiere et nytt serverless prosjekt. 
  Du får nå valg om type repo du vil lage. Du kan bevege deg opp eller ned i CLIet ved hjelp av piltastene. Man velger ved å trykke enter. Velg `starter` Node eller Python, litt etter hva du foretrekker. Jeg gikk for AWS - Python - Starter.
  Gi prosjektet et navn - det bør være noe annet enn det de andre velger, slik at du kan kjenne igjen prosjektet ditt i en liste med prosjekter senere. Tips for å huske prosjektnavnet: velg noe som inneholder navnet ditt.   
  Spør den om du vil lage en serverless konto kan du svare `nei`. 
  Spør den om å logge inn på et dashboard kan du svare `nei`.
  Spør den om du vil deploye prosjektet ditt svarer du `nei`. 
- I `serverless.yml` legg inn `region: eu-west-1` under `provider`.
- Endre `handler.js` til å ha en personlig melding.
- Deploy ved hjelp av kommandoen `serverless deploy`.

🙌 Bra jobba! 🙌 

Du har nettopp laget en funksjon (det du finner i handler.py eller handler.js), laget et oppsett for å kunne håndtere og deploye filene dine opp i skyen (serverless.yml filen) og lastet filene dine opp i Lambda (serverless deploy kommandoen)! I neste oppgave skal vi se litt på hva vi egentlig har dytta opp dit.

<details>
<summary> <h3>🚨 Feilmelding på deploy? 🚨</h3></summary>

#### `Error: This command can only be run in a Serverless service directory.`

```Environment: darwin, node 18.2.0, framework 3.14.0, plugin 6.2.1, SDK 4.3.2
Docs:        docs.serverless.com
Support:     forum.serverless.com
Bugs:        github.com/serverless/serverless/issues

Error:
This command can only be run in a Serverless service directory. Make sure to reference a valid config file in the current working directory if you're using a custom config file
```
💡 Løsning: pass på at du er inne i riktig mappe når du kjøerer `serverlss deploy`
<br>
<br>
#### `Error: The security token included in the request is invalid.`
```
Deploying testingTasks to stage dev (eu-west-1)

✖ Stack testingTasks-dev failed to deploy (0s)
Environment: darwin, node 18.2.0, framework 3.14.0, plugin 6.2.1, SDK 4.3.2
Credentials: Local, "default" profile
Docs:        docs.serverless.com
Support:     forum.serverless.com
Bugs:        github.com/serverless/serverless/issues

Error:
The security token included in the request is invalid.
```

💡 Løsning: Du har trolig feil `Access key` og `Access Secret`. Kjør `aws configure` om igjen og pass på å lime inn riktige verdier fra e-posten. Fortsatt trøbbel? Ta kontakt med en av kursholderne så de kan hjelpe deg å generere en ny key + secret.

</details>


## Oppgave 2
Nå skal vi ta å sjekke ut UIen og se hvordan koden kjører!
- Logg inn på https://console.aws.amazon.com/
  - Velg IAM user
  - account-id er `bekk-skyskolen`
  - brukernavn er bekk-eposten din
  - passord ser du på tavla. 
- I menyen i toppen søk etter og velg "lambda". Under "Functions" finn din funksjon!
- Trykk på den oransje "TEST"-knappen. Får du opp et vindu som spør om _configure test event_ så bare skriv et navn, f.eks. "test" og trykk save.
- 💥 BAM! Du har nå kjørt funksjonen din! Woop! 🥳🎉

<details>
<summary> <h3>🚨 Troubleshooting 🚨</h3></summary>
💡 Oppe til høyre ved siden av brukernavnet ditt står det en "region". AWS har en tendens til å sende en til feil region. Vi henger i eu-west-1. 

Se [bildene i losningsforslag2-mappen](https://github.com/halvorhm/skyskolen-lambda-workshop/tree/main/losningsforslag/oppgave2) for hvor du skal trykke.
</details>


## Oppgave 3
For å få litt mer ut av dette enn en hello world tenkte jeg vi gjøre om funksjonen vår til noe som administrer litt med S3-bøtter. 

En S3-bøtte brukes på Amazon Cloud Services til å holde data. Tenk på det som en litt fancy delt disk.  

Vi lager oss en bøtte aller først. Bøtter er unike i verden og må ha et unikt navn, så bruk gjerne en kombinasjon av ditt eget navn e.l.
`aws s3 mb s3://<mitt navn på min bøtte>`.

La os deretter skrive om funksjonen vår i handler.js/handler.py til å liste alle s3-bøttene som eksisterer i området vi jobber i på AWS! Et grunnlag for å få til dette finner du [her for node](https://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/s3-example-creating-buckets.html) og [her for python](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-example-creating-buckets.html#list-existing-buckets).

_OBS_ Hvis du endrer på funksjonsnavnet eller lager en ny funksjon må du vite at en Lambda-function må ta inn parameterne `(event, context)` eks: `def hello(event, context)`. Du trenger ikke bruke event eller context i funksjonen din, men en lamda-funksjon må ta disse inn for å kjøre (i hvertfall i python).

*Tester du koden din på nett nå i lambda vil den feile, og det er meningen. Det skal løses i neste oppgave, men vi kan kjøre lokalt nå først.*

Hvis du har lyst til å teste koden lokalt så kan vi "kjøre" en lambda-funksjon lokalt ved hjelp av en fin liten serverless-kommando. 
Vi bruker da `serverless invoke local --stage dev --function hello`. 
`stage` må her være det samme som du har brukt ellers og `function` viser til det funksjonen din er deklarert som under `functions`i serverless.yml.

Se om du får lista opp alle bøttene! Når koden fungerer, kjør en ny `serverless deploy --stage dev` for å dytte koden din ut.

Hvis du kjører denne i lambda vil du se at den feiler med et tilgangsproblem. Dette løser vi i oppgave 4!

<details>
<summary> <h3>🚨 Troubleshooting 🚨</h3></summary>

💡 Hvis du kjører python og prøver å kjøre lambdaen lokalt kan det hende du må installere boto3. 
For å løse følgende feilmelding ``` ModuleNotFoundError: No module named 'boto3' ```  kjør ``` pip3 install boto3 ``` i terminalen.

<br>

💡 Har du endret funksjonen din og får nå feilmeldingen: ``` TypeError: printBuckets() takes 0 positional arguments but 2 were given ```? Løsning: funksjonen din må ta inn parameterne `(event, context)` eks: `def hello(event, context)`. Du trenger ikke bruke event eller context i funksjonen din, men en lamda-funksjon må ta disse inn for å kjøre.
</details>

## Oppgave 4. 
Som nevnt har ikke lambda-funksjonen tilgang til å lese s3-bøttene. Dette kan vi fikse! Og akkurat nå mens vi tester er vi litt frekke og putter på litt ekstra tilganger.
Legg inn biten `iamRoleStatements` i din `serverless.yml` fil som vist under. Dette gir lambdaen tilgang tilå gjøre _alle_ s3-kommandoer mot _alle_ s3-bøtter.  

```yaml
provider:
  name: aws
  runtime: python3.8
  lambdaHashingVersion: 20201221
  region: eu-west-1
  iamRoleStatements:
    - Effect: 'Allow'
      Action:
        - 's3:*'
      Resource:
        - 'arn:aws:s3:::*'
```
Deploy på nytt! Nå burde ting funke!


## Oppgave 5
Nå prøver vi oss på litt løsere oppgaver, hvor vi må sjekke dokumentasjonen til serverless og sjekke events/triggers. 

Start med å laste opp noe i bøtta di. Kanskje et bilde eller et word-dokument, bare ikke velg noe sensitivt.

Først kan vi endre koden vår til å liste innholdet i en gitt bøtte ved hjelp av lenkene i oppgave 3.
Du finner dokumentasjon på hvordan du gjør ting mot s3 i boto3 biblioteket hvis du bruker python, eller javascript-sdken hvis du er i js. For serverless kan du se lenken under til deres dokumentasjon.
- https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.list_objects_v2
- https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#listObjectsV2-property
- https://www.serverless.com/framework/docs/providers/aws/events/s3/ 

Alle metoder tilgjengelig på boto3 klienten: https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#id205


### Oppgave 5.1
Først, list innholdet i bøtten din! Bruk det vi gjorde i oppgave 3 som utgangspunkt og modifiser funksjonen din ved hjelp av dokumentasjonen over. 

### Oppgave 5.2
Lag en cron-trigger så lambdaen kjører hvert minutt. Test det!

Nyttig lenke: https://www.serverless.com/blog/cron-jobs-on-aws
### Oppgave 5.3
Lag en event som trigger når nye objekter blir lagt til å bøtta. Test it!

Nyttig lenke: 
https://www.serverless.com/framework/docs/providers/aws/events/s3
