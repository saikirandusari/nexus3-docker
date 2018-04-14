# Automate nexus configuration with scripting API

Nexus 3 comes with a groovy script API that can be uploaded and executed on nexus through a REST API. More information
[here](https://help.sonatype.com/display/NXRM3/REST+and+Integration+API).

To develop a groovy script, it's easier to use a dependency system such as gradle or maven to manage it so we can have auto
completion in our favourite IDE.

Then to upload this script, it must be encoded in the Nexus REST JSON API. Of course we could directly write the groovy script
inside the JSON format which is completely fine and manageable for small script but when script gets bigger,
it becomes harder to manage too.

The goal of this project is to show an example of how nexus 3 configuration can be versioned and managed in a VCS repository such as git,
so your complete CI/CD pipeline infrastructure can be managed as code.

## Openshift

This example has been tested with [Minishift](https://github.com/minishift/minishift).

To start a new Openshift instance:

```sh
minishift config set cpus 4
minishift config set memory 4GB
minishift start
```

### Nexus 3 deployment

This assume you are working in cicd project. If not, please change the project name in the pipeline and adapt the following accordingly:

```sh
# if it does not exist yet
oc new-project cicd
# declare variables
version=latest
# import official nexus3 image
oc import-image nexus3:${version} --from=docker.io/sonatype/nexus3:${version} --confirm
# create the pipeline to build and deploy nexus
oc create -f https://raw.githubusercontent.com/arnaud-deprez/nexus3-docker/master/openshift/pipeline.yml
# start the pipeline
oc start-build nexus3-pipeline
```