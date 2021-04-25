pipeline {
  agent any

  parameters {
		text(name: 'test_name', defaultValue: 'test1', description: 'Specify test name') }
		text(name: 'site_url', defaultValue: 'http://www.google.com', description: 'Target site URL') }
        choice(name: 'performance_tool', choices: ['sitespeedtest', 'chromeuserexperience', 'lighthouse', 'gpsi'], description: 'Select website performance tool' )
		choice(name: 'browser', choices: ['chrome', 'firefx', 'safari', 'edge'], description: 'Select browser for testing' )
		choice(name: 'crawl_depth', choices: ['1', '2'], description: 'How deep to crawl (1=only one page, 2=include links from first page, etc.)' )
		choice(name: 'crawl_maxPages', choices: ['1', '2'], description: 'The max number of pages to test. Default is no limit.)' )
		choice(name: 'location', choices: ['onprem-mumbai', 'onprem-nagpur', 'azure-mumbai', 'aws-mumbai'], description: 'Select location for testing' )
    }
  stages {
    stage('Run Performance Test') {
      steps {
        script{
          if ("${performance_tool}" == "sitespeedtest"){
            sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 --graphite.host=192.168.0.7 ${site_url} --slug ${test_name} --graphite.addSlugToKey true -b ${browser}  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath /sitespeed.io/budget.json --budget.output junit
          }
          else{
            sh 'echo "Invalid target performance tools selection"'
          }
        }
      }
    }
    stage('Generated Inspector Report') {
      steps {
        junit allowEmptyResults: true, testResults: 'junit.xml'
         script {
            currentBuild.result = 'Success'
          }
      }
    }
  }
  post {
        always {  
            archiveArtifacts artifacts: 'manifest.json, junit.xml'
			publishHTML (target: [
			  allowMissing: false,
			  alwaysLinkToLastBuild: false,
			  keepAll: true,
			  reportDir: 'coverage',
			  reportFiles: 'output/index.html',
			  reportName: "Web Performance Report"
			])
            cleanWs()
        }
    }
}
