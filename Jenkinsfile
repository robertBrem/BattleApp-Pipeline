withEnv(["VERSION=1.0.${currentBuild.number}", "REGISTRY_EMAIL=brem_robert@hotmail.com"]) {
  stage "checkout, build, test and publish"
  node {
    git poll: true, url: "https://github.com/robertBrem/battleapp"
    def mvnHome = tool 'M3'
    sh "${mvnHome}/bin/mvn clean install"
    sh "./build.js"
  }
}