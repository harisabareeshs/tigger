node(){
    stage('Cloning Git') {
        checkout scm
    }

    stage('Install dependencies') {
        nodejs('nodejs') {
            sh 'npm install -g node-gyp'
            sh 'npm install -g npm@7.14.0'
            sh 'npm install --save-dev node-sass@4.14.1'
            sh 'npm ci'
            sh 'npm install --unsafe-perm=true --allow-root --verbose'

            echo "Modules installed"
        }

    }
    stage('Build') {
        nodejs('nodejs') {
           sh 'npm run build'
            echo "Build completed"
        }

    }

    stage('Package Build') {
        sh "tar -zcvf bundle.tar.gz dist/smartClean09"
    }

    stage('Artifacts Creation') {
        fingerprint 'bundle.tar.gz'
        archiveArtifacts 'bundle.tar.gz'
        echo "Artifacts created"
    }

    stage('Stash changes') {
        stash allowEmpty: true, includes: 'bundle.tar.gz', name: 'buildArtifacts'
    }
}

node('e2enode'){
    echo 'Unstash'
    unstash 'buildArtifacts'
    echo 'Artifacts copied'
    echo 'Copy'
    sh "yes | sudo -S | cp -R bundle.tar.gz /var/www/html/hms && cd /var/www/html/hms && tar -xvf bundle.tar.gz" && rm -rf bundle.tar.gz
    echo 'Copy completed'
}
