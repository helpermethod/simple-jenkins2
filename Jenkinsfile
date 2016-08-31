#!groovy

stage 'Checkout'

node {
    checkout scm

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

    stash 'jar'
}

stage 'Deploy'

node {
    unstash 'jar'

    withEnv(["PATH+MAVEN=${tool 'mvn3'}/bin"]) {
        echo 'mvn jar:jar deploy:deploy'
    }
}

stage 'Acceptance + Performance'

node {
    parallel(
        'acceptance': {
            echo 'Performing automated acceptance tests'
            sleep time: 5, unit: 'SECONDS'
        },
        'performance': {
            echo 'Performing automated performance tests'
            sleep time: 10, unit: 'SECONDS'
        }
    )
}

stage name: 'UAT'

node {
    echo 'Deploying to UAT'
    sleep time: 5, unit: 'SECONDS'
}

timeout(time: 5, unit: 'DAYS') {
    input 'Deploy to production?'
}

stage 'Production'

node {
    echo 'Deploying to production'
}