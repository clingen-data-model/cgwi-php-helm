# Deployment setup for various ClinGen workflow/personnel management infrastructure

For the most part, this is a fairly-standard helm chart.

## site/project-specific configs

Example configuration values/defaults are provided in `values.yaml` as usual, but
actual deployment requires the credentials contained under the `cgwi-configs`
directory/submodule, which is doubly-protected:

- only accessible according to access to sc.unc.edu (includes need to use UNC VPN)
- furthermore protected by git-crypt (which you'd need to install along with git)

If you have the relevant access, then you hopefully cloned this repo with
`git clone --recursive`, or can attach the submodule with
`git submodule init && git submodule update`.

You will then have to `cd` to the `cgwi-configs` directory and `git crypt unlock KEYFILE`
with the relevant KEYFILE that you would have been provided.

## deploying a project

1. You of course need to be connected to the relevant k8s/openshift cluster
   (and in appropriate namespace for the project)
2. `helm list` lets you check to see what is installed currently using helm
   (and is a good check to make sure you are in the right namespace)
3. `--dry-run` is a good thing to add to the command before doing an actual
   deployment just to make sure there isn't an obvious problem with the command

A typical command would be:

```
helm install gpm-demo . --values cgwi-configs/demo/gpm-demo.values.yaml
```

helm will print out some helpful NOTEs when done, somewhat customized to this particular
installation. You can see them later with `helm status gpm-demo` (replacing project
name as needed).

*Important*: one key part of those NOTEs that it is important to read refers to the
fact that the StatefulSet for the main app is *not* started by default, to all
additional initialization to be run (e.g., data migration, changing to another
git branch) before starting the pod managed by that sts.
