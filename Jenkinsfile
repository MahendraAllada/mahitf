pipeline {
    agent {
        node {
            label 'master'
        }
    }

    stages {
        stage('terraform started') {
            steps {
                sh 'echo "Started...!" '
            }
        }
        stage('terraform clone') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '2fb7dff5-dcb5-4e2f-94d0-90ef7ecc49a6', url: 'https://github.com/MahendraAllada/mahitf.git']]])
            }
        }
        stage('Parameters'){
            steps {
                sh label: '', script: ''' sed -i \"s/user/$access_key/g\" /var/lib/jenkins/workspace/terramahi/variables.tf
sed -i \"s/password/$secret_key/g\" /var/lib/jenkins/workspace/terramahi/variables.tf
sed -i \"s/t2.micro/$instance_type/g\" /var/lib/jenkins/workspace/terramahi/variables.tf
sed -i \"s/10/$instance_size/g\" /var/lib/jenkins/workspace/terramahi/ec2.tf
sed -i \"s/ca-central-1/$instance_region/g\" /var/lib/jenkins/workspace/terramahi/variables.tf
sed -i \"s/ca-central-1a/$subnet_zone/g\" /var/lib/jenkins/workspace/terramahi/variables.tf
sed -i \"s/mahicanada/$key_pair/g\" /var/lib/jenkins/workspace/terramahi/variables.tf
sed -i \"s/ami-054362537f5132ce2/$ami_id/g\" /var/lib/jenkins/workspace/terramahi/vpc.tf
'''
                  }
            }
            
        stage('terraform init') {
            steps {
                sh 'terraform init'
            }
        }
        stage('terraform plan') {
            steps {
                sh 'terraform plan'
            }
        }
        stage('terraform apply') {
            steps {
                sh 'terraform apply  -auto-approve'
                sleep 150
            }
        }
         stage('Application Deployment') {
            steps {
                sh label: '', script: '''pubIP=$(<publicip)
                echo "$pubIP"
                ssh -tt ec2-user@$pubIP
                echo "yes"
                sleep 5
                git clone -b branchPy https://github.com/MahendraAllada/mahitf.git
                sleep 5
                cd mahitf/
                python manage.py migrate
                python manage.py runserver 0.0.0.0:8000'''
            }
        }
       
        
    }
}
