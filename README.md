adaptTo() 2018 - Maven Archetypes for AEM
=========================================

Demo script for talk at adaptTo() 2018:<br/>
https://adapt.to/2018/en/schedule/maven-archetypes-for-aem.html


Requirements
------------

* Java 8
* Maven 3.3.9
* AEM 6.4
* AWS


Demo Steps
----------

## Create AEM project by archetype (local)

```
mvn archetype:generate -DinteractiveMode=false \
  -DarchetypeGroupId=io.wcm.maven.archetypes \
  -DarchetypeArtifactId=io.wcm.maven.archetypes.aem \
  -DarchetypeVersion=2.0.0 \
  -DprojectName=adaptToDemo2018 \
  -DgroupId=to.adapt \
  -DartifactId=to.adapt.demoapp \
  -Dversion=1.0.0-SNAPSHOT \
  -Dpackage=to.adapt.demoapp \
  -DpackageGroupName=adaptTo \
  -DoptionAemVersion=6.4 \
  -DoptionAemServicePack=n \
  -DoptionSlingModelsLatest=n \
  -DoptionSlingInitialContentBundle=n \
  -DoptionEditableTemplates=y \
  -DoptionMultiBundleLayout=n \
  -DoptionContextAwareConfig=n \
  -DoptionWcmioHandler=n \
  -DoptionAcsCommons=n \
  -DoptionFrontend=y
```

Build and deploy to local AEM instance using `build-deploy.sh` (the first "npm install" will take a while).


## Add distribution management to deploy to our Maven Repository (local)

```
  <distributionManagement>
    <repository>
      <id>adaptto</id>
      <url>http://maven-repo.adapt.to:8080/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
      <id>adaptto-snapshots</id>
      <url>http://maven-repo.adapt.to:8080/repository/maven-snapshots/</url>
    </snapshotRepository>
  </distributionManagement>
 ```

Open application on author instance: http://localhost:4502/editor.html/content/adaptToDemo2018/en.html


## Generate AEM configuration management project by archetype (AWS)

Put the following files in the directory where you excute the archetype command:

* `license.properties` - AEM license files
* `id_rsa.pub` - public key that is allowed to connect via SSH to AWS machines

```
mvn org.apache.maven.plugins:maven-archetype-plugin:3.0.2-180806-A536:generate -DinteractiveMode=false \
  -DarchetypeGroupId=io.wcm.maven.archetypes \
  -DarchetypeArtifactId=io.wcm.maven.archetypes.aem-confmgmt \
  -DarchetypeVersion=1.0.0 \
  -DconfigurationManagementName=adaptto-demo-2018 \
  -DprojectName=adaptToDemo2018 \
  -DgroupId=to.adapt \
  -DartifactId=to.adapt.demoapp \
  -Dversion=1.0.0-SNAPSHOT \
  -DoptionAnsible=y \
  -DoptionTerraform=y \
  -DoptionVagrant=n \
  -DansibleVaultPassword=YOUR_PASSWORD \
  -DaemAdminPassword=YOUR_PASSWORD \
  -DmavenRepositoryUrl=http://maven-repo.adapt.to:8080/repository/maven-public/ \
  -DmavenRepositoryUser=adaptto \
  -DmavenRepositoryPassword=YOUR_PASSWORD
```

## Create machines via terraform

Create terraform state and AWS machines.

```
cd terraform/terraform-state
terraform init
terraform plan -out state
terraform apply state

cd ..
terraform init
terraform plan -out infrastructure
terraform apply infrastructure
```

## Run Ansible playbook to setup PROD machines

```
cd ansible
ansible-playbook playbook-setup-prod.yml
```

## Put host names for testing in your local host file

As generated to `ansible/files/tmp/hosts`.


## Ready!

PROD environment URLs:

* Publish 1: http://publish1.website1.com
* Publish 2: http://publish2.website1.com
* Author: https://author.website1.com
