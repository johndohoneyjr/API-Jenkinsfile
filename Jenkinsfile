pipeline {   
  agent {
    node {
      label 'master'
    }  
  }
  environment {
       ATLAS_TOKEN           = "${env.ATLAS_TOKEN}"
       aws_access_key_id     = "${env.AWS_ACCESS_KEY_ID}"
       aws_secret_access_key = "${env.AWS_SECRET_ACCESS_KEY}"
       address               = "app.terraform.io"
       organization          = "johndohoneyjr"
       workspace             = "test-api"
       workspaceId           = "ws-wLCcZ16gZJoy6is3"
       ARTIFACTPATH          = "output"
       OUTPUT                = "bundle.tar.gz"
  }
  stages {
    stage ('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('build-tarball') {
        steps {
            sh 'rm -rf $ARTIFACTPATH'
            sh 'mkdir -p $ARTIFACTPATH'
            sh 'tar czf $ARTIFACTPATH/$OUTPUT --exclude $ARTIFACTPATH --exclude Jenkinsfile --exclude .git .'
            }
        }
    stage('Set-Env-Variables') {
      steps {
        sh '''
          curl -X POST \
            https://${address}/api/v2/vars \
            -H "authorization: Bearer $ATLAS_TOKEN" \
            -H 'content-type: application/vnd.api+json' \
            -d '{
                	"data": {
	                	"attributes": {
		                	"key": "AWS_ACCESS_KEY_ID",
		                	"value": "$aws_access_key_id",
		                	"category": "env",
		                	"hcl": false,
		                	"sensitive": true
		                 },
	               	"relationships": {
	                		"workspace": {
		                		"data": {
		               			"type": "workspaces",
		              			"id": "{workspaceId"
			                	}
			               }
		               }
	                }
              }'
        '''
      }
    }
    stage('Configuration-Version') {
       agent {
           docker {
              image 'python:2-alpine'
            }
        }
        steps {
           sh '''
            echo "Workspace ID: " $workspaceId
            echo "Creating configuration version."
            echo '{"data":{"type":"configuration-version"}}' > ./configversion.json
            //configuration_version_result=$(curl --header "Authorization: Bearer $ATLAS_TOKEN" --header "Content-Type: application/vnd.api+json" --data @configversion.json "https://${address}/api/v2/workspaces/${workspaceId}/configuration-versions")

            ''config_version_id=$(echo $configuration_version_result | python -c "import sys, json; print(json.load(sys.stdin)['data']['id'])")
            //upload_url=$(echo $configuration_version_result | python -c "import sys, json; print(json.load(sys.stdin)['data']['attributes']['upload-url'])")
            echo "Config Version ID: " $config_version_id
            //echo "Upload URL: " $upload_url

           '''
        }
    }
    stage('Start-Run') {

      steps {
        sh '''
         echo "Starting"
        '''
      }
    }
    stage('Clean-Up') {
      steps {
        
        cleanWs()
      }
    }
  }
}

