node {

	sh 'npm -v'

	def commit_abbrev = 'unknown'
	stage('Prepare Workspace') {
    checkout scm
		sh 'git clean -fdx'

		sh "git describe --always HEAD > .git/commit-abbrev"
		commit_abbrev = readFile('.git/commit-abbrev').trim()
		println commit_abbrev
	}

	stage('Build Java Backend') {
		sh './gradlew build'
	}

	stage('Build React Frontend') {
		sh 'export'
		sh 'cd client && npm install && npm test && npm run build:clean'
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
	}
}
