# Interfaccia REST api LMS
Documentazione per l'interfaccia REST api dell'LMS

## Considerazioni iniziali

⚠️ Quanto riportato in questa documentazione è una bozza e potrebbe essere soggetta a revisioni/modifiche.

## Endopoint

Questa interfaccia mette a disposizione agli sviluppatori 3 endpoint per eseguire delle semplici operazioni di selezione e aggiornamento dei dati raccolti dall'LMS.

| Endpoint | HTTP method | Descrizione |
| :- | :- | :- |
| `/api/v1/request/get` | `GET` | Intercetta tutte le richieste da importare |
| `/api/v1/request/mark-requests` | `PUT` | Contrassenga richieste come già importate |
| `/api/v1/request/update` | `PUT` | Aggiorna dati richieste (stato richiesta e prezzo finale) |

## /api/v1/request/get

Richiamando questo endpoint il server restituirà una risposta in formato JSON contenente la lista delle le richieste di contatto da importare. Per "richieste di contatto da importare" si intendono tutte quelle richieste che non sono state contrassegnate come importate attraverso il seconto endpoint `mark-requests`. Questo evita di andare a fare una selezione di informazioni che sono state precedenetemente imporate.

Di seguito viene riportata una risposta di esempio:

### Riposta JSON

```yaml
{
    "code": 200,
    "message": "OK",
    "data": [
        {
            "id": 19,
            "datetime": "2024-06-10 10:14:50",
            "status": 2,
            "from_web": true,
            "source": 1,
            "origin_page": "LP Gestione Rifiuti",
            "tmp_price": "100.00",
            "final_price": "100.00",
            "message": "Lorem ipsum dolor sit amet, consectetur adipiscing elit...",
            "lead_infos": {
                "id": 23,
                "first_name": "Mario",
                "last_name": "Rossi",
                "user_type": 2,
                "company_name": "SmartHome Innovations SRL",
                "fiscal_code": "45678901234",
                "company_vat": "45678901234",
                "address": "Viale dei Mille, 50",
                "province": "MI",
                "city": "Milano",
                "zip": "20129",
                "email": "mario.rossi@sh-innovations.com",
                "phone": "3335678901"
            },
            "marked": true
        },
        {
            "id": 20,
            "date": "10/06/2024 - 10:14",
            [...]
        },
        {
            "id": 21,
            "date": "10/06/2024 - 10:14",
            [...]
        }
    ]
}


```

### Interpretazione dati risposta

Di seguito una semplice legenda utile per interpretare i dati ricevuti nella risposta.

#### Richiesta di contatto

| Proprietà | Tipo | Descrizione |
| :- | :- | :- |
| `id` | `int` | Il codice identificativo univoco della richiesta di contatto |
| `datetime` | `string` | La data nella quale è stata inviata la richiesta di contatto |
| `status` | `int` | L'identificativo dello stato della richiesta di contatto |
| `from_web` | `bool` | Indica se la richiesta arriva dal sito web oppure se è stata inserita manualmente |
| `source` | `bool` | L'identificativo dell'origine della richiesta di contatto |
| `origin_page` | `string` &#124; `null` | La pagina web d'origine dalla quale è avvenuta la richiesta di contatto |
| `tmp_price` | `float` | Il prezzo temporaneo impostato per la richiesta di contatto |
| `final_price` | `float` | Il prezzo definitivo impostato per la richiesta di contatto |
| `message` | `string` &#124; `null` | Il messaggio della richiesta di contatto |
| `lead_infos` | `obj` | L'oggetto JSON contenenete l'anagrafica del contatto |
| `marked` | `bool` | Parametro custom che specifica se la richiesta di contatto sia stata contrassegnata o meno |

#### Anagrafica cliente

| Proprietà | Tipo | Descrizione |
| :- | :- | :- |
| `id` | `int` | Il codice identificativo univoco del contatto |
| `first_name` | `string` | Il nome del contatto |
| `last_name` | `string` | Il cognome del contatto |
| `user_type` | `int` | Il tipo di contatto (1 - Privato, 2 - Azienda, 3 - Ente pubblico) |
| `company_name` | `string` &#124; `null` | La ragione sociale del contatto |
| `fiscal_code` | `string` &#124; `null` | Il codice fiscale del contatto |
| `company_vat` | `string` &#124; `null` | La partita iva del contatto |
| `address` | `string` | L'indirizzo fisico del contatto |
| `province` | `string` | La provincia di provenienza del contatto |
| `city` | `string` | La città di provenienza del contatto |
| `zip` | `string` | Il codice postale di provenienza del contatto |
| `email` | `string` | L'email del contatto |
| `phone` | `string` | Il numero di telefono del contatto |

#### Stati delle richieste di conatto

| ID | Nome stato |
| :- | :- |
|1|Nuova|
|2|Trattativa confermata|
|3|Trattativa in corso|
|4|Trattativa persa|
|5|Appuntamento fissato|
|6|Da ricontattare|
|7|Non interessato|

#### Origini delle richieste

| ID | Nome origine |
| :- | :- |
|1|Form sito web|
|2|WhatsApp|
|3|Telefono|
|4|Facebook|
|5|Instagram|
|6|LinkedIn|
|7|In presenza|
|8|Email|
|9|Passaparola|
|10|Visita|
|11|Pubblicità tradizionale|
|12|Telemat|
|13|Cartello cantiere|
|14|Fiere|
|15|Protale fornitori (Sintel, ecc.)|

## /api/v1/request/mark-requests

Questo endpoint sarà utile per contrassegnare le richieste di contatto come "importate". Questa operazione andrà fatta "manualmente" in seguito alla chiamata dell'endpoint analizzato precedentemente.

Specificare il metodo HTTP `PUT` e inserire nel corpo della richiesta un JSON contenente la lista degli id delle richieste da contrassegnare.

Segue una request body di esempio:

```yaml
{
    "mark-requests": [
        19,
        20,
        21,
        23,
        25,
        [...]
    ]
}
```

Attraverso questo endpoint sarà anche possibile resettare come non contrassegnate le richieste di contatto.

```yaml
{
    "unmark-requests": [
        22,
        24,
        [...]
    ]
}
```

## /api/v1/request/update

Lo scopo di questo endpoint sarà quello di aggiornare le richieste di contatto raccolte dall'LMS. Come specificato precedentemente sarà possibile aggiornare solo due informazioni: lo stato e/o il valore finale della richiesta.

Specificare il metodo HTTP `PUT` e inserire nel corpo della richiesta un JSON contenente le informazioni delle richieste di contatto da aggiornare.

Segue una request body di esempio:

```yaml
{
    "requests": [
        {
            "id": 19,
            "status": 3
        },
        {
            "id": 20,
            "status": 2,
            "final_price": "2500.00"
        },
        [...]
    ]
}
```

**N.B.** Aggiorare il prezzo finale (`final_price`) della richiesta di contatto soltanto quanto lo stato (`status`) della medesima passa in *Trattativa confermata* (2). Violando questa condizione il server non aggiornerà le informazioni e restituirà una risposta di errore (400 Bad Request).


## Autenticazione

Per accedere all'interfaccia sarà obbligatorio autenticarsi: sarà quindi sufficiente aggiungere l'intestazione `Authorization` alle headers delle richieste specificando come valore la propria chiave api.
Lo schema di autenticazione utilizzato è il `Basic`.

```bash
curl -X GET \
  -H "Authorization: Basic [your_api_key]" \
  "https://[host_lms]/api/v1/request/get"
```
