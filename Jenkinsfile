node {

	sh 'npm -v'

	stage('Prepare Workspace') {
    checkout scm
		sh 'git clean -fdx'
	}

	stage('Build Java Backend') {
		sh './gradlew build'
	}

	stage('Build React Frontend') {
		sh 'export'
		sh 'cd client && npm install && npm test && npm run build:clean'
	}
}
