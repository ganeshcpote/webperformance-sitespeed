pipeline {
	agent {
		node
	    {
		label "${location}"
	    }
	}

  parameters {
		string(name: 'run_id', defaultValue: 'test${BUILD_ID}', description: 'Specify test id') 
		string(name: 'application_name', defaultValue: 'test${BUILD_ID}', description: 'Specify application name')
		string(name: 'site_url', defaultValue: 'https://www.google.com', description: 'Target site URL') 
	  	//string(name: 'performance_tools', defaultValue: 'sitespeedtest', description: 'Select website performance tool : - \n 1) sitespeedtest : Using Sitespeed.io \n 2) chromeuserexperience : Using Chrome User Experience Report (CrUx) \n 3) lighthouse : Using Lighthouse \n 4) gpsi : Using Google Page Speed Insights') 
	       	choice(name: 'performance_tools', choices: ['sitespeedtest', 'chromeuserexperience', 'lighthouse', 'gpsi'], description: 'Select website performance tool : - \n 1) sitespeedtest : Using Sitespeed.io \n 2) chromeuserexperience : Using Chrome User Experience Report (CrUx) \n 3) lighthouse : Using Lighthouse \n 4) gpsi : Using Google Page Speed Insights' )
		
	  	choice(name: 'browser', choices: ['chrome', 'firefox', 'safari', 'edge'], description: 'Select browser for testing' )
	  	//string(name: 'browser', defaultValue: 'chrome', description: 'Select browser for testing') 
	  
		choice(name: 'crawl_depth', choices: ['1', '2'], description: 'How deep to crawl (1=only one page, 2=include links from first page, etc.)' )
	  	//string(name: 'crawl_depth', defaultValue: '1', description: 'How deep to crawl (1=only one page, 2=include links from first page, etc.)' )
	  
		choice(name: 'crawl_maxPages', choices: ['1', '2', '3', '4', '5'], description: 'The max number of pages to test. Default is no limit.)' )
	  	//string(name: 'crawl_maxPages', defaultValue: '1', description: 'The max number of pages to test. Default is no limit.)' )
	  	
	  	//string(name: 'location', defaultValue: 'master', description: 'Select location for testing') 
		choice(name: 'location', choices: ['global', 'pune', 'mumbai'], description: 'Select location for testing' )
	  
	  	
	    	//choice(name: 'influxdb_host', choices: ['10.160.0.9', '35.200.214.141', '192.168.0.7'], description: 'Select Influx DB to push metrics' )
		string(name: 'user_id', defaultValue: 'test', description: 'Specify user name')
    }
  stages {
    stage('Run Performance Test') {
      steps {
        script{
          if ("${performance_tools}" == "sitespeedtest"){
            //sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 --graphite.host=${graphite_host} ${site_url} --slug ${run_id} --graphite.addSlugToKey true -b ${browser}  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
            sh 'docker run --privileged=true --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 --influxdb.host=${influxdb_host} ${site_url} --influxdb.username=root --influxdb.password=root --influxdb.port=8086 --influxdb.database=sitespeeddb  --slug ${run_id} -b ${browser}  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --influxdb.tags "application_name=${application_name},location=${location},user_id=${user_id},run_id=${run_id}" --plugins.remove html' 	  
          }
	 else if ("${performance_tools}" == "chromeuserexperience"){
            //sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 ${site_url} --graphite.host=${graphite_host} --slug ${run_id} --graphite.addSlugToKey true  --crux.key AIzaSyCP3qXPW-ZMa_TjxfOogEgbRsVRAspo4_4 --crux.formFactor ALL --crux.collect ALL --graphite.namespace sitespeed_io.crux  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
	    sh 'docker run --privileged=true --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.1.1 ${site_url} --influxdb.host=${influxdb_host} --influxdb.username=root --influxdb.password=root --influxdb.port=8086 --influxdb.database=sitespeeddb --slug ${run_id} --crux.key AIzaSyCP3qXPW-ZMa_TjxfOogEgbRsVRAspo4_4 --crux.formFactor ALL --crux.collect ALL -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.output junit --budget.suppressExitCode true --influxdb.tags "application_name=${application_name},location=${location},user_id=${user_id},run_id=${run_id}" --plugins.remove html'
          }
	else if ("${performance_tools}" == "lighthouse"){
            //sh 'docker run --shm-size=1g --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --graphite.host=${graphite_host} --slug ${run_id}  --plugins.add analysisstorer --graphite.namespace sitespeed_io.lighthouse --plugins.add /lighthouse -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output'
	    sh 'docker run --privileged=true --shm-size=1g --rm -i -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --influxdb.host=${influxdb_host} --influxdb.username=root --influxdb.port=8086 --influxdb.password=root --influxdb.database=sitespeeddb --slug ${run_id}  --plugins.add analysisstorer --plugins.add /lighthouse -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --influxdb.tags "application_name=${application_name},location=${location},user_id=${user_id},run_id=${run_id}" --plugins.remove html'
          }
	else if ("${performance_tools}" == "gpsi"){
            //sh 'docker run --rm -v "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --graphite.host=${graphite_host} --slug ${run_id}  --graphite.namespace sitespeed_io.gpsi --plugins.add analysisstorer --plugins.add /gpsi  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output --budget.configPath budget-old.json --budget.output junit --budget.suppressExitCode true'
	    sh 'docker run --privileged=true --rm -v -i "$(pwd):/sitespeed.io" sitespeedio/sitespeed.io:17.0.0-plus1 ${site_url} --influxdb.host=${influxdb_host} --influxdb.username=root --influxdb.password=root --influxdb.port=8086 --influxdb.database=sitespeeddb --slug ${run_id}  --plugins.add analysisstorer --plugins.add /gpsi  -d ${crawl_depth} -m ${crawl_maxPages} --outputFolder output  --budget.suppressExitCode true --influxdb.tags "application_name=${application_name},location=${location},user_id=${user_id},run_id=${run_id}" --plugins.remove html'
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
		script{
		   sh "curl -X PUT -H 'Content-Type: application/json' -d '{\"run_id\":\"${run_id}\", \"node_name\":\"${NODE_NAME}\", \"build_status\":\"${currentBuild.currentResult}\"}' 'http://${API_SERVER_IP}:8080/job/updatestatus'"
		}
            cleanWs()
        }
    }
}
