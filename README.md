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

Please note:

* These demo steps expect an Ansible control host set up read-to-use in AWS. If you want to set up a control host in your environment follow the steps in the REAMDE file contained in the AEM configuration management project generated in step 3.
* Some URLs point to IP addresses or hostnames that where set up only for the adaptTo() talk. Remove them or replace them with your own URLs.


## 1. Create AEM project by archetype (local)

```
mvn archetype:generate -DinteractiveMode=false \
  -DarchetypeGroupId=io.wcm.maven.archetypes \
  -DarchetypeArtifactId=io.wcm.maven.archetypes.aem \
  -DarchetypeVersion=2.0.2 \
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

Open application on author instance: http://localhost:4502/editor.html/content/adaptToDemo2018/en.html


## 2. Add distribution management to deploy to our Maven Repository (local)

```
  <distributionManagement>
    <repository>
      <id>adaptto</id>
      <url>http://52.215.173.143:8080/repository/maven-releases/</url>
    </repository>
    <snapshotRepository>
      <id>adaptto-snapshots</id>
      <url>http://52.215.173.143:8080/repository/maven-snapshots/</url>
    </snapshotRepository>
  </distributionManagement>
 ```

Deploy generated AEM application to Maven repository with `npm clean deploy`.


## 3. Generate AEM configuration management project by archetype (AWS Control Host)

Put the following files in the directory where you excute the archetype command:

* `license.properties` - AEM license file
* `id_rsa.pub` - public key that is allowed to connect via SSH to AWS machines

_**Please note:** The Maven Archetype Plugin from Maven Central is missing the required feature [ARCHETYPE-536](https://issues.apache.org/jira/browse/ARCHETYPE-536) - until this feature is released use the unofficial version `3.0.2-180806-A536` as shown below. This is available in the "wcm.io Intermediate Release Repository" described in [wcm.io Maven Repositories](http://wcm.io/maven.html)._

```
mvn org.apache.maven.plugins:maven-archetype-plugin:3.0.2-180806-A536:generate -DinteractiveMode=false \
  -DarchetypeGroupId=io.wcm.maven.archetypes \
  -DarchetypeArtifactId=io.wcm.maven.archetypes.aem-confmgmt \
  -DarchetypeVersion=1.0.2 \
  -DconfigurationManagementName=adaptto-demo-2018 \
  -DprojectName=adaptToDemo2018 \
  -DgroupId=to.adapt \
  -DartifactId=to.adapt.demoapp \
  -Dversion=1.0.0-SNAPSHOT \
  -DawsMachineSize=large \
  -DoptionAnsible=y \
  -DoptionTerraform=y \
  -DoptionVagrant=y \
  -DansibleVaultPassword=YOUR_PASSWORD \
  -DaemAdminPassword=YOUR_PASSWORD \
  -DmavenRepositoryUrl=http://52.215.173.143:8080/repository/adaptto/ \
  -DmavenRepositoryUser=adaptto \
  -DmavenRepositoryPassword=YOUR_PASSWORD \
  -DjavaDownloadBaseUrl=http://52.215.173.143/otn-pub/java
```

## 4. Create machines via terraform (AWS Control Host)

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

## 5. Run Ansible playbook to setup PROD machines (AWS Control Host)

```
cd ansible
ansible-playbook playbook-setup-prod.yml
```

## 6. Put host names for testing in your local host file (local)

As generated to `ansible/files/tmp/hosts_prod` (file to be found on AWS Control Host).


## 7. Ready!

PROD environment URLs:

* Publish 1: http://publish1.website1.com
* Publish 2: http://publish2.website1.com
* Author: https://author.website1.com


References / Further Links
--------------------------

* [wcm.io Maven Tooling](http://wcm.io/tooling/maven/)
* [wcm.io DevOps CONGA](http://devops.wcm.io/conga/)
* [wcm.io DevOps CONGA Training Materials](http://training.wcm.io/conga/)
* [wcm.io DevOps Ansible Automation for AEM](http://devops.wcm.io/ansible-aem/)
* Need Help? Use the [wcm.io Mailing Lists](http://wcm.io/mailing-lists.html)
