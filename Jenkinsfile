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
		// npm install WORKAROUND: https://github.com/npm/npm/issues/3497#issuecomment-23966536
		sh 'cd client && npm install --unsafe-perm && npm test && npm run build:clean'
	}
}
