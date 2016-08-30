stage 'Checkout'

node {
    git 'https://github.com/helpermethod/simple-jenkins2'

    stash 'src'
}

stage 'Build'

node {
    unstash 'src'

    withEnv(["PATH+MAVEN=${tool 'mvn3'}/bin"]) {
        // build packages once
        sh 'mvn package'
    }

    stash includes: 'target/*.jar', name: 'jar'
}

stage 'Deploy'

node {
    unstash 'jar'

    withEnv(["PATH+MAVEN=${tool 'mvn3'}/bin"]) {
        sh 'mvn jar:jar deploy:deploy'
    }
}