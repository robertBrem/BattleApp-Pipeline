withEnv([   "VERSION=1.0.${currentBuild.number}",
            "REGISTRY_EMAIL=brem_robert@hotmail.com",
            "KUBECTL=kubectl",
            "HOST=disruptor.ninja"]) {
  stage "checkout, build, test and publish"
  node {
    git url: "https://github.com/robertBrem/battleapp"
    def mvnHome = tool 'M3'
    sh "${mvnHome}/bin/mvn clean install"
    sh "./build.js"
  }

  stage "start test environment"
  node {
    git url: "https://github.com/robertBrem/BattleApp-StartTestEnv"
    sh "./start.js"
  }

  stage "system test"
  node {
    git url: "https://github.com/robertBrem/BattleApp-ST"
    def mvnHome = tool 'M3'
    sh "PORT=31080 ${mvnHome}/bin/mvn clean install failsafe:integration-test"
    step([$class: 'JUnitResultArchiver', testResults: '**/target/failsafe-reports/TEST-*.xml'])
  }
}
