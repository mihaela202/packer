def aws_region_var = ''

if(env.BRANCH_NAME ==~ "dev.*"){
    aws_region_var = "us-east-1"
}
else if(env.BRANCH_NAME ==~ "qa.*"){
    aws_region_var = "us-east-2"
}
else if(env.BRANCH_NAME ==~ "master"){
    aws_region_var = "us-west-2"
}
else {
    error("Branch Name Doesnt Match RegEx")
}

node {
    stage('Pull Repo') {
        checkout scm
    }

    def ami_name = "apache-${UUID.randomUUID().toString()}"

    withCredentials([usernamePassword(credentialsId: 'jenkins-aws-access-key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
        withEnv(["AWS_REGION=${aws_region_var}", "PACKER_AMI_NAME=${ami_name}"]) {
            stage('Packer Validate') {
                sh 'packer validate apache.json'
            }

            stage('Packer Build') {
                // sh 'packer build apache.json'                
            }

            stage('Create an instance') {
                ami_name = "apache-0be37ffe-6bd7-45f4-83f0-fbfb40461045"
                
                build job: 'terraform-ec2-by-ami-name', parameters: [
                    booleanParam(name: 'terraform_apply', value: true),
                    booleanParam(name: 'terraform_destroy', value: false),
                    string(name: 'environment', value: "${env.BRANCH_NAME}"),
                    string(name: 'ami_name', value: "${ami_name}")
                    ]
            }
        }  
    }
}

