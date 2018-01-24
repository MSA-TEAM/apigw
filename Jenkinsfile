node {
    stage ('소스체크아웃') {
        checkout([$class: 'GitSCM',
            branches: [[name: '*/master']],
            doGenerateSubmoduleConfigurations: false, extensions: [],
            submoduleCfg: [],
            userRemoteConfigs: [[credentialsId: 'DevPro', url: 'https://devpro.ktds.co.kr/msa/apigw.git']]])
    }
    stage ('스크립팅'){
        sh 'ssh ec2-user@ip-172-31-1-192 "mkdir -p /home/ec2-user/apigw/log"'
        sh 'scp ./start.sh ec2-user@ip-172-31-1-192:/home/ec2-user/apigw'
        sh 'scp ./shutdown.sh ec2-user@ip-172-31-1-192:/home/ec2-user/apigw'
        sh 'ssh ec2-user@ip-172-31-1-192 "chmod a+x /home/ec2-user/apigw/*.sh"'
    }

    stage ('서버 중지'){
        sh 'ssh ec2-user@ip-172-31-1-192 "/home/ec2-user/apigw/shutdown.sh || true"'
    }

    stage ('빌드') {
        sh './gradlew clean build'
    }

    stage ('배포') {
        sh 'scp build/libs/apigw-1.0.0-RELEASE.jar ec2-user@ip-172-31-1-192:/home/ec2-user/apigw'
    }

    stage ('서버 시작') {
        sh 'ssh ec2-user@ip-172-31-1-192 "/home/ec2-user/apigw/start.sh /home/ec2-user/apigw/apigw-1.0.0-RELEASE.jar prd"'
    }

}