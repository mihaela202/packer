def aws_region_var = ''

if(env.BRANCH_NAME ==~ "^dev.*$"){
    aws_region_var = "us-east-1"
}
else if(env.BRANCH_NAME ==~ "^qa.*$"){
    aws_region_var = "us-east-2"
}
else if(env.BRANCH_NAME ==~ "^master$"){
    aws_region_var = "us-west-2"
}

println(env.BRANCH_NAME)
println(aws_region_var)

node {
    stage('Pull Repo') {
        checkout scm
    }

    withCredentials([usernamePassword(credentialsId: 'jenkins-aws-access-key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
        withEnv(["AWS_REGION=${aws_region_var}", "PACKER_AMI_NAME=apache-${UUID.randomUUID().toString()}"]) {
            stage('Packer Validate') {
                sh 'packer validate apache.json'
            }

            stage('Packer Build') {
                sh 'packer build apache.json'
            }
        }  
    }
}

