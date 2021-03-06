# MTee platform configuration

This repository provides a minimal example of Kubernetes configuration to run the MTee platform. Configuration files are
only provided to illustrate how communication between components should be set up and should be revised before any
deployments.

For more information, please refer to the more detailed
platform [documentation](https://github.com/Project-MTee/mtee-platform/wiki) and to the documentation of each component
separately. A working demo of this platform can be found at [here](https://mt.cs.ut.ee).

The MTee platform consists of the following dockerized components:

- Existing standard components
    - [RabbitMQ message broker](https://www.rabbitmq.com/)
    - [MySQL database](https://www.mysql.com/)
    - [Clammit antivirus proxy](https://github.com/ifad/clammit)
    - [ClamAV antivirus](https://www.clamav.net/)
- Custom MTee components:
    - [Translation website](https://ghcr.io/project-mtee/website)
    - [Web translation proxy](https://ghcr.io/project-mtee/web-proxy)
    - [Translation API](https://ghcr.io/project-mtee/translation-api-service)
    - [Translation system service](https://ghcr.io/project-mtee/translation-system-service)
    - [Translation worker](https://ghcr.io/project-mtee/translation-worker)
    - [Domain detection worker](https://ghcr.io/project-mtee/domain-detection-worker)
    - [File translation service](https://ghcr.io/project-mtee/file-translation-service)
    - [File translation worker](https://ghcr.io/project-mtee/file-translation-worker)
- Integrated components:
    - [Grammatical error correction API](https://ghcr.io/tartunlp/grammar-api)
    - [Grammatical error correction worker](https://ghcr.io/tartunlp/grammar-worker)
    - [Automatic speech recognition API](https://ghcr.io/tartunlp/speech-to-text-api)
    - [Automatic speech recognition worker](https://ghcr.io/tartunlp/speech-to-text-worker)

## Platform setup

This sample requires an existing Kubernetes cluster with
the [NGINX ingress controller](https://kubernetes.github.io/ingress-nginx/deploy/) where the application can be deployed
to. For more information about setting up a cluster, please
see [Kubernetes documentation](https://kubernetes.io/docs/setup/). We also assume that the user
has [kubectl](https://kubernetes.io/docs/tasks/tools/) configured to connect to the cluster. For testing or development
purposes, setting up [minikube](https://minikube.sigs.k8s.io/docs/start/) and enabling the
[ingress controller](https://kubernetes.github.io/ingress-nginx/deploy/#minikube) should be sufficient.

- Create a new `secrets` directory from the template files found in the `secret_templates` directory.
- Check all files for any lines commented with `# TODO` as those lines refer to configuration that is likely different
  for different deployments.
- Apply configuration files using the command `kubectl create -f $filename`. Configuration files can be added in the
  following order to avoid any dependency conflicts.
    - All files in `secrets/`, `configuration/`, `storage/` and `services` directories
    - All files in `ingresses`
    - `deployments/rabbitmq.yaml` and `deployments/mysql.yaml`
    - All other files in `deployments/`

This should be sufficient to have the application running. However, there are other configuration aspects that may be
relevant depending on the deployment requirements:

- In case a namespace other than `default` is used, adding the `metadata.namespace` value to each configuration file.
- Configuring separate users for each application connecting to RabbitMQ.
- Configuring additional replicas and revising resource limits/requirements for each component depending on the expected
  workload.
- Configuring persistent storage for RabbitMQ and MySQL to ensure data persists after a restart.
- Using a [RabbitMQ Kubernetes operator](https://kubernetes.github.io/ingress-nginx/deploy/) instead.
- Revising the persistent volume sizes for file translation and speech recognition file storage depending on expected
  workload.
- Configuring ingress parameters:
  - maximum file upload size (`nginx.ingress.kubernetes.io/proxy-body-size`)
  - modify rate limits
  - etc.
- Reivew the version numbers of components. The version numbers used in this repository reflect the versions tested in
  the https://mt.cs.ut.ee demo environment, however, newer versions are likely also compatible.
