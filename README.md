# data4dappl

A web-site with additional pipelines and data processing that allow to browse and analyze data received from freedom of information requests sent by a group of Israeli lawyers and organizations.

The site is available here: https://www.odata.org.il/en/

## Development Quickstart

We use minikube to get a running environment which you can then do local development on

Fork and clone [hasadna/hasadna-k8s](https://github.com/hasadna/hasadna-k8s) and follow the [odata chart docs](https://github.com/hasadna/hasadna-k8s/blob/master/charts-external/odata/README.md) to setup a minikube environment.

The following script builds local docker image, send to minikube and starts port forwarding

```
docker build -t odata-ckan ckan &&\
bin/minikube_patch_image.sh ckan odata-ckan &&\
bin/minikube_port_forward.sh ckan 5000
```

Ckan should be available at http://localhost:5000

To speedup update of the ckan pod, you can set lower termination grace period

```
bin/minikube_kubectl.sh get deployment ckan -o yaml \
    | sed 's/terminationGracePeriodSeconds: 30/terminationGracePeriodSeconds: 1/g' \
    | bin/minikube_kubectl.sh replace -f -
```

For even faster development flow, you can run ckan locally but connect to the minikube infrastructure

The following command creates a ckan configuration file and port forwards all the infrastructure ports to localhost

```
bin/minikube_local_development.sh
```

Keep it running in the background, and start ckan locally from a Python virtualenv

We use [pipenv](https://docs.pipenv.org/) to manage the virtualenv and handle the dependencies

Install the required dependencies

```
bin/install.sh
```

Start ckan

```
pipenv run paster serve `pwd`/ckan/development-local.ini
```

Ckan is available at http://localhost:5000

## Installing the ckan extension on an existing ckan project

If you have an existing ckan installation and just want to install the odata plugin:

* Install the package (should run inside your ckan installations virtualenv):
  * `pip install -e 'git+https://github.com/OriHoch/data4dappl.git#egg=ckanext-odata_org_il&subdirectory=ckan/ckanext-odata_org_il'`
* Edit the configuration (E.g. /etc/ckan/default/development.ini)
  * add odata_org_il to ckan.plugins
* restart ckan
