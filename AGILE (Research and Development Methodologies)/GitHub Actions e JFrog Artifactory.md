---
aliases: [GAJA, GitHub Actions JFrog Artifactory]
tags: [agile-research-and-development-methodologies]
---
## GitHub Actions
**GitHub Actions** è una funzionalità di GitHub che consente di eseguire flussi di lavoro definiti dall'utente la cui esecuzione è attivata da [[Eventi|eventi]] relativi a *git*.  Ciò consente facilmente di definire una fase di *commit*, ma è abbastanza ricco di funzionalità da implementare un'intera pipeline di CD.

Alternative a GA sono Travis CI, Circle CI, Gitlab CI/CD, Buddy C, AWS CodePipeline, Azure Pipeline, Atlassian Bamboo e tanti altri.

Alternative self-hosted sono Jenkins e Buildvot.

I componenti principali di GA sono **workflows**, **jobs** e **steps**.
Un *workflow* è una sequenza di lavori che vengono eseguiti sui corridori. I *jobs* sono composti da *steps*, che sono comandi della shell o funzionalità di terze parti note come *action*.

### Workflows
I **flussi di lavoro (workflow)** vengono attivati da [[Eventi|eventi]] correlati a git. I flussi di lavoro sono definiti da file YAML (simili a JSON) in una directory *.github/workflows* all'interno di un repository di GitHub. Sono “contenitori” per una sequenza di *lavori (jobs)*. Un esempio di workflow è il seguente:
```yaml
name: workflow_name
on:
    push:
        branches: [ main ]
    workflow_dispatch: 
    
jobs:
    build:
        runs-on: ubuntu-latest
        steps:
            - name: Greetings
            run: echo Hello, world!
            
            - name: Multi-line Greetings
            run: | 
                echo Hello, world!
                echo Hi, I'm a multi-line script!
```

### Runner
Un **runner** è una macchina virtuale che esegue flussi di lavoro. I runner eseguono un lavoro alla volta. Selezioniamo il "sistema operativo" della macchina virtuale tramite l'attributo *job name.runs-on*.

### Steps
Un **step** è uno script di shell o un'azione. Gli script di shell iniziano con la chiave *run*, mentre le azioni hanno la chiave *uses*.

### Jobs
I **jobs** sono sequenze di passaggi che vengono eseguiti in ordine. I jobs possono essere eseguiti in parallelo o in sequenza (se dipendono l'uno dall'altro). Per impostazione predefinita, non condividono i dati. Per forzare l'ordine di esecuzione, possiamo usare il tasto *needs* in modo che un lavoro “dipenda da” un altro lavoro e inizi l'esecuzione solo dopo che quest'ultimo è terminato.

### Limiti di uso per GA
Il livello gratuito fornisce 3000 minuti di tempo di esecuzione del flusso di lavoro (imbrogliano un po' con l'arrotondamento). **Sei responsabile del tuo utilizzo (anche per qualsiasi strumento CI/CD che usi).** Se inizi a esaurire i limiti, destreggiati tra i repository all'interno del tuo team, fai attenzione a preservare l'intera cronologia di commit se lo fai (cambia upstream e invia a un nuovo repository). **Non creare un nuovo repository con un primo commit che è il “progetto già finito”.**

## JFrog Artifactory
**JFrog Artifactory** è un *repository di artefatti di livello industriale*, che ti consente di archiviare pacchetti [[Maven]], pacchetti Python, file zip e molti altri. Funzionalità di livello industriale significa anche complessità e curva di apprendimento di livello industriale: puoi fare di più rispetto agli artefatti di GitHub, è più simile al repository di artefatti del libro di testo ma è più complesso.

La piattaforma di JFrog offre anche una funzionalità *Pipelines* simile a GitHub Actions: flussi di lavoro definiti da file YAML, si integra con i repository *git* e così via.

### Artifactory API
**Alcune delle API non sono disponibili nella versione gratuita (richiede abbonamenti a pagamento ad *Artifactory*)**, quindi se vuoi cose simili (ad esempio "scarica la versione più recente") devi crearle tu stesso componendo chiamate REST disponibili gratuitamente. Ad esempio, nella versione gratuita non abbiamo accesso all'API per scaricare la "versione più recente di un artefatto con un nome file", ma abbiamo accesso alle "Filo Info", "Folder Info" e alle API "Artifact Search", in modo da poter recuperare tutti gli artefatti e quindi recuperare l'attributo *lastUpdated*. 

**Suggerimento, se si desidera utilizzare Artifactory:** applicare uno schema di denominazione fisso e uno schema repository + controllo delle versioni semantico, ordinare i nomi dei file degli artefatti e recuperarli dalla chiamata API "Folder Info".

### Integrazione tra JFrog Artifactory e GitHub Actions
Puoi integrare *Artifactory* in qualsiasi strumento CI tramite la sua API, utilizzare direttamente le richieste HTTP (ad esempio qualcosa che *Artifactory* espone già) o scrivere script che ne fanno uso. 
**Esiste anche uno strumento, JFrog CLI, che avvolge le API di Artifactory ed è disponibile come azione GitHub.**
