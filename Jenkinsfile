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
        sh 'mvn package -Dmaven.test.failure.ignore'
    }

    step $class: 'JUnitResultArchiver', testResults: '**/target/*-reports/TEST-*.xml'

    if (currentBuild.result == 'UNSTABLE') {
        error 'There are test failures'
    }

    stash includes: 'target/*.jar', name: 'jar'
}

stage 'Deploy'

node {
    unstash 'jar'

    withEnv(["PATH+MAVEN=${tool 'mvn3'}/bin"]) {
        echo 'mvn jar:jar deploy:deploy'
    }
}

stage 'Acceptance'

node {
    echo 'Performing automated acceptance tests'
}

stage 'UAT'

node {
    echo "Deploying to UAT"
}

input 'Deploy to production?'

stage name: 'Production', concurrency: 1

node {
    echo "Deploying to production"
}