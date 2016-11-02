node {

	sh 'npm -v'

	def commit_id = 'unknown'
	stage('Prepare Workspace') {
    checkout scm
		sh 'git clean -fdx'

		sh "git rev-parse HEAD > .git/commit-id"
		commit_id = readFile('.git/commit-id').trim()
		println commit_id
	}

	stage('Build Java Backend') {
		sh './gradlew build'
	}

	stage('Build React Frontend') {
		sh 'export'
		sh 'cd client && npm install && npm test && npm run build:clean'
	}




	stage('Build Docker') {
		docker.withRegistry('http://localhost:5000') {
				def server = docker.build("petclinic-server", "${WORKSPACE}/docker/server")

				server.push 'master'
        server.push "${commit_id}"
    }
	}
}
