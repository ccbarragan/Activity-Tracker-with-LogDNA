---

copyright:
  years: 2019
lastupdated: "2019-04-04"

keywords: IBM Cloud, LogDNA, Activity Tracker, web UI, browser

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

# Navegación a la interfaz de usuario web
{: #launch}

Tras suministrar una instancia del servicio {{site.data.keyword.at_full_notm}} en {{site.data.keyword.cloud_notm}}, puede ver, supervisar y gestionar sucesos a través de la interfaz de usuario web de {{site.data.keyword.at_full_notm}}.
{:shortdesc}


## Paso 1. Otorgar políticas de IAM a un usuario para visualizar datos 
{: #step1}

**Nota:** debe ser administrador del servicio {{site.data.keyword.at_full_notm}}, administrador de una instancia de {{site.data.keyword.at_full_notm}} o tener permisos de IAM de cuenta para poder otorgar políticas a otros usuarios.

En la tabla siguiente se muestra la política mínima que debe tener un usuario para poder iniciar la interfaz de usuario web y ver los datos a través de la interfaz de usuario web de {{site.data.keyword.at_full_notm}}:

| Rol                      | Permiso otorgado       |
|---------------------------|---------------------|
| Rol de la plataforma: `Visor`   | Permite al usuario ver la lista de instancias de servicio en el panel de control Observabilidad. |
| Rol de servicio: `Lector`    | Permite al usuario ver sucesos mediante la interfaz de usuario web. | 
{: caption="Tabla 1. Políticas de IAM" caption-side="top"} 

Para obtener más información, consulte
[Cómo otorgar permisos de usuario a un usuario o ID de servicio](/docs/services/Activity-Tracker-with-LogDNA?topic=logdnaat-iam_view_events#iam_view_events).


## Paso 2. Iniciar la interfaz de usuario web a través de la interfaz de usuario de {{site.data.keyword.cloud_notm}}
{: #launch_step2}

Puede iniciar la interfaz de usuario web dentro del contexto de una instancia de {{site.data.keyword.at_full_notm}}, desde la interfaz de usuario de {{site.data.keyword.cloud_notm}}. 

Siga los pasos siguientes para iniciar la interfaz de usuario web:

1. [Inicie sesión en su cuenta de {{site.data.keyword.cloud_notm}} ![Icono de enlace externo](../../icons/launch-glyph.svg "Icono de enlace externo")](https://cloud.ibm.com/login){:new_window}.

	Después de iniciar sesión con su ID de usuario y su contraseña, se abre el panel de control de {{site.data.keyword.cloud_notm}}.

2. Pulse el icono de **Menú** ![icono de Menú](../icons/icon_hamburger.svg) > **Observabilidad**. 

3. Seleccione **Activity Tracker**. 

    Se muestra la lista de instancias de {{site.data.keyword.at_full_notm}}.

    Hay una instancia por región.
    {: important}

4. Seleccione la región donde desee ver los sucesos. A continuación, pulse **Ver LogDNA**.

Se abre la interfaz de usuario web de {{site.data.keyword.at_full_notm}} y muestra la vista **Todo**. A través de esta vista, puede ver los sucesos de su cuenta para la región que haya seleccionado.



## Obtención del URL de la interfaz de usuario web desde {{site.data.keyword.cloud_notm}}
{: #launch_get}

Para obtener el URL de la interfaz de usuario web, realice los pasos siguientes desde un terminal:

1. Establezca el grupo de recursos donde se ha suministrado la instancia de {{site.data.keyword.at_full_notm}}.

    ```
    export logdna_rg_name=<Resource_Group_Name_Where_LogDNA_Instance_Is_Created>
    ```
    {: codeblock}

2. Establezca el nombre de la instancia de {{site.data.keyword.at_full_notm}}.

    ```
    export logdna_instance_name=<Your_LogDNA_Instance_Name>
    ```
    {: codeblock}

3. Establezca el punto final.

    ```
    export rc_endpoint=resource-controller.cloud.ibm.com
    ```
    {: codeblock}

4. Establezca la señal IAM.

    ```
    export iam_token=$(ibmcloud iam oauth-tokens | grep IAM | grep -oP  "eyJ.*")
    ```
    {: codeblock}

    **Nota:** si trabaja en un terminal de MacOS, el mandato es el siguiente: `export iam_token=$(ibmcloud iam oauth-tokens | grep IAM | grep -o  "eyJ.*")`

5. Establezca el ID del grupo de recursos.

    ```
    export resource_group_id=$(ibmcloud resource groups | grep "^$logdna_rg_name" | awk '{print $2}')
    ```
    {: codeblock}

6. Establezca el URL de la interfaz de usuario web en una variable.

    ```
    export dashboard_url=$(curl -H "Accept: application/json" -H "Authorization: Bearer $iam_token" "https://$rc_endpoint/v1/resource_instances?resource_group_id=$resource_group_id&type=service_instance" | jq ".resources[] | select(.name==\"$logdna_instance_name\") | .dashboard_url")
    ```
    {: codeblock}

7. Obtenga el URL de la interfaz de usuario web.

    ```
    echo $dashboard_url
    ```
    {: codeblock}

    

