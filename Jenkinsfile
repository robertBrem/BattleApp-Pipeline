withEnv([   "VERSION=1.0.${currentBuild.number}",
            "REGISTRY_EMAIL=brem_robert@hotmail.com",
            "KUBECTL=kubectl"]) {
  stage "checkout, build, test and publish"
  node {
    git poll: true, url: "https://github.com/robertBrem/battleapp"
    def mvnHome = tool 'M3'
    sh "${mvnHome}/bin/mvn clean install"
    sh "./build.js"
  }

  stage "start test environment"
  node {
    git url: "https://github.com/robertBrem/BattleApp-StartTestEnv"
    sh "./start"
  }
}
