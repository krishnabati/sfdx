pipeline { 
    agent any

    // options {
    //    timeout(time: 5, unit: 'MINUTES')  // timeout all agents on pipeline if not complete in 5 minutes or less.

    // }

stages {
        stage('Initializing') {
            steps {
                echo "Initializing"

                // determine if the build was trigger from a git event or manually built with parameters
            //    envSetup()

               
            }
        }
        stage('github pipeline') {
            steps {  
                echo "github Sync Target Branch"
                githubCheckout()
            }
        }
       
        stage('SFDX Deploy Target Org') {
            steps {  
                echo "Deploy Running ${env.BUILD_ID} on ${env.JENKINS_URL}"
                salesforceDeploy()
            }
        }
    }
}

def githubCheckout(){
 dir('github-checkout'){
checkout scm
commitChangeset = sh(returnStdout: true, script: 'git diff-tree --no-commit-id --name-status -r HEAD').trim()
echo "github-checkout"
echo "${commitChangeset}"
echo "${commitChangeset.size()}"
}
echo "########################"
echo "########################"
echo "########################"
sh 'ls github-checkout'
echo "Current GIT commit: ${env.GIT_COMMIT}"
echo "Previous known Successful GIT Commit: ${env.GIT_PREVIOUS_SUCCESSFUL_COMMIT}"
}

def salesforceDeploy() {
    authSF()
     def JOBURL = ""
    if("${env.BRANCH_NAME}".contains("/")) {
        JOBURL = "${env.BRANCH_NAME}".replace("/", "_")
    }
    else {
        JOBURL = "${env.BRANCH_NAME}"
    }
    // sh "sudo su -s /bin/bash jenkins"
    // sh "export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true"
   
    def JOBPATH="/var/lib/jenkins/workspace/multi_${JOBURL}"
    sh "cd ${JOBPATH}"
    echo JOBPATH
     
    //def varsfdx = tool 'sfdx'
 // def targetEnvironment='vscodeOrg'

    def varsfdx='/sbin'
    TEST_LEVEL='NoTestRun'
    def VALIDATE_ONLY = false
     deployBranchURL = ""
    if("${env.BRANCH_NAME}".contains("/")) {
        deployBranchURL = "${env.BRANCH_NAME}".replace("/", "_")
    }
    else {
        deployBranchURL = "${env.BRANCH_NAME}"
    }
    def DEPLOYDIR="/var/lib/jenkins/workspace/multi_${deployBranchURL}/force-app/main/default"
    echo DEPLOYDIR
    def SF_INSTANCE_URL = "https://login.salesforce.com"
targetEnvironment="vscodeaws"
sh '''
#!/bin/bash
export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true
echo Above Set Value: $SFDX_USE_GENERIC_KEYCHAIN
echo Shell is: $SHELL
which secret-tool
which sfdx
cd /var/lib/jenkins/workspace/multi_master
sfdx force:auth:sfdxurl:store -f authjenkinsci.txt -a vscodeaws
sfdx force:org:list
//  sfdx force:source:deploy --wait 10 --sourcepath /var/lib/jenkins/workspace/multi_master/force-app/main/default --testlevel NoTestRun -u vscodeaws --json
 '''
// targetEnvironment="vscodeOrg"
// sh "cd /var/lib/jenkins/workspace/multi_${deployBranchURL}"
//  sh "sfdx force:auth:sfdxurl:store -f authjenkinsci.txt -a ${targetEnvironment}"
//  sh "sfdx force:org:list"
//  sh "sfdx force:source:deploy --wait 10 --sourcepath ${DEPLOYDIR} --testlevel NoTestRun -u ${targetEnvironment}--json"

    
   
}

def authSF() {
    echo 'SF Auth method'
    def SF_AUTH_URL
    echo env.BRANCH_NAME
//     sh '''
// export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true
// echo Above Set Value: $SFDX_USE_GENERIC_KEYCHAIN
// echo Shell is: $SHELL
// which secret-tool
// which sfdx
// '''

// SF_AUTH_URL=sh "sfdx force:org:display -u sfdxdevelopment2020@gmail.com --verbose"
    // if ("${currentBuild.buildCauses}".contains("UserIdCause")) {
    //     def fields = env.getEnvironment()
    //     fields.each {
    //         key, value -> if("${key}".contains("${params.target_environment}")) { SF_AUTH_URL = "${value}"; }
    //     }
    // }
    // else if("${currentBuild.buildCauses}".contains("BranchEventCause")) {
    //     if(env.BRANCH_NAME == 'master' || env.CHANGE_TARGET == 'master') {
    //         SF_AUTH_URL = env.SFDX_AUTH_URL
    //     }
    //     else { // {PR} todo - better determine if its a PR env.CHANGE_TARGET?
    //         SF_AUTH_URL = env.SFDX_AUTH_URL
    //     }
    // }
    
    SF_AUTH_URL = env.SFDX_AUTH_URL
    echo SF_AUTH_URL
    writeFile file: 'authjenkinsci.txt', text: SF_AUTH_URL
    sh 'ls -l authjenkinsci.txt'
    sh 'cat authjenkinsci.txt'
    echo 'end sf auth method'
//      if (isUnix()) {

// sh '''
// export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true
// echo Above Set Value: $SFDX_USE_GENERIC_KEYCHAIN
// cd /var/lib/jenkins/workspace/multi_master
//  sfdx force:auth:sfdxurl:store -f authjenkinsci.txt -a targetEnvironment
//  sfdx force:org:list
//  sfdx force:source:deploy --wait 10 --sourcepath /var/lib/jenkins/workspace/multi_master/force-app/main/default --testlevel NoTestRun -u targetEnvironment --json
// echo Shell is: $SHELL
// which secret-tool
// which sfdx'''
//      }
   
}

def command(script) {
   if (isUnix()) { 
//        sh '''
// export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true
// echo Above Set Value: $SFDX_USE_GENERIC_KEYCHAIN
// '''

// sh '''
// export SFDX_USE_GENERIC_UNIX_KEYCHAIN=true
// echo Above Set Value: $SFDX_USE_GENERIC_KEYCHAIN
// cd /var/lib/jenkins/workspace/multi_master
//  sfdx force:auth:sfdxurl:store -f authjenkinsci.txt -a targetEnvironment
//  sfdx force:org:list
//  sfdx force:source:deploy --wait 10 --sourcepath /var/lib/jenkins/workspace/multi_master/force-app/main/default --testlevel NoTestRun -u targetEnvironment --json
// echo Shell is: $SHELL
// which secret-tool
// which sfdx'''

       return sh(returnStatus: true, script: script);
   } else {
       return bat(returnStatus: true, script: script);
   }
}
