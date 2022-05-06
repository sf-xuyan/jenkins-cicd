#!groovy
import groovy.json.JsonSlurperClassic

node {

    def SF_CONSUMER_KEY=env.SF_CONSUMER_KEY // Copy from Salesforce Connected App
    def SF_USERNAME=env.SF_USERNAME // org1 as the dev org
    def SERVER_KEY_CREDENTALS_ID=env.SERVER_KEY_CREDENTALS_ID // Copy from Jenkins global credentials(server.key) Id
    def TEST_LEVEL=env.TEST_LEVEL ?: "RunLocalTests"
    // def PACKAGE_NAME='0Ho1U000000CaUzSAK'
    // def PACKAGE_VERSION
    def SF_INSTANCE_URL=env.SF_INSTANCE_URL
    def KEYCHAINS_PWD=env.KEYCHAINS_PWD

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
            command "/usr/bin/security add-generic-password -a local -s sfdx -w ${KEYCHAINS_PWD}"

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