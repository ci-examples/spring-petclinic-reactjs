node {
	def commit_abbrev = 'unknown'
	stage('Prepare Workspace') {
    checkout scm
		sh 'git clean -fdx'

		def out = sh script: 'git describe --always HEAD', returnStdout: true
		commit_abbrev = out.trim()
		println commit_abbrev
	}

	stage('Build') {
      parallel (
          backend: {
            sh './gradlew build'
          },
          client: {
            sh 'export'
  		    	sh 'cd client && yarn install && yarn test && yarn run build:clean'
          },
          failFast: true
      )
	}

	stage('SonarQube analysis') {
    withSonarQubeEnv('sonar') {
      // requires SonarQube Scanner for Gradle 2.1+
      sh './gradlew cobertura sonarqube'
    }
  }

	stage('Build Docker') {
		sh './gradlew prepareDocker'
		docker.withRegistry('http://localhost:5000') {
					def server = docker.build("petclinic-server", "${WORKSPACE}/build/docker/server")

					server.push 'latest'
    			server.push "${commit_abbrev}"

					def client = docker.build("petclinic-client", "${WORKSPACE}/build/docker/client")

					client.push 'latest'
          client.push "${commit_abbrev}"
        }

		sh "./gradlew copyDockerComposeFile -Pdocker.server.image=petclinic-server.${commit_abbrev} -Pdocker.client.image=petclinic-client.${commit_abbrev}"
	}

	stage('Archive') {
		archiveArtifacts artifacts: 'build/reports/**'
		archiveArtifacts artifacts: 'build/docker/docker-compose.yml'
		publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'build/reports/cobertura', reportFiles: 'index.html', reportName: 'Cobertura'])
		publishHTML([allowMissing: true, alwaysLinkToLastBuild: false, keepAll: true, reportDir: 'build/reports/tests/test', reportFiles: 'index.html', reportName: 'Tests'])
	}
}
