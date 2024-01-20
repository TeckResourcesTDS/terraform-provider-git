# terraform-provider-git

This is a Teck customized provider created few year ago based on [github provider](https://registry.terraform.io/providers/integrations/github/latest). Because at that time, the github provider and terraform function didn't have what we need on GitHub repository file management. At the time of this note, related functionalities are replaced by 

and the code based is lost.

This provider was used in project factory [fix: remove git provider footprint](https://github.com/TeckResourcesCloudInfrastructurePrivate/terraform-cloud-project-factory/pull/235).

Below are the potential functionalities/behaviors based on the previous usage in project factory. 

  - ```git_files``` resource does a series of git actions as below
    * clone repo
    * checkout new branch
    * write folders/files to local repo
    * add folders/files
    * commit changes
    * push to remote branch
    * delete local copies

  - ```git_files``` seems does ```git commit --allow-empty -m "<commit message>" ``` to allow openning empty pull request in github.

  - ```git_files``` doesnt remove branch, and we have delete_branch_on_merge enabled on repo level to aviod dangling patching branches.
  
  - ```git_files``` resouce has an issue on destory, for some reason the patching branch will be created in portal, this means trigger git_file resource by detecting the difference between factory and github file wont work properly.






that creates LZ repo and perform LZ repo file patching.

Repo file patching will be triggered when there is a change in any provided file which marked as manage, and will beperformed via pull request.

## Notes

Below note will describe git module in details and also contains few notes for future development.

### Github Repository Onboarding

  - Github repository will be provisioned as following
  ![Github Repository Onboarding Dependency Tree](./github.png)
  - ```resource "git_files" "default_nonmanage" ``` Dump ALL(managed and unmanaged) files to default(main) and not managing them on going forward.
  - ```resource "git_files" "default_manage" ``` is where managed file will be managed on-going forward.
  - Due to the how terraform ```templatefile``` works, files(managed and unmanaged) need to be under the root path of azure module.

### Github Repository File Pathcing

  - As per the usage of ```github_repository_pull_request``` from github official provider, this resource can only be re-created when base_ref or head_ref changes. Therefore, we generate head_ref (patch branch name) based on the md5 of *managed* files content on *factory* side only to make sure new PR will always be triggered when factory file changes.

  - Maximium git hub branch name length is 255

  - Why not track both factory and repo file change
    * Pros: it triggers PR when file doesnt match with each end.
    * Cons it causes empty pr when patch pr gets merged and rerun LZ onboarding pipeline, this behavior will likely confuse LZ users.



  - ```github_branch``` if you want to create and manage patch branch via official github provider other than teck git provider. Make sure this issue is fixed: https://github.com/integrations/terraform-provider-github/issues/981

  - ```github_repository_file``` is not the perfect resource to manage the unmanage file, because even ignore_change is enabeld, terraform will still read the file in plan. However, there is a possibility that unmanaged file get removed from repo.
