---

copyright:
  years: 2019
lastupdated: "2019-05-01"

keywords: IBM Cloud, LogDNA, Activity Tracker, archive logs, COS, cloud object storage

subcollection: logdnaat

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:codeblock: .codeblock}
{:tip: .tip}
{:download: .download}
{:important: .important}
{:note: .note}

 
# Archiviazione di eventi a IBM Cloud Object Storage
{: #archiving}

Puoi archiviare gli eventi da un'istanza {{site.data.keyword.at_full_notm}} in un bucket in un'istanza {{site.data.keyword.cos_full_notm}} (COS). 
{:shortdesc}

Completa la seguente procedura per archiviare un'istanza {{site.data.keyword.at_full_notm}} in un bucket in un'istanza {{site.data.keyword.cos_full_notm}}:

## Prerequisiti
{: #archiving_prereqs}

* [Ulteriori informazioni sull'archiviazione di eventi](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-manage_events#manage_events_archive).

* **Devi avere un piano di servizio a pagamento** per il servizio {{site.data.keyword.at_full_notm}}. [Ulteriori informazioni](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-service_plan#service_plan). 

* Verifica che il tuo ID utente disponga delle autorizzazioni per avviare l'IU web e gestire gli eventi. La seguente tabella elenca i ruoli minimi che un utente deve avere per poter avviare l'IU web {{site.data.keyword.at_full_notm}} e visualizzare, ricercare e filtrare gli eventi:

| Ruolo                      | Autorizzazione concessa            |
|---------------------------|-------------------------------|  
| Ruolo piattaforma: `Viewer`     | Consente all'utente di visualizzare l'elenco delle istanze del servizio nel dashboard Osservabilità. |
| Ruolo servizio: `Manager`      | Consente all'utente di avviare l'IU web e gestire gli eventi nell'IU web.  |
{: caption="Tabella 1. Ruoli IAM" caption-side="top"} 

Per ulteriori informazioni su come configurare le politiche per un utente, vedi [Concessione delle autorizzazioni utente a un utente o ID servizio](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-iam_view_events#iam_view_events).



## Passo 1. Concedi le politiche IAM a un utente per utilizzare {{site.data.keyword.cos_full_notm}}
{: #archiving_step1}

**Nota:** questo passo deve essere completato dal proprietario dell'account o da un amministratore del servizio {{site.data.keyword.cos_full_notm}} su {{site.data.keyword.cloud_notm}}.

In qualità di amministratore del servizio {{site.data.keyword.cos_full_notm}}, devi poter eseguire il provisioning delle istanze del servizio, concedere ad altri utenti le autorizzazioni per lavorare con queste istanze e creare gli ID servizio. 

Ci sono diversi modi in cui puoi concedere a un utente l'autorizzazione a diventare un editor del servizio {{site.data.keyword.cos_full_notm}}:

* In qualità di amministratore del servizio nell'account, l'utente deve disporre di una politica IAM per il servizio {{site.data.keyword.cos_full_notm}} con il ruolo della piattaforma *Amministratore*. Devi assegnare a questo utente l'accesso a una singola risorsa nell'account. 

* In qualità di amministratore del servizio nel contesto di un gruppo di risorse, l'utente deve disporre di una politica IAM per il servizio {{site.data.keyword.cos_full_notm}} con il ruolo della piattaforma *Amministratore* nel contesto del gruppo di risorse. 


La seguente tabella elenca i ruoli di cui un utente deve disporre per completare le azioni elencate per il servizio {{site.data.keyword.cos_full_notm}}:

| Servizio                    | Ruoli della piattaforma    | Azione                                                                                        | 
|----------------------------|-------------------|-----------------------------------------------------------------------------------------------|       
| `Cloud Object Storage`     | Amministratore     | Consente all'utente di assegnare le politiche agli utenti nell'account per lavorare con il servizio {{site.data.keyword.cos_full_notm}}. |
| `Cloud Object Storage`     | Amministratore </br>Editor | Consente all'utente di eseguire il provisioning di un'istanza del servizio {{site.data.keyword.cos_full_notm}}.    |
| `Cloud Object Storage`     | Amministratore </br>Editor </br>Operatore | Consente all'utente di creare un ID servizio.    | 
{: caption="Tabella 1. Ruoli e azioni" caption-side="top"} 


Completa la seguente procedura per assegnare a un utente il ruolo di amministratore per il servizio {{site.data.keyword.cos_full_notm}} nel contesto di un gruppo di risorse: 

1. [Accedi al tuo account {{site.data.keyword.cloud_notm}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/login){:new_window}.

	Dopo aver eseguito l'accesso con i tuoi ID e password utente, viene aperta la IU {{site.data.keyword.cloud_notm}}.
    
2. Dalla barra dei menu, fai clic su **Gestisci** &gt; **Accesso (IAM)** e seleziona **Utenti**.
3. Dalla riga per l'utente a cui vuoi assegnare l'accesso, seleziona il menu **Azioni** e fai quindi clic su **Assegna accesso**.
4. Seleziona **Assegna l'accesso in un gruppo di risorse**.
5. Seleziona un gruppo di risorse.
6. Se all'utente non è stato già concesso un ruolo per il gruppo di risorse selezionato, scegli un ruolo per il campo **Assegna accesso a un gruppo di risorse**. 

    A seconda del ruolo che selezioni, l'utente può visualizzare il gruppo di risorse nel suo dashboard, modificarne il nome o gestire l'accesso degli utenti ad esso. 
    
    Puoi selezionare **Nessun accesso**, se vuoi che l'utente abbia accesso solo al servizio {{site.data.keyword.at_full_notm}} nel gruppo di risorse.

7. Seleziona **Cloud Object Storage**.
8. Seleziona il ruolo della piattaforma **Amministratore**.
9. Fai clic su **Assegna**.



## Passo 2. Provisioning di un'istanza di {{site.data.keyword.cos_full_notm}}
{: #archiving_step2}

**Nota:** questo passo deve essere completato da un editor o da un amministratore del servizio {{site.data.keyword.cos_full_notm}} su {{site.data.keyword.cloud_notm}}. 
Completa la seguente procedura per eseguire il provisioning di un'istanza {{site.data.keyword.cos_full_notm}}:

1. Dalla barra dei menu, fai clic su **Catalogo**. Viene aperto l'elenco dei servizi disponibili in {{site.data.keyword.cloud_notm}}.

2. Per filtrare l'elenco di servizi visualizzato, seleziona la categoria **Archiviazione**.

3. Fai clic sul tile **Object Storage**.

4. Immetti un nome per l'istanza del servizio.

5. Seleziona un gruppo di risorse. 

    Per impostazione predefinita, è impostato il gruppo di risorse **Predefinito**.

6. Seleziona un piano di servizio. 

    Per impostazione predefinita, è impostato il piano **Lite**.

7. Fai clic su **Crea**.



## Passo 3. Crea un bucket
{: #archiving_step3}

I bucket sono un modo per organizzare i tuoi dati in un'istanza {{site.data.keyword.cos_full_notm}}. 

Per gestire i bucket, al tuo utente devono essere concesse le autorizzazioni per lavorare con i bucket sull'istanza {{site.data.keyword.cos_full_notm}}. La seguente tabella descrive le diverse azioni e i diversi ruoli che un utente può avere per lavorare con i bucket:

| Servizio                    | Ruoli                   | Azione                             | 
|----------------------------|-------------------------|------------------------------------|       
| `Cloud Object Storage`     | Ruolo della piattaforma: Visualizzatore   | Consente all'utente di visualizzare tutti i bucket e di elencare gli oggetti al loro interno. |
| `Cloud Object Storage`     | Ruolo del servizio: Gestore   | Consente all'utente di rendere pubblici gli oggetti.                                                       |
| `Cloud Object Storage`     | Ruolo del servizio: Gestore </br>Scrittore | Consente all'utente di creare ed eliminare bucket e oggetti.                         | 
| `Cloud Object Storage`     | Ruolo del servizio: Lettore    | Consente all'utente di elencare e scaricare oggetti.                                                 |
{: caption="Tabella 1. Ruoli e azioni per gestire i bucket" caption-side="top"} 

**Nota:** per creare un bucket, il tuo utente deve disporre delle autorizzazioni di gestore o scrittore per l'istanza {{site.data.keyword.cos_full_notm}}.

Per creare un bucket, completa la seguente procedura:

1. Dal menu Navigazione, seleziona **Elenco risorse**.

2. Seleziona l'istanza {{site.data.keyword.cos_full_notm}} dove intendi creare il bucket.

3. Seleziona **Bucket**. Fai quindi clic su **Create Bucket**.

4. Immetti un nome bucket per il campo *Unique bucket name*.

    **Nota:** tutti i bucket in tutte le regioni in tutto il mondo condividono un singolo spazio dei nomi. 

    Puoi utilizzare il tuo nome dell'istanza {{site.data.keyword.at_full_notm}} come parte del nome del bucket. Ad esempio, per un'istanza con il nome *logdna-1*, puoi utilizzare *accountN-logdna-1* come tuo nome del bucket.

    Avrai bisogno di questo nome per configurare l'archiviazione mediante l'IU web {{site.data.keyword.at_full_notm}}.

5. Scegli il tipo di resilienza e un'ubicazione dove desideri che vengano archiviati fisicamente i tuoi dati.

    La resilienza si riferisce alla portata e alla scala dell'area geografica in cui sono distribuiti i tuoi dati. 
    
    La resilienza tra le regioni diffonderà i tuoi dati in diverse aree metropolitane.
    
    La resilienza regionale diffonderà i dati in una singola area metropolitana. 
    
    Un singolo data center distribuirà i dati solo nei dispositivi in un singolo sito.

    Per ulteriori informazioni, vedi il documento relativo alla [selezione di regioni ed endpoint](/docs/services/cloud-object-storage?topic=cloud-object-storage-endpoints).

6. Scegli il tipo di *Classe di archiviazione*.

    Puoi creare i bucket con classi di archiviazione differenti. Scegli la classe di archiviazione per il tuo bucket in base ai tuoi requisiti per richiamare i dati. Per ulteriori informazioni, vedi il documento relativo all'[utilizzo delle classi di archiviazione](/docs/services/cloud-object-storage?topic=cloud-object-storage-classes).

    **Nota:** non è possibile modificare la classe di archiviazione di un bucket dopo che il bucket è stato creato. Se gli oggetti devono essere riclassificati, è necessario spostare i dati in un altro bucket con la classe di archiviazione desiderata.

7. Facoltativamente, aggiungi una chiave Key Protect per crittografare i dati inattivi.

    Tutti gli oggetti vengono crittografati per impostazione predefinita utilizzando chiavi generate casualmente e una modalità AONT (all-or-nothing-transform). Nonostante questo modello di crittografia fornisca la sicurezza sui dati inattivi, alcuni carichi di lavoro devono disporre delle chiavi di crittografia utilizzate. Per ulteriori informazioni, vedi il documento relativo alla [gestione della crittografia](/docs/services/cloud-object-storage?topic=cloud-object-storage-encryption).




## Passo 4. Crea un ID servizio per l'istanza di {{site.data.keyword.cos_full_notm}}
{: #archiving_step4}

Un ID servizio identifica un servizio analogamente a come un ID utente identifica un utente. Gli ID servizio non sono collegati a un utente specifico. Se l'utente che crea l'ID servizio lascia la tua organizzazione e viene eliminato dall'account, l'ID servizio rimane.

Devi creare un ID servizio per la tua istanza {{site.data.keyword.cos_full_notm}}. Questo ID servizio viene utilizzato dall'istanza {{site.data.keyword.at_full_notm}} per autenticarsi presso la tua istanza {{site.data.keyword.cos_full_notm}} . 

Devi assegnare specifiche politiche di accesso all'ID servizio che limitano le autorizzazioni per l'utilizzo di specifici servizi o anche combinare le autorizzazioni per l'accesso a servizi differenti. Ad esempio, per limitare l'accesso a un singolo bucket, assicurati che l'ID servizio non abbia alcuna politica a livello di istanza utilizzando la console o la CLI.


Completa la seguente procedura per creare un ID servizio con autorizzazioni in scrittura per l'istanza {{site.data.keyword.cos_full_notm}}:

1. Dal dashboard, seleziona l'istanza {{site.data.keyword.cos_full_notm}} dove intendi creare il bucket.

2. Seleziona **Credenziali del servizio**. Seleziona quindi **Nuova credenziale**.

3. Immetti un nome che è facilmente riconoscibile. Ad esempio, puoi utilizzare l'ID servizio `activity-tracker-cos-serviceID`. 

4. Seleziona il ruolo **Scrittore**.

5. Fai clic su **Aggiungi**.

    Un nuovo ID servizio viene creato e aggiunto all'elenco. 

    **Nota:** l'ID servizio creato in {{site.data.keyword.cloud_notm}} ed elencato tramite l'IU IAM ha un nome generico. Il nome dell'ID servizio nell'IU IAM si associa al valore del campo **iam_apikey_name** che hai creato in questo passo tramite l'IU ID servizio COS.
    
6. [Facoltativo] Per impedire che l'ID servizio venga eliminato, dalla barra dei menu, fai clic su **Gestisci** &gt; **Accesso (IAM)**. Cerca l'ID servizio. Quindi, seleziona l'azione **Blocca**.


Per l'ID servizio che hai appena creato, fai clic su **Visualizza credenziali**. Puoi vedere le informazioni correlate all'ID servizio. 

* Copia la chiave API. Questo è il valore impostato per il campo **apikey**.
* Copia l'ID dell'istanza della risorsa. Questo è il valore impostato per il campo **resource_instance_id**.
* Copia il valore del campo **iam_apikey_name**.


## Passo 5. Limita l'ID servizio in modo che disponga solo delle autorizzazioni in scrittura per il bucket
{: #archiving_step5}

Se desideri limitare l'ID servizio in modo che disponga solo delle autorizzazioni in scrittura per un bucket, completa la seguente procedura:

1. Dall'IU COS, seleziona il bucket.

2. Dal menu del bucket, seleziona **Policies**. Si apre la pagina *Bucket access policies*.

3. Seleziona **Service IDs**.

4. Nel campo **Select a service ID**, cerca un ID servizio che ha il seguente nome: **auto-generated-serviceId-<ID che fa parte del valore iam_apikey_name>.

5. Seleziona l'ID servizio. Quindi, in **Access policies**, seleziona **Writer**.

6. Fai clic su **Create access policy**.



## Passo 6. Seleziona l'endpoint
{: #archiving_step6}

Un endpoint definisce dove cercare un bucket. Ci sono diversi endpoint a seconda della regione e del tipo di resilienza. Per ulteriori informazioni, vedi il documento relativo alla [selezione di regioni ed endpoint](/docs/services/cloud-object-storage?topic=cloud-object-storage-endpoints#endpoints).

Completa la seguente procedura per ottenere l'endpoint per il tuo bucket:

1. [Accedi al tuo account {{site.data.keyword.cloud_notm}} ![Icona link esterno](../../icons/launch-glyph.svg "Icona link esterno")](https://cloud.ibm.com/login){:new_window}.

	Dopo aver effettuato l'accesso, viene visualizzato il dashboard {{site.data.keyword.cloud_notm}}.

2. Dal dashboard, seleziona l'istanza {{site.data.keyword.cos_full_notm}} dove intendi creare il bucket.

3. Seleziona **Bucket**. Quindi, seleziona il bucket che hai creato in cui desideri archiviare gli eventi.

4. Seleziona **Configurazione**.

5. Copia uno degli endpoint privati. 



## Passo 7. Concedi le politiche IAM a un utente per archiviare gli eventi.
{: #archiving_step7}

La seguente tabella elenca le politiche di cui deve disporre un utente per configurare l'archiviazione degli eventi dall'IU web {{site.data.keyword.at_full_notm}} in un bucket in un'istanza {{site.data.keyword.cos_full_notm}}:

| Servizio                              | Ruolo                      | Autorizzazione concessa                  | 
|--------------------------------------|---------------------------|-------------------------------------|  
| `{{site.data.keyword.at_full_notm}}` | Ruolo della piattaforma: Visualizzatore     | Consente all'utente di visualizzare l'elenco di istanze del servizio nel dashboard di registrazione Osservabilità. |
| `{{site.data.keyword.at_full_notm}}` | Ruolo del servizio: Gestore     | Consente all'utente di avviare l'IU web e visualizzare gli eventi nell'IU web.                             |
{: caption="Tabella 2. Politiche IAM" caption-side="top"} 

[Ulteriori informazioni](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-iam#iam).

Completa la seguente procedura per assegnare a un utente l'autorizzazione per archiviare gli eventi: 

1. Dalla barra dei menu, fai clic su **Gestisci** &gt; **Accesso (IAM)** e seleziona **Utenti**.
2. Dalla riga per l'utente a cui vuoi assegnare l'accesso, seleziona il menu **Azioni** e fai quindi clic su **Assegna accesso**.
3. Seleziona **Assegna l'accesso in un gruppo di risorse**.
4. Seleziona un gruppo di risorse.
5. Se all'utente non è stato già concesso un ruolo per il gruppo di risorse selezionato, scegli un ruolo per il campo **Assegna accesso a un gruppo di risorse**. 

    A seconda del ruolo che selezioni, l'utente può visualizzare il gruppo di risorse nel suo dashboard, modificarne il nome o gestire l'accesso degli utenti ad esso. 
    
    Puoi selezionare **Nessun accesso**, se vuoi che l'utente abbia accesso solo al servizio {{site.data.keyword.at_full_notm}} nel gruppo di risorse.

6. Seleziona **IBM Log Analysis con LogDNA**.
7. Seleziona il ruolo della piattaforma **Visualizzatore**.
8. Seleziona il ruolo del servizio **Gestore**.
9. Fai clic su **Assegna**.



## Passo 8. Configura l'archiviazione per la tua istanza {{site.data.keyword.at_full_notm}}
{: #archiving_step8}


Completa la seguente procedura per configurare l'archiviazione della tua istanza {{site.data.keyword.at_full_notm}} in un bucket COS:

1. [Avvia l'IU web {{site.data.keyword.at_full_notm}}](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-launch).

2. Seleziona l'icona **Configuration**. Seleziona quindi **Archiving**. 

3. Seleziona **IBM Cloud Object Storage**.

4. Configura il bucket, l'endpoint, la chiave API e l'ID istanza in cui desideri vengano archiviati gli eventi.

    <table>
      <caption>Tabella 3. Campi COS</caption>
      <tr>
         <th>Campo</th>
         <th>Valore</th>
      </tr>
      <tr>
         <td>Bucket</td>
         <td>Imposta sul nome del bucket COS. </td>
      </tr>
      <tr>
         <td>Endpoint</td>
         <td>Imposta sull'endpoint privato del bucket COS.</td>
      </tr>
      <tr>
         <td>Chiave API</td>
         <td>Imposta sulla chiave API associata all'ID servizio COS.</td>
      </tr>
      <tr>
         <td>ID istanza</td>
         <td>Imposta sull'ID istanza COS. </td>
      </tr>
    </table>

5. Fai clic su **Save**.


Dopo che hai salvato la configurazione, gli eventi vengono archiviati una volta al giorno.



