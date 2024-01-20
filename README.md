# terraform-provider-git

This is a Teck customized provider created few year ago based on [github provider](https://registry.terraform.io/providers/integrations/github/latest) to faciliate GitHub repository file management. At the time of this note, this provider can be considered as ***DEPRECATED*** because related functionalities can be reproduced with github provider and terraform functions.

This provider will still be functional as long as the binary is available under [release](https://github.com/TeckResourcesTDS/terraform-provider-git/releases) and it is published [here](https://registry.terraform.io/providers/TeckResourcesTDS/git/latest).

There shouldn't be any other usage previously other than project factory.

The code based was lost and there hasnt been any update since publish as per github commit history.

There was no documentation on how the code was built and how to use the code. 

---

Just in case provider need to be re-enabled before offcial ***DEPRECATION***, below notes are created for potential functionalities/behaviors based on the previous usage in project factory. [fix: remove git provider footprint](https://github.com/TeckResourcesCloudInfrastructurePrivate/terraform-cloud-project-factory/pull/235).

- Terraform configuration: 

```
terraform {
  required_providers {
    git = {
      source = "TeckResourcesTDS/git"
      version = "0.3.4"
    }
  }
}

provider "git" {
  # Configuration options
}
```

- Resources:

  - ```git_files```
  * this resource does a series of git actions as below
    * clone repo
    * checkout a new branch
    * write folders/files to local repo
    * add folders/files
    * commit changes
    * push to remote branch
    * delete local copies
  * this resource seems does ```git commit --allow-empty -m "<commit message>" ``` to allow openning empty pull request in github.
  * this resource doesnt remove the branch it creates.

