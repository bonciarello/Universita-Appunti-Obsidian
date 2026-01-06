Il clickjacking è un attacco basato sull'interfaccia in cui un utente viene ingannato e indotto a cliccare su contenuti utilizzabili su un sito web nascosto cliccando su altri contenuti in un sito web malevolo.

Facciamo un esempio: un utente web accede a un sito web malevolo (forse un collegamento fornito da un'e-mail) e clicca su un pulsante per vincere un premio. Senza saperlo, è stato ingannato da un aggressore che ha premuto un pulsante nascosto alternativo e questo ha portato al pagamento di un account su un altro sito.

Questo è un esempio di attacco clickjacking. La tecnica si basa sull'incorporazione di una pagina web invisibile e utilizzabile (o più pagine) contenente un pulsante o un collegamento nascosto, ad esempio, all'interno di un iframe. L'iframe è sovrapposto al contenuto della pagina web malevola prevista dall'utente.

Questo attacco differisce da un attacco CSRF in quanto all'utente è richiesto di eseguire un'azione come un clic sul pulsante, mentre un attacco CSRF si basa sulla falsificazione di un'intera richiesta senza la conoscenza o l'input dell'utente.

La protezione contro gli attacchi CSRF è spesso fornita dall'uso di un token CSRF: un numero monouso specifico per la sessione o nonce. Gli attacchi di clickjacking non possono essere prevenuti tramite i token CSRF poiché una sessione di destinazione viene stabilita con contenuti caricati da un sito web autentico e con tutte le richieste che avvengono sul dominio. I token CSRF vengono inseriti nelle richieste e passati al server come parte di una sessione con comportamento normale. La differenza rispetto a una normale sessione utente è che il processo avviene all'interno di un iframe nascosto.
## Come costruire un attacco di clickjacking di base
Gli attacchi di clickjacking utilizzano CSS per creare e manipolare livelli. L'attaccante incorpora il sito web di destinazione come livello iframe sovrapposto al sito web malevolo. Un esempio che utilizza il tag di stile e i parametri è il seguente:

```html
<head>
<style>
#target_website {
position:relative;
width:128px;
height:128px;
opacity:0.00001;
z-index:2;
}
#decoy_website {
position:absolute;
top: 10px;
left: 10px;
z-index:1;
}
</style>
</head>
...
<body>
<div id="decoy_website">
...decoy web content here...
</div>
<iframe id="target_website" src="https://vulnerable-website.com">
</iframe>
</body>
```

L'iframe del sito web di destinazione è posizionato all'interno del browser in modo che vi sia una sovrapposizione precisa dell'azione di destinazione con il sito web esca utilizzando valori di posizione di larghezza e altezza appropriati.

I valori di posizione assoluti e relativi vengono utilizzati per garantire che il sito web di destinazione si sovrapponga accuratamente alla parte malevola indipendentemente dalle dimensioni dello schermo, dal tipo di browser e dalla piattaforma. Lo z-index determina l'ordine di sovrapposizione dei livelli iframe e sito web. Il valore di opacità è definito come 0.0 (o vicino a 0.0) in modo che il contenuto dell'iframe sia trasparente per l'utente.

La protezione dal clickjacking del browser potrebbe applicare il rilevamento della trasparenza dell'iframe basato sulla soglia (ad esempio, Chrome versione 76 include questo comportamento ma Firefox no). L'attaccante seleziona i valori di opacità in modo che l'effetto desiderato venga ottenuto senza attivare comportamenti di protezione.
### Clickjacking con inserimento di dati in un modulo precompilato
Alcuni siti web che richiedono il completamento e l'invio di un modulo consentono la precompilazione degli input del modulo utilizzando parametri GET prima dell'invio. Altri siti web potrebbero richiedere testo prima dell'invio del modulo. Poiché i valori GET fanno parte dell'URL, l'URL di destinazione può essere modificato per incorporare i valori scelti dall'attaccante e il pulsante "invia" trasparente viene sovrapposto al sito esca come nell'esempio di base del clickjacking.
### Script di frame busting
Gli attacchi di clickjacking sono possibili ogni volta che i siti web possono essere inquadrati. Pertanto, le tecniche preventive si basano sulla limitazione della capacità di inquadramento per i siti web.
Una comune protezione lato client attuata tramite il browser web è quella di utilizzare script di frame busting o frame breaking. Questi possono essere implementati tramite componenti aggiuntivi o estensioni JavaScript proprietari del browser come NoScript.

Gli script sono spesso creati in modo da eseguire alcuni o tutti i seguenti comportamenti:
- controllare e imporre che la finestra dell'applicazione corrente sia la finestra principale o superiore;
- rendere visibili tutti i frame;
- impedire di fare clic su frame invisibili;
- intercettare e segnalare potenziali attacchi di clickjacking all'utente.

Le tecniche di frame busting sono spesso specifiche del browser e della piattaforma e, grazie alla flessibilità dell'HTML, possono solitamente essere aggirate dagli aggressori. Poiché i frame buster sono JavaScript, le impostazioni di sicurezza del browser potrebbero impedirne il funzionamento o addirittura il browser potrebbe non supportare JavaScript.

Una soluzione alternativa efficace per gli aggressori contro i frame buster è quella di utilizzare l'attributo `sandbox` iframe HTML5. Quando questo è impostato con i valori `allow-forms` o `allow-scripts` e il valore `allow-top-navigation` è omesso, lo script frame buster può essere neutralizzato poiché l'iframe non può controllare se è o meno la finestra superiore:

`<iframe id="victim_website" src="https://victim-website.com" sandbox="allow-forms"></iframe>`

Sia i valori `allow-forms` che `allow-scripts` consentono le azioni specificate all'interno dell'iframe, ma la navigazione di livello superiore è disabilitata. Ciò inibisce i comportamenti di frame busting consentendo al contempo la funzionalità all'interno del sito di destinazione.
### Combinazione di clickjacking con un attacco DOM XSS
La vera potenza del clickjacking si rivela quando viene utilizzato come vettore per un altro attacco come un attacco DOM XSS. L'implementazione di questo attacco combinato è relativamente semplice, supponendo che l'aggressore abbia prima identificato l'exploit XSS. L'exploit XSS viene quindi combinato con l'URL di destinazione dell'iframe in modo che l'utente clicchi sul pulsante o sul collegamento e di conseguenza esegua l'attacco DOM XSS.
### Clickjacking multifase
La manipolazione degli input di un sito web di destinazione da parte di un aggressore può richiedere più azioni. Ad esempio, un aggressore potrebbe voler ingannare un utente per fargli acquistare qualcosa da un sito web di vendita al dettaglio, in modo che gli articoli debbano essere aggiunti a un carrello prima che l'ordine venga effettuato. Queste azioni possono essere implementate dall'aggressore utilizzando più divisioni o iframe. Tali attacchi richiedono notevole precisione e attenzione dal punto di vista dell'aggressore se devono essere efficaci e furtivi.
## Come prevenire gli attacchi di clickjacking
Il clickjacking è un comportamento lato browser e il suo successo o meno dipende dalla funzionalità del browser e dalla conformità agli standard web prevalenti e alle best practice. La protezione lato server contro il clickjacking è fornita definendo e comunicando vincoli sull'uso di componenti come gli iframe. Tuttavia, l'implementazione della protezione dipende dalla conformità del browser e dall'applicazione di questi vincoli. Due meccanismi per la protezione dal clickjacking lato server sono X-Frame-Options e Content Security Policy.
### X-Frame-Options
X-Frame-Options è un header che fornisce al proprietario del sito web il controllo sull'uso di iframe o oggetti in modo che l'inclusione di una pagina web all'interno di un frame possa essere vietata con la direttiva `deny`:
`X-Frame-Options: deny`

In alternativa, il framing può essere limitato alla stessa origine del sito web utilizzando la direttiva `sameorigin`:
`X-Frame-Options: sameorigin`

Oppure a un sito web denominato utilizzando la direttiva `allow-from`:
`X-Frame-Options: allow-from https://normal-website.com`

X-Frame-Options non è implementato in modo coerente nei browser: tuttavia, se applicato correttamente insieme alla Content Security Policy come parte di una strategia di difesa multilivello, può fornire una protezione efficace contro gli attacchi di clickjacking.
### Content Security Policy (CSP)
Content Security Policy (CSP) è un meccanismo di rilevamento e prevenzione che fornisce mitigazione contro attacchi quali XSS e clickjacking. CSP è solitamente implementato nel server web come un'intestazione di ritorno del formato:
`Content-Security-Policy: policy`

dove policy è una stringa di direttive di policy separate da punto e virgola. CSP fornisce al browser client informazioni sulle fonti consentite di risorse web che il browser può applicare al rilevamento e all'intercettazione di comportamenti dannosi.

La protezione consigliata contro il clickjacking è quella di incorporare la direttiva `frame-ancestors` nella Content Security Policy dell'applicazione. La direttiva `frame-ancestors 'none'` ha un comportamento simile alla direttiva X-Frame-Options deny. La direttiva `frame-ancestors 'self'` è ampiamente equivalente alla direttiva X-Frame-Options sameorigin. Il seguente CSP inserisce i frame nella whitelist solo per lo stesso dominio:
`Content-Security-Policy: frame-ancestors 'self';`

In alternativa, il framing può essere limitato a siti denominati:
`Content-Security-Policy: frame-ancestors normal-website.com;`

Per essere efficaci contro clickjacking e XSS, i CSP necessitano di sviluppo, implementazione e test accurati e dovrebbero essere utilizzati come parte di una strategia di difesa multistrato.
