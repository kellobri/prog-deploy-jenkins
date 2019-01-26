# Programmatic RStudio Connect Content Updates through Jenkins and GitHub Webhooks

Freestyle Project Build Steps:

- Clones github repo with `dockerfile` and customized `upload-and-deploy.sh` shell script
- Builds the docker image
- Runs the docker image with mounted volume
- Executes the shell script (passes in hardcoded content GUID and location of the content code + manifest file)

```
rm -rf prog-deploy-jenkins
git clone https://github.com/kellobri/prog-deploy-jenkins.git
stat prog-deploy-jenkins/docker/Dockerfile
chmod 755 prog-deploy-jenkins/upload-and-deploy.sh

cd prog-deploy-jenkins/
docker build -t rstudio-connect-deployer:latest docker

stat deployment-bundle/manifest.json
stat prog-deploy-jenkins/upload-and-deploy.sh
docker run --rm \
	--privileged=true \
    -e CONNECT_SERVER="http://ec2-52-90-255-153.compute-1.amazonaws.com:3939/" \
    -e CONNECT_API_KEY=$PUBLISHER_KEY \
    -v $(pwd):/content \
    -w /content \
    rstudio-connect-deployer:latest \
    /content/prog-deploy-jenkins/upload-and-deploy.sh 5c276b83-2eeb-427b-95a6-ac8915e22bfd /content/deployment-bundle
```
