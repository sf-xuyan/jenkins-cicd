#!groovy
import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY ?: "3MVG9pRzvMkjMb6nq5_7vWB7bzj1AvzgpqUcBlXjDx6HcCZKgL5Ck.8WO2aexmvmyF2QrpGG7YHVYw2mEQqqF" // Copy from Salesforce Connected App
    def SF_USERNAME=env.SF_USERNAME ?: "wilson@instance1.com" // org1 as the dev org
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID ?: "6ed79457-80b3-4d22-9d97-b7c7926b1bac" // Copy from Jenkins global credentials(server.key) Id
    def TEST_LEVEL="RunLocalTests"
    // def PACKAGE_NAME='0Ho1U000000CaUzSAK'
    // def PACKAGE_VERSION
    def SF_INSTANCE_URL=env.SF_INSTANCE_URL ?: "https://login.salesforce.com"

    def toolbelt=tool "toolbelt"

    // -------------------------------------------------------------------------
    // Check out code from source control.
    // -------------------------------------------------------------------------

    stage("checkout source") {
        checkout scm
    }


    // -------------------------------------------------------------------------
    // Run all the enclosed stages with access to the Salesforce
    // JWT key credentials.
    // -------------------------------------------------------------------------
    
    withEnv(["HOME=${env.WORKSPACE}"]) {
        
        withCredentials([file(credentialsId: SERVER_KEY_CREDENTALS_ID, variable: "server_key_file")]) {

            command "${toolbelt}/sfdx --version"

            // -------------------------------------------------------------------------
            // Authorize the Dev Hub org with JWT key and give it an alias.
            // -------------------------------------------------------------------------

            stage("Authorize Org") {
                rc = command "${toolbelt}/sfdx force:auth:jwt:grant --instanceurl ${SF_INSTANCE_URL} --clientid ${SF_CONSUMER_KEY} --username ${SF_USERNAME} --jwtkeyfile ${server_key_file} --setdefaultdevhubusername --setalias ciorg"
                if (rc != 0) {
                    error "Org authorization has failed."
                }
            }


            // -------------------------------------------------------------------------
            // Deploy source to Org.
            // -------------------------------------------------------------------------

            stage("Deploy source") {
                rc = command "${toolbelt}/sfdx force:source:deploy -x manifest/package.xml -l ${TEST_LEVEL} -u ciorg --json"
                if (rc != 0) {
                    error "Salesforce deploy to Org failed."
                }
            }
        }
    }
}

def command(script) {
    if (isUnix()) {
        return sh(returnStatus: true, script: script);
    } else {
        return bat(returnStatus: true, script: script);
    }
}