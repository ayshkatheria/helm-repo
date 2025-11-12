# helm-repo

1. To change the chart name at the time of chart creation, you simply provide the name you want when you run the helm create command.

> helm create <new-chart-name>  
> helm create webapp-color

2. Package your chart  
> helm package .

3. Create a directory to act as repo  
> mkdir helm-repo  
- move .tgz file into this directory  
> mv mychart-*.*.*.tgz helm-repo/  
- change the directory to helm-repo  
> cd helm-repo

4. Generate index .yaml  
> helm repo index . --url https://ayshkatheria.github.io/helm-repo/

Note :  
urls:  
  - webapp-color-0.1.0.tgz  
This is a relative path, but GitHub Pages requires the full absolute URL.

5. Host your repo  
- push your changes to git remote branch and make sure "index.yam" should be present  
- Goto settings and select page and publish it (wait for few seconds)

6. Now lets try to install same chart from repo  
- Add the repo to helm  
> helm repo add myrepo https://ayshkatheria.github.io/helm-repo/  
> helm search repo myrepo  

- if repo will not reflect update it in local  
> helm repo update

7. Install chart from repo  
> helm install myapp myrepo/chartname

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

# Helm Chart using OCI  
Q. How to Push chart and key

1. Need to generate PAT token classic with package permission

2. Then Authenticate it  
> echo "Add your token" | helm registry login ghcr.io --username ayshkatheria --password-stdin

3. Install GPG and generate keys  
> gpg --full-generate-key

4. Now export the key into .gpg format  
> gpg --export-secret-keys >~/.gnupg/secring.gpg  
> gpg --list-keys

5. Now package the chart with sign key  
> helm package --sign --key 'ayshtesting@gmail.com' --keyring ~/.gnupg/secring.gpg ./chartname  
> ls  
o/p ".tgz" & ".prov"

6. To upload a chart run push command  
> helm push webapp-color-0.1.0.tgz oci://ghcr.io/ayshkatheria/anyname  
(it will automatically create package with that name)

-----------------------------------------------------------------------------------------------------------------------------------------------------------------

Q. How to verify helm chat using pubkey and package  
- pull the chart with prov file  
1. helm pull oci://ghcr.io/ayshkatheria/hello-world/helloworld --version 0.1.0 --prov  

- Do ls and verify both the files are present  
".tgz" and ".prov"

2. Download pubkey from git repo just need to run below command no auth is required  
> curl -O https://raw.githubusercontent.com/ayshkatheria/helm-repo/main/mypubkey.asc  

- do cat key.asc and verify content tis there and not getting 404  

3. Import the key  
> gpg --import mypubkey.asc

4. Export it into .gpg as it wont support .kbx  
> gpg --export 'ayshtesting@gmail.com' > ~/.gnupg/pubring.gpg  
note: mail id or alisa will get running command  
> gpg --list-keys

5. Now verify the chart using key  
> helm verify helloworld-0.1.0.tgz --keyring ~/.gnupg/pubring.gpg
