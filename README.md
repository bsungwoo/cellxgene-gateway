# Overview

Cellxgene Gateway allows you to use the Cellxgene Server provided by the Chan Zuckerberg Institute (https://github.com/chanzuckerberg/cellxgene) with multiple datasets. It displays an index of available h5ad (anndata) files. When a user clicks on a file name, it launches a Cellxgene Server instance that loads that particular data file and once it is available  proxies requests to that server.

# Running locally

## Prequisites

1. This project requires python 3.6 or higher. Please check your version with

```bash
$ python --version
```

2. It is also a good idea to set up a venv

```bash
python -m venv .cellxgene-gateway
source .cellxgene-gateway/bin/activate # type `deactivate` to deactivate the venv
```

## Install cellxgene-gateway

### Option 1: Pip Install from Github

```bash
pip install git+https://github.com/Novartis/cellxgene-gateway
```
Note: you may need to downgrade h5py with `pip install h5py==2.9.0` due to an [issue](https://github.com/theislab/scanpy/issues/832) in a dependency.
### Option 2: Install from PyPI

```bash
# NOT YET DONE, COMING! STAY TUNED
```

## Running cellxgene gateway

1. Prepare a folder with .h5ad files, for example

```bash
mkdir ../cellxgene_data
wget https://github.com/chanzuckerberg/cellxgene/raw/master/example-dataset/pbmc3k.h5ad -O ../cellxgene_data/pbmc3k.h5ad
```


2. Set your environment variables correctly:

```bash
export CELLXGENE_DATA=../cellxgene_data  # change this directory if you put data in a different place.
export CELLXGENE_LOCATION=`which cellxgene`
```

3. Now, execute the cellxgene gateway:

```bash
cellxgene-gateway
```

Here's what the environment variables mean:

* `CELLXGENE_LOCATION` - the location of the cellxgene executable, e.g. `~/anaconda2/envs/cellxgene/bin/cellxgene`
* `CELLXGENE_DATA` - a directory that can contain subdirectories with `.h5ad` data files, *without* trailing slash, e.g. `/mnt/cellxgene_data`
Optional environment variables:
* `EXTERNAL_HOST` - the hostname and port from the perspective of the web browser, typically `localhost:5005` if running locally. Defaults to "localhost:{GATEWAY_PORT}"
* `EXTERNAL_PROTOCOL` - typically http when running locally, can be https when deployed if the gateway is behind a load balancer or reverse proxy that performs https termination. Default value "http"
* `GATEWAY_IP` - ip addess of instance gateway is running on, mostly used to display SSH instructions. Defaults to `socket.gethostbyname(socket.gethostname())`
* `GATEWAY_PORT` - local port that the gateway should bind to, defaults to 5005
* `GATEWAY_EXTRA_SCRIPTS` - JSON array of script paths, will be embedded into each page and forwarded with `--scripts` to cellxgene server
* `GATEWAY_ENABLE_UPLOAD` - Set to `true` or `1` to enable HTTP uploads. This is not recommended for a public server.
* `GATEWAY_ENABLE_ANNOTATIONS` - Set to `true` or to `1` to enable cellxgene annotations. 
* `GATEWAY_ENABLE_BACKED_MODE` - Set to `true` or to `1` to load AnnData in file-backed mode. This saves memory and speeds up launch time but may reduce overall performance.  

The defaults should be fine if you set up a venv and cellxgene_data folder as above.

# Customization

The current paradigm for customization is to modify files during a build or deployment phase:

* To modify CSS or JS on particular gateway pages, overwrite or append to the templates
* To add script tags such as for user analytics to all pages, set GATEWAY_EXTRA_SCRIPTS
  * these scripts will also be run on the pages served by cellxgene server via the --scripts parameter
  * See https://github.com/chanzuckerberg/cellxgene/pull/680 for details on --scripts parameter

Currently we use a bash script that copies the gateway to a "build" directory before modifying templates with sed and the like. There is probably a better way.

# Development

We’re actively developing.  Please see the "future work" section of the [wiki](https://github.com/Novartis/cellxgene-gateway/wiki#future-work). If you’re interested in being a contributor please reach out to [@alokito](https://github.com/alokito).

## Developer Install

If you want to develop the code, you will need to clone the repo. Make sure you have the prequesite listed above, then:

1. Clone the repo

```bash
    git clone https://github.com/Novartis/cellxgene-gateway.git
    cd cellxgene-gateway
```

2. Install requirements with

```bash
pip install -r requirements.txt
```

3. Install the gateway in developer mode

```bash
python setup.py develop
```

For convenience, the code repo includes a `run.sh.example` shell script to run the gateway.

## Running Tests

[![Build Status](https://travis-ci.org/Novartis/cellxgene-gateway.svg?branch=master)](https://travis-ci.org/Novartis/cellxgene-gateway)

```bash
    python -m unittest discover tests
```

## Running Linters

pip install isort flake8 black

```bash
isort -rc .
flake8 .
black -l 79 .
```

# Getting Help

If you need help for any reason, please make a github ticket. One of the contributors should help you out.

# Contributors

* Niket Patel - https://github.com/NiketPatel9
* Alok Saldanha - https://github.com/alokito
* Yohann Potier - https://github.com/ypotier
