pipeline {   
  agent {
    node {
      label 'master'
    }  
  }
  environment {
       ATLAS_TOKEN           = "${env.ATLAS_TOKEN}"
       AWS_ACCESS_KEY_ID     = "${env.AWS_ACCESS_KEY_ID}"
       AWS_SECRET_ACCESS_KEY = "${env.AWS_SECRET_ACCESS_KEY}"
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
	  echo "Quoting Hell"
	  echo "Hello = ${AWS_SECRET_ACCESS_KEY}"
	  echo 'Hello - \"$AWS_SECRET_ACCESS_KEY\"'
	  echo "{ \"KEY\" : \"$AWS_SECRET_ACCESS_KEY\"}"
	  echo ${AWS_SECRET_ACCESS_KEY}
	  echo
          curl -X POST \
            https://${address}/api/v2/vars \
            -H "authorization: Bearer $ATLAS_TOKEN" \
            -H "content-type: application/vnd.api+json" \
            -d '{
                	"data": {
	                	"attributes\": {
		                	"key": "AWS_ACCESS_KEY_ID\",
		                	"value": "$AWS_ACCESS_KEY_ID",
		                	"category": "env",
		                	"hcl": false,
		                	"sensitive": false
		                 },
	               	"relationships": {
	                		"workspace": {
		                		"data": {
		               			"type": "workspaces",
		              			"id": "ws-wLCcZ16gZJoy6is3"
			                	}
			               }
		               }
	                }
              }'
	      
          curl -X POST \
            https://${address}/api/v2/vars \
            -H "authorization: Bearer $ATLAS_TOKEN" \
            -H 'content-type: application/vnd.api+json' \
            -d '{
                	"data": {
	                	"attributes": {
		                	"key": "AWS_SECRET_ACCESS_KEY",
		                	"value": \"$AWS_SECRET_ACCESS_KEY\",
		                	"category": "env",
		                	"hcl": false,
		                	"sensitive": false
		                 },
	               	"relationships": {
	                		"workspace": {
		                		"data": {
		               			"type": "workspaces",
		              			"id": "ws-wLCcZ16gZJoy6is3"
			                	}
			               }
		               }
	                }
              }'
        '''
      }
    }
    stage('Configuration-Version') {
        steps {
           sh '''
            echo "Workspace ID: " $workspaceId
	    echo "Creating configuration version."
            echo '{"data":{"type":"configuration-version"}}' > ./configversion.json
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

