# Iris

[Blog Post](https://blog.doit-intl.com/auto-tagging-google-cloud-resources-6647cc7477c5)

In Greek mythology, Iris (/ˈaɪrɪs/; Greek: Ἶρις) is the personification of the rainbow and messenger of the gods. Iris was mostly the handmaiden to Hera.

Iris helps automatically assign labels to Google Cloud resources for better manageability and billing reporting. Each resource in Google Cloud will get an automatically generated label in a form of [iris_name:name], [iris_region:region] and finally [iris_zone:zone]. For example if you have a Google Compute Engine instance named `nginx`, Iris will automatically label this instance with [iris_name:nginx], [iris_region:us-central1] and [iris_zone:us-central1-a].

Iris will also label short-lived Google Compute Engine instances such as preemptible instances or instances managed by an Instance Group Manager by listening to Operations (Stackdriver) Logs and adding required labels on-demand. 

**Supported Google Cloud Products**

Iris is extensible through plugins. 
Right now, there are plugins for the following products:

* Google Compute Engine Disks
* Google Compute Engine Snapshots
* Google Cloud Storage
* Google CloudSQL (not yet implemented)
* Google BigQuery
* Google BigTable

**Installation**

We recommend deploying Iris in a
[new project](https://cloud.google.com/resource-manager/docs/creating-managing-projects#creating_a_project)
within your Google Cloud organization. 

You will need the following IAM permissions on your Google Cloud _organization_ (not just the project) 
to complete the deployment: 

 * App Engine Admin
 * Logs Configuration Writer
 * Pub/Sub Admin

##### Deploy
* Optionally edit `app.yaml`, changing the secret token for PubSub.
* Run  `./deploy.sh <project-id>` 

##### Configuration

Configuration is stored in the `config.json` file, which includes.

`labels` - A list of labels that will be applied to the resources 
(if the plugin implements a function by the name `_get_<LABELNAME>`).

### Local Development
For local development, rename and edit `dev_config.json.template`
to `dev_config.json`, edit it,then run `main.py` as an ordinary Flask application.

## Extension with plugins
Iris is easily extensible to support labeling of other GCP resources. 
You will need to create a Python file in the `/plugins` directory,
holding a subclass of `Plugin`. 

The Python file and class name should be the same, except for case:
The filename should be lowercase and the class name should be in Title case 
(only the first character should be in upper case).
 
In each class, in addition to implementing abstract methods,
you will need `_get_<LABELNAME>` methods. If the resource
cannot receive labels on-demand (as the result of a log event
generated when the resource is created), then also
override `is_on_demand` and return `False`.

