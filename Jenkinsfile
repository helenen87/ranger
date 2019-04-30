pipeline {
    agent any
    parameters {
        choice(
            name: 'action',
            choices: ['full_build','normal_build'],
            description: "Choose apply to create resoures, destroy to tear them down"
        )
        choice(
            name: 'environment',
            choices: ['lab','nonprod','prod'],
            description: "Choose the destination account"
        )
     }

    stages {
        stage('Default Build') {
            when {
                expression {params.action == 'full_build'}
            }
            steps {
                echo 'Building Ranger (full)...'
                sh 'build_ranger_using_docker.sh'
            }
        }
        stage('Normal Build') {
            when {
                expression {params.action == 'normal_build'}
            }
            steps {
                pwd
                ls -lrta
                echo 'Building Ranger (normal)...'
                sh 'build_ranger_using_docker.sh mvn -Pall clean install -DskipTests=true'
            }
        }
        stage('Deploy to Lab') {
            when {
                expression {params.environment == 'lab'}
            }
            steps {
                echo 'Deploying to lab account..'
                withAWS([role: 'arn:aws:iam::701603852992:role/endplatgnda-sandbox-iamr-bmx', region: 'us-west-2']) {
                  s3Upload(file:'target', bucket:'adapt-sandbox-shared-s3-us-west-2', path:'/ranger/target/')
                }
            }
        }
        stage('Deploy to Nonprod') {
            when {
                expression {params.environment == 'nonprod'}
            }
            steps {
                echo 'Deploying to nonprod account..'
                withAWS([role: 'arn:aws:iam::237901581861:role/endplatgnda-nonprod-iamr-bmx', region: 'us-west-2']) {
                  s3Upload(file:'target', bucket:'adapt-sandbox-shared-s3-us-west-2', path:'/ranger/target/')
                }
            }
        }
        stage('Deploy to Prod') {
            when {
                expression {params.environment == 'prod'}
            }
            steps {
                echo 'Deploying to prod account..'
                withAWS([role: 'arn:aws:iam::857069700355:role/endplatgnda-prod-iamr-bmx', region: 'us-west-2']) {
                  s3Upload(file:'target', bucket:'adapt-sandbox-shared-s3-us-west-2', path:'/ranger/target/')
                }
            }
        }
    }
}
