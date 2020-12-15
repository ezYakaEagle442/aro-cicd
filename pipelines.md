# Tekton Pipelines

- [Tekton overview](https://tekton.dev/docs/overview)
- [TektonCD Pipelines](https://github.com/tektoncd/pipeline/blob/master/docs/pipelines.md)
- [Guide to OpenShift pipelines part 2](https://www.openshift.com/blog/guide-to-openshift-pipelines-part-2-using-source-2-image-build-in-tekton)
- [Guide to OpenShift pipelines part4](https://www.openshift.com/blog/guide-to-openshift-pipelines-part-4-application-deployment-and-pipeline-orchestration-1)

## pre-req
Check [tkn cli is installed](./tools.md#how-to-install-tekton-cli)

```sh
tkn help
tkn version
```

## Create a dummy pipeline

```sh
projectname=pipelines-tutorial #3scale-test
oc new-project $projectname

oc config current-context
oc status
oc projects
oc project $projectname

oc get serviceaccount pipeline
oc describe sa pipeline

oc adm policy add-scc-to-user privileged -z pipeline # system:serviceaccount:3scale-test:pipeline
# oc policy add-role-to-user registry-editor -z pipeline

oc adm policy add-role-to-user edit -z pipeline
oc describe scc privileged

for tkncrd in $(oc get crds -l app.kubernetes.io/part-of=tekton-pipelines -o=custom-columns=:.metadata.name)
do
  if [[ "$tkncrd"="*.tekton.dev.*" ]]
    then
      echo "Verifying CRD $tkncrd"
      oc describe crd $tkncrd | grep -i "Short Names"
  fi
done



oc create -f https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/01_apply_manifest_task.yaml
oc create -f https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/02_update_deployment_task.yaml
oc create -f https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/03_persistent_volume_claim.yaml
tkn task ls

oc apply -f https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/04_pipeline.yaml
tkn pipeline list

# Lets start a pipeline to build and deploy backend application using tkn:
tkn pipeline start build-and-deploy \
    -w name=shared-workspace,volumeClaimTemplateFile=https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/03_persistent_volume_claim.yaml \
    -p deployment-name=vote-api \
    -p git-url=https://github.com/openshift-pipelines/vote-api.git \
    -p IMAGE=image-registry.openshift-image-registry.svc:5000/$projectname/vote-api

# Similarly, start a pipeline to build and deploy frontend application:
tkn pipeline start build-and-deploy \
    -w name=shared-workspace,volumeClaimTemplateFile=https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/03_persistent_volume_claim.yaml \
    -p deployment-name=vote-ui \
    -p git-url=https://github.com/openshift-pipelines/vote-ui.git \
    -p IMAGE=image-registry.openshift-image-registry.svc:5000/$projectname/vote-ui

tkn pipeline list
tkn pipelinerun ls
tkn pipeline logs -f

# to re-run the pipeline again, use the following short-hand command to rerun the last pipelinerun again that uses the same workspaces, params and sa used in the previous pipeline run:
tkn pipeline start build-and-deploy --last

#  get the route of the application by executing the following command and access the application
oc get route vote-ui --template='http://{{.spec.host}}'

```

## Create a Pipeline for a SpringBootApp

Here you can check git_url_springboot="https://github.com/spring-projects/spring-petclinic.git" defined in [set-var.md](./set-var.md)
Fork it on GitHub and then define it [set-var.md](./set-var.md) with git_url="https://github.com/your-project/xxx.git"
ex: git_url=https://github.com/ezYakaEagle442/gs-spring-boot-spock

```sh

# https://github.com/openshift/tektoncd-pipeline-operator/blob/master/deploy/resources/addons/02-clustertasks/s2i-java-8-pr/s2i-java-8-pr-task.yaml
oc apply -f https://raw.githubusercontent.com/ezYakaEagle442/aro-cicd/main/cnf/05_pipeline_java8.yaml
tkn pipeline describe build-and-deploy-java-8
# tkn clustertask describe s2i-java-8
# tkn clustertask describe maven

oc apply -f https://raw.githubusercontent.com/ezYakaEagle442/aro-cicd/main/cnf/06_maven_pvc.yaml
oc apply -f https://raw.githubusercontent.com/ezYakaEagle442/aro-cicd/main/cnf/07_maven_config_map.yaml
oc apply -f https://raw.githubusercontent.com/ezYakaEagle442/aro-cicd/main/cnf/08_java_pipeline_run.yaml

tkn pipeline start build-and-deploy-java-8 \
    -w name=shared-workspace,volumeClaimTemplateFile=https://raw.githubusercontent.com/openshift/pipelines-tutorial/master/01_pipeline/03_persistent_volume_claim.yaml \
    -p deployment-name=spring-boot-hello-demo \
    -p git-url=$git_url \
    -p git-revision=master \
    -p manifest-dir=k8S-app \
    -p IMAGE=image-registry.openshift-image-registry.svc:5000/$projectname/spring-boot-hello-demo

tkn pipeline list
tkn pr ls # tkn pipelinerun ls
tkn tr ls
tkn pipeline logs -f

# tkn tr describe build-and-deploy-java-8-run-6whb8-java-format-jz7nd
Status
STARTED          DURATION    STATUS
26 minutes ago   ---         Failed(TaskRunValidationFailed)
Message
bound workspaces did not match declared workspaces: didn't provide required values: [maven-settings]

# https://vincent.demeester.fr/articles/tekton-pipeline-without-pipeline-resources.html

```