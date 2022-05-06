#!groovy
import groovy.json.JsonSlurperClassic

node { 
    def SF_USERNAME="wilson@instance1.com" // org1 as the dev org
    def SF_HOST="https://login.salesforce.com" 
    def CONNECTED_APP_CONSUMER_KEY="3MVG9pRzvMkjMb6nq5_7vWB7bzj1AvzgpqUcBlXjDx6HcCZKgL5Ck.8WO2aexmvmyF2QrpGG7YHVYw2mEQqqF" // Copy from Salesforce Connected App
    def JWT_KEY_CRED_ID="6ed79457-80b3-4d22-9d97-b7c7926b1bac" // Copy from Jenkins global credentials(server.key) Id
    def toolbelt=tool 'toolbelt'//We create a variable to use sfdx from the Custom Tool that we created in Part I

    stage ( 'Check branch name' )  {  // First step to check that we are on the correct branch 
        println env.BRANCH_NAME 
        if ( env.BRANCH_NAME == "master" ){ // This name is the one we give when selecting the repository within the Pipeline 
            println ( 'Script from master!' )
            println toolbelt
        }
        else{
            println toolbelt
            error 'Incorrect branch'
        }
        checkout scm
    }

    withEnv(["HOME=${env.WORKSPACE}"]) {
        withCredentials([file(credentialsId: JWT_KEY_CRED_ID, variable: 'jwt_key_file')]) {        
            stage('Deployment') {
                script {
                    sh """
                    node --version
                    """
                    sh """
                    ${toolbelt}/sfdx --version
                    """
                }
                // if (isUnix()) {//Para sistemas Unix el comando varía un poco el formato
                //     rc = command "${toolbelt}/sfdx force:auth:logout --targetusername ${SF_USERNAME} -p" //Hacemos logout para evitar un error
                //     // Autorizamos la dev hub org
                //     rc = command "${toolbelt}/sfdx force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${jwt_key_file} --setdefaultdevhubusername --instanceurl ${SF_HOST}"
                // }else{//ejecutamos lo mismo para sistemas Windows
                //     rc = sh returnStatus: true, script:"\"${toolbelt}\" force:auth:logout --targetusername ${SF_USERNAME} -p"
                //     rc = bat returnStatus: true, script: "\"${toolbelt}\" force:auth:jwt:grant --clientid ${CONNECTED_APP_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile \"${jwt_key_file}\" --setdefaultdevhubusername --instanceurl ${SF_HOST}"
                // }
                // println rc
                // if (rc != 0) { error 'Org authorization has failed' }

                // //Realizamos el despliegue de todo force-app
                // if (isUnix()) {
                //     rmsg = sh returnStdout: true, script: "${toolbelt}/sfdx force:source:deploy --sourcepath force-app -u ${SF_USERNAME}"
                // }else{
                // rmsg = bat returnStdout: true, script: "\"${toolbelt}\" force:source:deploy --sourcepath force-app -u ${SF_USERNAME}"
                // }
                
                // printf rmsg
                // println(rmsg)
            }
        }
    }
}