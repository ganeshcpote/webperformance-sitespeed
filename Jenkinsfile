pipeline {
  agent any

  parameters {
		string(name: 'test_name', defaultValue: 'test1', description: 'Specify test name') 
		string(name: 'site_url', defaultValue: 'http://www.google.com', description: 'Target site URL') 
        	choice(name: 'performance_tool', choices: ['sitespeedtest', 'chromeuserexperience', 'lighthouse', 'gpsi'], description: 'Select website performance tool \n sitespeedtest : Using Sitespeed.io \n chromeuserexperience : Using Chrome User Experience Report (CrUx) \n lighthouse : Using Lighthouse \n gpsi : Using Google Page Speed Insights' )
		choice(name: 'browser', choices: ['chrome', 'firefox', 'safari', 'edge'], description: 'Select browser for testing' )
		choice(name: 'crawl_depth', choices: ['1', '2'], description: 'How deep to crawl (1=only one page, 2=include links from first page, etc.)' )
		choice(name: 'crawl_maxPages', choices: ['1', '2'], description: 'The max number of pages to test. Default is no limit.)' )
		choice(name: 'location', choices: ['onprem-mumbai', 'onprem-nagpur', 'azure-mumbai', 'aws-mumbai'], description: 'Select location for testing' )
    }
  stages {
    stage('Run Performance Test') {
      steps {
        script{
          if ("${performance_tool}" == "sitespeedtest"){
            sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 --graphite.host=192.168.0.7 ${site_url} --slug ${test_name} --graphite.addSlugToKey true -b ${browser}  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
          }
	 else if ("${performance_tool}" == "chromeuserexperience"){
            sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 ${site_url} --graphite.host=192.168.0.7 --slug ${test_name} --graphite.addSlugToKey true  --crux.key AIzaSyCP3qXPW-ZMa_TjxfOogEgbRsVRAspo4_4 --crux.formFactor ALL --crux.collect ALL --graphite.namespace sitespeed_io.crux  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
          }
	else if ("${performance_tool}" == "lighthouse"){
            sh 'docker run --shm-size=1g --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --graphite.host=192.168.0.7  --slug ${test_name}  --plugins.add analysisstorer --graphite.namespace sitespeed_io.lighthouse --plugins.add /lighthouse -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output'
          }
	else if ("${performance_tool}" == "gpsi"){
            sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --graphite.host=192.168.0.7  --slug ${test_name}  --graphite.namespace sitespeed_io.gpsi --plugins.add analysisstorer --plugins.add /gpsi  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
          }
          else{
            sh 'echo "Invalid target performance tools selection"'
          }
        }
      }
    }
    stage('Generated Budget Report') {
      steps {
        junit allowEmptyResults: true, testResults: 'junit.xml'
         script {
            currentBuild.result = 'Success'
          }
      }
    }
   stage('Publish HTML Report') {
      steps {
        publishHTML (target: [
	  allowMissing: false,
	  alwaysLinkToLastBuild: false,
	  keepAll: true,
	  reportDir: 'output',
	  reportFiles: 'index.html',
	  reportName: "Web Performance Report - ${performance_tool}"
	])
      }
    }
  }
  post {
        always {  
            cleanWs()
        }
    }
}
