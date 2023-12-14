
	Во первых можно настроить несколько параметров в jenkins job. Как?
Делаеш всё что описано в примере №8, точно также указываеш что project parameterized <-- отметить галочкой, заполнить поля, но 
после того как заполнил певый параметр не выходиш а нажимаеш "add" и заполняеш ещё одно такое же поле.
Для того чтобы добавить скрипт в jenkinsfile просто зделай инструкцию 2 раза как описано в 8 примере и jenkinsfile 
должен выглядеть вот так:
pipeline{
	agent any	
	parameters {
		choice choices: ['chrome', 'firefox'], description: 'Select the browser', name: 'BROWSER'
	    	choice choices: ['192.168.56.1', '192.168.100.3', '172.23.96.1'], description: 'Select the gri port', name: 'HUB_HOST'  <-- здееесь и расположен 2 параметр!

	}
	stages{
		stage('Start grid'){
			steps{
				bat "docker-compose -f grid.yaml up --scale ${params.BROWSER}=2 -d"
			}
		}	
		stage('Run tests'){
			steps{
				bat "docker-compose -f test_suite.yaml up"			
			}
		}
	}
	post {
		always {
			bat "docker-compose -f grid.yaml down"
			bat "docker-compose -f test_suite.yaml down"
			archiveArtifacts artifacts: 'output/flight-reservation/emailable-report.html', followSymlinks: false
			archiveArtifacts artifacts: 'output/vendorPortal/emailable-report.html', followSymlinks: false
		}
	}
}





