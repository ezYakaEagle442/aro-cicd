# aro-cicd
Azure RedHat OpenShift CI/CD build &amp; pipeline 101

# Introduction
This is an introduction to play with CI/CD in ARO : GitHub Actions & Tekton pipelines


## **Disclaimer**

**The features described in this workshop might be not yet production-ready, we enable preview-features for the purpose of learning.**

See also :

- [Azure ARO docs](https://docs.openshift.com/aro/4/builds/build-strategies.html#builds-understanding-openshift-pipeline)
- [Pipelines with Tekton  - OpenShift Blog](https://www.openshift.com/blog/pipelines_with_tekton)
- [Learn OpenShift](https://learn.openshift.com)
- [Learn OpenShift Pipelines](https://learn.openshift.com/middleware/pipelines/)
- [OpenShift pipeline tutorial](https://github.com/OpenShift/pipelines-tutorial)
- [Tekton overview](https://tekton.dev/docs/overview)
- [TektonCD Pipelines](https://github.com/tektoncd/pipeline/blob/master/docs/pipelines.md)
- [Guide to PenShift pipelines](https://www.openshift.com/blog/guide-to-openshift-pipelines-part-2-using-source-2-image-build-in-tekton)
- [Github Actions](https://github.com/features/actions)
- [RedHat & GitHub press release](https://www.redhat.com/en/about/press-releases/red-hat-and-github-collaborate-expand-developer-experience-red-hat-openshift-github-actions)
- [RedHat actions from GitHub marketplace](https://github.com/marketplace?query=redhat)

## ToC

1. Setup [Tools](tools.md)
1. Check [subscription](subscription.md)
1. Setup [environment variables](set-var.md)
1. Setup [pre-requisites](setup-prereq.md)
   1. Create RG
   1. Create Storage
   1. Get a Red Hat pull secret
   1. Setup [Network](setup-network.md)
1. Setup [ARO cluster](setup-aro.md)
1. Setup [HELM](setup-helm.md)
1. Setup [GitHub Action](action.md)
1. Setup [Openshift Pipelines](pipelines.md)

