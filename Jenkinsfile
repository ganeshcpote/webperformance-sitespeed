pipeline {
	agent any
  parameters {
	string(name: 'site_url', defaultValue: 'https://www.google.com', description: 'Target site URL') 
	string(name: 'webhook', defaultValue: 'http://requestbin.net/r/h0aj6bgu', description: 'Target webhook URL') 
	choice(name: 'performance_tools', choices: ['sitespeedtest', 'chromeuserexperience', 'lighthouse', 'gpsi'], description: 'Select website performance tool : - \n 1) sitespeedtest : Using Sitespeed.io \n 2) chromeuserexperience : Using Chrome User Experience Report (CrUx) \n 3) lighthouse : Using Lighthouse \n 4) gpsi : Using Google Page Speed Insights' )	  	
	choice(name: 'browser', choices: ['chrome', 'firefox', 'safari', 'edge'], description: 'Select browser for testing' )
	choice(name: 'crawl_depth', choices: ['1', '2', '3', '4', '5'], description: 'How deep to crawl (1=only one page, 2=include links from first page, etc.)' )
	choice(name: 'crawl_maxPages', choices: ['1', '2', '3', '4', '5'], description: 'The max number of pages to test. Default is no limit.)' )
    }
  stages {
    stage('Run Performance Test') {
      steps {
        script{
          if ("${performance_tools}" == "sitespeedtest"){
     	    sh 'docker run --privileged=true --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io-autobuild:webhook ${site_url} --slug ${BUILD_ID} -b ${browser}  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true --webhook.url http://requestbin.net/r/h0aj6bgu --resultBaseURL https://myfiles.com/' 
            //sh 'docker run --privileged=true --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 ${site_url} --slug ${BUILD_ID} -b ${browser}  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true' 	  
          }
	 else if ("${performance_tools}" == "chromeuserexperience"){
	    sh 'docker run --privileged=true --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 ${site_url}  --slug ${BUILD_ID} --crux.key AIzaSyCP3qXPW-ZMa_TjxfOogEgbRsVRAspo4_4 --crux.formFactor ALL --crux.collect ALL -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
          }
	else if ("${performance_tools}" == "lighthouse"){
	    sh 'docker run --privileged=true --shm-size=1g --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --slug ${BUILD_ID}  --plugins.add analysisstorer --plugins.add /lighthouse -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
          }
	else if ("${performance_tools}" == "gpsi"){
	    sh 'docker run --privileged=true --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url}  --slug ${BUILD_ID}  --plugins.add analysisstorer --plugins.add /gpsi  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true '
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
	  reportName: "Web Performance Report - ${performance_tools}"
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
