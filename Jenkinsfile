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

  stage "ui test"
  node {
    git url: "https://github.com/robertBrem/BattleApp-UIT"
    def mvnHome = tool 'M3'
    sh "${mvnHome}/bin/mvn clean install failsafe:integration-test"
    step([$class: 'JUnitResultArchiver', testResults: '**/target/failsafe-reports/TEST-*.xml'])
  }

  stage "last test"
  node {
    git url: "https://github.com/robertBrem/BattleApp-LT"
    def mvnHome = tool 'M3'
    sh "${mvnHome}/bin/mvn clean verify -Dperformancetest.webservice.host=disruptor.ninja -Dperformancetest.webservice.port=31080 -Dperformancetest.webservice.threads=5 -Dperformancetest.webservice.iterations=500"
    archiveArtifacts artifacts: 'target/reports/*.*', fingerprint: true
  }

  stage "consumer driven contract test"
  node {
    git url: "https://github.com/robertBrem/BattleApp-CDCT"
    def mvnHome = tool 'M3'
    sh "PORT=31080 ${mvnHome}/bin/mvn clean install failsafe:integration-test"
    step([$class: 'JUnitResultArchiver', testResults: '**/target/failsafe-reports/TEST-*.xml'])
  }

  stage "manual testing"
  input "everything ok?"

  stage "start canary"
  input "deploy the canary?"
  node {
    git url: "https://github.com/robertBrem/BattleApp-Canary"
    sh "./start.js"
  }
}
