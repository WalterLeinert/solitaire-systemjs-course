stage('CI') {
    node {
        
        notify('Started')
		
		checkout scm
    
        // git branch: 'jenkins2-course', 
        //    url: 'https://github.com/g0t4/solitaire-systemjs-course'
    
        // pull dependencies from npm
        // on windows use: bat 'npm install'
        sh 'npm install'
    
        // stash code & dependencies to expedite subsequent testing
        // and ensure same code & dependencies are used throughout the pipeline
        // stash is a temporary archive
        stash name: 'everything', 
              excludes: 'test-results/**', 
              includes: '**'
        
        // test with PhantomJS for "fast" "generic" results
        // on windows use: bat 'npm run test-single-run -- --browsers PhantomJS'
        sh 'npm run test-single-run -- --browsers PhantomJS'
        
        // archive karma test results (karma is configured to export junit xml files)
        step([$class: 'JUnitResultArchiver', 
              testResults: 'test-results/**/test-results.xml'])
              
    }
}


node('docker') {
    sh 'ls'
    sh 'rm -rf *'
    unstash 'everything'
    sh 'ls'
    
}


// parallel integration testing
stage('Browser Testing') {
   parallel chrome: {
        runTests("Chrome")
    }, firefox: {
        runTests("Firefox")
    }
}


def runTests(browser) {
    node {
        // currently no chome/firefox in jenkins container
        echo "skipping tests"
        
        // sh 'rm -rf *'
        // unstash 'everything'
        // sh "npm run test-single-run -- -- browsers ${browser}"

        // step([$class: 'JUnitResultArchiver', 
        //      testResults: 'test-results/**/test-results.xml'])

    }
}


def notify(status){
    emailext (
      from: "walter.leinert@outlook.com",
      to: "walter.leinert@outlook.com",
      subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
      body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a></p>""",
    )
}