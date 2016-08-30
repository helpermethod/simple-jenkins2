stage 'Checkout'

node {
    git 'https://github.com/helpermethod/simple-jenkins2'
    stash 'src'
}

stage 'Build'

node {
    unstash 'src'

    withEnv(["PATH+MAVEN=${tool 'mvn3'}/bin"]) {
        sh 'mvn package'
    }
}